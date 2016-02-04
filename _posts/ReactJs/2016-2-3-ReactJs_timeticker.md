---
layout: post
title: ReactJs计时器,Todos
categories: ReactJs
---

## 1. 计时器

<html>
    <head>
        <script src="//cdn.bootcss.com/react/0.14.7/react.js"></script>
        <script src="//cdn.bootcss.com/react/0.14.7/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="ticker" style="font-size:16px;"></div>
        <script type="text/babel">
            var Timer = React.createClass({
              getInitialState: function() {
                return {secondsElapsed: 0};
              },
              tick: function() {
                this.setState({secondsElapsed: this.state.secondsElapsed + 1});
              },
              componentDidMount: function() {
                this.interval = setInterval(this.tick, 1000);
              },
              componentWillUnmount: function() {
                clearInterval(this.interval);
              },
              render: function() {
                return (
                  <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
                );
              }
            });

            ReactDOM.render(
                <Timer />, 
                document.getElementById('ticker')
            );

        </script>
    </body>
</html>

```
<!DOCTYPE html>
<html>  
    <head>
        <script src="../build/react.js"></script>
        <script src="../build/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="ticker"></div>
        <script type="text/babel">
            var Timer = React.createClass({
              getInitialState: function() {
                return {secondsElapsed: 0};
              },
              tick: function() {
                this.setState({secondsElapsed: this.state.secondsElapsed + 1});
              },
              componentDidMount: function() {
                this.interval = setInterval(this.tick, 1000);
              },
              //如果不清除计时器, 导致内存泄露,时间是这么长的:
              //1s,3s,6s,10s,15s,21s,.....
              componentWillUnmount: function() {
                clearInterval(this.interval);
              },
              render: function() {
                return (
                  <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
                );
              }
            });

            ReactDOM.render(
                <Timer />, 
                document.getElementById('ticker')
            );

        </script>
    </body>
</html>
```

## 2. Todos

<html>
    <head>
        <script src="//cdn.bootcss.com/react/0.14.7/react.js"></script>
        <script src="//cdn.bootcss.com/react/0.14.7/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="todos"></div>
        <script type="text/babel">
            var TodoList = React.createClass({
              render: function() {
                var createItem = function(item) {
                  return <li key={item.id}>{item.text}</li>;
                };
                return <ul>{this.props.items.map(createItem)}</ul>;
              }
            });
            var TodoApp = React.createClass({
              getInitialState: function() {
                return {items: [], text: ''};
              },
              onChange: function(e) {
                this.setState({text: e.target.value});
              },
              handleSubmit: function(e) {
                e.preventDefault();
                var nextItems = this.state.items.concat([{text: this.state.text, id: Date.now()}]);
                var nextText = '';
                this.setState({items: nextItems, text: nextText});
              },
              render: function() {
                return (
                  <div>
                    <h3>TODO</h3>
                    <TodoList items={this.state.items} />
                    <form onSubmit={this.handleSubmit}>
                      <input onChange={this.onChange} value={this.state.text} />
                      <button>{'Add #' + (this.state.items.length + 1)}</button>
                    </form>
                  </div>
                );
              }
            });
            ReactDOM.render(
                <TodoApp />, 
                document.getElementById('todos')
            );

        </script>
    </body>
</html>

```
<html>
    <head>
        <script src="../build/react.js"></script>
        <script src="../build/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="todos"></div>
        <script type="text/babel">
            var TodoList = React.createClass({
              render: function() {
                var createItem = function(item) {
                  return <li key={item.id}>{item.text}</li>;
                };
                return <ul>{this.props.items.map(createItem)}</ul>;
              }
            });
            var TodoApp = React.createClass({
              getInitialState: function() {
                return {items: [], text: ''};
              },
              onChange: function(e) {
                this.setState({text: e.target.value});
              },
              handleSubmit: function(e) {
                e.preventDefault();
                var nextItems = this.state.items.concat([{text: this.state.text, id: Date.now()}]);
                var nextText = '';
                this.setState({items: nextItems, text: nextText});
              },
              render: function() {
                return (
                  <div>
                    <h3>TODO</h3>
                    <TodoList items={this.state.items} />
                    <form onSubmit={this.handleSubmit}>
                      <input onChange={this.onChange} value={this.state.text} />
                      <button>{'Add #' + (this.state.items.length + 1)}</button>
                    </form>
                  </div>
                );
              }
            });
            ReactDOM.render(
                <TodoApp />, 
                document.getElementById('todos')
            );

        </script>
    </body>
</html>
```

## 3. 输入文字监听

<html>
    <head>
        <script src="//cdn.bootcss.com/react/0.14.7/react.js"></script>
        <script src="//cdn.bootcss.com/react/0.14.7/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="reactInput"></div>
        <script type="text/babel">
            var MarkdownEditor = React.createClass({
              getInitialState: function() {
                return {value: 'Type some *markdown* here!'};
              },
              handleChange: function() {
                this.setState({value: this.refs.textarea.value});
              },
              rawMarkup: function() {
                return { __html: marked(this.state.value, {sanitize: true}) };
              },
              render: function() {
                return (
                  <div className="MarkdownEditor">
                    <h3>Input</h3>
                    <textarea
                      onChange={this.handleChange}
                      ref="textarea"
                      defaultValue={this.state.value} />
                    <h3>Output</h3>
                    <div
                      className="content"
                      dangerouslySetInnerHTML={this.rawMarkup()}
                    />
                  </div>
                );
              }
            });

            ReactDOM.render(
                <MarkdownEditor />, 
                document.getElementById('reactInput')
            );


        </script>
    </body>
</html>

```
var MarkdownEditor = React.createClass({
  getInitialState: function() {
    return {value: 'Type some *markdown* here!'};
  },
  handleChange: function() {
    this.setState({value: this.refs.textarea.value});
  },
  rawMarkup: function() {
    return { __html: marked(this.state.value, {sanitize: true}) };
  },
  render: function() {
    return (
      <div className="MarkdownEditor">
        <h3>Input</h3>
        <textarea
          onChange={this.handleChange}
          ref="textarea"
          defaultValue={this.state.value} />
        <h3>Output</h3>
        <div
          className="content"
          dangerouslySetInnerHTML={this.rawMarkup()}
        />
      </div>
    );
  }
});

ReactDOM.render(<MarkdownEditor />, mountNode);

```

by: 潘尚 <br />
time: 2016.2.4

