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

<br>
Decompile the (.apk) file using ( jadx-GUI )
<br>
 على خفيف Code Review ونبدأ شغل 
 <br>
   هنا قطعت الشك باليقين وعرفت إيش البارامترز بالضبط اللي قاعد يصير لها 
   <br>
   Encryption
  <br>
     ![](../../posts_pics/12.png)
<br>
<br>
- <b>Weak Encryption Algorithm:</b>

Encryptionهنا الفنكشن المسؤولة عن الـ 
<br>
Symmetric Encryption (AES electronic codebook mode encryption)
  <br>
     ![](../../posts_pics/13.png)
<br>
<br>
Keyعلى طول راح بالي للـ 
<br>
وفعلا ببحث سريع لقيته 
  <br>
     ![](../../posts_pics/13_1.png)
<br>
<br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">Password Generator(Crunch + Cusom Java script):</p></b></body></html>

 وبكذا فهمنا كل اللي صاير , الحين وقت نبدأ نسوي الشي اللي كنا مخططين له من البداية وهو باسووردز ليست من 0000 إلى 9999 
 <br>
  Encrypted لكن تكون 
<br>
بإستخدام نفس الفنكشن المستخدمة بالتطبيق
<br>
 بإستخدام Clear Text Passwords فالبداية سويت 
 <br>
 Crunch
  <br>
     ![](../../posts_pics/14.png)
<br>
<br>
GitHubموجودة بـ Library طيب أنا لقيت الـ 
<br>
بحطها تحت بالمصادر إن شاء الله 
<br>
  داخل الإكليبس وبكذا أكون جاهز أسوي السكريبت على السريع jar file للـ import كان علي أسوي 
<br>
النتيجة النهائية للكود :
  <br>
     ![](../../posts_pics/15.png)
<br>
<br>
clearPass.txt الكود بكل إختصار ياخذ كل اللي بملف الـ 
<br>
 Crunch اللي سويناه بإستخدام 
 <br>
  Encryption ويسوي للقيم 
 <br>
  encryptedPass.txt وبعدها يكتبها بملف  
 <br>
 النتيجة النهائية للملف قبل وبعد 
 <br>
      ![](../../posts_pics/16.png)
<br>
<br>
 
 > <html><body><b><p style="color:#A52A2A;font-size:25px">Account TakeOver:</p></b></body></html>
 
 Intruder وبكذا صرنا جاهزييييين ننقل الليست على الـ 
<br>
وناخذ أي حساب 
<br>
      ![](../../posts_pics/17.png)
<br>
<br>
 
Done.

 > <html><body><b><p style="color:#A52A2A;font-size:25px">Final word:</p></b></body></html>

دعواتكم لي ولوالدي ولمن يعز علي , وإعذروني على القصور , وأي تعديل أو إقتراح بكون شاكر جدا 
<br>
Happy Hacking ^_^...
<br>
<br>

 > <html><body><b><p style="color:#A52A2A;font-size:25px">References:</p></b></body></html>

-   https://frida.re/docs/home/
-   https://frida.re/docs/frida-ps/
-   https://github.com/sensepost/objection
-   https://github.com/tsug0d/AndroidMobilePentest101
-   https://blog.securityevaluators.com/bypassing-okhttp3-certificate-pinning-c68a872ca9c8
-   https://github.com/BullyBoo/Encryption
