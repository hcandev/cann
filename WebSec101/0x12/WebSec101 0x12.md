<h1 align="center">CS253 Sınavı - MDISEC Çözüyor Ben de Not alıyorum</h1>

* Standford [sınav soruları](https://web.stanford.edu/class/cs253/cs253-final-2019.pdf)

* [Cevap anahtarı](https://web.stanford.edu/class/cs253/cs253-final-2019-solutions.pdf)


# Doğru Yanlış

1. Code injection is caused when untrusted user data unexpectedly becomes code. 

- T 

2. It's OK to put sensitive information in hidden form fields since, after all, they're hidden. For example, <input type='hidden' name='databasePassword' value='hunter2'>.

- F 
- ***Yanlış çünkü saklı form alanları yalnızca web sayfasının görüntüsünde saklıdır. Kullanıcıdan saklı olamaz. Geliştirici modunu açtığımızda kaynak kodunda da saklı olarak gözükmez.***

3. The server can trust cookie values in HTTP requests to be untampered since the cookies are set by the server.  

- F 

- ***Cookieler sunucular tarafından belirlense de sonuçta kullanıcı tarafında depolanır(kullanıcının kullandığı browserda tutulur.). Bundan dolayı da kullanıcılar bu cookie değerlerini değiştirip kullanabilir. Alıştırmalarda cookie'lerin yerini değiştirip işlemler yapmıştık.***

4. The cookie attribute HttpOnly helps to mitigate the effects of XSS attacks by preventing client-side JavaScript from reading the cookie.

- T

- Doğrudur çünkü cookie'deki **HttpOnly** özelliği **browser'a** kullanıcı tarafından gelen **JS** kodlarını engellemesini söyler. Bu sayede kullanıcı **js** kullanarak cookie'ye erişemez. 

5. Your browser will save cookies even from sites you have not visited directly. ("Visited directly" means that e.g. you navigated to the site and its URL appeared in the browser's address bar).

- T  

- ***3.parti cookies olarak adlandırılan cookieler vardır. Bunlar belirli bir siteyi ziyaret edilmediğinde dahi kullanıcıya verilebilir. Örneğin x.com sitesine girdik ve orada bir e ticaret sitesinin reklamı var. Browser bu site reklamını yüklerken aynı zamanda bu siteye dair bir içerik de yükleyebilir. Dolayısıyla bu e ticaret sitesinin sunucusu biz oraya girmediğimiz halde bize bir çerez tanımlayabilir.*** 

6. You should prefer to use a blocklist (to block known bad input and allow everything else) rather than an allowlist (to only allow known good input and block everything else).

- F 

- ***Allowlist daha mantıklıdır. Çünkü izin verilen şeylerin dışındaki her şey bloklanır. Öte yandan blocklist'de ise sadece verilen girdiler bloklanır. Yani bloklanması unutulan şeyler olabilir ve bunlar saldırganlara bir açık verebilir. Bu yüzden allowlist daha makuldür. ***

7. Cross-site request forgery is a type of injection attack. 

- F 

- ***CSRF zaafiyetinde kod enjekte edilmiyor. Saldırgan kullanıcıya istenmeyen bir request yaptırtmaya çalışıyor. ***

8. HTML/JavaScript are the primary languages targeted by cross-site scripting attacks. 

- T

9. Reflected XSS occurs when a malicious user convinces a victim to send a request to a server with malicious input and the server echoes the input back to client.

- T 

10. The best way to prevent untrusted user input from exploiting your application is to use encryption.

- F

- ***Encryption saldırıları önlemede yardımcı olmaz. Yalnızca veri gizliliğini korur. Girdi doğrulama, çıktı şifreleme, sql injection önleyici yöntemler, CSP ve güvenlik kütüphaneleri gibi yöntemler saldırılara karşı önlem olabilir.***


11. You should set the Secure flag in a cookie to ensure that the cookie is only sent over encrypted HTTPS connections.

- T 

12. When accepting untrusted input from the user, we should escape it before it is added to the database so that we can later use it without worrying about escaping.

- F 

- ***Escaping yani kaçınma içeriğe bağlıdır. Context based yani içeriğe göre şifreleme yönteminde veriyi hangi context'de kullanacağımızı bilemeden bir şifreleme yapamayız. Bu yüzden yanlış.***

13. Two-factor authentication (a password together with a Time-based One-time Password (TOTP) code) is an example of defense-in-depth.

- T

14. The XSS Auditor was removed from Chrome because an attacker could use it to prevent specific scripts within a targeted page from executing.

- T 


# Kısa cevaplar

1. Name the three parts of a URL that are used to determine the URL's origin.

- ***Protocol, hostname, port***

2. Which character is most likely to be used in a SQL injection attack? Choose from: the singlequote ('), the null byte, the less than sign (<), or the greater than sign (>). 

- ***singlequote.***

3. You are a penetration tester evaluating a client's website for security vulnerabilities. You notice that their authentication system chooses sequential session IDs for users. Specifically, the first user to log in to the site gets a session ID of 1, the second user gets 2, the third user gets 3, and so on. Describe an attack against this authentication system.

- ***Bu saldırının adıne **session prediction(session tahmini)** denir. Saldırgan kendi session idsini ilk kullanıcınınkine eşitleyip 1. kullanıcının verilerine ulaşabilir. Eğer bu olmaz ise 2. 3. kullanıcılarının session id'lerini dener ve böyle devam eder.*** 

4. (Continued from previous question) The client "fixes" the issue by updating the server code so a
random number between 1 and 2 is chosen at startup and used as the first session ID given to a user. All subsequent session IDs are chosen by adding 1 to the last session ID given to a user. For instance, if the server randomly chose 9000 as the first session ID, then the second session ID would be 9001, and so on. Describe an attack against this authentication system.

- ***Saldırgan siteye giriş yapıp kendisine hangi session id'sinin verildiğine bakabilir ve eğer bu id'den 1 çıkarıp yazarsa kendinden önceki kullanıcının verisine ulaşabilir. Ya da 2 çıkarırsa önceki kullanıcıdan önceki kullanıcının verisine ulaşabilir.***

5. Why is it a bad idea to include detailed error information (e.g. including a stack trace) in the HTTP response when the server throws an exception? 

- ***Saldırgan error mesajlarını kullanarak sistemden veri çıkartabilir. Bu veriler hassas bilgiler taşıyabilir dolayısıyla tehlike doğurur.*** 


6. An attacker injects an XSS payload into the HTML page sent by your server. Given the following CSP, would the XSS attack succeed? Why or why not?

CSP: Content-Security-Policy: script-src 'self';
XSS: <script>alert(document.cookie)</script>

- ***Saldırı başarısız olur. **script-src 'self'** kısmı yazılan kodları sadece aynı origin(kök)'den gelirse çalıştırıyor. Yani web site ile aynı domain, protocol ve port olması lazım. Bu durumda inline kodları bloklanır. XSS'te bir inline script olduğu için bu çalışmaz.*** 


7. An attacker injects an XSS payload into the HTML page sent by your server. Given the following CSP, would the XSS attack succeed? Why or why not?

CSP: Content-Security-Policy: script-src 'self' https://javascript-cdn.com;
XSS: <script src='https://javascript-cdn.com/attacker-script.js'></script>

- ***Saldırı gerçekleşir. Çünkü geçtiğimiz soruya nazaran buradaki xss payload'u web adresiyle aynı origini paylaşıyor. Dolayısıyla **script-src 'self'** koşulu sağlanmış oluyor ve kod çalışıyor.*** 



8. Explain why including 'unsafe-inline' in a CSP makes it almost entirely ineffective at preventing XSS attacks.

- ***Çünkü bu kod inline kodlarını engellemiyor. XSS kodları da inline olduğundan bu koşul faydasız.*** 


9. Web browsers like Firefox and operating systems like macOS and Windows ship with a large built-in list of public keys of Certificate Authorities. What are these used for?  

- ***Web sitesi ziyaret edildiğinde o web sitesine ait güvenlik sertifikasının güvenilir bir otorite tarafından imzalanması gerekir. Bu güvenilir otorite de **CA** içerisinden bir otoritedir ve bu kurumun sağladığı sertifikalar güvenilir olur. Dolayısıyla built-in şekildeki **CA** tarafından verilen güvenlik sertifikaları ilgili sitelerin güvenliğini doğrulamaktadır.*** 

10. Describe a server-side defense that mitigates the effects of brute force (testing multiple passwords from a dictionary against a single account), credential stuffing (testing username/password pairs obtained from a breach), as well as password spraying (testing a single weak password against a large number of different accounts).

- ***Ip adresi üzerinden giriş denemeleri kısıtlanabilir. Belirli miktarda giriş denemesi sonrası Captcha gösterimi yapılabilir. Yeterince giriş denemesi sonrası kullanıcı yasaklanabilir.*** 

11. What is the difference between authentication and authorization? 

- ***Authentication kullanıcıyı doğrular. Kullanıcının kim olduğunu anlar ve kabul eder. Authorization ise kullanıcının sahip olabileceği yetkileri belirler.***


1. Same Origin Policy:
Would the following code running on https://attacker.com be allowed to print out the contents of the
Axess homepage, which include the currently logged-in user's grades? Why or why not?

```html 
<script>
const res = await fetch('https://axess.stanford.edu')
const data = await res.body.text()
console.log(data) // Haha, got your grades!
</script>
``` 
You can assume that https://axess.stanford.edu does not send any special HTTP headers such as
Access-Control-Allow-Origin, which are also known as "CORS" headers.

- ***Bir şey okuyamaz. Request bilgisayardan çıkar ve response gelir. Ancak browser gelen veriyi **CORS(Cross-Origin Resource Sharing).** kurallarına göre verir ya da vermez. Yani browser https://attacker.com sitesinin, https://axess.stanford.edu sitesindeki responselarını okuyamaz.*** 


2. More Same Origin Policy:
Would the following code running on https://attacker.com be allowed to listen to the 'submit' event on
the bank's login form and grab the username and password? Why or why not?

```html 
<iframe src='https://bank.com'></iframe>
<script>
const loginForm = window.frames[0].forms[0]
loginForm.addEventListener('submit', () => {
console.log(loginForm.username) // Haha, got your username...
console.log(loginForm.password) // ...and password!
})
</script>
``` 

- ***İki web sitesinin de origini farklı olduğu için birbirlerinin **DOM**'una **iframe** aracılığıyla ulaşamazlar. Dolayısıyla bu kod çalışmaz.*** 


3. CORS Preflight:
Explain why the browser must send an OPTIONS or "preflight" request to the server before it sends certain HTTP requests. To help jog your memory, here is an example of an OPTIONS request:

OPTIONS /resource/foo
Access-Control-Request-Method: DELETE
Origin: https://example.com


- ***Cors preflight request bir otomatik requesttir. Browser tarafından ilk başta deneme olarak gönderilir. Kontrol amaçlı yapılan bir requesttir ve sunucuya "Selam bu originden bu tarz requestler yollayabilir miyim?" sorusunu sorar aslında. Yukarıda da bu soru OPTIONS request ile sorulmuş.*** 


4. Cookies:
Your friend has built a personal site hosted at https://stanford.edu/~victim. They have built an authentication system so certain pages of the site can only be accessed by specific individuals. Once a user logs in successfully, the server sends a response with a Set-Cookie HTTP header to set a sessionId cookie in the user's browser.

```
Set-Cookie: sessionId=1234; Path=/~victim
``` 
Your friend is specifying the Path attribute on the cookie so that the cookie is scoped to the path prefix "/~victim". This means that the cookie will be sent when the user visits https://stanford.edu/~victim or https://stanford.edu/~victim/secret but not when they visit https://stanford.edu/~attacker. Explain how https://stanford.edu/~attacker can nonetheless read the sessionId cookie that was scoped to the victim's site.


- ***Path özelliği yetkilendirilmemiş okumayı engelleyemiyor. Saldırgan victim sayfasını iframe kullanarak açabilir çünkü aynı origindeler. Bu sayede victim sayfasındaki verilere ulaşabilir.*** 


5. More Cookies:
An attacker includes the following HTML in their site hosted at https://attacker.com which makes a GET request to a vulnerable bank server and transfers money into the attacker's account.
```html
<img src='https://bank.com/withdraw?amount=1000&to=attacker' />
``` 
The attacker is hoping the user is already authenticated with the bank site before they visit https://attacker.com and send the above GET request to the bank. The attacker entices users to visit their site by including hundreds of cute kittens like these ones:

![alt text](image.png)

Explain how the bank can modify their server code to protect users from this attack.

- ***Cookie özelliği "SameSite=lax" ya da "SameSite=strict" session cookie'sine eklenirse browser başka sitelerdeki cookie requestlerini dahil etmeyecektir.*** 


6. XSS:
The following Express route handler implements the homepage of the site at https://insecure.example.com but it is vulnerable to reflected XSS.

```js
app.get('/', (req, res) => {
let welcomeMessage = 'Welcome to our site!'
if (req.query.source) {
welcomeMessage = `Welcome ${req.query.source} reader!`
}
res.send(`
<h1>${welcomeMessage}</h1>
<p>This site uses top-of-the-line security and encryptions!!!1</p>
`)
})
```
Recall that the req.query property in Express is an object containing a property for each query string parameter in the route. For example, if the user visits https://insecure.example.com/?name=zelda, then the value of req.query will be { name: 'zelda' }. If there is no query string, it is the empty object,{}. Describe the XSS vulnerability in the code and provide a URL which an attacker could get a victim to visit in order to pull off a reflected XSS attack against them. The URL you provide should execute the following code: alert(document.cookie).

- ***buradaki **source** query(sorgu) parametresi temizlenmemiş. Dolayısıyla buraya xss kodu enjekte edilebilir:***
***https://insecure.example.com/?source=<script>alert(document.cookie)</script>***

7. More XSS:
The following Express route handler implements the logic for the login form of https://insecure.example.com but it is vulnerable to reflected XSS.

```js
app.post('/login', (req, res) => {
const { username, password } = req.body
if (isAuthValid(username, password)) {
res.send(`
<h1>Welcome logged in user!</h1>
<script>let username = '${jsStringEscape(username)}'alert('Hi there, ' + username)</script>
`)
} else {
res.send('Invalid username or password!')
}
})
// Escape a string so it can safely be used inside a JavaScript string within
// a <script> tag in an HTML page.
function jsStringEscape (str) {
return str
.replace(/'/g, "\\'") // Replace all ' with \'
.replace(/"/g, '\\"') // Replace all " with \"
}
// Returns true if the given login credentials are valid. False, otherwise.
function isAuthValid (username, password) {
// implementation omitted...
}
``` 
Describe the XSS vulnerability in the code and provide a URL which an attacker could get a victim to visit in order to pull off a reflected XSS attack against them. The URL you provide should execute the following code: alert(document.cookie).

Hint: Take a close look at the jsStringEscape function – it doesn't escape all the necessary characters!

Another hint: Think about what the final HTML page will look like with different possible username values!

Another, another hint: There are actually two vulnerabilities, but you only need to find one of them.


- ***JS string escape karakteri **(\)** engellenmemiş gözüküyor. Yalnızca tırnak işaretleri escape edilmiş. Dolayısıyla saldırgan şu şekilde stringi escape edebilir;***

***http://insecure.example.com/login?username=\';alert(document.cookie)//&password=123***



8. CSP:
The given CSP is applied to the given HTML page. Specify which resources, if any, will be blocked from loading by the CSP. There may be more than one. 
```
CSP: Content-Security-Policy: default-src 'none'; script-src 'self' https://partner.example.com img-src 'self' https://images.example.com; style-src 'self';
``` 

```html
HTML:
<!doctype html>
<html lang='en'>
<head>
<link rel='stylesheet' href='/style.css' />
<link rel='stylesheet' href='https://stylish.example.com/style.css' />
</head>
<body>
<script>alert('We have only the BEST memes!')</script>
<h1>Top memes:</h1>
<img src='https://images.example.com/cat1.jpg'>
<img src='https://images.example.com/cat2.jpg'>
<img src='/memes/cat3.jpg'>
<script src='/bundle.js'></script>
<script src='https://partner.example.com/analytics.js'></script>
</body>
</html>
```

- ***İlgili kısımlar engellenir;***

***<link rel='stylesheet' href='https://stylish.example.com/style.css' />: çünkü style-src self koşulu var ve buradaki adres verdiği style sitesi ile aynı değil.*** 

***<script>alert('We have only the BEST memes!')</script> bu bir inline scriptidir ve bloklanır. Çünkü 'unsafe-inline' koşulu eklenmemiş.***



9. HSTS Preload:
An attacker performed a DNS hijacking attack against your domain name. The attacker changed your domain's A record to point to their server IP address instead of yours. (Web browsers use the DNS A record to translate domain names to IP addresses). With the attacker in control of the DNS responses that your site visitors receive, their browsers will be directed to connect to the attacker's server instead of yours. Fortunately, your site is served using TLS and your site is loaded into the HTTPS Strict
Transport Security (HSTS) Preload List.

Explain how TLS and HSTS Preload protects visitors to your site from this attack.


- ***İlgili web sitesine giden kullanıcının devamlı olarak https requesti göndererek gitmesi ile alakalıdır. HSTS Preload security headera sahip olan websitelerine bu ayrıcalığı tanır. Bu ayrıcalığı ilgili web sitesini merkezi bir listeye alarak yapar ve bu listedeki siteler hsts politikalarını yükler. Dolayısıyla bunlar otomatik olarak sertifikalanır. Böylelikle HSTS Preload olan web sitelerine giden kullanıcılar her zaman **https** requesti ile gider dolayısıyla **ortadaki adam saldırısı** engellenir.*** 




10. Command injection:
The following Node.js program implements an HTTP server which accepts a user-provided filename and returns the contents of the specified file to the user, if it exists on the server. The file should only be returned if it exists in a folder named "static" where static files intended for viewing are stored.

```js
const express = require('express')
const childProcess = require('child_process')
const app = express()
app.get('/', (req, res) => {
res.send(`
<h1>File viewer</h1>
<form method='GET' action='/view'>
<input name='filename' />
<input type='submit' value='Submit' />
</form>
`)
})
app.get('/view', (req, res) => {
const { filename } = req.query
try {
const stdout = childProcess.execSync('cat static/' + filename)
// command succeeded, file exists
res.send(stdout.toString())
} catch (err) {
// command failed, file does not exist
res.send(err.toString())
}
})
app.listen(4000, '127.0.0.1')
``` 

Recall, the execSync function takes one or more commands to run, and runs them. If the command succeeds, the function returns the standard output. Otherwise, it throws an exception. Also recall, the cat program reads files sequentially, writing them to standard output. For example, the command cat file.txt will cause the contents of file.txt to be printed to the terminal.
Here's an example request and response interaction with this server.



Request:
GET /view?filename=hello.txt HTTP/1.1
Host: localhost:4000
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_1)
Response:
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Date: Tue, 10 Dec 2019 00:00:00 GMT
Hello, world!
There is a glaring security vulnerability in this server. What is the issue? How could the issue be fixed?
Hint: There are actually two security vulnerabilities, but you only need to find one of them.


- ***Bir tanesi LFI yani local file inclusion zafiyeti çünkü CMD cat komutu çalıştırıyor. Filename= ../../../../../../etc/passwd yazılırsa ekrana file'in contenti gelir.*** 

- ***Diğeri cmd injection zafiyetidir. örn &sleep 10; ya da $(sleep 10) cmd injection payloadlarına örnektir. ***


11. Fingerprinting:
List three unique attributes of a user's browser that a fingerprinting script could use to persistently identify the user even if they clear their cookies and other site data.

- ***browser katmanları, yüklenmiş fontların listesi, canvas fingerprinting, web audio fingerprinting, yüklü browser pluginleri, user-agent, local storage, browser dili, renk derinliği*** 


12. Logic bug:
The route handler below implements the "delete account" functionality which is common on most websites. This allows the user to completely delete their account. The actual deletion logic is in the deleteAccount function, which is not shown here. To confirm that the request came from the actual user, the request must include the user's password which is validated before the account is deleted.
```js
app.get('/delete', (req, res) => {
const { username, password } = req.body
if (!isAuthValid(username, password)) {
res.send('Invalid username or password.')
}
deleteAccount(username)
res.send('Account deleted.')
})
```
There are two severe security issues in the route handler. Identify the two issues

- ***İlki; kritik istekler(delete gibi) get yerine post ile gelmelidir. İkincisi CSRF ve no return or exit.*** 


# KAYNAK

[Web Security 0x12 | CS253 Sınavına Giriyor MDISEC](https://www.youtube.com/watch?v=6QaJ59AMkDg&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=16)