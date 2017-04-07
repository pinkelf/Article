### Keynote
1. React Developer Tools安装数量 50W+
2. Instagram app 使用React Native的情况
3. Airbnb app使用React Native
4. Facebook主APP大量使用React，(Hybrid模式：H5还是RN?)
5. 新特性如:React VR
6. 性能优化？

### Incrementally Adopting React Native at Facebook
1. 独立的RN APP: ads manager
2. Incrementally Adopting on main apps
讨论了一下几个方面:
  - Performance
    - startup time主要分布在两个地方
      - load up javascript engine and js bundle
        - only execute the requires needs for initial view
        - need not load all of native modules
      - run once procession: logic specific to each of views and network fetch to get data
        - parallel data fetching
  - ...
  - Develop Experience
