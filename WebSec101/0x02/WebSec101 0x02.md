<h1 align="center">IDOR ZAFİYETLERİ</h1>

# Web Uygulaması ve Kullanıcı Arasındaki İlişki

Bir web uygulaması kullanıcıdan girdiler alarak çalışır. Yani bizler web sayfasıyla etkileşime geçer ve onun veri tabanına girdiler yollarız. Çoğu web uygulamasında etkileşime geçebileceğimiz çok fazla alan vardır. Örneğin bir alışveriş sitesinde kendi adresimizi ya da ödeme bilgilerimizi kaydedebiliriz. Bu işlemleri yaparken de aslında uygulamanın veri tabanına çok fazla direktif veririz.Çoğu web uygulamasında da kullanıcının etkileşime geçebildiği çok fazla alan olması sebebiyle bu web uygulamalarının veri tabanında karmaşık ve katmanlı yapılar oluşur. Bu katmanlı ve karmaşık yapılar birbirleri ile iç içe geçmiş olduğundan zaafiyete açık hale gelebilir ve veri sızıntısı söz konusu olabilir.

# IDOR (Insecure Direct Object Reference)

Yukarıdaki yazıda web uygulamasının veri tabanı ile kullanıcı arasındaki ilişkiden bahsetmiştik. Kullanıcı veri tabanına web uygulamasının izin verdiği ölçüde erişebiliyor. Örnek olarak web sitelerinin bize çeşitli alanlarda(adres ekleme, satış bilgilerini kaydetme gibi) verdiği izinler gösterilebilir. Burada 'web uygulamasının izin verdiği ölçü' aslında kilit noktadır. Güvenli bir uygulamada kullanıcıların web uygulaması üzerindeki yetkileri sınırlı olmalıdır ki hem kullanabilsinler hem de veri sızmasın. 

Örneğin biz kendi adresimizi web uygulamasına kaydediyoruz ama aynı zamanda bir başkasının adresini görmemeliyiz. Eğer web uygulamasında böyle bir zaafiyet var ise ve kendimizin dışında başkasının adresini de görebiliyorsak  burada IDOR kategorisinde bir zaafiyet olduğunu söyleyebiliriz. En basit haliyle IDOR zaafiyeti budur.

* Zaten Türkçeleştirecek olursak da; **güvensiz doğrudan obje referansı** oluyor. Yani; **bir obje referansına(örneğimizde 'adres bilgisi' olarak verdiğimiz) güvensiz bir şekilde doğrudan erişim** olarak düşünebiliriz.

# Uygulamalı Anlatım

* Bu uygulamalı anlatımda zaafiyetli tasarlanmış bir web uygulaması üzerinden anlatım gerçekleştireceğiz.

* Çalışırken **Burp Suite** isimli bir araç kullanacağız.
    * Burp Suite'i bir hacker kumandası olarak düşünebiliriz. Web uygulama güvenliği ile ilgili işlemleri daha hızlı ve verimli şekilde gerçekleştirmek üzere geliştirilmiştir. 

* Örnek web uygulaması:

    **İlk Hesap**

    ![alt text](image.png)

* Burada görüldüğü üzere web uygulamasına giriş yapılmış ve adresler kısmındayız. 

* Aynı uygulama farklı bir tarayıcıdan farklı bir hesapla da yan tarafta açılıyor:

    **İkinci Hesap**

    ![alt text](image-1.png)

* İlk hesaba bir adres ekleniyor **'MDI-1 Adresi'** adında:

    ![alt text](image-2.png)

* İkinci hesaba da bir adres ekleniyor. İsmi **'MDI-2 Adresi'**:

    ![alt text](image-3.png)

* İlk hesaptaki adres web uygulamasından silindiği vakit **Burp Suite** uygulamasında şöyle bir çıktı alınıyor:

    ![alt text](image-4.png)

    * Dikkat ederseniz ilk satırda **'adress/delete/15'** yazan bir kısım var. Burada adress isimli bir kontrolcü olmalı çünkü
    zaten adresler kısmından silme işlemi yapılmıştı. Yanında da **'delete'** yazıyor çünkü silme işlemi yapıldı. Onun yanında da **15** yazıyor. Bu sayı, söz konusu adresin veritabanında ifade edildiği id(kimlik) değeridir. Bir id olduğundan eminiz ama ne tür bir id olduğunu henüz bilmiyoruz. 

