title: React-Todo示例
date: 2016-05-25 20:47:25
tags: React
---

近两天事情较少，准备做一个书单列表，初始阶段和之前看过的Todo基本相似。正好很久没看React，感觉好生疏，准备结合ES6重新写一个React的Todo示例。参考的源码和样式来源于[todomvc](http://todomvc.com/examples/react/#/)

<!-- more -->

### 样式
直接复用todomvc的，这个样式很简洁，同时该网站又把相关的css都放在同一个css文件中，直接下载下来就可以了。

### 源码
参考其[示例源码](https://github.com/tastejs/todomvc/tree/gh-pages/examples/react),
但是由于其源码用的不是ES6，同时用的是IEEF来做的模块化，所以需要改造一下。

改造点：

1. 在ES6中，可以直接用`class`扩展`React.Component`(注意大写首字母，跳过这个坑)

```
import React from 'react';

export default class TodoFooter extends React.Component {
    render() {
        return (
            <footer className="footer">
                <span className="todo-count">{this.props.count}</span>
                <ul className="filters">
                    <li><a href="">All</a></li>
                    <li><a href="">Active</a></li>
                    <li><a href="">Complete</a></li>
                </ul>
            </footer>
        )
    }
}
```

这个是改造TodoFooter的代码。
在其他模块引用时，只需要
```
import TodoFooter from './todoFooter.jsx';
```

2. `classNames` 模块已经单独抽出来维护了，[classnames的github地址](https://github.com/JedWatson/classnames)

3. ES6中，React不会默认绑定`this`，所以需要手动绑定一下
```
import React from 'react';
import classNames from 'classnames';

export default class TodoItem extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            editText: this.props.todo.title
        };
        this.id = this.props.itemId;
        this.handleSubmit = this.handleSubmit.bind(this);
        this.handleChange = this.handleChange.bind(this);
        this.handleEdit = this.handleEdit.bind(this);
        this.handleKeyDown  = this.handleKeyDown.bind(this);
        this.onToggle = this.onToggle.bind(this);
        this.onDestroy = this.onDestroy.bind(this);
    }

    handleSubmit() {
        var val = this.state.editText.trim();
        if (!val) {
            this.props.onDestroy(this.id);
        }
        else {
            this.props.onSave(this.id, val);
            this.setState({editText: val});
        }

    }
    handleEdit() {
        this.props.onEdit(this.id);
        this.setState({editText: this.props.todo.title});
    }
    handleKeyDown(e) {
        if (e.which === 27) {
            this.setState({editText: this.props.todo.title});
            this.props.onCancel(event);
        }
        else if (e.which === 13) {
            this.handleSubmit(e);
        }
    }
    handleChange(e) {
        if (this.props.editing) {
            this.setState({editText: e.target.value});
        }
    }
    onToggle() {
        this.props.onToggle(this.id);
    }
    onDestroy() {
        this.props.onDestroy(this.id);
    }

    render() {
        return (
            <li className={classNames({
                completed: this.props.todo.completed,
                editing: this.props.editing
            })}
            >
                <div className="view">
                    <input
                        className="toggle"
                        type="checkbox"
                        checked={this.props.todo.completed}
                        onChange={this.onToggle}
                    />
                    <label onDoubleClick={this.handleEdit}>
                        {this.props.todo.title}
                    </label>
                    <button className="destroy" onClick={this.onDestroy}></button>
                </div>
                <input
                    ref="editField"
                    className="edit"
                    value={this.state.editText}
                    onBlur={this.handleSubmit}
                    onChange={this.handleChange}
                    onKeyDown={this.handleKeyDown}
                />
            </li>
        )
    }
}
```

4. 事件传递参数，用`bind`

```
 <a onClick={this.props.onFilter.bind(this, ALL_TODOS)} className={classNames({selected: active === ALL_TODOS})}>All</a>
```
