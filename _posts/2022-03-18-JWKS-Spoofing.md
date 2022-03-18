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
بكل إختصار هو قيمة الـ public key 
وبعض المعلومات عنه على شكل
JavaScript Object Notation (JSON)
وراح نشوف قدام بإذن الله كيف ممكن نسويه.

`JSON Web Key Set (JWKS)` is a set of keys containing the public keys used to verify any JSON Web Token (JWT) issued by the authorization server and signed using the RS256 signing algorithm. According to Auth0.<br>

الـ JWKS هو فقط مجموعة من الـ JSON Web Keys (JWK) محطوطين بـ Array. 

Conection Establishment وفي خلال عملية الـ
 <br>
 Certificate  يقوم الكلاينت سايد بالتأكد من الـ  
<br>
 الكونيكشن بيتوقفMisMatch وإذا كان فيه أي 
<br>
طيب بعد ماأخذنا نبذة سطحية عن الموضوع (الموضوع أكبر من كذا بكثير جدا) خلونا الحين نذكر بعض طرق التخطي
<br>
Check زي ماقلنا فوق فيه عملية
<br>
  لازم بطريقة أو بأخرى نوقف العملية هذي علشان نصير قادرين نكون طرف ثالث في منتصف الكونيكشن ونقدر نعترض الترافيك 
 
 
:التالي Process فيه بعض الطرق تعتمد على الـ 
- Identifying the CLASS
- Identifying the method
- Finding the return value
- Hooking the method and modifying the return value

من هذي الطرق 
- SSLUnpinning - Xposed Module ( تطبيقات جاهزة وهذا سيناريو اليوم )
- Objection 
- Custom Frida Scripts

الطريقتين الثانية والثالثة بإذن الله بشرحها مستقبلا , وهي إن شاء الله سهلة في حال الوصول للدالة المعنيًة 

طيب ندخل في سيناريو اليوم , زي ماذكرت استخدمنا الطريقة الأولى بس قبل مانستخدمها على عماها خلونا نفهم وش اللي يصير خلف الكواليس
<br>
 UnSSLPinning هنا كلام من الريبو الرسمي الخاص ببروجكت
 
 ``` 
If you need to intercept the traffic from an app which uses certificate pinning, with a tool like Burp Proxy, the SSLUnpinning will help you with this hard work! The SSLUnpinning through Xposed Framework, makes several hooks in SSL classes to bypass the certificate verifications for one specific app, then you can intercept all your traffic
```
طيب معليكم من فلسفتهم الزايدة , بكل إختصار لو نشوف السورس كود الخاص بالبروجكت
<br>
 Common Libraries  هو يعتمد بشكل كامل على بعض الـ 
<br>
 ويطبق عليه البروسيس المذكور فوق
 <br>
okhttp3 ومنها اللي كانت موجودة بسيناريو اليوم
<br>
طيب كيف عرفنا التطبيق يستخدمها ؟
<br>
 decompiled apk source code  عن طريق الـ  
<br>
<br>
:<b>وبكذا بعد ماعرفنا خلونا نطبق</b>
<br>
<br>
 SSL Pinning هنا أول ماإكتشفت إن فيه 
<br>

![](../../posts_pics/-1.png)
<br>
<br>
 راح نفهم في قسم الستاتيك اناليسس كيف طلعناها okhttp3 library وهنا نشوف الـ 
<br>
![](../../posts_pics/0.png)
<br>
وعشان نفهم أكثر كيف قدر البروجكت يتخطاها , زي ماقلت لكم بتطبيق البروسيس المذكور فوق
<br>
 بالسطرين 10 , 13 Check وهنا نشوف عملية الـ
