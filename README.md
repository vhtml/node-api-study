# nodejs api学习梳理

###常用工具Utilities
* console
> Node提供一个全局对象'console'，你可以使用它输出字符串。

    1. `console.log`
        输出字符串或者对象，输出对象时调用了util.inspect
    2. `console.warn`
    3. `console.trace`
* util
>Node有一个'util'模块，它有如下的一些功能：

    var util = require('util';
    util.log('hello');
>这条命令输出当前时间戳和指定的字符串

"inspect"函数是一个很好用的工具，它可以检查和打印一个对象的属性，用以快速排除问题。用法:
`util.inspect(object, showHidden, depth=2, showColors);`
>第二个参数“showHidden"，可用于显示对象的非可枚举属性。第三个参数"depth"，表示最大递归层数，通常用来检查大型对象，默认是递归2层，指定null值表示不限递归层数完整遍历对象。

util还有一些其他模块，比如继承等。

###Buffers
>用于处理二进制数据，这是mode标准方式传输数据。
>通常情况下，你可以向每一个可接收数据Node API传输Buffer。
>在回调函数中接收数据，也可以接收Buffer（除非特别指明数据流的编码）

将一个UTF-8字符串创建为Buffer：
    var buf = new Buffer('Hello World')

将一个其他编码的字符串创建为Buffer:
    var buf = new Buffer('45fd58f2','base64')

>可接受的编码有："ascii"、"utf8"和"base64"。

创建一个指定长度的空Buffer：
    var buf = new Buffer(1024)

然后这样操作它：
    buf[20]=56; //set byte 20 to 56
或者输出为一个空的utf-8字符串：
    var str = buf.toString();
或者输出为其它编码的字符串：
    var str = buf.toString('base64')
>utf-8是node默认的编码

* 分割Buffer
    >一个buffer可以被分割为一个更小的buffer:

    var buffer = new Buffer('this is the string in my buffer');
    var slice = buffer.slice(10,20);
    
    >slice函数并不创建新的buffer内存，它使用的是原有的buffer。
* 复制Buffer
    >复制一个buffer的一部分至另外一个buffer：

    var buffer = new Buffer('this is the string in my buffer');
    var slice = new Buffer(10);
    var targetStart = 0;
    var sourceStart = 10;
    var sourceEnd = 20;
    buffer.copy(slice, targetStart, sourceStart, sourceEnd);

###事件发射器
* .addListener
    >监听事件，等价于"on"方法

    var fs = require('fs');
    var readStream = fs.createReadStream('/etc/passwd');
    readStream.on('data',function(data){
        console.log(data);
    });
    readStream.on('end',function(){
        console.log('file end');
    });
* .once
    >一次性的监听一个事件
* .removeListener
    >移除一个事件的监听函数
* .removeAllListeners
    >移除一个事件的所有监听函数
* 创建一个事件发射器

    var EventEmitter = require('events').EventEmitter;  
    var util = require('util');
    //Here is MyClass contructor
    var MyClass = function(option1, option2){
        this.option1 = option1;
        this.option2 = option2;
    }
    util.inherits(MyClass, EventEmitter);
    
>util.inherits是一个实现对象间原型继承的函数，因此你的MyClass实例就可以继承事件发射器的原型方法。
>这样，MyClass实例可以发射事件：
    
    MyClass.prototype.someMethod = function(){
        this.emit('custom event','some arguments');
    }
如下，可以监听这个叫做'custom event'的事件了：
    var myInstance = new MyClass(1,2);
    myInstance.on('custom event',function(){
        console.log('got a custom event');
    });

>提示：事件发射器是促进接口解耦的一个好方法，它是一种提高各个接口的独立性的软件设计技术，帮助你更加灵活的编写代码。