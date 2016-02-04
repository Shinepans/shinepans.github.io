---
layout: post
title: ReactJs计时器
categories: ReactJs
---

## 1. 计时器

<html>
    <head>
        <script src="../build/react.js"></script>
        <script src="../build/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="example" style="font-size:16px;"></div>
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
                document.getElementById('example')
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
        <div id="example"></div>
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
                document.getElementById('example')
            );

        </script>
    </body>
</html>
```

by: 潘尚 <br />
time: 2016.2.4

