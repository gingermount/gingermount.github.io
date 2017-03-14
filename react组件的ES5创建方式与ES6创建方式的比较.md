# react组件的ES5创建方式与ES6创建方式的比较
  React自从诞生后，随着版本的更新迭代和es6的逐渐普及，在es5原生方式React.createClass的组件创建方式的基础上，又有了es6形式的extends React.Component的组件创建方式，那么这两种定义组件方式都有什么特点，相互之间有哪些异同，下面将具体说明。

  
## es5原生方式的React.createClass
   React.createClass是react最开始的写法，使用JavaScript中ES5的语法在react中创建组件。
 具体的代码示例如下：

```js
var test = React.createClass({
 propTypes: {
  initialValue: React.PropTypes.string
 },
 defaultProps: {
  initialValue: ''
 },
 
 getInitialState: function() {
  return {
   inputValue: this.props.initialValue || '0'
  };
 },
 inputHandleChange: function(e) {
  this.setState({
   value: e.target.value
  });
 },
 render: function() {
  return (
   <div>
    <input onChange={this.inputHandleChange} value={this.state.inputValue} />
   </div>
  );
 }
});

```


## es6形式的extends React.Component
   react从更新到v0.13版本之后支持了ES6的class方法，使得开发者可以用extends React.Component的方法去创建组件。目前来说，这种方法已经在逐渐代替es5原生方式的React.createClass方法，最终将会完全取而代之。这也是现在react官方最为推荐的一种方式。
   上面的代码用这种方式重写如下：
   
 ```js
class test extends React.Component {
 static propTypes = {
  initialValue: React.PropTypes.string
 };
 static defaultProps = {
  initialValue: ''
 };
 constructor(props) {
  super(props);
  this.state = {
   inputValue: props.initialValue || '0'
  };
 }
 inputHanleChange = (e) => {
  this.setState({
   inputValue: e.target.value
  });
 }
 render() {
  return (
   <div>
    <input onChange={this.inputHandleChange} value={this.state.inputValue} />
   </div>
  );
 }
}

```
## extends React.Component与React.createClass的比较
   两者主要有以下几点不同：
   组件初始状态state的配置不同。
   函数this的绑定不同。
   组件属性类型propTypes及其默认props属性defaultProps配置不同。
   
### 组件初始状态state的配置不同
   React.createClass写法，state状态的初始化是通过getInitialState方法来配置的；   
```js   
   getInitialState: function() {
    return {
     inputValue: this.props.initialValue || '0'
    };
   },
```   
   React.Component写法，state状态的初始化是在constructor中声明的。
```js   
   constructor(props) {
    super(props);
    this.state = {
     inputValue: props.initialValue || '0'
    };
   }
```  
### 函数this绑定不同
   React.createClass写法，其成员函数会直接绑定，可直接使用。  
```js   
   inputHandleChange: function(e) {
    this.setState({
     value: e.target.value
    });
   },
   render: function() {
    return (
     <div>
      <input onChange={this.inputHandleChange} value={this.state.inputValue} />
     </div>
    );
   }
```   
   React.Component写法，其成员函数不会自动绑定this，需要手动绑定,可以使用ES6特有的箭头函数来简化此写法。
```js   
   inputHandleChange = (e) => {
    this.setState({
     inputValue: e.target.value
    });
   }
   render() {
    return (
     <div>
      <input onChange={this.inputHandleChange} value={this.state.inputValue} />
     </div>
    );
   }
```  
### 组件属性类型propTypes及其默认props属性defaultProps配置不同
   React.createClass写法:   
```js   
   propTypes: {
    initialValue: React.PropTypes.string
   },
   getDefaultProps(){
    return{
     initialValue: ''
    }
   },
```   
   React.Component写法:
```js   
   static propTypes = {
    initialValue: React.PropTypes.string
   };
   static defaultProps = {
    initialValue: ''
   };
```
