<h1 align="center">SSL ve HSTS</h1>

## SSL(Secure Sockets Layer)

SSL, Güvenlik protokol standartıdır. Online iletişimde ağ sunucusu ile browser arasındaki bağlantıları şifreler. 

# Güvenli İletişim Nasıl Başlar?

Web uygulaması ile kullanıcı arasındaki ilişkiyi biliyoruz. Kullanıcı ve web uygulaması arasında bir request response döngüsü vardır;

![alt text](<WebSec101 0x0E_ss/image.png>)

Fakat bu iletişim döngüsü bir saldırgan tarafından izlenebilir. Buna **Man-in-the-Middle (MITM)** saldırısı yani **ortadaki adam saldırısı** tarzında türkçeleştirebiliriz;

![alt text](<WebSec101 0x0E_ss/image-1.png>)

Görüleceği üzere saldırgan buradan kullanıcı ile web uygulaması arasındaki veri trafiğini izleyebilir. İşte SSL'in devreye girdiği nokta da aslında burası. Bu tarz saldırıları önlemek için web uygulamalarının **güvenli iletişim katmanına** yani **SSL'e** çıkması gerekir ki bu veri akışı güvenli olsun. Bu noktada **browser** devreye girer. Browser burada **SSL** ile iletişim kurmak isteyecektir. Yani **güvenli iletişim katmanında** iletişim kurmak isteyecektir. Bunun için de web uygulaması Browser'a kendi **güvenlik sertifikasını** sunmak zorundadır. Sonrasında browser bu sertifikanın doğrulunu kontrol edecektir. Ancak bu sertifika **MITM** tarafından taklit edilebilir. Yani ortadaki adam da web uygulaması için bir sertifika oluşturabilir ve onu browser'a yollayabilir. Dolayısıyla **SSL** tarafında iletişim hala mümkün değil. Yani henüz güvenli iletişim başlamıyor. 

Bu noktada Browser'ın kendisine gelen sertifikaları bir şekilde doğrulaması gerekiyor. Yani web uygulamasından dönen sertifikaların da ikinci bir doğrulamaya ihtiyacı var. Burada da **Certificate Authorities(CA)** devreye girer. Yani sertifika otoriteleri. **CA**'nın burada yaptığı şey web uygulamasından gelen sertifikayı gerçekten o uygulamaya mı ait yoksa değil mi diye kontrol etmektir. Bunu da o web uygulamasından daha önce anlaşmalı olarak aldığı sertifikaya bakarak yapar. 

Örneğin x.com diye bir site var ve sertifikasını belirli miktarda para vererek bir **CA** kurumuna yolladı ki doğrulama işlemini yapsın. **CA**'da bu durumda artık x.com'dan gelen sertifika doğrulamasını x.com'dan daha önce aldığı sertifikayla yapacaktır. Bu durumda **SSL** tarafında iletişim mümkün olacaktır.

Yani burada **SSL** ile ilgili her şey **CA** kısmında bitiyor. Ancak **CA**'da tamamen güvenli değil. İmzalanan sertifikalar yine bir yazılım üzerinden yapıldığı için **vulnerability** yani **güvenlik açığı** söz konusu olmaktadır. **CA**'nın imzalama yöntemleri zafiyet barındırabiliyor. Örneğin imzaladığı sertifikayı doğrudan vermeyip info@x.com'a yollayabilir ki sadece o uygulama sahibi ulaşabilsin ve güvenli bir aktarım olsun. Ancak bu mail adresi hacklebilir ve sertifikalar yine saldırganın eline geçebilir. Sonuçta **CA** tarafında da **SSL** mevzusu tam olarak çözülmüş olmuyor. Bu durumda bize kullanıcı requestinin daha en başından güvenli bir ortamda yani **SSL** boyutunda çıkması gereken bir senaryo lazım. 

# HSTS (HTTP Strict Transport Security)

* Sözlük
    * security header:  Ziyaretçinin tarayıcısına belirli güvenlik özelliklerini uygulaması talimatını veren bir HTTP başlıkları kümesidir.

Ağ güvenlik protokolüdür. Browserların, web adresleriyle sadece güvenli **https** bağlantıları aracılığyla etkileşime geçmesini sağlar. 

Örneğimizden devam edelim;

![alt text](<WebSec101 0x0E_ss/image-2.png>)

Şimdi burada kullanıcının ilk defa girdiği bir web uygulamasını düşünelim. Kullanıcıdan bir request gidiyor ve sonrasında web uygulamasından da bir response dönüyor. İşte bu response'da eğer **hsts** **security header**'ı var ise o zaman kullanıcının browser'ı bu bilgiyi kaydediyor. Diyor ki ben bu web sitesine **her zaman** **https** olarak gitmek zorundayım. Yani kullanıcının bundan sonraki bütün requestleri bu web sitesine standart olarak **https** şeklinde gidecek. 

## HSTS Preload(HSTS Önyükleme)

Peki bir web sitesini ilk defa ziyaret ederken ortada bir adam varsa ne olur? İlk defa ziyaret ettiğimiz bir sitede bu ilk ziyaret sırasında saldırıya açık konumda oluruz. Çünkü ilk requestimiz http olarak gider. **https** olarak gitmesi için bir response'a ihtiyacımız vardır. İşte ilk defa ziyaret ettiğimizde dahi **https** olarak gitmemiz için bir yol var. Burada da **HSTS preload** devreye girer.

HSTS Preload, web tarayıcılarına önceden belirlenmiş bir liste üzerinden sitelerin **HSTS politikalarını** yüklemelerine izin verir. Bu merkezi bir listedir ve tarayıcı sahipleri(chrome, opera, mozilla, brave) tarafından tutulur. İlk defa ziyaret ettiğiniz bir site daha önceden bu listeye eklendiyse sizin ilk requestiniz dahil bütün requestleriniz **https** olarak gidecektir. Bu da güvenliği maksiumum seviyeye çıkarır ve ortadaki adam saldırısını önler. 

Sitelerin bu listeye eklenebilmesinin şartı da **hsts header**'ına sahip olmalarıdır. Yani browser yöneticileri bunu kontrol eder ve ona göre bu web sitelerini listeye alır.

# KAYNAK

[Web Security 0x0E | SSL Temelde Nedir ? HSTS
](https://www.youtube.com/watch?v=XlgG-Aw2nos&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=15)
