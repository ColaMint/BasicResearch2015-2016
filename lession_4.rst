李铭：XSS与SQL注入攻击
===================

XSS
----

Cross-site scripting（XSS，跨站脚本攻击）是一种代码注入攻击，攻击者通过它能够在用户的浏览器上执行恶意代码。

XSS分类
~~~~~~~~


#. Persistent XSS（持久型XSS）
    
    #.. image:: _static/persistent-xss.png

    #. 攻击者使用网站的表单往网站的数据库插入恶意脚本
    #. 受害者请求网站的一个页面
    #. 网站从数据库取出恶意脚本，构造出一个将包含恶意脚本的页面返回给受害者
    #. 受害者的浏览器执行恶意脚本，将cookie等信息发送给攻击这的服务器

#. Reflected XSS（反射型XSS）

    #.. image:: _static/reflected-xss.png

    #. 攻击者构造一个包含恶意脚本字符串的URL并发送给受害者
    #. 受害者用浏览器打开URL
    #. 网站将URL中的恶意脚本作为网页内容的一部分，返回给受害者
    #. 受害者的浏览器执行恶意脚本，将cookie等信息发送给攻击这的服务器 


#. DOM-based XSS（基于DOM的XSS）

    #.. image:: _static/dom-based-xss.png

    #. 攻击者构造一个包含恶意脚本字符串的URL并发送给受害者
    #. 受害者用浏览器打开URL
    #. 网站到接受请求，但没有在返回内容中包含恶意脚本
    #. 受害者执行返回页面中的合法脚本，导致恶意脚本被插入到页面中
    #. 受害者的浏览器执行恶意脚本，将cookie等信息发送给攻击这的服务器 

防止XSS
~~~~~~~~

    * HTML encode
    
    .. code-block:: html 
        
        "<script>...</script>"    
        --- HTML encode --->   
        "&lt;script&gt;...&lt;/script&gt;"

    * 用户输入内容校验

    为了避免某些encoding无法防止的情况，例如：

    .. code-block:: js 
         
        userInput = "javascript:xxxx"
        document.querySelector('a').href = userInput
    
    以及一些用户自定义HTML的情况，需要对用户输入的内容进行校验。

    可以建立适当的黑/白名单，对用户输入的标签以及URL的协议进行限制，避免用户输入的内容包含恶意脚本。    

    例如，可以建立黑名单，禁止用户输入的内容包含<script>...</script>和javascript协议

    * Content Security Policy (CSP)，禁止浏览器向不信任的源发送请求

    通过在返回的HTTP报头中包含Content‑Security‑Policy这个Header来启用CSP，示例：

    .. code-block:: text 
    
        Content‑Security‑Policy:
        script‑src 'self' scripts.example.com;
        media‑src 'none';
        img‑src *;
        default‑src 'self' http://*.example.com

SQL注入攻击
--------
SQL注入攻击（SQL Injection），简称注入攻击，是Web开发中最常见的一种安全漏洞。

造成SQL注入的原因是因为程序没有有效过滤用户的输入，使攻击者成功的向服务器提交恶意的SQL查询代码，程序在接收后错误的将攻击者的输入作为查询语句的一部分执行，导致原始的查询逻辑被改变，额外的执行了攻击者精心构造的恶意代码。

Example
~~~~~~~

Form：

.. code-block:: html 
    
    <form action="/login" method="POST">
    <p>Username: <input type="text" name="username" /></p>
    <p>Password: <input type="password" name="password" /></p>
    <p><input type="submit" value="登陆" /></p>
    </form>

PHP Server Code：

.. code-block:: php

    $username = $_POST['username']; 
    $password = $_POST['password']; 
    $sql = "SELECT * FROM user WHERE username='{$username}' AND password='{$password}'";

如果用户输入的用户名为"myuser' or 'foo' = 'foo' --"，密码随意，那么得到的sql语句为：

.. code-block:: sql

    SELECT * FROM user WHERE username='myuser' or 'foo'=='foo' -- AND password='xxx'

"--"之后的内容为注释，所以可以无视密码登陆
 
防止SQL注入攻击
~~~~~~~~~~~~~~~

#. 严格限制Web应用的数据库的操作权限，给此用户提供仅仅能够满足其工作的最低权限，从而最大限度的减少注入攻击对数据库的危害。
#. 检查输入的数据是否具有所期望的数据格式，严格限制变量的类型，例如使用regexp包进行一些匹配处理，或者使用strconv包对字符串转化成其他基本类型的数据进行判断。
#. 对进入数据库的特殊字符（'"\尖括号&*;等）进行转义处理，或编码转换。
#. 所有的查询语句建议使用数据库提供的参数化查询接口，参数化的语句使用参数而不是将用户输入变量嵌入到SQL语句中，即不要直接拼接SQL语句。
#. 在应用发布之前建议使用专业的SQL注入检测工具进行检测，以及时修补被发现的SQL注入漏洞。网上有很多这方面的开源工具，例如sqlmap、SQLninja等。
#. 避免网站打印出SQL错误信息，比如类型错误、字段不匹配等，把代码里的SQL语句暴露出来，以防止攻击者利用这些错误信息进行SQL注入


参考资料
--------

.. [1] http://www.acunetix.com/websitesecurity/cross-site-scripting/
.. [2] https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.4.md
