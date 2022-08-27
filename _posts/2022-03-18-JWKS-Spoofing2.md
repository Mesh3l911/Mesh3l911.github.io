---
title: JSON Web Key Sets Spoofing (JWKS Spoofing)
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
  

:الهدف بالنسبة لي من الموضوع نقطتين أساسية
 
 <ul><li><b> راح تعطيك فهم كافي للتطبيق بيساعدك في إكتشاف ثغرات أخرى POCs for CVEs كتابة </b></li></ul>
 <ul><li><b>
 Chaining Mindset التفكير دائما بأقصوى خطورة ممكنة للثغرة المكتشفة قبل كتابة التقرير 
   
 </b></li></ul>
 
 لآخر الثغرات في دروبال وهي POC في البداية كنت أحاول أكتب
 
 
 ```
 Arbitrary PHP code execution (CVE-2022-25277)
 # Drupal core - Critical - Arbitrary PHP code execution - SA-CORE-2022-014
```

   بعد ما إنتهيت من مراجعة السورس كود فهمت بالضبط طريقة الـ
   
 File Upload Process

وهي سلمكم الله (بإختصار) قبل رفع أي ملف يتم مراجعة الإمتداد, هل هو موجود بالبلاك ليست ولالا؟

إذا نعم

Will append a (.txt) extension

وتكون النتيجة

Mesh3l.php.txt


ومنها لاحظت بأن الإمتداد

(.svg)

ماكان من ضمنها.
`lib/Drupal/Core/File/FileSystemInterface.php`

![](../../posts_pics/INSECURE_EXTENSION_REGEX.png)

على طول بدون ماأضيع وقت فكرت أرفع ملف

(.svg) 

وأشوف يصير أي نوع من الـ

Filtration/Sanitization 

لمحتوى الملف ولالا ؟
وللأسف الإجابة كانت لا وكانت موجودة بكل إصدارات دروبال 




> <html><body><b><p style="color:#A52A2A;font-size:25px">Exploit Development:</p></b></body></html>

زي ماقلت لكم فوق, هنا من قبل أفكر بأي شي ثاني عرفت إنه عن طريق الـ

XSS

عندنا القدر نسوي أي آكشن ممكن يسويه الآدمن بمجرد زيارته للملف, عاد لكم أن تتخيلون كمية الآكشنز الموجودة بمشروع ضخم مثل دروبال

ALERT IS NOT AN EXPLOITATION, it's nothing but a basic POC.

طيب هنا فيه خيارات كثير جدا ممكن نسويها لكن أنا إخترت بكل بساطة نسجل حساب بصلاحيات محدودة وعن طريق الثغرة نجعل الآدمن يرفع صلاحيات الحساب إلى أعلى شي ممكن.


> <html><body><b><p style="color:#A52A2A;font-size:25px">Triggering the vulnerability (Simple POC):</p></b></body></html>

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


  

> <html><body><b><p style="color:#A52A2A;font-size:25px">Challenge (Whitebox Approach):</p></b></body></html>

بعد مافهمنا الآتاك الحين خلونا نشوف التحدي اللي سويته ونفهم كل شي عن طريق مراجعة الكود:

```python

const express = require('express');
const app = express();
const JWT = require('jsonwebtoken');
const jwt = require('express-jwt');
const jwksClient = require('jwks-rsa');

app.use((req, res, next) => {
    if(!req.headers.authorization){next()}
    const decodedToken = JWT.decode(req.headers.authorization.split(' ')[1]);
    req.jku = decodedToken.jku
    if(decodedToken.username === "Mesh3l_911"){
        next()
    }else{
        res.send("You r not Mesh3l_911");
    }
  })

app.use((req, res, next) => jwt({
    secret: jwksClient.expressJwtSecret({
        jwksUri: req.jku
    }),
    algorithms: ['RS256'],
}).unless({path: ['/']})(req, res, next))

app.get('/', (req, res) => {
    res.send('Welcome 2 my little chall, start from ( /source.zip ) & Enjoy ur time ^_^');
})

app.post('/flag', (req, res) => {
    res.send("Great j0b ^_^, The Flag is: Mesh3l_911{Jwks_Sp00fing}");
})

app.listen(80, () => console.log('Listening on port 80'));

```

jku هنا لو نشوف السطر رقم 20 راح تلاحظون إني جالس أخذ قيمة الـ 

Payload اللي راح تكون موجودة فالـ jku وهو قيمة الـ User Controlled Input عن طريق 

```jwksUri: req.jku```

وليست بالهيدر ؟ Payload موجودة بالـ jku طيب ليش فالتحدي قيمة 

Code Review أنا سويت كذا عشان أجبركم تسوون 

jku وبكذا خلاص كل اللي علينا نسوي الإستغلال اللي شرحناه فوق بس الفرق الوحيد إن قيمة 

Header وليست فالـ Payload راح تكون فالـ


وسلامتكم.

دعواتكم لي ولوالدي ومن أحب 

Happy Hacking ^_^

> <html><body><b><p style="color:#A52A2A;font-size:25px">References:</p></b></body></html>

-   https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-key-sets
-   https://assets.ctfassets.net/2ntc334xpx65/o5J4X472PQUI4ai6cAcqg/13a2611de03b2c8edbd09c3ca14ae86b/jwt-handbook-v0_14_1.pdf
-   https://www.npmjs.com/package/jsonwebtoken
-   https://www.npmjs.com/package/jwks-rsa
-   https://www.npmjs.com/package/express-jwt
-   https://www.npmjs.com/package/rsa-pem-to-jwk
-   https://russelldavies.github.io/jwk-creator/
-   https://book.hacktricks.xyz/pentesting-web/hacking-jwt-json-web-tokens
