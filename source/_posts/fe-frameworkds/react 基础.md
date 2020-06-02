---
title: React 学习笔记
date: 
categories:
- 前端框架
tags:
- react
---

# React 学习笔记

代码的注释应该是解释为什么，而不是解释是什么

# 撩课 react

props 传入构造函数

state 构造函数自带



```javascript
// 1. 创建 组件类

class Dog extends React.Component {
	constructor(props) {
		super(props)
		
		// 2. 初始化 state
		this.state = {
			age: 1,
			friends: []
		}
	}
	
	// 3. 设置 props 属性的默认值
  static defaultProps = {
  	name: '阿财',
  	gender: 'male',
  }
  
  // 4. 设置 props 属性的类型
  static propTypes = {
  	name: PropTypes.string.isRequired,
  	gender: PropTypes.stringjav
  }
  
  render() {
  	const {name, gender} = this.props
  	const {age, friends} = this.state
  	return (
  		<div>
  			<p>狗的名字：{name}</p>
  			<p>狗的性别：{gender}</p>
				<p>狗的年龄：{age}</p>
  			<p>狗的性别：{
  				friends.map((friend, index) => (
  					<span key={index}>{item}</span>
  				))
  			}</p>
				<button onClick={() => addYear()}>长一岁</button>
  		</div>
  	)
  }
  
  addYear() {
    let tempArr = this.state.friends
    tempArr.push('狗友' + Math.ceil(Math.random()*100))
  	this.setState({
  		ages: this.state.age + 1,
  		friends: tempArr
  	})
  }
}

ReactDOM.render(<CustomTextInput/>, document.getElementById('app'))
```



## ref 使用场景

* 处理焦点、文本选择或者媒体控制
* 触发强制动画
* 集成第三方 DOM 库



```
class CustomTextInput extends React.Component{
	constructor(props) {
		super(props)
		this.myInput = React.createRef()
	}
	
	render() {
		return (
			<div>
				<input ref={this.myInput} type="text" placeholder="请输入"/>
				<button onClick={() => this.focusTextInput()}>获取焦点</button>
			</div>
		)
	}
	
	focusTextInput() {
		this.myInput.current.focus()
	}
}

ReactDOM.render(<CustomTextInput/>, document.getElementById('app'))
```



