---
layout: post
title: Async.serial
categories: 同步异步编程
---


## Async.serial 的用法与总结以及适应场景总结

### 代码

```
// cb 是回调, 返回给运行的serial中的params
function task1(cb) {
    // .. codes
    if(true) {
        cb(null, 'task 1 done!');
    }
    if(err) {
        cb('task 1 failed', 'err in 1: some words...');
    }
}
// 同理 task1
function task2(cb) {
    // .. codes
    if(true) {
        cb(null, 'task 2 done!');
    }
    if(err) {
        cb('task 2 failed', 'err in 2: some words...');
    }
}

/* 
 * 控制流程
 * 
 * @param err: 监听是否返回错误
 * @param results: 收集运行返回结果
 */
async.series([t1,t2,t3,t4,t5,t6,t7], function(err, results) {
    if(cberr) {
        console.log('Error: ' + cberr);
    }
    console.log('Run Tasks: ' + results.length + 'items \n');
    console.log(results);
});
```

> 如果上述全部运行成功, 那么会输出: Run Tasks: 2 items

> ['task 1 done', 'task 2 done']

- 这里, 是在nodejs中串行执行每一个异步函数
- cb有两个参数, 第一个是错误参数err, 如果有错误, 可以利用cb抛出错误, 如果没有, 则给null表示没有错误

- 第二个是返回运行成功的结果, 将会保存在results数组中.

**总结**

> 如果在需要连接数据库, 保持连接, 然后运行一些列任务的时候, 最后断开数据库连接等等场景, 需要串行执行任务, 可以很好的应用async.serial方法进行执行.