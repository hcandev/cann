<h1 align="center">Biraz Portswigger Alıştırmaları</h1>

# İLK ALIŞTIRMA 

* Burpsuite kullanarak https://portswigger.net/ üzerinden birkaç alıştırma yapacağız.

* Burpsuite'i açalım ve Proxy kısmına gelip ***'Open Browser'*** tuşuna basalım.

* Açılan browser'da https://portswigger.net/ adresine gidelim ve kendimize bir hesap oluşturalım.

* Portswigger'da hesap oluşturup şifremizi kaydettikten sonra https://portswigger.net/web-security/all-labs#access-control-vulnerabilities buraya girelim.

* Buradan ***'User ID controlled by request parameter with data leakage in redirect'*** testine tıklayalım; 

![alt text](<WebSec101 0x05_ss/image.png>)

* Yukarıda bu testteki erişim kontrol zaafiyetinin varlığından söz ediliyor. Bu testi çözebilmemiz için ***carlos*** kullanıcısının API key'ini bulmamızı istiyor. Ancak bize bunu wiener'in hesabına girerek yapmamızı istiyor. ***Access the Lab*** kısmına tıklayıp devam edelim;

* Açılan sayfada sağ üstte ***Account Login*** tuşuna basalım ve giriş kısmına ***wiener***, şifreye de ***peter*** yazalım;

* Giriş yaptıktan sonra BurpSuite uygulamasına gelelim ve **Proxy** sekmesi altındaki ***HTTP History*** sekmesine tıklayalım;

![alt text](<WebSec101 0x05_ss/image-1.png>)

* Buradan biraz aşağı inip seçilmiş kısmı bulalım;

![alt text](<WebSec101 0x05_ss/image-2.png>)

* Görüldüğü üzere burada weiner hesabındaki request ve response verilerini görebiliyoruz. Bize verilen görevde carlos'unkiler lazım.

![alt text](<WebSec101 0x05_ss/image-3.png>)

* Bunu yapmak için sol alttaki ***Request*** adlı pencerede yazılan request kodunun tamamını seçip kopyalayalım;

![alt text](<WebSec101 0x05_ss/image-4.png>)

* Bu sefer ***Proxy*** sekmesi yerine ***Repeater*** sekmesine gelip kopyaladığımız kodu ***Request*** penceresine yapıştıralım ve sonrasında ilk satırda yazan ***'GET /my-account?id=wiener HTTP/2'*** kısmında wiener yazan yere carlos yazalım;
![alt text](<WebSec101 0x05_ss/image-5.png>)

* Ardından sol yukarıdaki ***Send*** tuşuna basalım ve çıkan küçük pencereye ok deyip tekrardan ***Send*** tuşuna basalım;

![alt text](<WebSec101 0x05_ss/image-6.png>)

* Gördüğümüz gibi ***response*** kısmında artık carlos'a ait veriler var. Bu kısımdan biraz aşağı doğru inip ***carlos'a*** ait API anahtarını bulup kopyalayalım;

![alt text](<WebSec101 0x05_ss/image-7.png>)

* Browser'a tekrar gelip submit solution kısmına tıkladıktan sonra kodu yapıştırıp Tamam tuşuna basalım.

![alt text](<WebSec101 0x05_ss/image-8.png>)

![alt text](<WebSec101 0x05_ss/image-9.png>)


* Böylece bir alıştırmayı çözmüş olduk. Görüldüğü üzere bize verilen hesap üzerinden giriş yaptık ve bu giriş bilgisinin sunucuya gönderdiği sorguyu Burpsuit ile yakaladık. Sonra buradaki bir girdiyi değiştirerek sunucuya başka bir sorgu yolladık. Bu sorgu başka bir kullanıcının bilgisini içerdiğinden sunucudan bize gelen response'da o kullancıya ait veriler çıktı. Basit bir IDOR zaafiyetini uygulamalı olarak yapmış olduk.

# İKİNCİ ALIŞTIRMA

* Bu sefer https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references bu kısmı açalım.

* Bu alıştırmada sunucunun dosya sisteminde kullanıcı sohbet kayıtlarının tutulduğundan bahsediyor. Carlos'un şifresini bulup hesabına giriş yapabilirsek alıştırmayı tamamlayacakmışız. Devam etmek için ***Access the Lab** tuşuna basalım. 

![alt text](<WebSec101 0x05_ss/image-10.png>)

* Sağ üstten ***Live Chat*** kısmına tıklayıp bir mesaj yazıp send tuşuna basalım. Sonra da ***View Transcript*** tuşuna basalım. Bu ***'2.txt'*** isimli bir dosya indirecek. Şimdi Burpsuite geçelim;

![alt text](<WebSec101 0x05_ss/image-11.png>)

* ***Proxy*** sekmesinden ***HTTP History*** sekmesine gelip biraz aşağı gelip indirme işlemi yapılan dosyayı bulalım;

![alt text](<WebSec101 0x05_ss/image-12.png>)

* Buradan sol aşağıdaki ***Request*** penceresine sağ tıklayıp ***Send to Repeater*** tuşuna basalım ve kod ***Repeater*** sekmesine gitsin;

![alt text](<WebSec101 0x05_ss/image-13.png>)

* Yukarıdan Repeater sekmesine gelelim ve gönderdiğimiz kod burada bizi bekliyor olacak. Burada send tuşuna bastığımızda yazdığımız mesajı ***response*** penceresinde görebiliyoruz;

![alt text](<WebSec101 0x05_ss/image-14.png>)

* Şimdi ***Request*** penceresinde ***GET /download-transcript/2.txt HTTP/2*** yazan yerdeki ***2.txt*** ifadesini ***1.txt*** olarak değiştirelim ve tekrar ***Send*** tuşuna basalım;

![alt text](<WebSec101 0x05_ss/image-16.png>)

* ***Response*** kısmında carlos'un sohbet kayıtlarını görebiliyoruz. Burada bir şifre de verilmiş. Kopyalayalım.

* Siteye geri dönüp sağ üstte ***My Account*** kısmına tıklayalım ve giriş ismine ***carlos***, şifreye de kopyaladığımız ***16nyqyiw7o0lgilleryo*** şifreyi yazalım ve giriş yapalım.

![alt text](<WebSec101 0x05_ss/image-17.png>)

* Böylece bu alıştırmayı da yapmış olduk. 


* Bu sefer sohbet kayıtlarındaki verilere sızıp onları kullandık. Bunu yapmak için de önce bizim bir mesaj yazması gerekti. Sonra bu mesajı sorgu olarak sunucuya giderken Burpsuite üzerinden yakaladık ve kodu burada tekrar çalıştırıp işlevini gördük. Kendi yazdığımız mesajı da görünce tek yapmamız gereken bu sorguyu biraz değiştirmek oldu. Hatırlarsak sohbetimizin transkriptini indirmiştik. Bunun adı da ***2.txt*** idi. Onun ismini değiştirip requesti tekrar çalıştırdığımızda aslında başka bir kullanıcının transkriptine ulaşmış olduk. Böylece o kullanıcının mesajlarını görebilir olduk ve verilere ulaştık. 


# KAYNAK
[ Web Security 101 0x05 - HackerConf.Stream! Goygoy ve Akabinde Web Security Academy
](https://www.youtube.com/watch?v=iYPqOWJR1nY&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=5)