## cron task for node by redis
Inspired by the [bull](https://github.com/OptimalBits/bull) Prefectly support in cluster mode.


## feature
1. 业务方可以定义定时时间、时间结束的触发任务
2. 业务方可以更新或者删除已经发布的定时任务
3. 定时任务管理平台统一接收和调度任务
4. 设置准确的定时时间
5. 时间结束触发客户端，不能重复消费

## usage


### client
1. Initialize the redis config
2. register function for callback
3. publish task

```
var redisConfig = {
  host: '127.0.0.1',
  port: 6379,
  db: 1,
  password: 'root',
  // other redis config
};
const queue = require('cron-redis')('test', redisConfig);

var moment = require('moment');

function hello (x, y){
  console.log(new Date());
  console.log(x + ' + '+ y +' = %s', x+y);
}
var task1 = {
  method: hello.name,
  params: [2, 3],
  rule: moment().add(3, 's').toDate()
}

var task2 = {
  method: hello.name,
  params: [4, 5],
  rule: '* */1 * * * *'
}
queue.register(hello)
queue.publish(task1);
queue.publish(task2);


```

### API

#### require('cron-redis')(appName, redisConfig)
init config

* appName {String} what your app's name?
* redisConfig {Object}  
    

```
{
  host: '127.0.0.1',
  port: 6379,
  DB: 1,
  opts: {
    auth_pass: 'root',
    password: 'root'
  }
}
```
 
 
#### register
register a task 
* method {Function | Object} // method for cron task callback
   
   ```
   function hello (){
      console.log('hello');
   }
   queue.register(hello);
   
   
   or 
   
   var methods = {
    hello: function(){
       console.log('hello');
    },
    // and more
   }
   
   queue.register(methods);

   ```
   
   
#### publish
add a task to queue

* task {Object}
  * method {String} the name of method  will be callback.
  * params {Array} 
  * rule {options}  cron rule . {Date} or {* */1 * * * *}  
  * desc {options} description for task
  * uniqueID {options} keep task unique in queue
  
 
###### Supported format
 
 ```
 *    *    *    *    *    *
 ┬    ┬    ┬    ┬    ┬    ┬
 │    │    │    │    │    |
 │    │    │    │    │    └ day of week (0 - 7) (0 or 7 is Sun)
 │    │    │    │    └───── month (1 - 12)
 │    │    │    └────────── day of month (1 - 31)
 │    │    └─────────────── hour (0 - 23)
 │    └──────────────────── minute (0 - 59)
 └───────────────────────── second (0 - 59, optional)
 ```
or 

```
new Date();
moment().add(3, 's').toDate()
```

#### list 
the queue task in redis

* @return {Array[Object]} 

```
  [{ data: '{"method":"hello","params":[4,5],"rule":"* */1 * * * *"}',
    opts: '{"delay":60000}',
    progress: '0',
    delay: '0',
    timestamp: '1458008690942',
    attempts: '1',
    attemptsMade: '1',
    stacktrace: '[]',
    returnvalue: 'undefined',
    key: 'bull:test:111' },
   ]
```

### del
delete some hash key

* @param key {string} key of task


### More 
[bull](https://github.com/OptimalBits/bull) 
