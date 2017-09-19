---
layout: post
title: 在网页、博客、React中配置mathjax
categories: notes
---

mathjax是一个非常好用的工具

> 在网页端、博客上配置mathjax

- 加载script

```html
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

- 设置mathjax

```html
<script> 
MathJax.Hub.Config({
	tex2jax: {
		skipTags: [
			'script', 
			'style', 
			'pre'
		]
	}
}); 
</script>

```

> react 加载mathjax， 下面是按需加载的mathjax

- 加载marked

```
import marked from 'marked';
// ....
<div dangerouslySetInnerHTML={{__html: marked(this.props.content)}}></div>
```

- 在文档传入后，判断是否需要加载mathjax

```javascript
componentWillReceiveProps = () => {
        // 如果原文包含 mathjax 表达式， 加载 mathjax组件
        if (this.props.content && this.props.content.indexOf('$$') !== -1) {
            let js_mathjax   = 'https://cdn.bootcss.com/mathjax/2.7.0/MathJax.js?config=TeX-AMS_CHTML';
            this.loadJS(js_mathjax).then(function(msg){});
        }
    };

    componentWillMount = () => {
        if (this.props.content && this.props.content.indexOf('$$') !== -1) {
            let js_mathjax   = 'https://cdn.bootcss.com/mathjax/2.7.0/MathJax.js?config=TeX-AMS_CHTML';
            this.loadJS(js_mathjax).then(function(msg){});
        }
    };
```

- 动态加载JS方案

```javascript
loadJS = (url) => {
        return new Promise(function(resolve, reject) {
            let script = document.createElement('script');
            script.type = "text/javascript";
            if (script.readyState){
                script.onreadystatechange = function() {
                    if (script.readyState === "loaded" ||
                        script.readyState === "complete") {
                        script.onreadystatechange = null;
                        resolve('success: '+url);
                    }
                };
            } else {
                script.onload = function(){
                    resolve('success: '+url);
                };
            }
            script.onerror = function() {
                reject(Error(url + 'load error!'));
            };
            script.src = url;
            document.body.appendChild(script);
        });
    };
```

- 最后在markdown显示的模块下方加入

```javascript
{this.showMathJax}

// showMathjax:
showMathjax = () => {
        if (this.props.content && this.props.content.indexOf('$$') !== -1) {
            if(window.MathJax){
                window.MathJax.Hub.Queue(["Typeset",MathJax.Hub,"output"]);
            }else{
                setTimeout(this.runningMath.bind(this), 1000);
            }
        } else {
            return null;
        }
    };
```

mathjax 在网页端公式显示中是一个很好的解决方案。

```
$$mathjax: E=mc^2$$
```

效果：

$$mathjax: E=mc^2$$