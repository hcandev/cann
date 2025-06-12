<h1 align="center">Bir Hacker’ın Gözünden Modern Web</h1>

Modern web'in nasıl çalıştığına dair temel bir anlatımla başlayalım.

İki bilgisayar ve bir modem düşünelim: 

![alt text](<WebSec101 0x04_ss/image.png>)

İlk başta bu iki bilgisayar da modemden ip adresi isteyecektir. Aksi takdirde web'e bağlanamaz:

![alt text](<WebSec101 0x04_ss/image-1.png>)

Görüldüğü üzere iki bilgisayara da ip adresi tanımlandı. Bu işlem DHCP sayesinde olur.

## DHCP(Dynamic Host Configuration Protocol)

Dinamik Yapılandırma Protokolü olarak düşünebiliriz. Bu protokol, ağda bulunan her bir bilgisayarın ağ bağlantısı ayarlarının otomatik olarak atanmasını sağlar.

 ![alt text](<WebSec101 0x04_ss/image-2.png>)


Görsele geri dönelim.

![alt text](<WebSec101 0x04_ss/image-1.png>)

Şimdi yukarıda iki bilgisayar ve bir modem görüyoruz. Bu bilgisayarlar birbirleri ile ya da modem ile iletişim kurmadan önce ikinci layerda(OSI katmanlarını hatırlayalım. Burada 2. katman Data-Link'e denk gelir.) gerçekleşen bir durum söz konusudur. Bu söz konusu durumun adı ARP olarak kısaltılır.

## ARP(Address Resolution Protocol)

Adres çözümleme protokolü olarak adlandırılır. Adından da anlaşılacağı üzere bir tanımlama yapar. Verdiğimiz örnekte PC-1'in PC-2'yi tanımlamasında rol oynamaktadır. Bu işlem şöyle gerçekleşir; PC-1'in PC-2'yi tanımak istediği noktada PC-2'nin MAC adresinin bilinmesi gerekmektedir. Bunu aldıktan sonra PC-1, PC-2'nin MAC adresini **ARP tablosuna** kaydeder. Bu sayede artık PC-2 PC-1 için bilinir olur. ARP tablosunu da PC-1'in işletim sistemi yönetir. 

![alt text](<WebSec101 0x04_ss/image-3.png>)

Görüldüğü üzere artık bilgisayarlarda birbirlerinin ip adresleri tanımlı ve bu adresler kendi ARP tablolarında kayıtlı şekilde duruyor. Aynı zamanda modemde de iki bilgisayarın ipleri tanımlı haldedir. 

## ARP Poisoning

ARP zehirlenmesi olarak düşünebiliriz. Aslında bu olguda ARP gerçekten de zehirleniyor. Çünkü ARP normalde farklı adresleri çözümlüyor ve tanımlıyordu. Bu sayede yerel ağ içerisindeki farklı cihazlar birbirinden haberdar oluyor ve birbirlerini tanımlıyordu. Ancak bir saldırganın yerel ağda bu kimlikleri değiştirip veri akışını yönlendirebilmesi mümkün oluyor. Örneğimiz üzerinden anlatalım: 

![alt text](<WebSec101 0x04_ss/image-4.png>)

Burada iki bilgisayarda birbirini tanıyor haldeydi. Dolayısıyla modem de onları tanıyor. Ancak saldırgan bu ipleri değiştirip PC-2'nin kimliği olarak PC-1'inkini gösterebilir: 

![alt text](<WebSec101 0x04_ss/image-5.png>)

Görüldüğü üzere modem'de artık iki bilgisayarın ipsi de aynı yani PC-1'inkine eşitlenmiş. Bu durumda modemin kurduğu veri aktarımında PC-2 dışarıda kalacaktır. Dolayısıyla PC-2, x.com diye bir siteye girdiğinde bir veri akışı oluşacaktır. Bu akış PC-1 tarafından izlenebilir hale gelecektir. Bu durum basitçe ARP poisoning olarak adlandırılır.


Görselimiz üzerinden devam edelim:

![alt text](<WebSec101 0x04_ss/image-6.png>)

Anlattığımız görselde yerel ağdaki durumları konuşmuştuk. Peki internete nasıl bağlanıyoruz. Bir siteye girerken neler oluyor?

## Gateway(Geçiş Kapısı), Subnet(Alt ağ), DNS(Domain Name System)

* Gateway: İki farklı ağı birbirine bağlar ve verinin bu iki ağ arasında nasıl aktığını kontrol eder. Yerel ağımızı(LAN) internet'e bağlar. Örneğin, Evde(Yerel ağ) olduğunuzu düşünün ve sosyalleşmek(internet) istiyorsunuz. Sosyalleşmek için dışarı çıkmanız gerek. Dışarı çıkmanız için evin kapısından(gateway) geçmeniz gerek.

* Subnet: Subnet(subnetwork'ün kısaltması) büyük bir bilgisayar ağının küçük bir kısmını oluşturur. Subnet büyük ağları organize etmeyi, korumayı ve yönetmeyi daha verimli hale getirir. Örneğin bir şirket binası içerisindesiniz. Dolayısıyla birden fazla kat var. Bu katları subnet olarak düşünün. Her katta da şirketin farklı departmanların olduğunu düşünün. İlk katta resepsiyon, ikinci katta IT departmanı kısmı üçüncü katta reklam departmanı vs. Her kat(subnet) ayrı departmanlara bölündüğü için bu departmanlar kat içerisindeki iletişimi daha verimli yapacaklardır. Yani IT'ler kendilerine ait kata sahip olduğu için oradaki veri akışı daha temiz ve daha az dikkat dağıtıcı olacaktır. Diğer departmanlarla iletişim kurmak istediklerinde ise merdiven ya da asansörü kullanarak(bu durumda merdiven ve asansör modem görevini görür) o katlara gideceklerdir. İşte ağ'da da subnet'in görevi budur. Konuşmak istediğim kişiyle aynı ağda mıyım? sorusunu sorar. Ya da örneğimizden hareketle konuşmak istediğim kişiyle aynı katta mıyım? sorusu sorulur. Ağ içinde ağ olarak düşünebiliriz.     

* DNS: İnternetin telefon rehberi olarak düşünebiliriz. Website isimlerini bilgisayarların birbirlerini bulduğu ip adreslerine çevirir. Örneğin telefonumdan Ahmet'i arayacağım ve ismine tıklayıp arayabiliyorum. İnternete de **'www.facebook.com'** yazdığımda aslında benzer şey oluyor. Bilgisayarım DNS sunucusuna 'www.facebook.com'un adresi ne diye soruyor ve ardından DNS sunucusu ip adresini bulup bilgisayarımı bağlıyor ve ben de facebook'a girmiş oluyorum. 

![alt text](<WebSec101 0x04_ss/image-7.png>)

Görüldüğü üzere DHCP'nin bilgisayara tanımladığı diğer ağ bağlantısı ayarlarını da ekledik. DNS, Gateway ve Subnet. 


Şimdi x.com isimli bir siteye gitmek istediğimizi düşünelim:

![alt text](<WebSec101 0x04_ss/image-8.png>)

Bilgisayarımızın bu durum için internete bağlanması gerekecek. x.com'a bağlanmak istediğimiz için ve daha önce bu siteye girmediğimizi düşünürsek DNS'e x.com'un ip adresi bilgisayar tarafından sorulacak. Yani öncelikle DNS ile konuşulur.

## Bir İnternete Adresine Bağlanmak

x.com isimli bir websiteye bağlanmak istediğimizi varsayalım. Bilgisayarımız DNS'e(8.8.8.8- Bu DNS'in adı **Resolver** diye de geçer.) x.com'un ip adresini sorar. Eğer bu DNS ip adresini bilmiyorsa **Route DNS'e(Kök DNS)** gider ve ona sorar. **Route DNS**  **Resolver'a** ip'yi araması için başka bir adres verir. Bu sefer **Resolver**, **TLD(Top Level Domain)** DNS'ine gider ve x.com'un ip'sini burada arar. **TLD** de **Resolver'ı** kayıtları tutan bir adrese yönlendirir. Bu kayıt tutan adresin adı da **Authoritative DNS(Yetkili DNS)**'tir.**Resolver** sonunda burada x.com'un adresini bulur. Bu adres de bilgisayarımıza getirilir ve sonunda siteye girilir.   

## Riskler

* x.com'a bağlanırken çeşitli riskler söz konusu olabilir;

* İlk girildiğinde DNS'e bu adres kaydedilir. Ancak bir saldırganın DNS'e(8.8.8.8) sızdığı bir durumda bu adres değiştirilebilir ve bilgisayar tekrar x.com'a girmek istediğinde kendini saldırganın belirlediği bir ip adresinde bulabilir. 

* TLD kısmına sızıldığı takdirde tüm '.com' ile biten adresler istenilen adresler ile değiştirilebilir.

* Authoritative DNS(Yetkili DNS), ele geçirilirse ciddi anlamda veri sızıntısı yaşanabilir. Bu durumda tüm kayıtlar ve e postalar ele geçirilebilir hale gelir.


***'DNS internetin en zayıf halkasıdır' söylemi buradan gelmektedir.*** 



Bilgisayarımızın, ipsinin 1337 olduğunu düşündüğümüz bir sunucuya bağlandığını düşünelim. Burada aslında yukarıdaki adımları tamamlayıp bağlanıyoruz o yüzden hızlı geçtik. Şimdi bu bağlantı yapılırken bilgisayar protokol olarak TCP'yi kullanır. TCP paketinde;

Source Ip(Kaynak ip):bilgisayarın ipsidir. Örneğimizde 10.0.0.5.
Destination Ip(Hedef ip): 1337 olarak belirlemistik(rastgele bir değer)
Source Port(Kaynak port): Bu sayı işletim sistemi tarafından rastgele verilir.64357 diyelim.
Destination Port(Hedef port):80



## NAT(Network Address Translation)

TCP, bilgisayardan hedef sunucuya giderken Gateway'den geçecektir. Burada Gateway'den çıktıktan sonra bilgisayarın ipsi(10.0.0.5) yerine başka bir ip yazılır. Bu işleme NAT denmektedir. **Ağ Adresi Değişimi** olarak düşünebiliriz. İnternette gezinirken router'ın(modem) ürettiği farklı bir ip adresi bilgisayar ipsi olarak gözükür. Aynı zamanda örnekte gördüğümüz PC-1 ve PC-2'nin internette gezinirkenki ipleri de aynı olacaktır bu durumda. Çünkü router ikisine de aynı ip'yi atamaktadır. 

## Sunucuya Bağlanırken

Tekrardan 1337 ipli sunucuya bağlandığımız konuya dönelim. En son bilgisayar tcp'yi kullanarak gateway'den internete bağlanmıştı ve sunucuya ulaşmıştı. Şimdi burada bilgisayar sunucuya ilk defa gittiği için bir bağlantı oluşturacaktır. TCP kullanarak bağlantı oluşturan bilgisayar aslında **TCP SYN** paketini kullanır. Buradaki bahsi geçen **'SYN'** ***'synchronize'*** yani ***'senkronize'*** nin kısaltmasıdır ve sunucuya bağlantı isteğini gönderir. Sunucudan bir geri dönüş olacaktır ve bu geri dönüşün adı da **'SYN-ACK'** olarak adlandırılır. Buradaki **'ACK'** ***'acknowledge'***ın kısaltmasıdır yani ***'onaylamak'*** ya da ***'kabul etmek'***. Yani yanıt olarak sunucu ***'senkronize olmayı kabul ediyorum'*** diyor. Bundan sonra da kullanıcıdan yani bilgisayardan **'ACK'** tipinde bir cevap gelir ve bi önceki **'ACK'** ile aynı anlama gelir ve ***'son onay'*** olarak sunucuya cevap verir. Bu sürecin adı da ***'TCP 3 Way Handshake'*** olarak adlandırılır. Bi önceki derste bahsetmiştik. Bu süreç kimin kim olduğunun bilinmesi ile ilgilidir. Bir çeşit tanışma faslı. Bu sayede ip bazlı zafiyetlerin önüne geçiliyor. 

TCP 3 Way Handshake'den sonra artık http'ye çıkma vakti. Http'de sadece request-response ilişkisi olduğundan bi önceki ders bahsetmiştik. Yani kullanıcıdan bir **request(sorgu, istek)** gidiyor sunucuya ve sunucudan da buna cevap olarak **response** dönüyor. Http tarafında bu yaşanırken aynı zamanda çok fazla sayıda **TCP** paketi gidip gelmektedir. Bu süreçler **Wireshark** isimli uygulamada tamamen izlenebilir.

## Sunucuya Bağlanırken FireWall

Firewall aslında biz sunucuya bağlanmadan üzerinden geçtiğimiz bir bölgedir. Bu aslında Network Management(Ağ yönetimi) işi yapmaktadır. Yani kimin söz konusu sunucuya girip giremeyeceğini belirler. Bizim örneğimizde 1337 ipli sunucuya girip giremeyeceğimizi kontrol ediyor. Bilgisayardaki antivirüs gibi bir şey. Bahsettiğimiz TCP 3 Way Handshake aslında Firewall ile yaşanmaktadır. Bunun temel nedeni sunucunun gücünü Firewall ile arttırmasıdır. Çeşitli saldırılar sunucuya doğrudan etki ettiğinde sunucunun gücünü tüketebilir. Ancak sunucudan önce Firewall ile etkileşime geçildiğinde aslında bunun önüne geçilmiş olur. Böylelikle sunucu hem daha az hafızaya sahip olur hem de korunmuş olur.  

## Virtual Hosting

Vhost kısaltmasıyla da kullanılır. Birden fazla web sitesini tek bir sunucuda(ya da bir ip adresinde) barındırır. Bir alışveriş merkezini düşünelim. İçerisinde çok sayıda farklı mağaza var. Ancak bütün bu mağazalar aynı bina içinde. **Burada alışveriş merkezi Vhost**, **mağazalar ise çeşitli websiteleridir**. Bu özelliğiyle aslında NAT'a benzer. NAT da yerel ağdaki bilgisayarları internete bağlarken bu bilgisayarlara aynı ip adresini tanımlıyordu.

## Reverse Proxy

Sunucunun gücünün yetmediği durumları hayal edelim. Örneğin bir alışveriş sitesinin aşırı indirimlerden dolayı çöktüğünü veya sınav zamanı ÖSYM'nin web sitesinin çöktüğünü düşünelim. Bu gibi durumlarda **Reverse Proxy** devreye girer. Ama ondan önce sunucu örneğimize geri gidelim. Biz 1337 sunucusuna bağlanırken firewalldan geçip öyle bağlanıyorduk;

![alt text](<WebSec101 0x04_ss/image-9.png>)

şimdi burada sunucunun yetmediği senaryoların önüne geçmek için şöyle bir yöntem izleniyor;

![alt text](<WebSec101 0x04_ss/image-10.png>)

görüldüğü üzere 1337 sunucusu 2 kez daha oluşturuldu ve veritabanları da sunuculardan ayrılmış halde. Amaç sunucu üzerindeki olası yükü azaltmak olduğundan sunucu parçalara bölündü ve şimdi gelen ip'lerin sunuculardan hangisine yönlendirileceği kaldı. İşte burada Reverse Proxy ya da diğer adıyla Load Balancer(yükleme dengeleyici) devreye girer;

![alt text](<WebSec101 0x04_ss/image-11.png>)

Reverse Proxy burada gelen ip'leri uygunluk durumuna göre müsait olan sunucuya göndermektedir. Basitçe bu işi yapar. 

## Çıkması muhtemel sorunlar

* RP(reverse proxy), ip'leri belirli sunuculara yönlendirmektedir. Belirli sunuculara giren bu ipler burada session bilgisine sahip olacaklardır ve veritabanına session bilgisine sahip olduğu ip adresinden kaydedilecektir. Dolayısıyla RP aynı ip adresini diğer sunuculara gönderdiğinde orada ilgili ip'nin session bilgisi olmadığı için o ip için veri kaybı olacaktır. 

![alt text](<WebSec101 0x04_ss/image-12.png>)

Örneğin RP bizi burada ilk sunucuya aktardı ve biz etkileşime girip siteye giriş yaptık. Dolayısıyla bilgimiz 1.sunucuda bir oturum açılması sonucu veritabanına kaydedildi. Ama bir sonraki seferde RP bizi 2. veya 3.sunucuya yollarsa buradaki sunucularda sessionımız(oturumumuz) olmadığı için verilerimiz burada olmayacaktır. Bunun önüne Reverse Proxy içerisine bir cookie atanarak geçiliyor. Buradaki cookie aslında bizim bilgilerimizin hangi sunucuya gittiğine dair veri taşır ve sadece RP içerisinde bulunur. Yani RP bizim 1.sunucuya giriş yaptığımızı bilir ve bir sonraki seferde de bizi o sunucuya yollar. Çünkü ilkinde oraya gitmiştik ve bilgilerimiz orada.


## SQL Proxy

Sunucudaki veritabanlarının yetmediği durumlar söz konusu olabilir. Burada SQL Proxy devreye girer. Bu Proxy servisi de Reverse Proxy'e benzer. Sunucudan giden SQL sorguları bu SQL Proxy servisine soruluyor o da bu sorguların uygunluğuna göre database'lere sorgu yapıyor. Yani burada da verileri hafifletme söz konusudur; 

![alt text](<WebSec101 0x04_ss/image-13.png>)





## Gelip Geçici Session

Session bilgisinin farklı yerlerde tutulmasına ilişkin bilgileri geçen ders vermiştik. Session'ın sunucu veya Databasede tutulmasının dezavantajlarından da bahsetmiştik. Bu durumda Session için de aslında mikroservis yapılarına başvurulabiliyor. Geçtiğimiz ders Redis'ten bahsetmiştik. Basitçe özetlemek gerekirse session bilgisi sunucuda veya databasede tutulmuyor. Bunların yerine session servislerinde tutuluyor. Hem daha hızlı hem de daha verimli;

![alt text](<WebSec101 0x04_ss/image-14.png>)

Redisin çöktüğü durumları da düşünmek gerek tabii. Bu durumda redis gibi mikroservislerin backup'ını(yedeğini) almak akıllıca olacaktır. 

**Reverse Proxy**, **SQL Proxy**, **Redis** gibi yapılara Mikroservis Yapılar denir.(microservices architecture)


## Statik Dosyalar ve CDN(Content Delivery Network)

Web sitelerinde statik dosyalar bulunabilir. Bunlara örnek olarak bir websitesindeki resim dosyasını verebiliriz. Bu dosya statiktir çünkü değişmez ve farklı sunucularda da olur;

![alt text](<WebSec101 0x04_ss/image-15.png>)

Buradaki gördüğümüz resim statiktir. Şimdi örnek resmimize geri gelelim;

![alt text](<WebSec101 0x04_ss/image-16.png>)

Görüldüğü üzere 3 sunucu var. Burada bu resmin 3 sunucuda da olması gerekir doğal olarak. İşte bunu sağlayan da bir mikroservis var ve onun adı da **CDN**dir. Yani **İçerik Dağıtım Ağı**;

![alt text](<WebSec101 0x04_ss/image-17.png>)

## Atak Vektörleri(Attack Vectors)

Şu ana kadar anlattığımız her şey hacklenebilir. Arp Poisoning, internette dolaşmayı sağlayan routerlardan birinin ele geçirilmesi veya sunucuda Reverse Proxy'nin ele geçirilmesi. Bunlar bir uygulamanın sistemine girip o uygulamayı tehlike altına sokmak için çeşitli yollardır. Bu yollara  **Atak Vektörleri** denir.


# KAYNAK

[Bir Hacker’ın Gözünden Modern Web Nasıl Çalışır ? ](https://www.youtube.com/watch?v=3AgDSw0I89A&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=5)
