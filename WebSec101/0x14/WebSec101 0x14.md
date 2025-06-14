<h1 align="center">Business Logic Vulnerabilities</h1>

Bir uygulamadaki özelliklerin mantığıyla ilgili hatalardan oluşan zafiyetlere denri. Buradaki esas problem SQL injection veya XSS gibi teknik konular değildir. Buradaki zafiyetler geliştiricilerin uygulamadaki bilinçli koyulan özelliklerin nasıl suistimal edileceğini öngörememesi ile ilgilidir. 



# Alıştırma #1

* https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-excessive-trust-in-client-side-controls bu alıştırmadan başlıyoruz.

* Uygulamanın satın alma tarafında bir mantık hatası bulunuyormuş. Bizim bunu kullanarak "Lightweight l33t leather jacket" isimli ürünü satın alıp labı çözmemizi istiyor. 

* Öncelikle uygulamayı tanıyalım;

![alt text](<WebSec101 0x14_ss/image.png>)

* Önce giriş yapalım ve bizden istediği ceketi sepete ekleyelim ve place order demeye çalışalım;

![alt text](<WebSec101 0x14_ss/image-1.png>)

* Evet kabul etmedi tahmin ettiğimiz gibi. Şimdi sepetten kaldıralım;

![alt text](<WebSec101 0x14_ss/image-2.png>)

* Fark ediyoruz ki hesabımızda 100$ var. Belki fiyat istenilen cekete yetmiyor peki ya ceket hiç bir zaman o kadar pahalı olmasaydı?

* Burp suiteden intercepti açalım ve tekrardan bizden istediği ceketi sepete eklemek için **add to cart** diyelim;

![alt text](<WebSec101 0x14_ss/image-3.png>)

* Şimdi requeste bakalım;

![alt text](<WebSec101 0x14_ss/image-4.png>)

* Evet görüyoruz ki aşağıda fiyatı belli. Biz bunu değiştirmeyi deneylim. 133700 yazan yere 1000 yazalım ve forward tuşuna sitedeki siparişimiz geçene kadar basalım;

![alt text](<WebSec101 0x14_ss/image-5.png>)

* Evet sağ üstte sipariş geçmiş. Şimdi siparişlerim sayfasına basalım ve burp suite üzerinden forward tuşuna siparişler sayfasına gelene kadar basalım;

![alt text](<WebSec101 0x14_ss/image-6.png>)

* Ceketin fiyatı 10 dolar oldu. Şimdi Place Order tuşuna basıp yine forward tuşuna basalım sipariş onaylanana kadar;

![alt text](<WebSec101 0x14_ss/image-7.png>)

* Satın alma başarılı.

* Buradaki mantık hatası requestte fiyat bilgisinin verilmiş olmasıyla ilgilidir. Buradaki fiyat bilgisinin requestte gözüküyor olması gereksizdir ve saldırgan tarafından kolayca kullanılabilir.

# Alıştırma #2

* https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-high-level 

* Geçenki alıştırmayla aynı.

* Geçen yaptıklarımızı deneyelim;

![alt text](<WebSec101 0x14_ss/image-8.png>)

* Bu sefer fiyat bilgisi bize gözükmüyor. Başka bir şey denemeliyiz. Şimdi intercepti kapatıp bu kodu repeater'a yollayalım;

![alt text](<WebSec101 0x14_ss/image-9.png>)

* Burada quantity yerine -1 yazıp send tuşuna basalım ve sayfayı yenileyelim;

![alt text](<WebSec101 0x14_ss/image-10.png>)

* Place order diyelim;

![alt text](<WebSec101 0x14_ss/image-11.png>)

* Şu anda fiyatı 0 olduğu için kabul etmiyor. Ama eğer biz 100 doların altında bir ürün daha sepete eklersek o zaman satın alabiliriz. Tabii bunu tam tersi yapacağız;

![alt text](<WebSec101 0x14_ss/image-18.png>)

* Yukarıda product idye 3 yazalım ve send diyelim;

![alt text](<WebSec101 0x14_ss/image-19.png>)

* Şimdi tekrar 1 yazıp yine send diyelim ve sepeti yenileyelim;

![alt text](<WebSec101 0x14_ss/image-20.png>)