<br>
```java
public void check(String hostname, List<Certificate> peerCertificates) 
throws SSLPeerUnverifiedException {
    List<Pin> pins = findMatchingPins(hostname);
    if (pins.isEmpty()) return;
 …
      for (int p = 0, pinsSize = pins.size(); p < pinsSize; p++) {
        Pin pin = pins.get(p);
        if (pin.hashAlgorithm.equals("sha256/")) {
          if (sha256 == null) sha256 = sha256(x509Certificate);
          if (pin.hash.equals(sha256)) return; // Success!
        } else if (pin.hashAlgorithm.equals("sha1/")) {
          if (sha1 == null) sha1 = sha1(x509Certificate);
          if (pin.hash.equals(sha1)) return; // Success!
        } else {
          throw new AssertionError();
        }
      }
    }
…
}
```
<br>
<br>
SSLUnpinning - Xposed Module وهنا الـ 
<br>
![](../../posts_pics/1.png)
<br>
<br>
طريقة إستعماله سهلة جدا 
<br>
![](../../posts_pics/2_1.png)
<br>
<br>
وأخيرا 
<br>
![](../../posts_pics/3.png)
<br>
<br>

> <html><body><b><p style="color:#A52A2A;font-size:25px">Network Traffic Analysis:</p></b></body></html>

 :توصلت لثلاث أشياء API endpoints بعد فحص بعض الـ 
<br>
-  Rate Limit مافيه أي 
-  بمجرد عمل ريسيت للباسورد راح يتم عمل باسوورد جديد مكون من 4 أرقام فقط ( بإعتبار الشخص مفروض يدخل وبعدين يغيره )
-   Encryption صاير لها Request Body النقطة الأخيرة وهي اللي جابت كل الشغل اللي تحت كانت قيم المتغيرات فالـ  

فهنا كل اللي علينا نسوي ريسيت باسوورد على أي حساب وبعد مايصير الباسوورد مجرد 4 أرقام نبدأ بتخمينها بحكم إن مافيه ريت ليمت
<br>
Encrypted  لكن المشكلة قيم البارامترز كانت 
<br>

 مثل 0000 أو 9999 وأبدأ أخمن لأن الباك ايند سيرفر ماراح يتعرف عليها أبدأ Clear Text Passwords ماأقدر أرسل 
 
 <br>
 <br>
 بلشة صح ؟ اتفق 
 <br>
 <br>
   لاحظوا هنا قيمة الباسوورد , على فكرة أنا أدخلتها 0000
   <br>
    Encryption بس زي ماذكرت فيه عملية
  <br>
  ![](../../posts_pics/4.png)
<br>
<br>
 Login endpoint وهنا للتأكيد مافيه أي ريت ليمت على الـ 
 <br>
   ![](../../posts_pics/5.png)
<br>
<br>
:والآن نقول بسم الله تونا نبدأ 
<br>
   ![](../../posts_pics/happ.gif)
<br>
<br>
> <html><body><b><p style="color:#A52A2A;font-size:25px">Basic Dynamic Analysis(Genymotion, adb, Frida, Objection):</p></b></body></html>

    
Encryption بعد ماشفنا فوق إن فيه عملية 
<br>
Encryption Algorithm الحين الهدف صار إننا ندور على الـ
<br>
 ونحاول بطريقة ما نسوي باسووردز ليست من 0000 إلى 9999 بنفس المبدأ  
 <br>
 طيب عشان ندور على الفنكشن المسؤولة عندنا طريقتين , 
 الأولى ستاتيك نقعد نبحث بالكود لين تطلع عيوننا أو نخمن إسم الفنكشن ونسوي سيرش سريع
 <br>
    والثانية عن طريق الداينمك , بكل إختصار نشوف الكلاسس والميثودز 
 <br>
 that are being loaded at run-time
 <br>
 وبعدها نروح ندور عليها فالكود
 <br>
