# React组件数据存储方式

  react组件中的数据可以有多种保存方式，我们将依次进行讨论他们的差异，并给出他们的适用场景。

### 一、本地 state

 state是react开发中最熟知也是最常用的数据存储方式，其最大的特点就是使用setState方法使state的值发生变化时，会触发整个组件的重新渲染（re-render）。同时，这个 state中的值也能传递给子组件，子组件中可通过 props 来获取传递的值。
 下面的例子给出了一个利用state值写的计数器。
 
 ```js
  import React from 'react'

  class Test extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        value: 0
      }
    }

    add = () => {
      this.setState({
        data: this.state.value + 1
      })
    }

    render() {
      return (
        <div>
          <button
            onClick={this.add }>
            点击计数加1
          </button>
          { this.state.value }
        </div>
      )
    }
  }

export default Test
 ```
 这个例子中的state中的值会存在Test组件中，可以传递给子组件（子组件中的props）。
 
#### 适合使用的场景

  绝大部分情况作为暂时性保存数据的首选，例如影响UI状态和暂时的数据（表单的输入等）。事实上ReactJs的精髓之处也正是在于state中值的改变而触发重新渲染。如果储存的值仅在本组件中中临时使用，或者仅需要传给子组件，那么都可以用state来存储。如果要存储为全局性的变量，那么将采用其他的方式。

### 二、sessionStorage/redux store

 如果某个数值希望存储下来用作全局变量，在任何其他地方都能获取，那么可以将其存入sessionStorage中，在其他需要使用的地方再从sessionStorage中取出。
 例如我们在某登录页中设置登陆时会获取一个列表corpList，这个列表需要在后续所有模块中都能获取到，那么我们就可以将它存入到sessionStorage中。
 ```js
  import React from 'react';
  const storage = $.sessionStorage;
  export default class Login extends React.Component {
    constructor(props) {
      super(props)
    }
    login = () => {
         $.ajax({
          url: '/eweb/eAnnuityLogin.do',
          type: 'POST',
          data: {},
          success: (res) => {
            storage.set("list", res.corpList);
          },
        });
       };  
   }
   render() {
      return (
        <div>
          <button
            onClick={this.Login}>
            登陆
        </div>
      )
    }
  }
 ```
 经过此页面登陆后，在其他页面中，如果我们需要调取这个列表数据，只需从sessionStorage中取出即可。
  ```js
  import React from 'react';
  const storage = $.sessionStorage;
  export default class Tab1 extends React.Component {
    constructor(props) {
      super(props);
       this.state = {
          list:storage.get("list")
       }
    }
    showLog = () => {
      console.log(this.state.list)
    ｝
    render() {
      return (
        <div>
          <button
            onClick={this.showLog}>
            获取列表信息
        </div>
      )
    }
  }
 ```
 同理，如果此组件中对list进行改变，重新存入sessionStorage中，又可以在别处取到新的list。
 
#### 适合使用的场景

  全局型的变量，适合保存非临时、需要其他大量组件获取的数据状态。最好的例子是登陆状态，因为在登录状态改变的同时，多数组件需要访问这个数据信息并做出响应，这些组件将需要重新渲染与更新的信息。总之是适合于跨组件、跨路由进行数值的传递。同样的场景也适用于用redux进行全局控制。

### 三、this
 与存在this.state中的数据类似，使用this也可以存储本地的临时数据，不同的是没有setState方法可以直接触发重新渲染。
 还是第一个场景中的计数器的例子，如果我们要实现它，那么就需要这么改写。
 
 ```js
  import React from 'react'

  class Test extends React.Component {
    constructor(props) {
      super(props)
      this.value = 0
    }

    add = () => {
      this.value =+1;
      this.forceUpdate()    
    }

    render() {
      return (
        <div>
          <button
            onClick={this.add }>
            点击计数加1
          </button>
          { this.value }
        </div>
      )
    }
  }

export default Test
```

  当然这个例子其实用this显然是不合适的，只是为了比较下和state的异同，可以看到，数据存储方式非常相似，但是由于this中值的改变不能触发重新渲染，需要用
forceUpdate方法。但是当组件需要用forceUpdate方法来手动触发的时候，这就不是一个正常的react组件。若真要使用this，应该在改变后不需要重新渲染的场合。

#### 四、Static 静态方式

  static用来声明一个类的s值，它和this的不同在于它不像 this 那样去实例化一个类再去访问值。如下述代码所示：
 ```js  
  class Test extends React.Component {
  constructor() {
    super()
    this.prototypeProperty = {
      baz: "qux"
    }
  }
  static staticProperty = {
    foo: "bar"
  };
  
  render() {
    return (<div>My App</div>)
  }
}

const proto = new App();
const proto2 = proto.prototypeProperty // => { baz: "qux" }
const stat = App.staticProperty // => { foo: "bar" }
 ```
#### 适合应用的场景
 static其他场合应用较少，最主要的是用来定义PropTypes和defaultProps。
 ```js 
  static defaultProps = {
     hasExport: true
    }
  static propTypes = {
    titleName:React.PropTypes.string.isRequired,
    id:React.PropTypes.string.isRequired,
    hasBack:React.PropTypes.bool,
  }  
  render() {
    return (<div> name={this.props.titleName} id={this.props.id} hasBack={this.props.hasBack}/></div>)
  }  
 ``` 
  这在自己封装工具类的组件时常常用到，比如一个button组件，每一个按钮的渲染都需要相似的值。

#### 五、其他

  这是一种不提倡却可以实现的方式，可以把数值存储在一个全局变量中，然后进行导出。
 ```js
  import React from 'react'
  let value =0;
  class Test extends React.Component {
    constructor(props) {
      super(props)
    }

    add = () => {
      value =+1;
      this.forceUpdate()    
    }

    render() {
      return (
        <div>
          <button
            onClick={this.add }>
            点击计数加1
          </button>
          { value }
        </div>
      )
    }
  }

export default Test
```
 这个例子与this比较相似，只是使用了一个全局变量value，并可以导出这个value。但是并不提倡这么做，容易造成数据混乱。如果要用全局性，跨组件跨路由的变量，应该使用sessionStorage或者Redux。



