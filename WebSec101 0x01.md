<h1 align="center">SQL Injection</h1>

# SQL Ne demek
* Bir veritabanında kullanılan proglamlama dilidir. Verileri yönetmek ve tasarlamak için kullanılır. 

# SQL Injection Ne Demek?
* Saldırganın web uygulamalarındaki SQL sorgularını kendi lehine kullanabilmesidir. Adı üstünde 'SQL Enjeksiyonu'.
    ![alt text](image-1.png)

# Bazı Temel SQL sorguları.
* Başlarken SQL'in temelini anlamak gerekiyor. Dilin temelinde nasıl çalıştığını anlamak SQL Injection'ı anlamaktır. Dolayısıyla burası önemli.
* Buradaki ana hedef bazı temel SQL mantığını anlamaktır.
    * Kullanılacak bazı ifadeler için ön bilgi:
        * 'SELECT' ifadesi veritabanından veri sorgulamak için kullanılır.
        * 'string' kavramı programlama dillerinde karakterlerden oluşan dizileri ifade eder. Genellikle karakterler tırnak içinde belirtilir. 'araba', '2121231' gibi. Farklı programlama dillerinde bunları belirtme biçimi değişebilir.
        * 'INT' ifadesi integer (tam sayı) ifade eder.


* Aşağıda SQL kodunun bazı sorgularını kullanacağız ve temel prensiplerin bir kısmını anlamaya çalışacağız.

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

* Şimdi bir web uygulamasında SQL Injection olup olmadığını anlamayı ve SQL Injection varsa neler yapılabileceğini göreceğiz.
* http://testphp.vulnweb.com/categories.php bu web uygulaması alıştırma yapılabilmesi için test olarak geliştirilmiştir. Çalışırken bu kullanılacak.

* Girdikten sonra 'Browse Categories' kısmına gelip 'Posters' sekmesine tıklıyoruz ve şöyle bir ekran çıkıyor:

    ![alt text](image-4.png)

* Bu sekmenin aynısından bir tane daha açıyoruz ve arama kısmındaki 'http://testphp.vulnweb.com/listproducts.php?cat=1' sorgusunun sonuna 1 yerine 2 yazıyoruz ve bu ekran çıkıyor:

    ![alt text](image-5.png)