<br>
- ```Dynamic Analysis```:is the process of testing and evaluation of a program by executing data at run-time <br>
- ```Genymotion```: Emulator that gives us rooted devices <br>
- ```Android Debug Bridge(adb)```: Android Debug Bridge (adb) is a versatile command-line tool that lets you communicate with a device. The adb command facilitates a variety of device actions, such as installing and debugging apps, and it provides access to a Unix shell that you can use to run a variety of commands on a device <br>
- ```Frida```:it’s a dynamic code instrumentation toolkit. It lets you inject snippets of JavaScript or your own library into native apps on Windows, macOS, GNU/Linux, iOS, Android, and QNX. Frida also provides you with some simple tools built on top of the Frida API<br>
- ```Objection```:is a runtime mobile exploration toolkit, powered by [Frida](https://www.frida.re/), built to help you assess the security posture of your mobile applications<br><br>

بعد ماعرفنا التولز نبدأ بسم الله نطبق 
<br>
<br>
 Emulator بعد ماننزل التولز اللي فوق نتصل بالجهاز الموجود بالـ 
  <br>
  adb عن طريق 
  <br>
  Frida-server بعدها ننقل الـ
  <br>
   Frida toolsلداخل الجهاز عشان نقدر نستفيد من بعض الـ  
   <br>
      ![](../../posts_pics/6.png)
   <br>
   <br>
   
   Objection طيب الحين راح نبدأ نستخدم الـ 
   <br>
   هنا من الريبو الرسمي الخاص بالبروجكت 
   <br>
   <br>
   For all supported platforms, objection allows you to:

-   Patch iOS and Android applications, embedding a Frida gadget that can be used with `objection` or just Frida itself.
-   Interact with the filesystem, listing entries as well as upload & download files where permitted.
-   Perform various memory related tasks, such as listing loaded modules and their respective exports.
-   Attempt to bypass and simulate jailbroken or rooted environments.
-   ```Discover loaded classes and list their respective methods.```
-   Perform common SSL pinning bypasses.
-   Dynamically dump arguments from methods called as you use the target application.
-   Interact with SQLite databases inline without the need to download the targeted database and use an external tool.
-   Execute custom Frida scripts.
<br>
<br>
اللي يهمنا حاليا هو الشي اللي ضللته . 
<br>
بسم الله نشوف كيف نسويها 
<br>
<br>
 Package قبل نسويها نحتاج نطلع إسم الـ
 <br>
 Frida-tools هنا راح نستخدم وحدة من الـ
 <br>
`frida-ps -U` : list running processes
<br>
   ![](../../posts_pics/7.png)
<br>
<br>
 بعد ماطلعت إسم الباكج الحين راح نستخدمه في الـ
<br>
`objection --gadget Package_Name explore` 
<br>
هنا نسوي ليست لكل الكلاسز
<br>
that are being loaded at run-time
<br>
`android hooking list classes`
<br>
   ![](../../posts_pics/8_11.png)
<br>
<br>
<br>
 وبعد ماوصلنا لأسماء الكلاسز نبدأ نسوي دمب لكل الميثودز الموجودة بكل كلاس 
  <br>
  ```android hooking watch class Class_Name```
  <br>
Login وهنا نشوف وصلنا للـ
<br>
  لأن كان هدفي من البداية أشوف وش من البارامترز بالضبط  
  <br>
  Encryption اللي يصير لها 
<br>
   ![](../../posts_pics/9_1.png)
<br>
<br>
 Encryption  وهنا قدرت أوصل لإسم الكلاس والفنكشن المسوؤلة عن الـ 
<br>
![](../../posts_pics/11_1.png)
<br>
<br>
وبعد ماوصلنا لإسم الكلاس والفنكشن خلاص نقدر نبحث عنها ونبدأ شغل الستاتيك بإذن الله 
<br>
   ![](../../posts_pics/10_1.png)
<br>
<br>
> <html><body><b><p style="color:#A52A2A;font-size:25px">Basic Static Analysis( Jadx-GUI ):</p></b></body></html>

طيب بعد ماعرفنا إسم الكلاسس والفنكشنز الحين بكل بساطة 
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
