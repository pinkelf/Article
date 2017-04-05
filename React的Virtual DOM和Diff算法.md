Virtual DOM概念
DOM是什么，Virtual DOM是什么，为什么引入Virtual DOM
http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/
https://www.codecademy.com/articles/react-virtual-dom

Virtual DOM Diff算法? 时间复杂度
- DOM Diff算法是什么？需要解决的问题是什么？一颗树结构转换到另一个树结构，
- 传统循环递归对节点进行依次对比，效率低下，算法复杂度达到 O(n^3)
- React如何优化Diff算法效率  O(n)
  源码在 ReactMultiChild.js中
  - diff策略
    - DOM 节点跨层级的移动操作少到可以忽略不计
    - 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
    - 对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。
  - 三个粒度的diff
    - tree diff    
      对树进行分层比较，两棵树只会对同一层次的节点进行比较
      https://pic1.zhimg.com/0c08dbb6b1e0745780de4d208ad51d34_b.png

      如果出现了 DOM 节点跨层级的移动操作，React diff 会有怎样的表现呢？
      https://pic2.zhimg.com/d712a73769688afe1ef1a055391d99ed_b.png
      React diff 的执行情况：create A -> create B -> create C -> delete A。

      当出现节点跨层级移动时，并不会出现想象中的移动操作，而是以 A 为根节点的树被整个重新创建，这是一种影响 React 性能的操作，因此 React 官方建议不要进行 DOM 节点跨层级的操作。

    - component diff
      如果是同一类型的组件，按照原策略继续比较 virtual DOM tree。

      如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点。

      对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切的知道这点那可以节省大量的 diff 运算时间，因此 React 允许用户通过 shouldComponentUpdate() 来判断该组件是否需要进行 diff。

    - element diff

      当节点处于同一层级时，React diff 提供了三种节点操作，分别为：INSERT_MARKUP（插入）、MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。

      INSERT_MARKUP，新的 component 类型不在老集合里， 即是全新的节点，需要对新节点执行插入操作。

      MOVE_EXISTING，在老集合有新 component 类型，且 element 是可更新的类型，generateComponentChildren 已调用 receiveComponent，这种情况下 prevChild=nextChild，就需要做移动操作，可以复用以前的 DOM 节点。

      REMOVE_NODE，老 component 类型，在新集合里也有，但对应的 element 不同则不能直接复用和更新，需要执行删除操作，或者老 component 不在新集合里的，也需要执行删除操作。

      https://pic2.zhimg.com/7541670c089b84c59b84e9438e92a8e9_b.png

      https://pic4.zhimg.com/c0aa97d996de5e7f1069e97ca3accfeb_b.png

      异常情况
      https://pic2.zhimg.com/1b8dac5b9b3e4452dec8d5447d7717ad_b.png

- 总结
  - React 通过制定大胆的 diff 策略，将 O(n3) 复杂度的问题转换成 O(n) 复杂度的问题；

  - React 通过分层求异的策略，对 tree diff 进行算法优化；

  - React 通过相同类生成相似树形结构，不同类生成不同树形结构的策略，对 component diff 进行算法优化；

  - React 通过设置唯一 key的策略，对 element diff 进行算法优化；

  - 建议，在开发组件时，保持稳定的 DOM 结构会有助于性能的提升；

  - 建议，在开发过程中，尽量减少类似将最后一个节点移动到列表首部的操作，当节点数量过大或更新操作过于频繁时，在一定程度上会影响 React 的渲染性能。


差异应用到DOM树

参考资料
- https://zhuanlan.zhihu.com/p/20346379