* Şimdi yukarıdaki işlemin aynısını ikinci hesaptaki kullanıcı için de yapılıyor. Yani ikinci hesabın adresi uygulama üzerinden siliniyor ve **Burp Suite**'den çıktıya bakılıyor:

    ![alt text](image-5.png)

    * Burada ilk satırda **adress/delete/12** göze çarpıyor. Görüldüğü üzere 15 olan id değeri bu sefer 12. Buradan veri tabanındaki id değerinin kullanıcıların id değeri olduğunu yorumlayabiliriz. Ama bu halen bir varsayımdır.   

    * Şimdi burada 12 yerine 15 yazıp bu kodu tekrar çalıştırırsak idsi 15 olan kullancının adresi silinir mi? Bunu görmek için kodun sonundaki 12 yerine 15 yazılıyor ve forward tuşuna  basılıyor. Sunucudan şu şekil bir cevap geliyor:

        ![alt text](image-6.png)

        * '302 found' yazıyor. Buradan sistemin bir error(hata) üretmediğini söyleyebiliyoruz. Ama bu yanıltıcı bir varsayım olur çünkü '302 found' bilgisi verilse dahi bu sadece bir geri dönüştür. Bu yüzden siteye gidip adress kısmına tekrar giriliyor ve arayüzde şöyle bir mesaj ile karşılaşılıyor:

            ![alt text](image-7.png)

            * **'Authorization failure(yetkilendirme başarısız)'** geri dönüşünü alıyoruz. Ancak bunun yazıyor oluşu bu siteden veri sızdırılamayacağı anlamına gelmiyor. 
    

* Tekrardan 1. kullanıcının adres bilgilerine gittiğimizde ise adresin silinmediğini görebiliyoruz:

    ![alt text](image-8.png)


* Şimdi id olarak çok uzun bi sayı yazılıyor ve sunucunun ne tepki vereceği ölçülüyor:

    ![alt text](image-9.png)


* Bu sefer gelen yanıt **'404 Not Found'** hata mesajı oluyor.  

    ![alt text](image-11.png)


* Buradan uygulamanın davranışından veritabanında nasıl çalıştığına dair izler görebiliriz. Şu ana kadar uygulamanın adres kimliklerinin varlığı üzerinden bir çıktı ürettiğini gördük. 12 ve 15 var olan adresler olduğu için error mesajı üretmedi. Ancak çok absürt bir sayı girdiğimizde öyle bir adres muhtemelen olmadığı için **'404 Not Found'** hatası aldık. Buradan tersine mühendislik yapar arkaplandaki çalışma mantığına yönelik varsayımlarda bulunabiliyoruz. 


* Sonuç olarak bu web uygulamasında **IDOR** zaafiyetinden bahsedemeyiz. Çünkü bir kullanıcının hesabından başka bir kullanıcının adresini silmek başarılamıyor. Eğer silinebilseydi IDOR'dan bahsedebilirdik. 

# Missing Function Level Access

* **'Fonksiyon Seviyesinde Yetki Kontrolü Eksikliğini'** olarak türkçeleştiriliyor. Genelde **IDOR(Veriye Yetkisiz Erişim)** ile karıştırılır.

* Burp Suite'den Intruder(Saldırganı ifade ediyor) kısmına geliniyor ve ilk satırdaki **'invalidmethodname'(bu rastgele bir isim çünkü zaten fonksiyon ismi sorgusu yapacağız yani fonksiyon ismine ne yazacağımız önemli değil)** fonksiyonu işaretleniyor. Buradaki amaç veri tabanında daha ne kadar fonksiyon olup olmadığını öğrenmek:

    ![alt text](image-16.png)

* Sonra **Payloads** kısmına geliniyor ve **Payload Options** kısmından **Add from list...** basılıyor ve oradan **Form Field Names** butonuna tıklanıyor. Burada aslında ***'adress/invalidmethodname'*** sorgusu yapılan veritabanında ***'invalidmethodname'*** fonksiyonu gibi başka hangi fonksiyonlar var o bulunmaya çalışıyor. Bulmak için de isimlere ihtiyaç var ve bu isimler de liste halinde burada sıralanmış. Kolaylık olsun diye onları bir arada aratıyor ve işimizi hızlandırıyor: 

    ![alt text](image-13.png)

* Görüldüğü üzere isim listesi geldi. Devam ediliyor ve **Positions** kısmına gelinip **Start Attack(sağ üstte gözüküyor)** tuşuna basılıyor:

    ![alt text](image-17.png)

* Şimdi ise ekrana sorgusu yapılan bu isimler geldi:

    ![alt text](image-15.png)

* Bu ekranda aşağı indiğimizde **'edit'** fonksiyonunu görüyoruz ve yanında  diğer fonksiyonlar gibi **'404'** yazmıyor **'202'** yazıyor. Demek ki bu fonksiyon site arayüzünde olmasa dahi veritabanında arka planda mevcut ve işler vaziyette. Site arayüzünde editlemeye dair bir seçenek yoktu sadece delete vardı hatırlarsak.

* Şimdi **edit** fonksiyonunu adrese yazılıyor. **'address/edit/15'** Bu yazıldığınd aşağıdaki ekrana ulaşılabiliyor:

    ![alt text](image-18.png)

    * Bu zaafiyette, ***edit*** isimli bir fonksiyonun bana yani kullanıcıya sunulmadığı halde erişebilmem söz konusudur. Bu zaafiyetin adı ise ***'Missing Function Level Access'*** ya da diğer adıyla ***'Fonksiyon Seviyesinde Yetki Kontrolü Eksikliği'***dir.



