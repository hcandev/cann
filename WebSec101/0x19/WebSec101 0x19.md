<h1 align="center">Kaynak Kodu İnceleme Sonar-Source</h1>

Bu alıştırmada Sonarsource tarafından paylaşılan çeşitli kaynak kodları üzerinden zafiyet tespiti yapılacak.

# 1

![alt text](<WebSec101 0x19_ss/image-1.png>)

* Yukarıda c# ile yazılmıs ve .net platformunda çalışan kod mevcut. İlk baştaki fonksiyon .net'den alınmış. Aşağıdaki satırda ise **packageId** değişken string değeri verilmiş. Burada **cmd injection** var. Birisi **packageId** yerine;
```
somepackage && del C:\important\file.txt
``` 
tarzı bir kod yazarsa o zaman bu değişken çağrıldığında silme komutunu çalıştıracaktır. Bu yüzden **Command Injection** zafiyeti var.

# 2

![alt text](<WebSec101 0x19_ss/image-3.png>)

* Buradaki zafiyet **Code Evaluation** zafiyetidir. Buna sebep olan ana satır;

```php
return eval("return ($expression);");
```

* Eval fonksiyonu $expression değişkeninde saklanmış php kodunu çalıştırıyor. Burada gelen içerik ise kullanıcı inputuna bağlı;

```php
$_POST['_mail_body']
```

* Dolayısıyla saldırgan burada **_mail_body** requesti olarak;

```php
_mail_body=<tag express="phpinfo()">
```

* Gönderirse kod bloğunda şu çalışır;

```php
return eval("return (phpinfo());");
```

* Burada saldırgan artık dosyaları okuyabilir, sistem kodları çalıştırabilir veritabanına ulaşabilir ya da sunucuyu ele geçirebilir. 

* Bunu önlemenin yolu eval fonksiyonunu hiç kullanmamaktır. 

# 3 

![alt text](<WebSec101 0x19_ss/image-4.png>)

* Burası SQL Injection. Bundan sorumlu kod satırı ise;

```java
criteria.add(Restrictions.sqlRestriction(nodeParameterName + " = ?", nodeParmValue, new StringType()));

```

* Sebebi,**nodeParameterName**'in dinamik olarak kullanıcı inputundan yapılanıyor oluşudur;

```java
final String nodeParameterName = ("snmp" + nodeParm).toLowerCase();
```

* Bu değişken de doğrudan;

```java
Restrictions.sqlRestriction(nodeParameterName + " = ?", ...)
```

* Kısmına gider. Yani saldırgan **nodeParm** kısmına giderek sql komutları yazabilir. Örn;

```java
nodeParm = "foo from users where '1'='1"
```

* Sonrasında bu değişken de şuraya gider;

```java
sqlRestriction("snmpfoo from users where '1'='1' = ?", ...)
```

* Bunu önlemenin yolu da kullanıcı inputunun SQL tanımlayıcılarına etki etmemesini tamamen önlemektir. 


# 4

![alt text](<WebSec101 0x19_ss/image-6.png>)

* Buradaki zafiyet **Race Condition**'dır. Buna sebep olan kod;

```python
tmp = tempfile.mktemp(suffix='.pdf', prefix='stoqlib-reporting')
```

* Bu kod bir dosya adı oluşturur ancak bunu saklamaz. Bundan dolayı da saldırgan dosya oluşturulurkenki geçen sürede aynı isimde bir dosya oluşturabilir(**race condition**). Bu da üzerine dosya yazmasına sebep olur ve zafiyet açığa çıkarır. 

# 5 

![alt text](<WebSec101 0x19_ss/image-8.png>)

Burada LFI(local file inclusion) zafiyeti var.

```
require_once "sites/$site_id.php";
```

* Buradaki **siteid** cookie ya da hostname'den getiriliyor. Ancak regex kontrolü olsa bile;

```
preg_match('/[^A-Za-z0-9._-]/', $site_id)
```

* Directory Traversal '../' yapılabilir. Çünkü buradaki regex kodu **'.' '_' '-'** karakterlerini engeller. Ancak **'../'** engellenmiyor.

* Örneğin saldırgan şu tarz bir işlem yapabilir;

```
Cookie: site=../../../../etc/passwd%00
```

* Sonrasında da bu kod içeride;

```
require_once "sites/../../../../etc/passwd.php";
``` 

* Bu noktada çalışacaktır. Bunu portswigger alıştırmalarında kullanmıştık.  

# 6 


![alt text](<WebSec101 0x19_ss/image-7.png>)

Buradaki kod .odt dosyasını işler. Bu da yüksek ihtimalle içerisinde birden çok xml dosyası barındıran bir zip arşividir. **extractContent()** metodu dosyayı açıyor ve content.xml dosyasını arıyor. 

* Burada **XXE zafiyeti** mevcut. Bunun nedeni **SAXBuilder** kullanarak **XML parse** işlemi yapmasıdır. Çünkü **SAXBuilder** yapısında XXE işlevinin engellenmiş olduğuna dair bir veri yok. XML belgesi doğrudan kullanıcının kontrol ettiği input ile inşa edilir(.odt).

örn;

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<foo>&xxe;</foo>
```

# 7 

![alt text](<WebSec101 0x19_ss/image-10.png>)

* Burada **XML tabanlı Deserialization** zafiyeti mevcut. 

```
XmlSerializer serializer = new XmlSerializer(t);  // Type 't' is attacker-controlled
ExchangeData data = (ExchangeData)serializer.Deserialize(textReader);
```

* Buradaki **t** değişkeni user input'a bağlı;

```
var dataType = Type.GetType(rootItem.GetAttribute("dataType"));
```

* Saldırgan burada **dataType** özelliğinde herhangi bir **type name** belirtebilir. Sonra bu type XmlSerializer kullanılarak serialize edilir. 

# 8 

![alt text](<WebSec101 0x19_ss/image-12.png>)

* Burada **XSS** var. **$upload name** ve **$upload error** parametreleri **user input** ile alınıyor. Dolayısıyla buraya XSS injection yapılabilir. 

# 9 

![alt text](<WebSec101 0x19_ss/image-13.png>)

* Burada **mode** ve **repHome** değişkenleri kullanıcıdan alınıyor. Sonrasında ise **repHome** **installConfig** fonksiyonuna gömülüyor ve buradan da **File dest** parametresine gidiyor bu input. Bu sayede sunucudaki herhangi bir dosyanın içeriğini değiştirme imkanı doğmaktadır. Buradaki zafiyetin adı da **Arbitrary File Overwriting(İsteğe bağlı Dosya Üzerine Yazma)**. 


# 10 

![alt text](<WebSec101 0x19_ss/image-14.png>)

Burada **Arbitrary File Deletion** zafiyeti mevcut. Saldırgan sunucudaki istediği dosyayı silebilme yetkisine sahip olur. Bunu da **get_addon_path()** fonksiyonunun içerisindeki bir açıktan yararlanarak yapar. Bu fonksiyon aşağıda **os.unlink(self.get_addon_path())** metodu ile birlikte silme işleminde kullanılır. **get_addon_path()** fonksiyonu da içerisinde **get** talebiyle **filename token** ve **path** oluşturuyor. Yani bu da inputa bağlı ve dolayısıyla zafiyete açık. 

# KAYNAK

[Web Security 0x19 | Source-Code Review SonarSource #CodeChallenge !](https://www.youtube.com/watch?v=V_ogHUIF8E0&list=PLwP4ObPL5GY940XhCtAykxLxLEOKCu0nT&index=23)