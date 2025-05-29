<h1 align="center">Biraz Daha Portswigger Alıştırmaları</h1>


## İkinci Alıştırma

* https://portswigger.net/ sitesine girip hesabımıza giriş yapalım. Ardından https://portswigger.net/web-security/access-control/lab-multi-step-process-with-no-access-control-on-one-step bu alıştırmaya tıklayalım;

![alt text](<WebSec101 0x06_ss/image.png>)

* Alıştırmada bizden istediği **wiener** isimli kullanıcıya admin yetkileri vermek. Anlattığına göre bir admin paneli(yönetici paneli) var ve buradan kullanıcılara admin yetkisi veriliyor veya alınıyor. Bu yetki verme süreci de birden fazla adımdan oluşuyor(Örneğin yetki ver tuşuna basıyoruz ve ardından *onaylıyor musunuz* diye soruyor ve bu sorudan sonra yetki veriliyor.). **Access the Lab** tuşuna basıp ilerleyelim;

![alt text](<WebSec101 0x06_ss/image-1.png>)

* **My account** kısmına girip bize verdiği admin kullanıcı bilgilerini girelim(administrator:admin);

![alt text](<WebSec101 0x06_ss/image-2.png>)

* Sonra **Admin Panel** yazan yere tıklayalım;

![alt text](<WebSec101 0x06_ss/image-3.png>)

* Şöyle bir ekran çıkıyor;

![alt text](<WebSec101 0x06_ss/image-4.png>)

* Görüldüğü üzere **carlos** kullanıcısının yetkisini yükseltebiliyoruz. Ama bizim **weiner** kullanıcısı için bu işlemi yapmamız gerek. Bu yüzden carlosu bu panelden yetkilendirip ilgili **request(sorgu)** bilgisini almaya çalışacağız. **carlos** kullanıcısı seçili kalsın ve **upgrade use** diyelim ve çıkan ekranda bekleyelim;

![alt text](<WebSec101 0x06_ss/image-5.png>)

* Burada henüz bir şeye bakmadan burpsuite uygulamamıza gelip **Proxy** sekmesinden **Intercept** sekmesine gelelim;

![alt text](<WebSec101 0x06_ss/image-6.png>)

* **Intercept(yakalama) off** yazan yere tıklayalım ve açalım. Bunu açtıktan sonra sitede yaptığımız bir sonraki sorgu yakalanacaktır;

![alt text](<WebSec101 0x06_ss/image-7.png>)

* Geri browser'a gelelim ve Yes tuşuna basalım ardından tekrar burpsuite'e gelelim;

![alt text](<WebSec101 0x06_ss/image-8.png>)

* Görüldüğü üzere **request** yakalandı ve yetki verme **requestine** sahibiz artık. Buradan **Request** penceresine sağ tıklayıp Send to Repeater tuşuna basalım ve ardından **Intercept'i** kapatalım.(Kapatmazsak internet adresinde işlem yapamayız, çünkü sorguyu yakalamak için yükleme ekranında bıraktı bizi.);

![alt text](<WebSec101 0x06_ss/image-9.png>)

![alt text](<WebSec101 0x06_ss/image-6.png>)

* Browser'a geri gelip **My Account** kısmından **logout(çıkış)** yapalım ve **wiener** kullanıcısının hesabına girelim(wiener:peter);

![alt text](<WebSec101 0x06_ss/image-13.png>)

* Bu adımdan sonra tekrar BurpSuite'e gelelim ve **Proxy-->Http** History kısmından bu yaptığımız login işleminin sorgusunu bulalım(url kısmında /login isminde gözüküyor ona dikkat ederseniz bulması daha kolay ayrıca loginlerden en aşağıda olanı en yeni yapılan işlemdir.);

![alt text](<WebSec101 0x06_ss/image-20.png>)

