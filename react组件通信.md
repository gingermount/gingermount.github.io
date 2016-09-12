# React组件之间的通信
  React在数据流向性上的一大特点就是他的单向性，这个特点使得组件之间的通信会有一些麻烦。这里主要分三种情况，从最基本的父子之间进行通信（即父组件传递数值到子组件，和子组件传递到父组件），到拥有同一父节点的兄弟组件之间的通信，以及没有任何层级关系的两个组件互相之间的通信。

###父子组件之间的通信

 父组件更新子组件状态的过程很简单，在每一个开发过程中都经常使用，即通过props来进行数值的传递。父组件更新状态，那么通过props会将数值传递给子组件，重新渲染数据。
 如果不止有父子两层，而是有更多层嵌套，那么也将通过props向下传递。这种通过顶点的props改变，重新渲染子节点的方式，是严格意义上react唯一提供的交流方式。
 由于props的传递是单向的，子组件不能通过同样的方式去改变父组件。而事实上，react也没有提供专门的事件供子组件去通知父组件。那么怎样才能让子组件去通知父组件呢，同样要利用父组件到子组件的props传递，这次是父组件传递一个回调函数给子组件，子组件调用这个触发即可。
 
 具体的代码示例如下：

```js
class Father extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  changeSon(){
    return (e)=>{
      this.setState({
        sonValue: "子组件：1",
      })
    }
  }
  changeFather(){
    this.setState({
      fatherValue: "父组件：1",
    })
  }
  render(){
    return (
      <div>
        <button onClick={this.changeSon()} >
            改变子组件状态值至1
        </button>
        <Son 
          value={this.state.sonValue || "子组件：0"} 
          changeFather={this. changeFather.bind(this)}
        />
        {this.state. fatherValue || "父组件：0"}
      </div>
    )
  }
}
class Son extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
    render(){
    return (
      <div>
        {this.props.value}
        <button onClick={this.props.changeFather}>
            改变父组件状态值至1
        </button>
      </div>
    )
  }
}


```

### 兄弟组件之间的通信
 当同一个父组件有两个或多个子组件时，这些子组件之间可以进行间接通信，即通过上面的方法，以父组件为媒介，通过父组件的回调函数来互相改变状态。
 
 ```js
class Father extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  changeBrother(){
    return (e)=>{
      this.setState({
        value: "已通信",
      })
    }
  } 
  render(){
    return (
      <div>
        <Older changeBrother ={this. changeBrother ()}/>
        <Younger value={this.state.value}/>
      </div>
    )
  }
} 
class  Older extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
render(){
    return (
      <div>
        <button onClick={this.props.changeBrother}>
            改变状态
        </button>
      </div>
    )
  }
}
class Younger extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
render(){
    return (
      <div>
         {this.props.value || "未通信"
      </div>
    )
  }
}


```
### 无层级关系的组件通信
 在嵌套关系复杂时，用上述方法实现两个距离较远的组件间的通信，需要繁琐的组件亲戚调用。这时候可以使用全局事件。全局事件的原理是，改变数据的组件发起一个事件，使用数据的组件监听这个事件，在事件处理函数中触发setState来改变视图或者做其他的操作。这种事件模型有第三方的类库，EventEmitter或PostalJS。然而这样做也打破了单项数据流的机制，可能会导致数据流不可控。所以并不建议使用。
 为了在更加清晰和方便地在react中管理数据， Flux架构被提出，后面又有redux对其进行改进和优化。Redux有着统一的对组件state的管理，从而达到数据可控的目的。

### 结语
 （1）父组件改变子组件的状态，可直接通过props传递。子组件改变父组件的状态，可通过父组件传递一个回调函数给子组件，子组件去调用这个方法。
 （2）有同一个父组件的兄弟组件之间进行通信，可通过父组件为媒介，用上述父子间通信的方式进行过渡。
 （3）上述两种简单的通信，我们可以采用简单地传递props或者回调函数的方式，但是当项目中组件较多，层级关系复杂，有些时候这些简单地方式就不方便了。这时候更加能建议使用能够Flux、Redux、trandux等类库。

