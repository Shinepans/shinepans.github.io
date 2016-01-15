### First thing   (1-4-2016) 
- blog nodejs
    - login
    - update personal Info
    - two pages
    - use not ejs but jade
    - database:mongo
    
### Second thing 
- team work 
    - admin page
    - know how to use sass
    - make it aquaintance to use Git
    
### Notes
- 生成一个express实例
```
var app=express(); 
```
- 设置views为存放静态文件的路径

```
app.set('views',path.join(__dirname,'views')); 
```
- 设置图标

```
app.use(favicon(__dirname+'/public/favicon.ico'));
```
- 加载日志中间件

```
app.use(logger('dev'));
```

- 加载解析json的中间件

```
app.use(bodyParser.json());
```
- 加载解析urlencoded请求的中间件

```
app.use(bodyParser.urlencode({extended:false}));
```
- 加载解析cookie的中间件

```
app.use(cookieParser());
```
- 设置public文件夹为存放静态文件的目录
```
app.use(express.static(path.join(__dirname,'public')))
```
- 路由控制器
```
app.use('/',routes);和app.use('/user',users);
```
- 捕获404错误
```
app.use(function(req,res,next){
    var err=new Error('Not found');
    err.status=404;
    next(err);});
```
- 启动工程监听 3000端口,成功后打印Express server

```
var server=app.listen(app.get('port'),function(){
    debug('Express server listening on port'+server.address().port)
});
```
- req.query:处理get请求,获取get请求参数
- req.params:处理/:xxx形式的get或post请求,获取请求参数
- req.body:处理post请求,获取post请求
- req.param():处理get和post请求,查找优先级从高到低
- req.params->req.body->req.query


