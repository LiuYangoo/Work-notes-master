#   Validate校验插件的使用  
>维护人员：**liuyang**  
>创建时间：2017-03-16   

###  1.首先引入需要引入的包     
       jquery-1.8.3.js(依赖jar，必须有)   
       jquery.validate.min.js（核心jar）
       messages_zh.js（中文信息提示包,如果自定义提示信息，可不要）
访问 [jQuery Validate]("https://jqueryvalidation.org/") 官网，下载最新版的 jQuery Validate 插件。       
###  2. Validate校验插件的引用
##### 　　1、将校验规则写到控件中
>　　方便快捷，但比较局限，适用于简单的校验。
使用class="{}"的方式，必须引入包：jquery.metadata.js

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>教程</title>
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/lib/jquery.js"></script>
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/dist/jquery.validate.min.js"></script>
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/dist/localization/messages_zh.js"></script>
<script>
$.validator.setDefaults({
    submitHandler: function() {
      alert("提交事件!");
    }
});
$().ready(function() {
    $("#commentForm").validate();
});
</script>
<style>
.error{
	color:red;
}
</style>
</head>
<body>
<form class="cmxform" id="commentForm" method="get" action="">
  <fieldset>
    <legend>输入您的名字，邮箱，URL，备注。</legend>
    <p>
      <label for="cname">Name (必需, 最小两个字母)</label>
      <input id="cname" name="name" minlength="2" type="text" required>
    </p>
    <p>
      <label for="cemail">E-Mail (必需)</label>
      <input id="cemail" type="email" name="email" required>
    </p>
    <p>
      <label for="curl">URL (可选)</label>
      <input id="curl" type="url" name="url">
    </p>
    <p>
      <label for="ccomment">备注 (必需)</label>
      <textarea id="ccomment" name="comment" required></textarea>
    </p>
    <p>
      <input class="submit" type="submit" value="Submit">
    </p>
  </fieldset>
</form>
</body>
</html>
```
效果图

![](assets/003/001-c7f9a744.png)
>可以使用如下的方法，修改提示内容：
class="{required:true,minlength:5,messages:{required:'请输入科目名称'}}" 

##### 　　2、将校验规则写到 js 代码中
>　　相比较写在控件中，写到 js 代码中比较独立。具有可扩展行，可应对比较复杂的校验需求。

```js
<script type="text/javascript">
$().ready(function() {  
   $("#J_Form").validate({
          rules: {
               tmplid: "required",//input name="tmplid"
               size:"required",
               path:{
                 required:true,
                 url:true;
               }
          },
          messages: {
              tmplid: "<font color='red'>*请选择接口模板名！</font>",
              size: "<font color='red'>*请选择支持尺寸！</font>",
              path:{
                 required:"<font color='red'>*请输入效果预览图地址！</font>",
                 url:"<font color='red'>*请输入效果预览图地址！</font>",
              }
          },
   });
   //自定义校验规则
   $.validator.addMethod("url",function(value,element){
   		 var reg=/(http|https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&:/~\+#]*[\w\-\@?^=%&/~\+#])?/;
        if(!reg.test(value)){
  		      return false;
  	  	}else{
  		      return true;
  		  }},"<span class='info-tip'><p><font color='red'>&nbsp;&nbsp;&nbsp;请输入合法的路径！</font><p></span>");

 });
 var sub1 = 0;
 $.validator.setDefaults({
    submitHandler: function() {
        if(sub1 == 1) {
    		   alert("请勿重复提交数据!!!");
    		   return false;
    	  } else {
    		   sub1 = 1;
    		   form.submit();
    	  }
  }

});
</script>
```
>注：如果文本框的name是对象.属性，写成"user.name": "required"   
>var str = "[@/'\"#$%&^*！￥……（）【】｛｝——，、。《》：“”；‘’,|_]+";  
>var reg = new RegExp(str);

###  3. Validate校验插件的特殊情况用法
##### 　　1、动态增加和删除校验
```js
if($("#Parent_id").val()==""){
      $("#area_id").rules("add",{required:true});
}else{
      $("#area_id").rules("remove","required");
}
```
