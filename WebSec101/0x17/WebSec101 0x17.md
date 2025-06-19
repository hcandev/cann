<h1 align="center">Server-Side Template Injection Giriş</h1>

* Sözlük
    * MVC(Model View Controller): Model Görüntü Kontrolcüsü olarak düşünülebilir. Bu kodları organize eden bir tasarım modelidir. Bu sayede kodların yönetilmesi daha kolay hale gelir. 


Sunucu Taraflı Şablon Enjeksiyonu olarak düşünebiliriz.

Şöyle bir kod düşünelim;

```html
<html>
    <div>
        Merhaba MDISEC
    </div>
</html>
```

Bu kod nihayetinde browser'da bir html return edecektir. Yukarıdaki 'MDISEC' yazan kısım kontrol edilebilir bir değerdir. Sonuçta bu kısım kullanıcı adını içeriyor ve hangi kullanıcı girdiyse o kullanıcının adı gözükecek. Yani bu kısım dinamik ve değişken bir değer.

![alt text](<WebSec101 0x17_ss/image.png>)

Şimdi burada varsayalım ki kullanıcı web uygulamasına giriş yaparak kullanıcı adı requestini giriyor ve bu isim database'de karşılığı olduğu için **MVC** panelinde gözüküyor. **SSTI** zafiyeti de burada devreye giriyor; 

```html
return view('home.html', name)

<html>
    <div>
        Merhaba {{ name }}
    </div>
</html>
```

Yukarıdaki kodda  **{{ name }}** yazan yer aslında dinamik olarak ayrılmış bölmedir. Buradan kullanıcı adı bilgileri dinamik olarak giriş yapan kullanıcıya veriliyor. SSTI'ın ortaya çıktığı yer bu noktadır. Çünkü aslında bu dinamik kısma ulaşıldığı vakit buraya kod enjekte edilebilir. Bunun sonucunda da **SSTI zafiyeti** söz konusu olur. 

Buradan yapacağımız yorum kodun dinamikliği ile ilişkili. Bu dinamik yapıların çoğaldığı durumları düşündüğümüzde aslında sızılacak noktaların çokluğu da söz konusu olmaktadır. İşte bu ortamda işin boyutu da büyür.


# Alıştırma #1

* https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic

* Bu lab'da SSTI zafiyeti varmış. Bunun sebebi de güvensiz **ERB template** yapısıymış. Bizden bu ERB template'inin kullanma kılavuzunu inceleyerek nasıl kod çalıştıracağımızı bulmamız isteniyor. Sonra da carlosun **morale.txt** dosyasını silerek labı tamamlayacakmışız. 

* İlk olarak **ssti** zafiyetinin varlığını anlamaya çalışalım.

* İlk ürüne tıklayıp requestini aldığımızda; 

![alt text](<WebSec101 0x17_ss/image-1.png>)

* Şöyle bir kısım ile karşılaşıyoruz. Şimdi buraya selam yazıp response'a bakalım;

![alt text](<WebSec101 0x17_ss/image-2.png>)

* Evet görüldüğü üzere buraya yazdığımız değer response'da div bölmesinde gözüküyor. Bir diğer örnek; 
**<script>alert(1)</script>** yazarsak;

![alt text](<WebSec101 0x17_ss/image-3.png>)

* Bu da demek oluyor ki biz buraya kod enjekte edebiliriz. Çünkü dinamik olarak verilen mesaj değişkenine doğrudan etki edebiliyoruz ve bu sayade **ssti** zafiyetini tespit etmiş oluyoruz. 

* Ancak fark ediyoruz ki yazdığımız kod sadece gözüküyor yani bir işlevi yok. Bu yüzden bu web uygulamasının kullandığı template'e dair fikir edinmemiz gerek. Bu yüzden;

* https://www.puppet.com/docs/puppet/5.5/lang_template_erb bu siteye geliyoruz ve bir takım kodları görüyoruz;

![alt text](<WebSec101 0x17_ss/image-4.png>)

* Bu kod print koduymuş. Deneyelim;

![alt text](<WebSec101 0x17_ss/image-5.png>)

* Şöyle bir şey yazalım ve send diyelim; 

![alt text](<WebSec101 0x17_ss/image-6.png>)

* Response kısmında kodun dışına yazdığımız gözüktü. Yani kod burada gözükmedi. Bunun sebebi print edecek bir şey yazdırmadığımızdan. Buradan da kodun işlediğini söyleyebiliriz. Doğrulayalım;

