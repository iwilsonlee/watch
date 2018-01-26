# watch   

<a href="https://travis-ci.org/jayZOU/watch/builds/286933612" target="_blank"><img src="https://api.travis-ci.org/jayZOU/watch.svg?branch=master" alt="Build Status"></a>

小程序不提供类似于vue一样监听数据([vm.watch](https://cn.vuejs.org/v2/guide/computed.html#%E8%AE%A1%E7%AE%97%E5%B1%9E%E6%80%A7-vs-%E8%A2%AB%E8%A7%82%E5%AF%9F%E7%9A%84%E5%B1%9E%E6%80%A7))，当数据改变时触发回调检测改变数据类型是否符合要求。  
![enter image description here](https://sfault-image.b0.upaiyun.com/270/694/2706941227-59df1b4702528_articlex)

现在，使用wach即可扩展类似vm.watch的功能
## install   
[链接下载](https://raw.githubusercontent.com/jayZOU/watch/master/src/libs/watch.js)   
## Usage     
### 引入watch库   
```javascript
import Watch from '../../libs/watch';
```


### 配置初始化检察对象   
```javascript
import Watch from '../../libs/watch';
let watch; //预先全局定义watch实例名称
Page({
    data: {
        a: 'hello',
        b: {
            c: {
                d: 33
            },
            e: [1, 2, [3, 4]]
        }
    },
    onLoad: function () {
    	watch = new Watch(this); //必须实例化Watch，否则下面的watch监听时间不起作用
	},
    watch: {//以下是通过watch监听事件来监听通过watch.setData方法设置的data变量
        a: function(val, oldVal) {
            console.log('new: %s, old: %s', val, oldVal);
        },
        'b.c.d': function(val, oldVal) {
            console.log('new: %s, old: %s', val, oldVal);
        },
        'b.e[2][0]': function(val, oldVal) {
            console.log('new: %s, old: %s', val, oldVal);
        },
        'b.e[3][4]': function(val, oldVal) {
            console.log('new: %s, old: %s', val, oldVal);
        },
    },
    bindTapChoose: function () {
    	var newA = this.data.a + ' world!';
	//这里必须使用watch进行setData设置data变量值，否则不会有响应效果。watch.setData继承原生小程序的this.setData方法，只是增加了响应data变量的功能。
	watch.setData({
		a: newA
	});
    }
})
```
可以将需要监听的数据放入**watch**里面，当数据改变时推送相应的订阅事件(注：不在data里面的数据项不会放入观察者列表，比如上面的`'b.e[3][4]'`)

### 实例化watch   
```javascript
watch = new Watch(this);
```
当**watch**创建示例初始化时会把监听数据项放入观察者列表里面

### 改变数据   
```javascript
watch.setData({
	a: 2,
	'b.c.d': 3,
	'b.e[2][0]': 444,
	c: 123
})

watch.data('b.e[2][0]', 555);
```

## API   
### watch.setData(obj)   
等价于原生小程序`this.setData`，会改变数据并更新视图，也会触发回调（假如有配置）  

**示例：**   

```javascript
watch.setData({
	a: 2,
	'b.c.d': 3,
	'b.e[2][0]': 444,
	c: 123
})
//等价于
// this.setData({
// 	a: 2,
// 	'b.c.d': 3,
// 	'b.e[2][0]': 444,
// 	c: 123
// })
```
### watch.data(key, val)   
等价于原生小程序`this.data.a = 3`，之后改变数据不更新视图，也会触发回调（假如有配置）  

**示例：**  
```javascript
watch.data('b.e[2][0]', 555);
//等价于this.b.e[2][0] = 555
```
### watch.getter(data, path)   
能根据提供的路径深度获取数据  

**示例：**  
```javascript
watch.get({
        a: '1',
        b: {
            c: {
                d: 33
            },
            e: [1, 2, [3, 4]]
        }
    }, 'b.e[2][0]');
 //3
```

### watch.unSubscribe(key)   
删除观察者，改变数据不触发回调  

**示例：**  
```javascript
watch.unSubscribe('b.e[2][0]');
```

## test
`npm test`  

## demo  
`git clone https://github.com/jayZOU/watch.git`   
打开小程序开发工具，新建项目，定位目录到**watch/src**
