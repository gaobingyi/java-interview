# Spring

### Spring的生命周期

- 实例化
- 属性赋值
- 初始化
- 销毁

Bean的扩展：可以在不同的时刻，通过不同的BeanPostProcessor和Aware对Bean进行扩展

> https://zhuanlan.zhihu.com/p/158468104

### Spring的循环依赖

针对单例bean的setter注入，使用“三级缓存”提前暴露早期对象，解决循环依赖问题

- 一级缓存：singtonObjects，完整的对象
- 二级缓存：earlySingthonObjects，早期（不完整）的对象，由三级缓存中对象工厂创建
- 三级缓存：singtonFactories，若对象需要被代理，则创建的是代理对象

对象的获取：singtonObjects -> earlySingthonObjects -> singtonFactories

只有在发生循环依赖的情况下才会用到earlySingthonObjects和singtonFactories，也只有在发生循环依赖时，才会在实例化阶段提前创建代理对象（正常情况下，代理对象是在初始化阶段才创建的）

> https://juejin.cn/post/7201785917061021751
> 