* Şimdi product id'ye 3 quantity'e de -1 yazalım ve sepeti iki kez yenileyelim;

* Sonra aşağıdaki gibi ampul sayısını -13'e kadar getirelim ve place order diyelim;

![alt text](<WebSec101 0x14_ss/image-21.png>)

![alt text](<WebSec101 0x14_ss/image-22.png>)

* Lab tamamlanmış oldu. 

# Alıştırma #3 

* https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-low-level bu alıştırmadan devam edelim.

* Aynı alıştırma fakat bu sefer kullandığımız iki yöntem de engellendi. Dolayısıyla başka bir yol bulmalıyız.

* Biz yine bizden istenen ceketi sepete ekleyelim ve bu requesti repeater'a gönderelim;

![alt text](<WebSec101 0x14_ss/image-23.png>)

![alt text](<WebSec101 0x14_ss/image-24.png>)

* Repeaterdaki koda sağ tıklayıp Send to Intruder diyelim ve Intruder sekmesine geçelim; 

![alt text](<WebSec101 0x14_ss/image-25.png>)

* Quantity kısmını 99 yapalım ve hemen yanına **&asd=rastgele** yazalım ve sonra **rastgele** yazan yeri seçip yukarıdan **add** diyelim; 

![alt text](<WebSec101 0x14_ss/image-26.png>)

![alt text](<WebSec101 0x14_ss/image-27.png>)

* Payload type yazan yeri **Numbers** seçelim;

![alt text](<WebSec101 0x14_ss/image-28.png>)

* Aşağıdaki ayarları girelim; 

![alt text](<WebSec101 0x14_ss/image-29.png>)

* **Start attack** diyelim; 

* Böyle bir ekran gelecek ve devamlı request yollayacak;

![alt text](<WebSec101 0x14_ss/image-30.png>)

* Sepet kısmına gelip her yenile dediğimizde değer değişecek;

![alt text](<WebSec101 0x14_ss/image-31.png>)

![alt text](<WebSec101 0x14_ss/image-32.png>)

* Şimdi buradaki tutar belirli bir limite geldiğinde negatif değer gösterecektir. Buna **integer overflow** denir. Ancak burpsuite'in bu versiyonunda bu requestler yavaş gidiyor dolayısıyla [ilker yılmazdan](https://github.com/mdisec/mdisec-twitch-yayinlari/blob/master/docs/Web-Security-Notes-Pdf/notlar/0x14%20%7C%20Business%20Logic%20Vulnerabilities%20(Back%20back%20back%20tick%20%5E%5E)/0x14.md) aldığım python kodunu kullanacağım;

* Yaptığı şey az önce burpsuite de yapmaya çalıştığımızla aynı sadece daha hızlı yapıyor. Buradaki bize verilen header, url ve cookie bilgilerini intruder'a yolladığımız koddakiler ile değişelim ve çalıştıralım;

![alt text](<WebSec101 0x14_ss/image-33.png>)

* Dozu biraz aşmışız. Bu yüzden + olacak şekilde başka bir üründen belirli bir miktar almalıyız;

![alt text](<WebSec101 0x14_ss/image-34.png>)

* Kodu çalıştırıp sayfayı her yenilediğimizde sayı + ya doğru gidiyor;

![alt text](<WebSec101 0x14_ss/image-35.png>)

* Bu + değerini bakiyemizi geçmeyecek şekilde ayarlarsak o zaman sipariş verebiliriz.

* Sipariş değeri +'ya yaklaştıkça kod satırının en sonundaki;

```python
with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
    executor.map(send_request, range(1, 250))
```

* Range fonksiyon değerini arttırıp düşürebiliriz. Burada ben 250'ye düşürüp tekrardan request yolluyorum ki fiyat bakiyemi çok fazla geçmesin.

* Sonunda değer bir şekilde +'ya yaklaştı ve burdan sonra site üzerinden + tuşuna basarak ilerliyorum; 

![alt text](<WebSec101 0x14_ss/image-37.png>)

* Place order diyebiliriz; 

![alt text](<WebSec101 0x14_ss/image-38.png>)


# KAYNAK

[Web Security 0x14 | Back back back tick ^^](https://www.youtube.com/watch?v=NDPUSV802xY&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=18)