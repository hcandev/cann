<h1 align="center">Directory Traversal</h1>


Directory Traversal(Path Traversal olarak da bilinir.) Dizin gezintisi olarak düşünülebilir. Saldırgan bu tip bir zafiyette bir web uygulamasındaki izin verilen klasör yapısının dışındaki dosya ve dizinlere ulaşabilir. 

# Alıştırma #1

* https://portswigger.net/web-security/file-path-traversal/lab-simple

* Bu lab'da path traversal zafiyeti varmış. Olduğu yer ise ürün resimlerinin gösterildiği kısımmış. Bizden istediği **/etc/passwd** dizinindeki bilgilere erişmemiz. 

* Lab'a girdikten sonra intercept'i açıp bir ürün için **View Details** tuşuna basalım ve forward dedikten sonra requesti yakalayalım sonra da repeater'a yollayalım;

![alt text](<WebSec101 0x18_ss/image.png>)

* Dikkat edersek get talebinde **GET /image?filename=49.jpg** şeklinde bir adres var ve en sonunda filename var. 49.jpg yazan yeri değiştirip;

```
../../../../../../../../../../../../../../../../../../../../../../etc/passwd
```

* Şeklinde bir adres yazalım. **../** kısmının çok olma sebebi aslında ne kadar fazla klasör olduğundan emin olmamamız ile ilişkili. Bu yüzden biraz çok yazdık ve en sona da bizden istediği klasörleri koyduk;

![alt text](<WebSec101 0x18_ss/image-1.png>)

![alt text](<WebSec101 0x18_ss/image-2.png>)

* Send dediğimizde response döndü ve lab çözüldü. 

# Alıştırma #2 

* https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass

* Bir önceki alıştırmadaki aynı zafiyet aynı kısımdaymış. Dolayısıyla bir önceki çözümümüz engellenmiş ve bizden farklı bir şekilde **/etc/passwd** adresine ulaşmamızı istiyor. 

* Bir önceki alıştırma ile aynı adımları izledik ve;

![alt text](<WebSec101 0x18_ss/image-3.png>)

* 'No such a file' şeklinde bir response aldık. 

* Bazı durumlarda dosyalar doğrudan okunabiliyor. Dolayısıyla üst dizinlere çıkmadan zaten bulunulan dizinde okuma işlemi yapılabilir. Yani başa eklediğimiz **../../** kısımlarını kaldırısak;

![alt text](<WebSec101 0x18_ss/image-4.png>)

* Response'da istenilen bilgiler gelmiş oluyor. Sonra da lab çözülüyor zaten;

![alt text](<WebSec101 0x18_ss/image-5.png>)

# Alıştırma #3

* https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively

* Yine aynı zafiyet aynı kısımda. Yine aynı verilere erişmemiz isteniyor fakat farklı yol denememiz gerekecek. 

* Aynı şekilde requesti yakalayıp repeater'a yollayalım;

![alt text](<WebSec101 0x18_ss/image-6.png>)

* Şimdi jpg adresini şöyle güncelleyip send diyelim;

![alt text](<WebSec101 0x18_ss/image-7.png>)

* Evet response **200 OK** olarak döndü ve bu kod çalıştı. Buradan aslında **directory traversal zafiyetini** tespit etmiş oluyoruz çünkü içerideki sistem **../** yazdığımız yeri silip direkt dosyayı getiriyor. Peki biz bundan iki tane yazsak?;

```
....//
```

* İşte bu şekilde **../** kısmını sildiğinde bile geri yine **../** kısmı kalacak. Şimdi adresimize böyle ulaşmayı deneyelim;

![alt text](<WebSec101 0x18_ss/image-8.png>)

* Labı çözmüş olduk;

![alt text](<WebSec101 0x18_ss/image-9.png>)


# Alıştırma #4 

* https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode

* Aynı şeyler fakat bu sefer sistemin **dizin** kısmında decode yaptığı söyleniyor. Dolayısıyla buna göre bir yöntem izlemeliyiz.

* Aynı adımları izleyip requesti aldık ve hemen **../** işaretini jpg dosyasından önce koyup send dedik ve;

![alt text](<WebSec101 0x18_ss/image-10.png>)

* Path traversal zafiyetinin varlığını doğruladık.

* Evet şimdi yapacağımız arka planda yapılan decode işlemine göre olmalı. Eğer arkaplandaki sistem encoding yapıyorsa biz de kodumuzu iki kez şifreleriz. Yani;

```
/../../../etc/passwd
```

* Şeklindeki kodu iki kez encode'larız.

* Kodumuzu normal yazalım ve sağ tıklayıp şifreleme kısmına gelip tıklayalım;

![alt text](<WebSec101 0x18_ss/image-11.png>)

* Şimdi aynısını şifrelenmiş kodu seçip bir daha yapalım ve send tuşuna basalım; 

![alt text](<WebSec101 0x18_ss/image-12.png>)

![alt text](<WebSec101 0x18_ss/image-13.png>)

* Bilgiler geldi ve labı çözdük.

# Alıştırma #5

* https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path

* Yine aynı zafiyet. Yine farklı bir çözüm gerekiyor. Fakat burada bir bilgi daha var ve o da uygulamanın tam dosya dizinini requestte parametre olarak gösterdiğini söylüyor. Yani bu dosya dizini olmadan request parametresi çalışmıyor.

* Siteye girip bir ürün resmini açıp o requesti yakalayalım;

![alt text](<WebSec101 0x18_ss/image-14.png>)

* Bize bahsettiği tam dosya dizini **/var/www/images/23.jpg** olarak verilmiş. Şimdi buraya bizden istediği adresi yazıp deneme yapalım. Yani **/var/www/images/etc/passwd** 

![alt text](<WebSec101 0x18_ss/image-15.png>)

* No such file dedi. Şimdi de dosya dizini ekleyerek yazalım;

![alt text](<WebSec101 0x18_ss/image-16.png>)

* Evet bu sefer kabul etti ve lab çözüldü. 

# Alıştırma #6

* https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass

* Aynı kısımda aynı zafiyet var yine. Uygulama bu sefer verilen dosya ismini beklenen dosya uzantısı ile doğruluyormuş. 

* Yine aynı requesti alıp repeater'a yollayalım;

![alt text](<WebSec101 0x18_ss/image-17.png>)

* Şimdi burada uzantının aynı olması gerektiği koşulu var dolayısıyla bizden gitmemizi istediği yere giderken bunu bypass etmemiz gerekecek. Bunun da yolu **%00** komutudur. Bu komut çoğu modern dilde çalışmaz ancak eski php/java kurulumlarında mevcuttur. filename'den sonra;

```
../../../../../../../../../../../../../../../../etc/passwd%00.jpg
``` 

* Komutunu ekliyoruz. Yine fazladan klasör dizini ekleyerek başladık **(../)**. Sonra bizden istediği klasörü yazdık**(/etc/passwd)** . En sonda ise **%00.jpg** yazarak bir jpg koşulunu sağladık ve bundan önce gelen dosya dizinini de saydırmış olduk. Dolayısıyla bu request çalıştı;

![alt text](<WebSec101 0x18_ss/image-18.png>)
 
* Bu lab da tamamlanmış oldu. 

# KAYNAK


[Web Security 0x18 | Directory Traversal ../../etc/passwd](https://www.youtube.com/watch?v=wNMyiqixL1g&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=22)

