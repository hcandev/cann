<h1 align="center">XSS Çözümleri</h1>

**XSS CHEAT SHEET**
https://portswigger.net/web-security/cross-site-scripting/cheat-sheet 


# Alıştırma #1

* Sözlük

  * **HTMLJanitor library**: html kodlarında çeşitli js kodlarını temizleyen bir kütüphane. Bu sayede aslında xss saldırılarının önüne geçiliyor.

  * **DOM clobbering**: Bir saldırganın html içinde özel olarak hazırlanan keywordleri kullanarak(örneğin **id** veya **name**) JS değişkenlerini veya DOM nesnelerini üzerine yazmasıdır.

* https://portswigger.net/web-security/dom-based/dom-clobbering/lab-dom-clobbering-attributes-to-bypass-html-filters bu adrese geliyoruz.


* Alıştırmada web uygulamasının **HTMLJanitor library** kullandığından bahsediyor. Ancak bu kütüphaneyi kullanırken **DOM clobbering**'e karşı zaafiyeti olduğunu da ekliyor. 


* Siteye girelim ve bir post seçip **ViewPost** tuşuna basalım;

![alt text](<WebSec101 0x0B_ss/image.png>)

* Aşağı doğru indiğimizde yorumlar kısmını göreceğiz. Bizde bir yorum yazalım;

![alt text](<WebSec101 0x0B_ss/image-4.png>)

* Yukarıdaki şekilde sallamasyon bir şeyler yazdık. Yalnız dikkat edersek yorum kısmında yazı ile beraber çeşitli kodlar da var. Bunu yazmamızın sebebi aslında sitenin bize başta bahsettiği **htmljanitor**'un hangi kodları temizlediğini ve hangi kodlara izin verdiğini bulmak. Sonuçta yorum kısmında js kodları çalıştırdık ve site altyapısının **xss**'i önlemek için bu kodları silip silmeyeceğine bakacağız. Bu yorumu **Post Comment** tuşuna basarak yollayalım;

![alt text](<WebSec101 0x0B_ss/image-1.png>)

* Gördüğümüz gibi yorumumuz en altta gelmiş. Dikkat edersek yazdığımız bazı şeyler silinmiş. 

* Şimdi web uygulamasına sağ tıklayıp **incele** diyelim ve bizim yorumumuzun olduğu ekranın olduğu kodun üzerine gelelim;

![alt text](<WebSec101 0x0B_ss/image-2.png>)

* Sağ tıklayıp **edit as html** tuşuna basalım;

![alt text](<WebSec101 0x0B_ss/image-3.png>)

* Yazdığımız kodu görebiliyoruz html tarafında;

![alt text](<WebSec101 0x0B_ss/image-5.png>)

* Buradan yazdığımız kodların silindiğini yine görebiliriz. Ancak <form> şeklinde yazdığımız kod silinmemiş.

* Web uygulamasına gelip sağ tıklayalım ve sayfa kaynağını görüntüle tuşuna basalım.
(kısayolu ctrl+u);

![alt text](<WebSec101 0x0B_ss/image-6.png>)

* Biraz aşağıda iki js dosyası görüyoruz. Aşağıdakine tıklayalım ve kodu kopyalayıp vs code ile açalım;

![alt text](<WebSec101 0x0B_ss/image-7.png>)

* İşaretli kısımda hangi taglere izin verildiğini görebiliyoruz. Burada bizim browserda gördüğümüz **form** tagı da var. Burada ayrı olarak **value, name, type, id** gibi taglere de izin verildiğini görüyoruz. 

* Şimdi yorum kısmına **<form onclick=alert(1)><input id=attributes>buraya tiklaa** kodunu yazalım ve tekrar yorum yapalım;

![alt text](<WebSec101 0x0B_ss/image-8.png>)

* Aşağı inip yorumumuzu bulduktan sonra yazımızın hemen üstündeki form alanına tıklayalım;

![alt text](<WebSec101 0x0B_ss/image-9.png>)

* Evet görüldüğü gibi alert çalıştı.

* Şimdi inputtaki id yerine başka bir şey yazalım ve öyle yorum yapalım örn; <form onclick=alert(1)><input id=can>haha

![alt text](<WebSec101 0x0B_ss/image-10.png>)

* Evet bu sefer yorumdaki form kısmına tıkladığımızda **alert()** çalışmadı. Koda baktığımızda da **alert()** silinmiş gözüküyor. Buradan form içerisindeki fonksiyonun çalışmasının inputun içerisindeki **id=attributes** tanımlaması ile ilişkisinin olduğuna dair bir yorum yapabiliriz. 

* Bunun da daha detaylı sebebi;

![alt text](<WebSec101 0x0B_ss/image-11.png>)

* Yukarıdaki js kodu ile ilişkili. Bu kod web uygulamasının beslendiği js kodu. Biraz yukarıda da bir benzerine bakmıştık. Şimdi buradaki görselde attribute'a ilişkin bir takım şartlar mevcut. Burada aslında bizim form elementininin bir attribute'u oluşmuş oluyor. Ama bu yalnızca <input id=attributes> yazdığımızda oluşuyor. Çünkü **input** tagi aslında **form** taginin altında bir tag. Dolayısıyla bu kodda **form**'un devraldığı **attribute** parametrisi okunmamış oluyor. Bundan dolayı da **htmljanitor** yazdığımız **js** kodunu silmiyor. E bu olduğu vakit kodun aşağısında attribute'u silen kod da hiç çalışmamış oluyor(aşağıdaki **if** ile başlayan kod). Böyle olduğu için de bu js kodunda bir zaafiyet bulmuş oluyoruz.

* Şimdi **<form id=x tabindex=0 onfocus=print()><input id=attributes>** kodu ile yorum yapalım. Burada **onfocus** parametresi geldiği için **id=x** ve **tabindex=0** gibi parametreler de eklendi. Bunun sebebi sayfa yüklendiğinde ilgili yoruma odaklansın diye(odaklanmadan kasıt, tab tuşuna basarak seçmektir.);

![alt text](<WebSec101 0x0B_ss/image-12.png>)

* Şimdi sitenin  **exploit server** kısmına gidelim ve body kısmına <iframe src=https://YOUR-LAB-ID.web-security-academy.net/post?postId=3 onload="setTimeout(()=>this.src=this.src+'#x',500)"> kodunu yapıştıralım;

![alt text](<WebSec101 0x0B_ss/image-15.png>)

* Yorum yaptığımız blog kısmındaki id'yi **YOUR-LAB-ID** yerine yapıştıralım.

* Sonra **post id** yazan yere;

![alt text](<WebSec101 0x0B_ss/image-14.png>)

* Yorum yaptığımız postun urlsindeki post id'yi yapıştıralım ve sonra exploit server ekranından önce **store** sonra da **Deliver Exploit to Victim** diyelim;

![alt text](<WebSec101 0x0B_ss/image-16.png>)

* Böylelikle bu alıştırma tamamlanmış oldu.


# Alıştırma #2

* https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded bu alıştırmaya gelelim.

* Sitenin arama özelliğinde xss olduğunu söylüyor ve çözmemiz için buraya alert fonksiyonu yazmamız isteniyor. 

* Siteye girip arama yerine direkt **<script>alert(1)</script>** kodunu yazalım; 

![alt text](<WebSec101 0x0B_ss/image-17.png>)

![alt text](<WebSec101 0x0B_ss/image-18.png>)

* Hızlıca çözüldü.

# KAYNAK 

[Web Security 0x0B | Web Security Academy'de XSS Çözmeye Devam](https://www.youtube.com/watch?v=Cy9qGc_A_Ic&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=12)

