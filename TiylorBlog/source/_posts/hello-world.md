---
title: Hello World
date: 2021-04-24
comments: true
tags: 
- 异步
categories:
- 技术

---
传统网站存在的问题

网速慢时，只能等待

表单提交后，如果内容一项不合格，就需要重新填写所有表单内容

页面跳转，需要重新加载整个页面，造成资源浪费



### Ajax概述

**Asynchronous JavaScript and XML**”（异步的[JavaScript](https://zh.wikipedia.org/wiki/JavaScript)与[XML](https://zh.wikipedia.org/wiki/XML)技术）--[wikipedia](https://zh.wikipedia.org/wiki/AJAX)

是浏览器提供的一套方法，可以实现页面无刷新更新数据，提高用户浏览网站应用的体验

### Ajax 应用场景

1. 页面上拉加载更多数据

2. 列表数据无刷新分页

3. 表单项离开焦点数据验证

4. 搜索框提示文字下拉列表

### Ajax的运行环境

Ajax技术需要运行在网站环境中才能生效，当前会使用Node创建的服务器作为网站服务器

【需要express基础，学完再来】... ...【回来继续】

#### Ajax 运行原理

Ajax相当于**浏览器发送请求与接收响应的代理人**，以实现在不影响用户浏览页面的情况下，局部更新页面数据，从而提高用户体验

![image-20200508122726537](E:\0a文档\前端笔记\image-20200508122726537.png)

#### Ajax的使用步骤

1. 创建Ajax对象

   ```var xhr = new XMLHttpRequest()```

2. 告诉Ajax请求地址以及请求方式

``` xhr.open('get','https://www.gsau.edu.cn')```

3. 发送请求

   ```xhr.send();```

4. 获取服务器端给客户端的响应数据

   ```js
   xhr.onload= function(){
   	console.log(xhr.responseText)
   }
   ```

   下面就是示例：在pubic文件夹下的01Ajax入门.html文件中定义了Ajax请求，路由是/first,然后在服务器端即app.js中，响应了hello Ajax，在Ajax中使用``` xhr.responseText```即可得到这个响应信息。

```HTML
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body>
	<script type="text/javascript">
		// 1 创建Ajax对象
		const xhr = new XMLHttpRequest();
		// 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
		xhr.open('get','http://localhost:3000/first')
		// 3 发送请求
		xhr.send()
		// 4 获取服务器响应到客户端的数据 xhr.responseText
		// 当Ajax接收完服务器端的响应数据后触发onload事件
		xhr.onload = function(){
			console.log(xhr.responseText)
		}
	</script>
</body>
</html>
```

```js
// 引入express框架
const express = require('express')
// 路径处理模块
const path = require('path')
// 创建web服务器
const app = express()

// 静态资源访问服务功能
app.use(express.static(path.join(__dirname,'public')));
app.get('/first',(req,res)=>{
	res.send('hello ajax')// 响应给客户端的信息
})

app.listen(3000)
console.log('服务器启动成功')
```

![image-20200508130147324](E:\0a文档\前端笔记\image-20200508130147324.png)

#### 服务器端响应的数据格式

​	在真实的项目中，**服务器端大多数情况下会以JSON对象作为响应数据的格式**。当客户端拿到响应数据时，要将JSON数据和HTML 字符串进行拼接，然后将字符串的结果展示在页面中。

​	在http请求与响应的过程中，无论是请求参数还是响应内容，如果是对象类型，最终都会转换为对象字符串进行传输。

```js
app.get('/responseData',(req,res)=>{
	res.send({'name':'zs'});
})  // S：服务端响应JSON数据

// C：客户端发送Ajax请求并得到返回数据
// 1 创建Ajax对象
		const xhr = new XMLHttpRequest();
		// 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
		xhr.open('get','http://localhost:3000/responseData')
		// 3 发送请求
		xhr.send()
		// 4 获取服务器响应到客户端的数据 xhr.responseText
		// 当Ajax接收完服务器端的响应数据后触发onload事件
		xhr.onload = function(){
			console.log(xhr.responseText)  // 字符串格式
			// console.log(typeof xhr.responseText)
			var responseText = JSON.parse(xhr.responseText)//将json字符串转换为json对象
			console.log(responseText) // 对象类型
			var str = '<h2>'+responseText.name+'</h2>' // 通过DOM操作，将数据写入页面
			document.body.innerHTML = str;
		}
```

#### 请求参数 传递

传统网站表单提交，get方式会将参数放到地址后面，post参数会放到请求体里面。

##### Ajax -GET

Get请求方式：``` xhr.open('get','http://localhost:3000?name=zs&age=20') ```

```js
var params ='username='+nameValue+'&age='+ageValue+'' // 需要自己拼接请求参数

xhr.open('get','http://localhost:3000/get?'+params) //设置请求方式和请求的完整地址

xhr.send() // 发送请求
xhr.onload = function(){
	console.log(xhr.responseText)
	} // 输出服务端的返回值
```

##### Ajax-POST

``` js
xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')
xhr.send('name=zhangsan&age=20');
```

post请求需要在请求头明确设置请求参数的类型，即Content-Type属性

> 回顾：HTTP请求和响应过程中传递的数据块就叫报文

```js
// 客户端
var params ='username='+nameValue+'&age='+ageValue;

			xhr.open('post','http://localhost:3000/post')
		
			xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded') //设置请求参数的类型
			xhr.send(params)
			xhr.onload = function(){
				console.log(xhr.responseText)
			}
```

```js
// 服务端
...
const bodyParser = require('body-parser')
app.use( bodyParser.urlencoded({extended: true}) )
...
app.post('/post',(req,res)=>{
	console.log(req)
	res.send(req.body)
});
```

![image-20200513162946406](AJAX.assets/image-20200513162946406.png)

> body-parser相关的可以访问https://www.cnblogs.com/lovekiku123/p/11961094.html和https://blog.csdn.net/web_youth/article/details/80053187?utm_source=blogxgwz0，了解

#### 请求参数的格式

**Content-Type类型**：

1. `` application/x-www-form-urlecoded ``  

   传统表单提交就是这种参数格式，**GET请求只能提交这种参数格式**

   ​	``` name=zhangsan&age=20 ```

2. `` application/json `` 

   提交json格式的数据 ，需要POST方式提交

   ​	``` JSON.stringify() //将json对象转换为json字符串 ```

```js
// 客户端
var xhr = new XMLHttpRequest()

xhr.open('post','http://localhost:3000/json')
		
xhr.setRequestHeader('Content-Type','application/json') //设置请求参数的类型

xhr.send(JSON.stringify({name:'zhangsan',age:20}))

xhr.onload = function(){
	console.log(xhr.responseText)
}
```

```js
// 服务端
...
const bodyParser = require('body-parser')
app.use(bodyParser.json());
...
app.post('/json',(req,res)=>{
	res.send(req.body)
});
```

![image-20200513164603450](AJAX.assets/image-20200513164603450.png)

3. `` text/xml ``    提交xml格式的数据

4. `` multipart/form-data `` 文件提交

#### 获取服务器端的响应

Ajax 状态码：在创建ajax对象，配置ajax对象，发送请求，以及接收完服务端响应数据，这个过程中的**每一个步骤**都会对应一个数值，这个数值就是ajax状态码

0. 请求未初始化，open未设置

1. 请求已建立，open以配置，未发送，未调用send方法 

2. 请求已发送 

3. 请求处理中，接受到了部分数据，正在接受服务端的响应数据 

4. 响应数据接受完成，客户端可以使用响应数据了

获取ajax状态码（readyState属性）：`` xhr.readyState ``

onreadystatechange 事件

当Ajax状态码发生变化时将自动触发该事件

```js
const xhr = new XMLHttpRequest();
console.log(xhr.readyState)
// // 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
xhr.open('get','http://localhost:3000/readyState')

// // 3 发送请求
		
console.log(xhr.readyState)
xhr.onreadystatechange=function (){
	console.log(xhr.readyState)
}
xhr.send()
```

![image-20200513172332274](AJAX.assets/image-20200513172332274.png)

```js
xhr.onreadystatechange=function (){
			if(xhr.readyState==4){
				console.log(xhr.responseText)
			}
		}
		xhr.send()
```

![image-20200513172444084](AJAX.assets/image-20200513172444084.png)

![EB53555AF6B307DCCF82F4A5C60379AF](AJAX.assets/EB53555AF6B307DCCF82F4A5C60379AF.png)

#### Ajax 错误处理

1. 网络畅通，服务端能接受到请求，服务端返回的结果不是预期结果

   可以判断服务器端返回的状态码，分别进行处理，`` xhr.status `` 获取http状态码

2. 网络畅通，服务器端没有接收到请求，返回404状态码

   检查请求地址是否错误 

3. 网络畅通，服务器端能接受到请求，服务器端返回500状态码（内部服务器错误）

   ``` GET http://localhost:3000/error 500 (Internal Server Error) ```

4. 网络中断，请求无法发送到服务器端

   会触发xhr对象下面的onerror事件，在onerror事件处理函数中对错误进行处理

```js
// 客户端
<button id="btn">发送ajax请求</button>
	<script type="text/javascript">
		var btn = document.getElementById('btn');
		btn.addEventListener('click',()=>{
			const xhr = new XMLHttpRequest();
		// 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
		xhr.open('get','http://localhost:3000/error')
		// 3 发送请求
		xhr.send()
		// 4 获取服务器响应到客户端的数据 xhr.responseText
		// 当Ajax接收完服务器端的响应数据后触发onload事件
		xhr.onload = function(){
			// console.log(xhr.status) // http 状态码
			console.log(xhr.responseText)
			if(xhr.status == 400){
				alert('请求出错')
			}
			}
		xhr.onerror = ()=>{
			alert('网络中断，无法发送Ajax请求')
		}
		})
	</script>
```

```js
// 服务端
app.get('/error',(req,res)=>{
	// console.log(zbc)
	res.status(400).send('not ok')
})
```



![image-20200513183014884](AJAX.assets/image-20200513183014884.png)

ajax状态码表示Ajax请求的过程状态，是ajax对象返回的，HTTP状态码表示请求的处理结果，是服务器端返回的。

#### 低版本IE浏览器的缓存问题

> 问题：在低版本的IE浏览器中，Ajax请求有着严重的缓存问题，即在请求地址不发生变化的情况下，只有第一次请求会真正发送到服务器端，后续的请求都会从浏览器的缓存中获取结果。即使服务器端的数据更新了，客户端依然拿到的是缓存中的旧数据。

**解决方案**:

在请求地址的后面加请求参数，保证每一次请求中的请求参数的值不相同

`` xhr.open('get','http://localhost:3000?t='+Math.random()); `

```js
// 客户端
<button id="btn">发送ajax请求</button>
	<script type="text/javascript">
		var btn = document.getElementById('btn');
		btn.onclick = function(){
			var xhr = new XMLHttpRequest();
			// 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
			xhr.open('get','http://localhost:3000/cache?t='+Math.random())
			// 3 发送请求
			xhr.send()
			// 4 获取服务器响应到客户端的数据 xhr.responseText
			// 当Ajax接收完服务器端的响应数据后触发onload事件
			xhr.onreadystatechange = function(){
				// console.log(xhr.status) // http 状态码
					if(xhr.readyState == 4 && xhr.status == 200){
						alert(xhr.responseText)
					}
				}
		}	
	</script>
```

```js
// 服务端
const fs = require('fs')
...
app.get('/cache',(req,res)=>{
	fs.readFile('./test.txt',(err,result)=>{
		res.send(result)
	})
})
```

![image-20200513192848878](AJAX.assets/image-20200513192848878.png)

### 异步编程

#### 同步异步概述

**同步**，做完一件事，才能做下一件事，一行代码执行完才能执行下一行代码；

**异步**，开始做一件事，中途可以做其他事，回来可以接着做这件未完成的事，代码而言就是，程序不会等待异步代码执行完毕才去执行下一行代码，而是接着执行下面的代码，当异步代码执行完毕之后，就自动调用准备好的回调函数执行操作，如计时器。

![2D12FEE455FCC485507FE39CA241AD74](AJAX.assets/2D12FEE455FCC485507FE39CA241AD74.png)

而Ajax是异步请求，所以如下

```js
		// 1 创建Ajax对象
const xhr = new XMLHttpRequest();
		// 2 以什么方式向哪发送请求  用open方法设置请求方式 请求地址
xhr.open('get','http://localhost:3000/first')
		// 3 发送请求
xhr.send()
		// 4 获取服务器响应到客户端的数据 xhr.responseText
xhr.onload = function(){
	console.log(2)
	console.log(xhr.responseText)
}

console.log(1)
```

![image-20200513194319531](AJAX.assets/image-20200513194319531.png)

### Ajax 封装

发送一次请求代码过多，封装函数即可解决这个问题

```js
ajax({
	type:'get',
	url:'http://www.gsau.edu.cn',
	success:function(data){
		console.log(data)
	}
})
```

```js
// 客户端
function ajax(options){
	var xhr = new XMLHttpRequest();
	xhr.open(options.type,options.url);
	xhr.send()
	xhr.onload=function(){
		options.success(xhr.responseText)
	}
}
ajax({
	type:'get',
	url:'http://localhost:3000/first',
	success:function(data){
		console.log(data)
	}
})
```

![image-20200514081243588](AJAX.assets/image-20200514081243588.png)

#### 请求参数的问题

1. 请求参数位置的问题：将请求参数传递到ajax函数内部

   get放在请求地址的后面，post需要放在send方法中。

2. 请求参数格式的问题，

   `` application/x-www-form-urlecoded ``      ``` name=zhangsan&age=20 ```

   `` application/json ``                                         ``  {name:'zhangsan',age:20} ``

综合使用对象格式传递参数

```js
// 客户端封装ajax函数，非必要参数使用默认值
<script type="text/javascript">
		function ajax(options){
			// 存储默认值
			var defaults={
				type:'get',
				url:'',
				data:{},
				header:{
					'Content-Type':'application/x-www-form-urlecoded'
				},
				success:function(){},
				error:function(){}
			}
			// 使用options对象中的属性覆盖defaults对象中的属性
			Object.assign(defaults,options);

			// 创建Ajax对象
			var xhr = new XMLHttpRequest();
			var params = '';//拼接请求参数的字符串
			for(var attr in defaults.data){
				params+= attr+'='+defaults.data[attr] +'&'
			}
			params = params.substr(0,params.length -1)
			// 将对象格式拼接为地址参数格式
			console.log(params)

			// 如果请求方式是get，就将参数拼接到地址上
			if(defaults.type=='get'){
				defaults.url = defaults.url+ '?' +params;
			}
			// 配置ajax对象
			xhr.open(defaults.type,defaults.url);
			// 如果请求参方式是post
			if(defaults.type == 'post' ){

				var contentType = defaults.header['Content-Type']; // !!!获得请求参数格式
				xhr.setRequestHeader('Content-Type',contentType)// 设置请求参数格式类型
				// 如果参数格式是json，先转化为字符串格式然后，在send函数内作为请求体发送
				if(contentType == 'application/json'){
					xhr.send(JSON.stringify(defaults.data))
				}else{
					// 向服务器端传递普通类型的参数
					xhr.send(params)
				}
				
			}else{
				// GET方式send直接发送
				xhr.send()
			}
			
			xhr.onload=function(){
				let contentType = xhr.getResponseHeader('Content-Type');
				let responseText = xhr.responseText;
				// 判断返回的数据类型是json，就把json字符串转换为json对象返回
				if(contentType.includes('application/json')){
							responseText = JSON.parse(responseText)
				}

				// http状态 200 时调用success函数
					if(xhr.status == 200){
						
						console.log(contentType);
						// xhr.getResponseHeader 获取响应头中的数据
							// 将结果返回调用处理函数处
							defaults.success(responseText,xhr)
						
					}else{
						defaults.error(responseText,xhr);
					}
			}
		}
				
		// 调用ajax函数，自动发送请求并得到返回结果
		ajax({
			url:'http://localhost:3000/responseData',

			success:function(data){
				console.log('success函数：')
				console.log(data)
			},
			
		})
	</script>
```

```js
// 服务端
...
const bodyParser = require('body-parser')
const app = express()
app.use(bodyParser.json());
...

app.get('/first',(req,res)=>{
	res.send('hello ajax')// 响应给客户端的信息
});
app.post('/first',(req,res)=>{
	res.send(req.body)// 响应给客户端的信息
});
app.get('/responseData',(req,res)=>{
	res.send({'name':'zs'});
});
```

![image-20200514111015585](AJAX.assets/image-20200514111015585.png)

### 模板引擎概述(客户端)

art-template

1. 下载并引入库文件

``` <script src="./js/template-web.js"></script> ```

2. 准备art-template 模板

   ``` js
   <script id='tp1' type="text/html">
   	<div class='box'></div>
   </script>
   ```

3. 告诉模板引擎将那一个模板和哪个数据进行拼接

   `` var html = temlate('tp1',{username:'zhangsan',age:'20'}); ``

4. 将拼接好的html字符串添加到页面中

   `` document.getElementById('container').innerHTML = html; ``

5. 通过模板语法告诉模板引擎，数据和html字符串要如何拼接

```js
<script id='tp1' type="text/html">
	<div class='box'>{{ username }}</div>
</script>
```

```js
// 案例：
......
<script type="text/javascript" src="js/template-web.js"></script>
......


<div id="container"></div>
	
<!-- 1、准备模板并且写入模板变量 -->
<script id="tp1" type="text/html">
	<h1>{{name}}</h1>
	<h1>{{age}}</h1>
</script>

<!-- 2、设置相应模板对应的数据对象，返回拼接好的html字符串 -->
<script type="text/javascript">
	var html = template('tp1',{
		name:'zhangsan',
		age:20
	});
	console.log(html)
	// 3、将自动拼接好的网页字符串写入网页
	document.getElementById('container').innerHTML= html;
</script>
```

![image-20200514160813091](AJAX.assets/image-20200514160813091.png)

### 案例·1：验证邮箱地址的唯一性

【有问题，暂留】

### 案例2：案例搜索框内容自动提示

### 案例 3 ：省市区联动（ok）

### FormDate

#### FormDate 对象的作用

1. 模拟THML表单，相当于将HTML表单映射成表单对象，自动将表单对象中的数据拼接成请求参数的格式
2. 异步上传二进制文件

#### FormData对象的使用

1. **准备HTML 表单**

```HTml
<form id="form">
		<input type="text" name="username">
		<input type="password" name="password">
		<input type="button" name="">
</form>
```



2. **将HTML表单转化为formdata 对象**

```js
var form = document.getElementById('form');
var formData = new FormData(form);
```

3. **提交表单对象**

```js
xhr.send(formData)
```

formData  只能用于post请求

> 在这里，使用formidable模块来使得服务端解析客户端发送的FormData对象的数据

它的使用方法：

```js
1. // 创建formidable表单解析对象
	const form = new formidable.IncomingForm();
2. // 解析客户端传递过来的FormData对象
	form.parse(req, (err, fields, files) => {
		res.send(fields);
	});
```

```js
// 客户端
<form id="form">
		<input type="text" name="username">
		<input type="password" name="password">
		<input type="button" id='btn' value="提交">
</form>
	<script type="text/javascript" src="../js/ajax.js"></script>
	<script type="text/javascript">
		var btn = document.getElementById('btn');
		var form = document.getElementById('form');

		btn.onclick = function(){
			var formData = new FormData(form);
			var xhr = new XMLHttpRequest();
			xhr.open('post','http://localhost:3000/formData')
			xhr.send(formData);
			xhr.onload=function(){
				if(xhr.status == 200){
					console.log(xhr.responseText)
				}
			}
		}
	</script>
```

```js
// 服务端
app.post('/formData', (req, res) => {
	// 创建formidable表单解析对象
	const form = new formidable.IncomingForm();
	// 解析客户端传递过来的FormData对象
	form.parse(req, (err, fields, files) => {
		res.send(fields);
	});
});
```

#### FormData 对象的实例方法

1. 获取表单对象中的值

`` formData.get('key') ``

2. 设置表单对象中属性的值

`` formData.set('key','value') ``

3. 删除表单对象中属性的值

   `` formData.delete('key')``

4. 向表单对象中追加属性值

`` formData.append('key','value') ``

注意：set方法与append方法的区别是，在属性名已存在的情况下，set 会覆盖已有键名的值，append会保留两个值。服务器端会默认接受最后一个重名的键值。

![image-20200518213342180](AJAX.assets/image-20200518213342180.png)

#### FormData 二进制文件上传

`` <input type='file' id='file'/> ``

```js
// 客户端
<form id="form">
		<div class="form-group">
			<label>请选择文件</label>
			<input type="file" id="file">
			<input type="button" id='btn' value="提交">
			<div class="progress">
				<div class="progress-bar" style="width: 0%;" id="bar">0%</div>
			</div>
			<!-- <progress value="" max="" id="bar"></progress> -->
		</div>		
	</form>
	
	<script type="text/javascript">
		var btn = document.getElementById('btn');
		var file = document.getElementById('file');
		var bar = document.getElementById('bar')

		file.onchange = function(){
			var formData = new FormData();
			// 将文件追加到FormData对象中，默认文件控件有一个数组是保存文件的
			formData.append('attrname',this.files[0]);
			// 创建并设置、发送ajax请求，然后监听服务端返回的信息
			var xhr = new XMLHttpRequest();
			xhr.open('post','http://localhost:3000/upload');
			// 在文件上传过程中持续触发
			xhr.upload.onprogress = function(e){
				// console.log(e)
				let result = (e.loaded/e.total) * 100 +'%';
				// 设置进度条百分比
				bar.style.width = result ;
				// 设置进度条显示百分比
				bar.innerHTML= result ;
			}

			xhr.send(formData)
			xhr.onload = function(){
				if(xhr.status == 200){
					console.log(xhr.responseText)
				}
			}
		}	
	</script>
```

```js
// 服务端
app.post('/upload',(req,res)=>{
	// 创建formidable表单解析对象
	const form = new formidable.IncomingForm();
	// 设置客户端上传文件的存储路径
	form.uploadDir = path.join(__dirname,'public','uploads')
	// 保留上传文件的后缀名字
	form.keepExtensions = true;
	// 解析客户端传递过来的FormData对象
	form.parse(req,(err,fields,files)=>{
		res.send('ok')
	})
})
```

![image-20200519144658067](AJAX.assets/image-20200519144658067.png)

#### FormData 文件上传图片即时预览

在将图片上传至服务端以后，服务端通常都会将图片地址作为相应数据传递给客户端，客户端可以从响应数据中获取图片地址，然后将图片再显示在页面中。

```js
// 客户端
xhr.onload = function(){
				if(xhr.status == 200){
					var result = JSON.parse(xhr.responseText)
					// 动态创建img表单
					var img = document.createElement('img');
					img.src = result.path;
					img.onload = function(){
						box.appendChild(img);
					}
				}
			}
```

```js
// 服务端
form.parse(req,(err,fields,files)=>{
		res.send({
			path:files.attrname.path.split('public')[1],
		})
	})
```

### Ajax同源政策

#### Ajax请求限制

Ajax只能向自己的服务器发送请求，比如现有一A网站，有一B网站，A网站的html文件只能向A网站服务器发送Ajax请求，B同样是这样，但是A网站不能向B网站发送Ajax请求，同理，B网站也不能向A网站发送Ajax请求。

##### 什么是同源？？？

​    如果两个页面拥有相同的**协议**、**域名**和**端口**，那么这两个页面就属于同一个源，其中只要一个不相同，就是不同源。端口默认值80。

#### 使用JSONP 解决同源限制问题

jsonp 是 json with padding 的缩写，它不属于Ajax请求，但它可以模拟Ajax请求

客户端：函数的定义和   （服务端函数的）调用

在服务端将json数据作为函数的参数，通过参数返回客户端的原函数，即填充（padding）json数据，实现了非同源向客户端发送数据的需求，可以看到的是，需要服务端需要和客户端的函数保持一致。

1. 将不同源的服务端请求地址写在script标签的src 属性中

![6D9C62D432FB072C67939240DD70CFE8](AJAX.assets/6D9C62D432FB072C67939240DD70CFE8.png)

2. 服务器端响应数据必须是一个函数的调用，真正要发送给客户端的数据需要作为函数调用的参数

```js
const data = 'fn({name:"zs",age:"20"})';
res.send(data)
```

3. 在客户端提前准备好这个函数的定义，在客户端全局作用域下定义函数fn

 `` function fn(data){} ``

4. 在fn函数内部对服务端返回的数据进行处理

   `` function fn(data){console.log(data);} ``

    

 ```js
// 客户端
<script> 
           function fn(data){
           	console.log('客户端的fn函数被调用了')
           	console.log(data)
           }
	</script>
	<!-- 1 请求地址写在script -->
	<script src="http://localhost:3001/test" type="text/javascript" charset="utf-8"></script>
 ```

```js
// :3001  服务端
app.get('/test',(req,res)=>{
	const result = 'fn({name:"张三"})';
	res.send(result)
})

// 监听端口
app.listen(3001);
// 控制台提示输出
console.log('服务器启动成功');
```

![image-20200519172600170](AJAX.assets/image-20200519172600170.png)

#### JSONP 代码优化

1. 客户端需要将函数名称传递到服务器端
2. 将script 请求的发送变成动态请求

```js
<button type="" id="btn">点我发送请求</button>
	<script> 
           function fn3(data){
           	console.log('客户端的fn函数被调用了')
           	console.log(data)
           }
	</script>
	<!-- 1 请求地址写在script -->
	

	<script>
		var btn = document.getElementById('btn')
		btn.onclick = function(){
			var script = document.createElement('script');
			// 设置src属性
			script.src = `http://localhost:3001/better?callback=fn3`;
			// 将script标签追加到页面中
			document.body.appendChild(script)
			script.onload= function(){
				document.body.removeChild(script);
			}
		}
	</script>
```

```js
app.get('/better',(req,res)=>{
	// 接受客户端传递过来的名称
	const fnName = req.query.callback;
	// 将函数名称对应的函数调用代码返回客户端
	const result = fnName+'({name:"张三"})';
	res.send(result)
})
```

3. 封装jsonp 函数，方便请求发送

```js
// 客户端
function jsonp(options){
			var script = document.createElement('script');
			var fnName = 'myJsonp' + Math.random().toString().replace('.','')
			window[fnName] = options.success;
			script.src = options.url+'?callback='+fnName;
			document.body.appendChild(script);
			script.onload= function(){
				document.body.removeChild(script)
			}
		}

     	var btn = document.getElementById('btn')

		btn.onclick = function(){

			jsonp({
			url:'http://localhost:3001/better',
			success:function(data){
				console.log(123);
				console.log(data)
			}
			});

		};
		
		var btn1 = document.getElementById('btn1')

		btn1.onclick = function(){

			jsonp({
			url:'http://localhost:3001/better',
			success:function(data){
				console.log(456);
				console.log(data)
			}
			});

		};
```

```js
// 服务端
app.get('/better',(req,res)=>{
	// 接受客户端传递过来的名称
	const fnName = req.query.callback;
	// 将函数名称对应的函数调用代码返回客户端
	const result = fnName+'({name:"张三"})';
	setTimeout(()=>{
		res.send(result)
	},1000)
	
})
```

### 案例：腾讯天气（待补充）

#### 访问非同源数据 服务器端解决方案

同源政策是浏览器给予Ajax技术的限制，服务器端是不存在同源政策限制

![39662EE7A9F5FB5022BD02320FBC7090](AJAX.assets/39662EE7A9F5FB5022BD02320FBC7090.png)

```js
	//客户端
	<button type="" id="btn">点我发送请求</button>

	<script src="../js/ajax.js"></script>
     <script type="text/javascript">
     	var btn = document.getElementById('btn')
		btn.onclick = function(){
			ajax({
				type:'get',
				url:'http://localhost:3000/server',
				success:function(data){
					console.log(data);
				}
			})
		};
	</script>
```

```js
// 同源服务端
app.get('/server',(req,res)=>{
	request('http://localhost:3001/cross',(err,response,body)=>{
		// console.log(err)
		// console.log(response)
		// console.log(body)
		res.send(body)
	})
})
```

```js
// 非同源服务端
app.get('/cross',(req,res)=>{
	res.send('ok')
})
```

![image-20200521152847254](AJAX.assets/image-20200521152847254.png)

#### 跨域请求之cookie

客户端与服务器端使用HTTP协议（无状态）交换信息

cookie是实现服务端与客户端身份识别的一种技术，由服务端响应cookie给客户端，由客户端请求时一并发送cookie，就实现了身份识别。

![C3092A7C949F3673D3111CB0EC4D2EA6](AJAX.assets/C3092A7C949F3673D3111CB0EC4D2EA6.png)

#### withCredentials 属性

在使用Ajax技术发送跨域请求时，默认情况下不会在请求中携带cookie信息。

在**客户端**  withCredentials :指定在涉及到跨域请求时，是否携带cookie信息，默认值为false

在**服务器端**，响应头中设置 Access-Control-Allow-Credentials:true  允许客户端发送请求时携带cookie

具体看本地实际代码设置

参考链接：http://www.ruanyifeng.com/blog/2016/04/cors.html

### $.ajax() 方法概述

作用：发送Ajax请求

```js
$.ajax({
    type:'get',
    url:'http://www.example.com',
    data:{name:'zhangsan',age:'20'},
    contentType:'application/x-www-for-urlencoded',
    beforeSend:function(){
        return false
    },
    success:function(response){},
    error:function(xhr){}
})
```

