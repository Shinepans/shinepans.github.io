---
layout: post
title:  Redis这样存入可能更快
categories: [php]
---

最近，踩过很多的坑。自己踩过，和团队一起也踩过。这里记录一个小坑，如果大家遇到了应该也会有共鸣的。

## 遇到的问题 ##

前些日子有一些日志需要处理。日志文件的大写都是10G+，数亿条的。刚开始我解决的思路是这样的：首先用awk+sed将日志清洗一遍，然后用PHP或者Python将日志逐行读取，接着逐行按照特定的格式(key-value或者hash)的格式存入到redis（主从或者集群）之中。

可是，后面一试效率吓死人。我用的是Predis连接集群的方式，存入的格式是hash格式。因为和服务器性能和日志的长度有关系，这里我也没法给出一个时间来。但是，如果按照这个进度来，我们肯定是接受不了的，因为我们的数据是需要在短期内就被用作另一个系统的。

## 解决的办法 ##

我们首先想到文件的读取方式是不是有问题。```排除了```

然后我想是不是语言的性能的问题。用Python的方式实现了一遍，提升了一点点，但是效果不是很明显。```排除了```

后面团队中有经验的同事，想到了一种Redis事务的方法。```采用了```


**php Predis使用的方式**

Predis是PHP连接集群的一个类库。到目前为止大概是PHP使用redis3.0 cluster唯一的方式。

```php
// ......省略引入类库代码
Predis\Autoloader::register();
$client = new Predis\Client(array('tcp://192.168.0.8:6379','tcp://192.168.0.8:6380,array('cluster'=>'redis'));
// 这句话很重要。获取集群的map表
$client->set('fuck', 'you');
$redis = $client->pipeline();
$count = 0;
$perNum = 0;
if ($handle) {
    while(($buffer = fgets($handle, 4096)) !== false) {
            $aa = trim($buffer[0]);
            $bb = trim($buffer[1]);
            try {
                $redis->setnx($aa, $bb);
                $count++;
                if($count % 1000 == 0) {
                    $redis->execute();
                    // 这两句也很重要。注销对象，并且创建新的空对象。
                    unset($redis);
                    $redis = $client->pipeline();
                    $perNum = 0;
                }
                $perNum++;
            } catch(Exception $e) {
                 continue;
            }
        }
    }
    // 处理文件末尾不足1000的部分。
   if($perNum > 0) {
        $redis->execute();
    }
}
```

**php redis使用事务的方式**

php redis处理事务的方式比较简单。因为网上使用这种方式都比较多。主要的函数就两个。multi() 和 exec()。原理是先打包，然后一次性执行。实现的方式和上述方式相仿。这里不赘述，注意函数名称的变化就好。

## 总结 ##

redis3.0 cluster的坑很多。连接redis的phpredis和Predis的坑也很多。另外:目前redis3.0真的不是很稳定，能不用集群的方式用代理的方式替代就用稳定的代理方式吧。我们私下也在写自己的代理，尽管这周集群运转比较正常。

那天看到亚一程在微博吐槽phpredis源码，并且正在重构。后面一想：同样是人差距还真的挺大的。


（全文完）


