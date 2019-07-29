## 虚拟DOM

可以参考[回流和重绘](https://github.com/528066535/summary-web/blob/master/file/hc.md)

### 一. 正常DOM的渲染过程

第一步，用HTML分析器，分析HTML元素，构建一颗DOM树(标记化和树构建)。   

第二步，用CSS分析器，分析CSS文件和元素上的inline样式，生成页面的样式表。   

第三步，将DOM树和样式表，关联起来，构建一颗Render树(这一过程又称为Attachment)。每个DOM节点都有attach方法，接受样式信息，
返回一个render对象(又名renderer)。这些render对象最终会被构建成一颗Render树。   

第四步，有了Render树，浏览器开始布局，为每个Render树上的节点确定一个在显示屏上出现的精确坐标。   

第五步，Render树和节点显示坐标都有了，就调用每个节点paint方法，把它们绘制出来。

### 二. 什么是虚拟DOM？虚拟DOM是为了解决什么样的问题？

早期我们在写界面的都是基本都是通过JS或者JQuery来操作DOM，当我们要面对越来越复杂的界面的时候，我们对应的状态也越来越多，DOM的操作也会
越来越频繁了。这样也会导致代码越来越复杂，逻辑也越来越混乱，性能也会直线下降，稍微多一些的列表或者说状态的更改，就会导致界面卡着不动了。

这种对应的操作被称作命令式操作，虽然简单易用，但是显然已经不适用于复杂的需求开发了。于是，出现了另外一种操作，叫做声明式操作，我们只需要
关注状态。

声明式就像你告诉你朋友画一幅画，你不用去管他怎么画的细节。

命令式就像按照你的命令，你朋友一步步把画画出来。

所以声明式操作DOM，我们只需要关注状态，状态就是对应的JS中任意的数据了。框架会帮助我们渲染。

而最简单的渲染方式就是每次当数据发生变化，把旧的DOM删除，创建新的DOM，但是这样也会引发和命令式操作一样的复杂数据的问题，于是，为了解决
这个问题，出现了虚拟DOM。

虚拟DOM的解决方式是通过状态生成的一个虚拟节点（VNode）树，再使用虚拟节点树进行渲染。在渲染之前，会将新生成的虚拟节点和旧的虚拟DOM对比，
只渲染不同的虚拟DOM树。这个对比的算法就是diff算法。

### 三. VNode

#### 什么是VNode？

简单的说VNode就是一个类，VNode创建的实例是用来描述一个DOM元素的。它可以是元素节点、文本节点、注释节点、组件节点、函数式组件、克隆节点。
而所有的真的的DOM都是通过VNode的实例插入到界面的。

#### VNode的作用

我们知道了界面每次渲染都是根据VNode的实例创建真实的DOM然后再渲染到界面上，所以我们可以把上一次的VNode实例缓存起来，当再次渲染的时候，与旧
的实例对比，然后再发现不同的地方，只渲染不同的地方就行。

Vue对状态的侦测采用的是组件级别的，也就是说当状态发生变化，只通知对应状态的组件，渲染对应的组件。也就是说一个组件中的其中一个状态发生变化，
就会渲染整个组件了。

但是如果一个组件内只有细微的一点点变化，而将整个组件重新渲染，这样比较浪费性能，所以又回到了上面的那句话，我们可以把上一次的VNode实例缓存
起来，当再次渲染的时候，与旧的实例对比，然后再发现不同的地方，只渲染不同的地方就行。

### 四. 总结

虚拟DOM是通过状态生成虚拟节点树，再根据虚拟节点树进行渲染，为了节省性能，每次渲染之前会对比上次渲染前的虚拟DOM，只渲染不同的部分。

Vue2.0变化侦测的粒度是中等粒度，也就是组件级别的，与Vue1.0对比，Vue1.0的粒度是每个状态，所以需要的watch数量非常庞大，需要大量的内存
所以组件级别的侦测会大大缩减依赖数量和watcher数量。

Vue中是通过模板来描述状态与师徒之间的映射关系，所以会先把模板编译成渲染函数，再通过渲染函数生成虚拟节点，最后再用虚拟节点更新视图。

每个虚拟节点表示一个真实的DOM元素，而当一个状态发生变化时，会对这个状态所在的组件进行更新，每次渲染视图前会对比新的虚拟DOM和老的虚拟DOM，
只对需要更新的部分渲染。