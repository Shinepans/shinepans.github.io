---
layout: post
title: Async.serial, Async.parallel
categories: 同步异步编程
---


## Async 串行与并行配合使用

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
// 同理 task1
function task3(cb) {
    // .. codes
    if(true) {
        cb(null, 'task 3 done!');
    }
    if(err) {
        cb('task 3 failed', 'err in 3: some words...');
    }
}
// 同理 task1
function task4(cb) {
    // .. codes
    if(true) {
        cb(null, 'task 4 done!');
    }
    if(err) {
        cb('task 4 failed', 'err in 4: some words...');
    }
}
/* 
 * 控制流程
 * 
 * @param err: 监听是否返回错误
 * @param results: 收集运行返回结果
 * @cb: 回调函数
 */
function RunSameTime(cb) {
    async.parallel([t2,t3,t4,t5,t6], function(cberr,results) {
        if(cberr) {
            console.log('Error: ' + cberr);
            cb('t2~t3 err', 't2~t3');
        }
        console.log('Parallel Tasks: ' + results.length + 'items \n');
        console.log(results);
        cb(null, results);
    });
}
/* 
 * 控制流程
 * 
 * @param err: 监听是否返回错误
 * @param results: 收集运行返回结果
 */
async.series([t1,RunSameTime,t4], function(err, results) {
    if(cberr) {
        console.log('Error: ' + cberr);
    }
    console.log('Run Tasks: ' + results.length + 'items \n');
    console.log(results);
});
```

> 如果上述全部运行成功, 那么会输出: Run Tasks: 3 items

> 串行: ['task 1 done', 并行: ['task 2 done', 'task 3 done'], 'task 4 done']


**总结**

> 配合串行与并行, 可以大大节省运行时间与增加效率, 充分考虑使用的情景, 可以提高代码效率.