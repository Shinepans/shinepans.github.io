---
layout: post
title: 一个bootstrap的modal
categories: nodejs
---

## Hide before click some title

- use jade

### code1

```
a.navbar-link(href="#", data-toggle="modal", data-target="#signupModal") 注册
        span &nbsp;|&nbsp;
        a.navbar-link(href="#", data-toggle="modal", data-target="#signinModal") 登录
        span &nbsp;|&nbsp;
        a.navbar-link(href="#", data-toggle="modal", data-target="#aboutModal") 关于
```

### code2

```
#signupModal.modal.fade
  .modal-dialog
    .modal-content
      form(method="POST", action="/user/signup")
        .modal-header 注册
        .modal-body
          .form-group
            label(for="signupName") 用户名
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
            label(for="signinName") 用户名
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
            h1 Welcome to use the Shine Blog
        .modal-footer
          button.btn.btn-default(type="button", data-dismiss="modal") 关闭


```