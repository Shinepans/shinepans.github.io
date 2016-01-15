---
layout: post
title: nodejs笔记2
categories: nodejs
---
### 文件结构
- app.js : 入口文件
- package.json : 工程信息以及模块依赖
    - 通过npm安装模块时输入命令 : npm instal xxx --save , 会自动把模块信息保存在package.jason中
- node_modules : 存放模块
- public : 存放image , css , js 等文件
- routes : 存放路由文件
- views : 存放视图文件
    - 通俗理解即前端
        - 如jade,ejs,html等(个人使用jade)
- 存放可执行文件

### node.js express中的MVC模式
- Model
    - node提供的模块,中间件,在用express创建项目时,产生node_modules即表示M层,即Model
    - 模块如jade,mongoose,morgan,body-parser等等
- View
    - express生成项目时会产生views,即前端
- Controller
    - 即视图向控制器发出请求,由控制器选择相应的模型来处理
    - 模型返回的结果给控制器,由控制器来选择合适的视图,生成界面给用户
    - 如通过res.render来渲染jade文件
    
### 路由控制
- 代码

```
router.get('/', function(req, res){
  res.render('index', { title: 'HomePage' });
});
```
- 意义 : 访问主页时调用jade模板引擎渲染index.jade文件
    - 其中title : 'HomePage'的作用:将index.jade中所有title赋值为HomePage
- 建议 : 一般把路由控制写在单独的文件里,方便管理
    - 实现方法 : 在app.js中写入routes(app);
    - 在路由控制文件中写入
    
    ```
    module.exports = function(app){
        app.get('/',function(req,res)
        {
            res.render('index',{title:'HomePage'});
        }
            );
    };
    ```
    - 含义 : 网站主页(默认域名)访问时,会通过路由,然后指引jade模板引擎去渲染index.jade文件

    