* Burada gördüğümüz gibi **Request** kısmında wiener kullanıcısının bilgileri sorgulanmış vaziyette. Burada dikkat edersek sağ tarafta **Response** penceresinde de session=P0yelKTXXrjvoipzOjpOyGRWsES4arPe şeklinde bir cookie'de tanımlanmış bu kullancıya. Bu cookie'yi kopyalayalım ve ardından **Repeater** sekmesine gelelim;

![alt text](<WebSec101 0x06_ss/image-21.png>)

![alt text](<WebSec101 0x06_ss/image-16.png>)

* Burada admin sorgumuz duruyor ve adminin yetkilendirdiği carlos kullanıcısına ait yetki yükseltme sorgusu var. Dikkat edersek yukarıda bir cookie var ve aşağıda username kısmında carlos yazıyor. Düşünelim, eğer biz wiener'in kopyaladığımız cookiesini carlosunkiyle değiştirsek ve carlos yazan yere de wiener yazsak bu yetkilendirme işlemini wienere çevirebilir miyiz? Deneyelim;

![alt text](<WebSec101 0x06_ss/image-17.png>)

* Sol üstteki Send tuşuna basarak devam edelim;

![alt text](<WebSec101 0x06_ss/image-18.png>)

* Response kısmında 302 found çıktı, yani bu sorgu çalıştı ve web sitesine de dönersek alıştırmayı tamamlamış olduğumuzu göreceğiz;

![alt text](<WebSec101 0x06_ss/image-19.png>)

* Burada yaptığımız admin paneline girip oradan adminin yetkilendirme için sahip olduğu sorguyu yakaladık ve o sorgudaki kullanıcı bilgilerini değiştirdik. Dolayısıyla istediğimiz kullanıcıya yetkilendirme yapabildik. 


# İkinci Alıştırma

* https://portswigger.net/web-security/access-control/lab-referer-based-access-control Bu alıştırma sayfasına gelelim ve Access the Lab diyerek devam edelim. Bizden istediği bi önceki alıştırma ile aynı sadece ufak bir farklılık var;


* Admin hesabına giriş yapalım; 

![alt text](<WebSec101 0x06_ss/image-2.png>)

* Giriş yaptıktan sonra admin paneline gelip BurpSuite'den Intercepti açalım ve ardından carlos'a yetki verelim;

![alt text](<WebSec101 0x06_ss/image-22.png>)

* Request kısmında yakalanan sorguyu görüyoruz. Bunu Repeater'a yollayalım ve intercepti kapatıp admin hesabından çıkış yapalım;

* Şimdi wiener hesabına girelim;

![alt text](<WebSec101 0x06_ss/image-23.png>)

* Girdikten sonra BurpSuite'den bu girişin sorgusunu yakalayalım;

![alt text](<WebSec101 0x06_ss/image-24.png>)

* Response kısmından cookie bilgisini kopyalayalım ve ardından Repeater kısmına gelelim;

![alt text](<WebSec101 0x06_ss/image-25.png>)

* Burada cookie kısmını kopyaladığımız cookie ile değiştirelim ve Send tuşuna basalım;

![alt text](<WebSec101 0x06_ss/image-26.png>)

* 302 found verdi yani sorgu başarılı;

* Şimdi aynı ekranda carlos yazan yere weiner yazalım;

![alt text](<WebSec101 0x06_ss/image-27.png>)

* Ardından Send tuşuna basalım ve alıştırmayı tamamlayalım;

![alt text](<WebSec101 0x06_ss/image-33.png>)

![alt text](<WebSec101 0x06_ss/image-34.png>)

* Burdada yine bi önceki alıştırmayla aynı metodu kullandık. Sorguyu admin paneli üzerinden başka kullancıya verdiğimiz yetkiyle yakaladık ve bu sorgudaki kullanıcı bilgilerini(cookie ve kullanıcı adı) istediğimiz kullanıcı için değiştirdik ve sonuçta alıştırmayı tamamlamış olduk.









