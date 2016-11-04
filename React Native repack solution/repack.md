## React Native 分包方案  
&emsp;&emsp;由于React Native在业务代码打包时，会将所有依赖的js都打包成一个完整的js.bundle，因此导致包体积较大。在处理多页面或者多业务场景时，会使用到多个包。这种情况下，包过大会导致大量的冗余代码下载，很慢的加载速度，用户体验很差，因此需要进行分包。

### 方案
&emsp;&emsp;考虑到依赖的js基础库基本完全相同，因此想到将公共部分独立提取出来，作为基础js库，直接内置在客户端本地，供业务代码依赖使用。  
现行的方案有两种:
- 使用脚本进行处理，在打包时即将js.bundle分拆为common.js和business.js。将common.js内置在客户端，business.js则部署在服务端。由于现有的RN框架不支持多js加载，因此客户端在下载完business.js后，需要先合并js，然后再加载。（携程使用的是此方案)

![](https://github.com/ustcqidi/Image/blob/master/rn1.png?raw=true)  

- 首先建立一个无业务代码的RN工程，并打包，得到base.bundle并内置在客户端。然后打包正常的业务工程，得到js.bundle。对两者进行bsdiff，获取patch，并部署在服务端。客户端下载patch后，apply patch，获得完整的js.bundle，再进行加载。（58使用的是此方案）

![](https://github.com/ustcqidi/Image/blob/master/rn2.png?raw=true)  

&emsp;&emsp;对比两种方案，前者的打包处理脚本较为复杂，同时存在RN版本间的兼容性问题。后者使用bsdiff，更加稳定和通用。因此，我们使用后者作为我们的分包方案。

### 流程
1. 为特定版本的RN制作无业务逻辑的base.bundle，并内置在客户端中。
2. 业务开发正常开发调试打包业务。
3. 使用bsdiff得到diff文件，并部署在服务端。
4. 客户端下载diff文件，并apply到本地的base.bundle上，得到完整的js.bundle。
5. 客户端加载并运行RN页面。  

![](https://github.com/ustcqidi/Image/blob/master/rn3.jpeg?raw=true)  

### 数据
&emsp;&emsp;使用最新的电商demo以及之前的独立转盘demo进行测试，diff的效率很理想，比js.bundle和base.bundle之差更小一些。  

| base.bundle   | js.bundle     | patch.diff  |
|:-------:|:------:|:-----:|
| 558,777字节     | 798,113 字节 | 79,835 字节 |
| 558,777字节     | 581,480 字节  |   18,821 字节 |

&emsp;&emsp;对电商demo，在荣耀7上进行apply测试若干次，其中：
- 耗时时间基本在5ms-20ms之间。
- cpu占用在5%以下。
- 内存分配稳定，基本可以忽略。


### RN包部署说明
&emsp;&emsp;由于RN的全量js.bundle较大，且有较多的共用代码重复，因此考虑使用基于bsdiff的分包方案。为此，只在服务端放置业务的diff.patch文件，在客户端则内置共用代码。然后客户端动态获取patch文件，并在本地merge，得到完整可用的js.bundle。

#### JsPatch方案
&emsp;&emsp;在现有的客户端代码中，JsPatch的实现方案与我们的需求最为类似，具有参考意义。
其实现方式如下:
- 当patch后台进行任何修改时，会更新时间戳。
- 在原有的接口initLogin时，获取关于hotfix的时间戳数据。(jspatch也会获取相关的开关数据，androidhotfix则只获取时间戳)
- 比较获得的时间戳与本地保存的时间戳。如果不相等，则认为有更新，并通过/fusion/3.0/hotfix/js接口获取patch的全部信息。
- 根据获取到的版本和签名，决定是否需要从特定地址进行下载并更新。

#### RN方案分析
&emsp;&emsp;JsPatch是由id唯一确定的，而RN的patch则是由业务方id和客户端中内置的RN框架版本两者共同唯一确定的。
在initLogin的逻辑中，RN需要和jspatch保持一致，使用时间戳和相应的开关接口。 RN的patch不是直接使用的，而是需要在本地再做一次操作的。因此，服务端最好能存有两份md5的文件校验值，一份是patch本身的，一份是完整文件本身的。

&emsp;&emsp;以JsPatch的方案为基础，依据上述三点，进行简单修改，即可获得RN的分包方案。

&emsp;&emsp;对于initLogin接口，增加reactnative和reactnative_switch字段，内容依旧为时间戳和功能开关。
定义新接口，比如"/fusion/3.0/reactnative/js"。登陆时，用获取到的reactnative的时间戳，与本地保存的时间戳做比较。如果不同，则去新定义的接口获取数据。
新的请求接口沿用原有的参数，后台需要根据app_version过滤对应版本的patch后，返回给客户端。
客户端拿到数据后，比较相同业务的version是否相同，如果服务端较新，则从patch地址下载zip包。
新的接口的返回值，需要增加新的字段，代表所对应的业务的id和业务单独的开关。另外，由于需要考虑zip下载失败的情况，因此，再增加一个字段，允许直接配置全量包的地址。
基于以上，方案设计如下：

&emsp;&emsp;initLogin接口增加字段：

|JSON节点	|类型	|含义|
|:------:|:------:|:------:|
|reactnative	|long	 	|后台上次操作的时间戳，与jspatch的设计方式一致
|reactnative_switch	|bool	 	|RN的全局开关。（在灰度测试或者厂商黑名单等情况下考虑使用）

### rn数据请求接口
&emsp;&emsp;设计新的接口/fusion/3.0/reactnative/js以请求数据。接口参数与JsPatch的请求格式一致。特别注意：这里使用app的version而不是使用rn框架的version，方便后台管理。

### rn数据返回格式
|JSON节点	|类型	|取值范围	|含义|
|:------:|:------:|:------:|:------:|
|code	|int	|0-接口处理成功 其他值表示接口处理失败	 ||
|patches|	JSON Object	 ||	Patch 列表

|JSON节点	|类型	|含义
|:------:|:------:|:------:|
|patches[i]/download|	String|	patch ZIP包的下载地址
|patches[i]/sig	|String	|patch ZIP包的签名
|patches[i]/version	|String	|patch 版本号
|patches[i]/id	|String	|patch 的唯一编号
|patches[i]/bid	|String	|patch 对应的biz的id
|patches[i]/switch	|String	|patch 的开关
|patches[i]/addr	|String	|js.bundle的全量线上地址，作为备用方案

### 后台管理系统
- 新增全量js.bundle的线上地址配置项。客户端会使用此项作为备用方案。
- 基线版本配置项可使用诸如"7.10~7.12"以及"7.12+"这种格式，来使一个业务zip包匹配多版本。


### patch自动制作系统
&emsp;&emsp;为了方便业务开发，提供良好的用户体验，我方需要提供patch自动制作的系统，使得业务方可以提交完整的js.bundle并自动运行得到可在线上配置的rn patch。功能点如下：
- 允许业务方上传完整的js.bundle。
- 系统自动上传js.bundle到cdn，并获得其路径。
- 业务方选择需要的rn/app版本，系统通过调用内置的空bundle进行bsdiff，生成diff.patch。
- 系统同时生成config文件，格式为"json"，添加客户端需要的字段，比如md5等。
- 系统生成diff.patch+config的完整zip包。
- 系统自动将zip上传到后台配置系统，并设置好配置项。