## 组件的例子

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>React 练习</title>
    <style>
      #app {
        margin: 50px auto;
        width: 600px;
      }

      fieldset {
        border: 1px solid purple;
        margin-bottom: 20px;
      }

      fieldset input {
        width: 200px;
        height: 30px;
        margin: 10px 0;
      }

      table {
        width: 600px;
        border: 2px solid purple;
        text-align: center;
      }

      thead {
        background-color: purple;
        color: #fff;
      }
    </style>
  </head>

  <body>
    <div id="app"></div>
    <script src="../js/react.development.js"></script>
    <script src="../js/react-dom.development.js"></script>
    <script src="../js/prop-types.js"></script>
    <script src="../js/babel.min.js"></script>
    <script type="text/babel">
      // 1. 父组件 -------------------------------
      class App extends React.Component {
        constructor(props) {
          super(props);
          // 初始化状态
          this.state = {
            studentArr: [
              { name: "周杰伦", age: 30, gender: "男", phone: "18899989839" },
              { name: "谢霆锋", age: 40, gender: "男", phone: "18299839890" },
              { name: "刘德华", age: 50, gender: "男", phone: "18998938783" }
            ]
          };

          // 绑定this
          this.delFromArrWithIndex = this.delFromArrWithIndex.bind(this);
          this.addToArr = this.addToArr.bind(this);
        }

        /**
         * 根据索引删除一条学生记录
         * @param index
         */
        delFromArrWithIndex(index) {
          // 1. 删除
          const { studentArr } = this.state;
          studentArr.splice(index, 1);
          // 2. 更新状态
          this.setState({
            studentArr
          });
        }

        /**
         * 插入一条学生记录
         * @param {object}student
         */
        addToArr(student) {
          // 1. 插入
          const { studentArr } = this.state;
          studentArr.unshift(student);
          // 2. 更新数据
          this.setState({
            studentArr
          });
        }

        render() {
          return (
            <div>
              <Add addToArr={this.addToArr} />
              <List
                studentArr={this.state.studentArr}
                delFromArr={this.delFromArrWithIndex}
              />
            </div>
          );
        }
      }

      // 2. 子组件 - 添加版块 -------------------------------
      class Add extends React.Component {
        constructor(props) {
          super(props);
          // 绑定ref
          this.stuName = React.createRef();
          this.stuAge = React.createRef();
          this.stuGender = React.createRef();
          this.stuPhone = React.createRef();
        }
        //  设置props中属性的类型
        static propTypes = {
          addToArr: PropTypes.func.isRequired
        };
        render() {
          return (
            <div>
              <fieldset>
                <legend>撩课信息录入系统(React版)</legend>
                <div>
                  <span>姓名: </span>
                  <input
                    ref={this.stuName}
                    type="text"
                    placeholder="请输入姓名"
                  />
                </div>
                <div>
                  <span>年龄: </span>
                  <input
                    ref={this.stuAge}
                    type="text"
                    placeholder="请输入年龄"
                  />
                </div>
                <div>
                  <span>性别: </span>
                  <select ref={this.stuGender}>
                    <option value="男">男</option>
                    <option value="女">女</option>
                  </select>
                </div>
                <div>
                  <span>手机: </span>
                  <input
                    ref={this.stuPhone}
                    type="text"
                    placeholder="请输入手机号码"
                  />
                </div>
                <button onClick={() => this.dealWithClick()}>创建新用户</button>
              </fieldset>
            </div>
          );
        }

        // 点击添加按钮
        dealWithClick() {
          // 1. 读取用户输入的数据
          const name = this.stuName.current.value;
          const age = this.stuAge.current.value;
          const gender = this.stuGender.current.value;
          const phone = this.stuPhone.current.value;

          // 2. 数据的校验
          if (!name || !age || !gender || !phone) {
            alert("输入的数据不能为空！");
            return;
          }

          // 3. 添加数据
          this.props.addToArr({ name, age, gender, phone });

          // 4. 清除输入框中的数据
          this.stuName.current.value = "";
          this.stuAge.current.value = "";
          this.stuGender.current.value = "男";
          this.stuPhone.current.value = "";
        }
      }

      // 3. 子组件 - 展示版块 -------------------------------
      class List extends React.Component {
        //  设置props中属性的类型
        static propTypes = {
          studentArr: PropTypes.array.isRequired,
          delFromArr: PropTypes.func.isRequired
        };

        render() {
          // console.log(this.props.studentArr);
          const { studentArr, delFromArr } = this.props;
          return (
            <div>
              <table>
                <thead>
                  <tr>
                    <td>姓名</td>
                    <td>性别</td>
                    <td>年龄</td>
                    <td>手机</td>
                    <td>删除</td>
                  </tr>
                </thead>
                <tbody>
                  {studentArr.map((stu, index) => (
                    <tr key={index}>
                      <td>{stu.name}</td>
                      <td>{stu.gender}</td>
                      <td>{stu.age}</td>
                      <td>{stu.phone}</td>
                      <td>
                        <button onClick={() => delFromArr(index)}>删除</button>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          );
        }
      }

      // 4. 渲染组件
      ReactDOM.render(<App />, document.getElementById("app"));
    </script>
  </body>
</html>

```



## 生命周期

### 阶段划分

1. 初始化阶段
   1. **constructor**
   2. static getDerivedStateFromProps()
   3. **componentWillMount()** / UNSAFE_componentWillMount()
   4. **render()**
   5. **componentDidMount()**
2. 更新阶段（props 或者 state 的改变可能会引起组件的更新，组件重新渲染的过程中会调用）
   1. componentWillReceiveProps()
   2. UNSAFE_componentWillReceiveProps()
   3. static getDerivedStateFromProps()
   4. shouldComponentUpdate()
   5. **componentWillUpdate()** / UNSAFE_componentWillUpdate()：内存里边，数据已经是最新的了，界面还没有更新
   6. **render()**
   7. getSnapshotBeforeUpdate()
   8. **componentDidUpdate()**
3. 卸载阶段
   1. componentWillUnmount()
4. 错误处理
   1. componentDidCatch()