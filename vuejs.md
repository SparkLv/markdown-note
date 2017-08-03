#Vue
##Vue实例
###构造器  
每个Vuejs应用都是通过构造函数Vue创建一个Vue的根实例启动的  
在实例化Vue时，需要传入一个选项对象，可以包含数据，模板，挂在元素，方法，生命周期钩子等选项。  
所有Vuejs组件其实都是被扩展的Vue实例。  
###属性与方法  
每个Vue实例都会代理其data对象里所有的属性，并且只有这些被代理的属性是响应的。但是，如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。  
除了data属性，Vue实例暴露一些有用的实例属性和方法，这些属性和方法都有前缀$。
###生命周期  
每个Vue实例在被创建之前都要经过一系列的初始化过程。在这个过程中，实例会调用一些生命周期钩子，钩子的this指向调用它的Vue实例。  
![vue-life](img/vue-life-cycle.png)