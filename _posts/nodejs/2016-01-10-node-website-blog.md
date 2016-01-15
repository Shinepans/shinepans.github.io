---
layout: post
title: nodejs建站
categories: nodejs
---

# Nodejs建站3-成果展示
![最终成果](http://img.blog.csdn.net/20160109105310718)

![这里写图片描述](http://img.blog.csdn.net/20160109105426500)

## 核心代码
- 核心路由
```
app.get('/',Index.index);

  app.get('/shake',Index.shake)
  app.post('/user/blog', function (req, res) {
  var currentUser = req.session.user,
      post = new Post(currentUser.name, req.body.blogTitle, req.body.blogPost);
  post.save(function (err) {
    if (err) {
      
      return res.redirect('/');
    }
    console.log(typeof(post));
    req.flash('success', '发布成功!');
    res.redirect('/');//发表成功跳转到主页
  });
})

  // 
  app.post('/user/signup', User.signup)
  app.post('/user/signin', User.signin)
  app.get('/signin', User.showSignin)
  app.get('/signup', User.showSignup)
  app.get('/logout', User.logout)
```

- 核心控制器

```
exports.shake = function(req, res) {
    res.render('shake',{
        title: 'Shake!!!'
    })
}

// index page
exports.index = function(req, res) {
    Post.get(null, function(err, posts) {
      if (err) {
        posts = [];
      }
      console.log(posts);
      res.render('index', {
        title: '首页',
        posts: posts
      })
    })
}

// search page
exports.search = function(req, res) {
  var catId = req.query.cat
  var q = req.query.q
  var page = parseInt(req.query.p, 10) || 0
  var count = 2
  var index = page * count

  if (catId) {
    Category
      .find({_id: catId})
      .populate({
        path: 'blog',
        select: 'title'
      })
      .exec(function(err, categories) {
        if (err) {
          console.log(err)
        }
        var category = categories[0] || {}
        var movies = category.movies || []
        var results = movies.slice(index, index + count)

        res.render('results', {
          title: '结果',
          keyword: category.name,
          currentPage: (page + 1),
          query: 'cat=' + catId,
          totalPage: Math.ceil(movies.length / count),
          movies: results
        })
      })
  }
  else {
  ......
```
- 核心model

```
var mongodb = require('./db');

function Post(name, title, post) {
    this.name = name;
    this.title = title;
    this.post = post;
}

module.exports = Post;

//save blog and other infomation
Post.prototype.save = function(callback) {
    var date = new Date();
    //save time
    var time = {
        
            year : date.getFullYear(),
            month : date.getFullYear() + "-" + (date.getMonth() + 1),
            day : date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate(),
            minute : date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate() + " " + 
            date.getHours() + ":" + (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()) 
    }
    //save values
    var post = {
            name: this.name,
            time: time,
            title: this.title,
            post: this.post
    };
    //open mongodb
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        //read posts
        db.collection('posts', function (err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            } 
            //insert the blog to posts
            collection.insert(post, {
                safe: true
            }, function (err) {
                mongodb.close();
                if (err) {
                    return callback(err);//faulr ,back err info
                }
                callback(null);// back err (null)
            });
        });
    });
};

//read posts and other info
Post.get = function(name, callback) {
    //open mondodb
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        //read posts collection
        db.collection('posts', function(err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            }
            var query = {};
            if (name) {
                query.name = name;
            }
            // use query serch posts
            collection.find(query).sort({
                time: -1
            }).toArray(function (err, docs) {
                mongodb.close();
                if (err) {
                    return callback(err);//fault ,back err
                }
                callback(null, docs);// true, back collections, use jquery ,back to views, use jade engine!
            });
        });
    });
};
```

- 核心views
header.jade

```
.container
    .row
        .page-header.clearfix
            h1= title
            .col-md-4
                small 查找文章
            .col-md-8
                form(method='GET', action='/results')
                    .input-group.col-sm-4.pull-right
                        input.form-control(type='text', name='q')
                        span.input-group-btn
                            button.btn.btn-default(type='submit') 搜索文章


.navbar.navbar-default.navbar-fixed-top
    .container
        .navbar-header
            a.navbar-brand(href="/") 潘尚的博客
        if user
            p.navbar-text.navbar-right
                span 欢迎登录,#{user.name}
                span &nbsp;|&nbsp;
                a.navbar-link(href="/shake") 摇摇
                span &nbsp;|&nbsp;
                a.navbar-link(href="#", data-toggle="modal", data-target="#blogModal") 发表
                span &nbsp;|&nbsp;
                a.navbar-link(href="#", data-toggle="modal", data-target="#randomModal") 随机
                span &nbsp;|&nbsp;
                a.navbar-link(href="#", data-toggle="modal", data-target="#aboutModal") 关于
                span &nbsp;|&nbsp;
                a.navbar-link(href="/logout") 退出
                span &nbsp;|&nbsp;
        else
            p.navbar-text.navbar-right
                a.navbar-link(href="#", data-toggle="modal", data-target="#signupModal") 注册
                span &nbsp;|&nbsp;
                a.navbar-link(href="#", data-toggle="modal", data-target="#signinModal") 登录
                span &nbsp;|&nbsp;
                a.navbar-link(href="/shake") 摇一摇
                span &nbsp;|&nbsp;
                a.navbar-link(href="#", data-toggle="modal", data-target="#aboutModal") 关于
#signupModal.modal.fade
    .modal-dialog
        .modal-content
            form(method="POST", action="/user/signup")
                .modal-header 注册
                .modal-body
                    .form-group
                        label(for="signupName") 姓名
                        input#signupName.form-control(name="user[name]", type="text")
                    .form-group
                        label(for="signupPassword") 密码
                        input#signupPassword.form-control(name="user[password]", type="text")
                .modal-footer
                    button.btn.btn-default(type="button", data-dismiss="modal") 关闭
                    button.btn.btn-success(type="submit") 提交
#signinModal.modal.fade
    .modal-dialog
        .modal-content
            form(method="POST", action="/user/signin")
                .modal-header 登录
                .modal-body
                    .form-group
                        label(for="signinName") 姓名
                        input#signinName.form-control(name="user[name]", type="text")
                    .form-group
                        label(for="signinPassword") 密码
                        input#signinPassword.form-control(name="user[password]", type="text")
                .modal-footer
                    button.btn.btn-default(type="button", data-dismiss="modal") 关闭
                    button.btn.btn-success(type="submit") 提交
#aboutModal.modal.fade
    .modal-dialog
        .modal-content
            form(method="POST", action="/user/signup")
                .modal-header 关于
                .modal-body
                        h2 Welcome! 
                        br
                        h4 欢迎来到潘尚的博客!
                        h5 v 1.0.0
                .modal-footer
                    button.btn.btn-default(type="button", data-dismiss="modal") 关闭
#blogModal.modal.fade
    .modal-dialog
        .modal-content
            form(method="POST", action="/user/blog")
                .modal-header 编辑内容
                .modal-body
                    .form-group
                        label(for="blogTitle") 标题
                        input#blogTitle.form-control(rows=5, cols=200, name="blogTitle", type="text")
                    .form-group
                        label(for="blogContent") 内容
                        textarea#blogText.form-control(name="blogPost", type="text")
                .modal-footer
                    button.btn.btn-default(type="button", data-dismiss="modal") 关闭
                    button.btn.btn-success(type="submit") 提交内容
#randomModal.modal.fade
    .modal-dialog
        .modal-content
            form(method="POST", action="/user/blog")
                .modal-header 摇一摇换文章
                .modal-body
                    .form-group
                        label(for="blogTitle") 文章
                        input#blogTitle.form-control(rows=5, cols=200, name="blogTitle", type="text")
                    .form-group
                        label(for="blogContent") 内容
                        textarea#blogText.form-control(name="blogPost", type="text")
                .modal-footer
                    button.btn.btn-default(type="button", data-dismiss="modal") 关闭


```



# summary
- 我喜欢用Jade, 因为这对团队来说是及其好的事, 严格的缩进使得代码规范整齐美观, 省去的复杂的尖括号, 相比ejs的凌乱来说, 这是在是完美之物! 所以个人推荐使用jade, 还有两个比较好的css框架: materializecss和 bootstrap
- 目前个人技术栈范围: nodejs,express,mongodb,jade,js
- 较喜欢使用 materializecss, mongoose,

By the way:  complete code is available, please connect me 
shinepans@live.com
my complete code is on the website of github