![alt text](<WebSec101 0x17_ss/image-7.png>)

* 7*7 yazdık şimdi send dediğimizde CAN yazan yerin yanında 49 görmemiz lazım;

![alt text](<WebSec101 0x17_ss/image-8.png>)

* Demek bu kod çalışıyor. Şimdi bunu kullanarak carlos'un dosyasını silmeyi deneyeceğiz;

```
File.open('/etc/passwd').read
```

* Bu kod dosya okuma işlemi yapıyor. Eğer bunu print kodunun içine koyup çalıştırırsak;

![alt text](<WebSec101 0x17_ss/image-9.png>)


* Response'da dosyaları görebileceğiz. Şimdi bizden istenen dosyalara gitmek için kodu şöyle değiştirelim;

```
system('rm%20-rf%20/home/carlos/morale.txt')
```

* Bu kod sisteme erişip belirtilen dosyayı silmeye yarıyor ve **ERB template** ile ilgili. Şimdi bunu çalıştıralım ve labı tamamlayalım;

![alt text](<WebSec101 0x17_ss/image-11.png>)

![alt text](<WebSec101 0x17_ss/image-12.png>)

# Alıştırma #2 

* https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic-code-context

* Geçen seferki ile aynı zafiyet. Bu sefer **Tornado** isimli bir template kullanılıyormuş. Yine morale.txt silinmeliymiş.

* Verdiği hesaba giriş yapalım ve my account kısmından **prefer name** özelliğini kullanarak requesti yakalayalım;

![alt text](<WebSec101 0x17_ss/image-12.png>)

* Requestte username parametresi gözüküyor. 

* Şimdi ürünlerden birine yorum yapalım;

![alt text](<WebSec101 0x17_ss/image-13.png>)

* Geri koda dönüp username yazan yere  7*7 yazalım;

![alt text](<WebSec101 0x17_ss/image-16.png>)

* Şimdi yorum kısmını yenileyelim;

![alt text](<WebSec101 0x17_ss/image-17.png>)

* Evet görüldüğü gibi isim kısmında 49 yazıyor. Demek ki dinamik bir değer üzerinde kod çalıştırabiliyoruz.

```
__import__("os").system("rm -rf /home/carlos/morale.txt")
```

* Yukarıdaki kodu girersek silme işlemini yapacağız. Bu kod da **tornado template**'e özel bir silme metodu.

![alt text](<WebSec101 0x17_ss/image-18.png>)

* Lab çözülmüş oluyor. 

# Alıştırma #3

* https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-using-documentation

* Yine aynı zafiyet tek fark bu sefer hangi template engine kullanılmış onu bulmamızı ve silme işlemini bundan sonra yapmamızı istiyor.

* Lab'a girelim ve hesaba giriş yaptıktan sonra bir ürüne tıklayıp edit template diyelim;

![alt text](<WebSec101 0x17_ss/image-19.png>)

![alt text](<WebSec101 0x17_ss/image-20.png>)

* Template kısmında  **${product.name}** js tarzı bir yapı görüyoruz. Şimdi burayı silip şu kodu yazalım;

![alt text](<WebSec101 0x17_ss/image-21.png>)

* Yazıp **preview** tuşuna bastıktan sonra aşağıda 49 sayısını görüyoruz. Şimdi template tipini bulmaya çalışalım ve sayıları silip object yazalım;

![alt text](<WebSec101 0x17_ss/image-22.png>)

* Hata verdi ancak aynı zamanda hatanın içinde **FreeMarker Template**'ini kullandığını da yazdı. Şimdi tek yapmamız gereken bu template'de sisteme nasıl ulaşacağımızı bulup istenilen dosyayı silmek. 

* Bize lazım olan kod ise;

```
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```

![alt text](<WebSec101 0x17_ss/image-23.png>)

* Preview diyelim;

![alt text](<WebSec101 0x17_ss/image-24.png>)

* Lab çözüldü.

* Bu lablar boyunca öğrendiğimiz şey aslında farklı template'lerin işleyişini kullanarak bu işleyişleri kendi lehimize kullanmak. Yani stratejimiz belli ancak araçlar devamlı değiştiği için neyi nerde nasıl bulacağımızı öğrenmiş olduk. Bu cybersecurity alanında ve genel olarak development alanında çok önemli bir şey.


# KAYNAK

[Web Security 0x17 | Server-Side Template Injection Giriş - 1](https://www.youtube.com/watch?v=w-GbdWzss0g&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=21)