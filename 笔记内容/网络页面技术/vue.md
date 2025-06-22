## 入门想法

```vue
<script>
	var vue = new Vue({
        data:{
            
        },
        created(){
            
        },
       method:{
        <!-- 弹出添加窗口-->
        openSave(){
            this.dialogFormVisible = true;
            this.resetForm();
         },
           //添加
           resetForm(){
              this.formData = {}; 
           },
           savaBook(){
               
           },
           getAll(){
               axios.get("/books").then((res)=>{
               		this.dataList = res.data;
               });
           },
       }     
    })
</script>
```

## axios 一个基于 promise 的网络请求库

## 响应结构

```vue
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 是服务器响应头
  // 所有的 header 名称都是小写，而且可以使用方括号语法访问
  // 例如: `response.headers['content-type']`
  headers: {},

  // `config` 是 `axios` 请求的配置信息
  config: {},

  // `request` 是生成此响应的请求
  // 在node.js中它是最后一个ClientRequest实例 (in redirects)，
  // 在浏览器中则是 XMLHttpRequest 实例
  request: {}
}
```

```
axios.get('/user/12345')
  .then(function (response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```

```vue
for(let i=0;i<array1.length;i++){
     this.$set(this.array2,i,this.array1[i] }
```

```vue
  data: {
                    // 定义字符串
                    name: "谷哥的小弟",
                    // 定义整数
                    number:9527,
                    // 定义数组
                    hobby:["篮球","足球","击剑"],
                    // 定义对象
                    user:{id: 21, name: "zxx", age: 50},
                    // 定义对象数组
                    users:[
                        {id: 21, name: "zxx", age: 50},
                        {id: 22, name: "zxc", age: 51},
                        {id: 23, name: "zcc", age: 52},
                    ]
                }
```

