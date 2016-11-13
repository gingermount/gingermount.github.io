# React “双向”数据绑定

  双向数据绑定的概念从angularJs开始流行，而在react的应用中，同样有很多这样的需求场景。
  准确的说，react强调的是数据的单向流动性，应该叫做“单向”数据绑定。但是单向数据流也总会有数据的来源，当数据来源于用户输入时，那么所示现的效果，也就相当于其他框架中的“双向”数据绑定。下面说一下如何实现这种效果。一种是不用插件的传统写法，而另一种是用插件后，更为简捷的写法。
### 一、不用插件的传统写法
  
  不用插件的思路基本都是通过在react中监听一个onChange事件来实现，从可输入的DOM元素中获取数据源，然后通过onChange事件来调用setState方法改变相应的值，就能够实现双向绑定的效果。
  下面的例子给出了一个不用插件的传统写法的例子：
 
 ```js
  import React from 'react'

  var Native = React.createClass({
    getInitialState() {
        return {
          showText:''
        }
    },
    onChange(e){
        console.log(event.target);
        this.setState({
          showTest:e.target.value
        })
    },
    render:function(){
        var showTest = this.state.showTest;
        return (
            <div>
                <input type="text" onChange={this.onChange} value={showText} />
                <span>{showText}</span>
            </div>
        )
     }
   });
 ```
 这个例子是我们经常用到的一种写法，思路和数据流向都很清晰，然而如果页面上的输入项特别多，我们要全部实现绑定，需要频繁得写方法去更新state，那么用这种写法就比较繁琐。为了更简洁的，能容易大量进行绑定，可以用插件来进行简化。
 

### 二、利用LinkedStateMixin

  使用LinkedStateMixin，可以在当前组件中添加一个叫做linkState()的方法。这个方法可以返回一个ReactLink对象，包含state当前的值和一个用来改变它的回调函数。也就是相当于集成了上面传统写法中自己实现的部分。
  LinkedStateMixin在React.addons.js中，如下所示，
 ```js
    React.addons = {
        CSSTransitionGroup:ReactCSSTransitionGroup,
        LinkedStateMixin:LinkedStateMixin,
        PureRednerMixin:ReactComponentWihtPureRednerMixin,
        TransitionGroup:ReactTransitionGroup,
        batchedUpdates:ReactUpdates.batchedUpdates,
        classSet:cx,
        cloneWithProps:classWithProps,
        createFragement:ReactFragment.create,
        update:update
    };  

 ```
   使用LinkedStateMixin主要原理是用linkState()返回一个ReactLink对象，然后有这个对象来处理相应的参数。那么如何在组件中引入这个对象呢？react.js中提供了一个mixins的函数，可以将多个引用对象以数组形式展示出来。所以我们可以用mixins来引用公共插件LinkedStateMixin。
   同样实现与上一个实现方案中一样的例子，代码如下：
   
 ```js
    var withPlug = React.createClass({
    mixins:[React.addons.LinkedStateMixin],
    getInitialState() {
        return {
          showText:''
        }
    },
    render:function(){
        var showTest = this.state.showTest;
        return (
            <div>
                <input type="text" valueLink = {this.linkState('showText')} />
                <span>{showText}</span>
            </div>
        )
     }
   });
 ```
   通过上面的例子可以看到，ReactLink其实本质上只是提供了onchange setState模式的简单包装和约定。要使用它，其约定的形式为：
   
   1、需要mixins添加引用
   
   2、原先的value绑定换成valueLink。参数从this.state.XX换成this.linkState('XX')
   
   其中Reactlink对象也可以在组件间通过props传递。
   
   还应该注意的一点是，在输入项中，checkbox的情况有其不同，因为它的value属性的改变行为发生的时机有区别。checkbox若被选中，其value属性值是在表单提交的时候发送出去，而在选中和非选中的状态切换时，value属性不改变，也不能像上述那样拿到改变的状态值。所以在用LinkedStateMixin时，提供了特殊的checkedLink，来代替valueLink，代码如下如下：
   
```js
    var checkTest = React.createClass({
    mixins:[React.addons.LinkedStateMixin
    getInitialState() {
        return {
          checkValue:''
        }
    },
    render:function(){
        var checkValue = this.state.checkValue;
        return (
            <div>
                <input type="checkbox" checkedLink={this.linkState('checkValue')} />
                <span>{checkValue}</span>
            </div>
        )
     }
   });
 ```


