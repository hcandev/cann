<h1 align="center">SSRF(Server Side Request Forgery) Giriş</h1>


Sunucu taraflı sorgu zafiyetinde saldırgan sunucuya istenmeyen requestler yaptırabilir. Yani saldırgan sunucuyu kullanarak uygulamadaki iç sistemlere ulaşıp burada veri sızıntısı gerçekleştirebilir. 

# Alıştırma #1 

* https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter bu alıştırmaya gelelim.

* Bu alıştırmada stok kontrol etme özelliği bulunuyormuş ve bu özellik iç sistemden veri getiriyormuş. Bizden de bu özelliği kullanarak admin arayüzüne erişmemizi ve carlos kullanıcısını silmemizi istiyor. 

* Lab'a girelim bir ürün seçelim ve ardından stok check dedikten sonra bu requesti repeater'a yollayalım;

![alt text](<WebSec101 0x15_ss/image.png>)

* Seçili yeri silip bize verilen **http://localhost/admin** adresini yazalım; 

![alt text](<WebSec101 0x15_ss/image-1.png>)

* Şimdi **admin** yazan yeri seçelim ve  sağ tıklayıp ilgili kısımdan şifreleme yapalım; 

![alt text](<WebSec101 0x15_ss/image-2.png>)

![alt text](<WebSec101 0x15_ss/image-3.png>)

* Aynı işlemi tekrarlayalım;

![alt text](<WebSec101 0x15_ss/image-4.png>)

* Şimdi iki kez şifreleme sebebimiz aslında parser'ı yanıltmak. Çünkü normalde sunucudan giden requestlerin şifresi parser tarafından çözümlenir ve buna göre erişim verilir ya da verilmez. Ancak iki kez şifrelediğimizde parser sadece 1 kere çözümleyeceği için **admin** yazan yer halen şifreli kalacaktır ve bu kod da parserdan geçmiş olacaktır. Bundan dolayı erişim için verilen izin faslını bypass etmiş oluyoruz. Bu yüzden iki kez şifreleme yaptık.

* Şimdi localhost yazan yeri **127.1** ile değiştirelim;

![alt text](<WebSec101 0x15_ss/image-5.png>)

* **localhost** keywordü de blacklistte olduğundan burayı **127.1** ile değiştirdik. Başta bu ip adresi doğru gözükmeyebilir ama parser bunu 127.0.0.1 olarak okuyacaktır. Buraya 127.1 yazma sebebimiz bu haliyle blacklistte olmayışı ile ilgilidir. Yani kod geçsin diye böyle yazdık yoksa bu kod kabul edilmez.  

* Şimdi send diyelim ve admin panele ulaşalım;

![alt text](<WebSec101 0x15_ss/image-6.png>)

* Aşağı inip **delete carlos** komutunu bulup kopyalayalım;

![alt text](<WebSec101 0x15_ss/image-7.png>)

* Şimdi bunu kodumuzun yanına yapıştırıp tekrar send diyelim; 

![alt text](<WebSec101 0x15_ss/image-8.png>)

* Send dedikten sonra lab çözülmüş olacak. 

# Alıştırma #2

* https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter bu alıştırmaya gelelim.

* Geçenki alıştırma ile aynı fakat farklı bir yöntem izlememiz gerekecek. Buradaki iç sistemde yasaklanan bilgiler yerine izin verilen bilgiler kullanılmış dolayısıyla kodumuzu ona göre yazacağız.

* Geçen alıştırmadaki gibi **check stock** özelliğini kullanarak request bilgisini repeater'a yollayalım;

![alt text](<WebSec101 0x15_ss/image-9.png>)

* Stock api kısmını şöyle değiştirelim: http://127.0.0.1%252f@stock.weliketoshop.net:8080/admin?product/stock/check?productId=1%26storeId%3D1

![alt text](<WebSec101 0x15_ss/image-10.png>)

* Şimdi değiştirdiğimiz bu kodu anlamaya çalışalım;

* Bu kod parser tarafından decode edildiğinde;

http://127.0.0.1/@stock.weliketoshop.net:8080/admin?product/stock/check?productId=1&storeId=1 

* Yukarıdaki halini alacaktır. 

* Modern browserlarda şu url formu kullanılır: **http://username@host:port/path**. Yani başta kullanıcı adı geliyor sonrasında host ve port sonrasında ise path. Burada **@** den önceki kısmı kullanıcı bilgileri olarak alınmaktadır. 

* Burada **127.0.0.1** adresini kullanıcı adı olarak işaret ediyoruz. Yani bilgisayarımızı. **stock.weliketoshop.net** adresini host olarak kullanıyoruz ki parser bunu tanıyıp izin versin. Sonrasında da path kısmına **admin** keywordünü ekliyoruz ki admin paneline ulaşabilelim. Şimdi burada **stock.weliketoshop.net** yazılı olduğundan bu isim parser tarafından görülüyor ve buradaki kodun tamamına izin veriliyor. ***Yani yaptığımız bu yöntemi damsız alınmayan bir bara araya tanıdık sokarak girmek gibi düşünebiliriz.***

* Buradaki kodun son hali iç sisteme: **http://127.0.0.1:80/admin** olarak gider. Yani bilgisayarımızda sunucudaki admin yetkilerine erişmiş oluruz. 

* Şimdi kullandığımız şifre yöntemlerine bakalım. **127.0.0.1%252f** kısmındaki **%252f** aslında **'/'** karakterinin iki kez şifrelenmiş halidir. Bu sayede parser bu kodu bir kere decode edeceği için **'/'** işaretini geçirebileceğiz.   

* Şimdi send tuşuna basalım ve carlosun delete komutunu bulalım; 

![alt text](<WebSec101 0x15_ss/image-11.png>)

* Kodumuza adminden sonra encode yaparak ekleyelim. yani **/admin/delete** diyoruz ve en sona **%26username%3dcarlos** şeklinde olacak. Parser bunu **/admin/delete?username=carlos** şeklinde okuyacaktır. Encode yapmadan bunu parserdan geçiremeyiz. Sonuçta tüm kod şöyle oluyor;

* ***http://127.0.0.1%252f@stock.weliketoshop.net:8080/admin/delete?product/stock/check?productId=1%26storeId%3D1%26username%3dcarlos***

![alt text](<WebSec101 0x15_ss/image-13.png>)

* Send diyelim ve labı tamamlayalım. 


# KAYNAK

[Web Security 0x15 | Yeni Burp Suite 2020.10 ve SSRF Giriş](https://www.youtube.com/watch?v=5rbQaOepMls&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=19)
