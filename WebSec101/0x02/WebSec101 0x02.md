<h1 align="center">IDOR ZAFİYETLERİ</h1>

# Web Uygulamasının Bazı Çalışma Mantıkları

Bir web uygulaması kullanıcıdan girdiler alarak çalışır. Yani bizler web sayfasıyla etkileşime geçer ve onun veri tabanına girdiler yollarız. Çoğu web uygulamasında etkileşime geçebileceğimiz çok fazla alan vardır. Örneğin bir alışveriş sitesinde kendi adresimizi ya da ödeme bilgilerimizi kaydedebiliriz. Bu işlemleri yaparken de aslında uygulamanın veri tabanına çok fazla direktif veririz.Çoğu web uygulamasında da kullanıcının etkileşime geçebildiği çok fazla alan olması sebebiyle bu web uygulamalarının veri tabanında karmaşık ve katmanlı yapılar oluşur. Bu katmanlı ve karmaşık yapılar birbirleri ile iç içe geçmiş olduğundan zaafiyete açık hale gelebilir ve veri sızıntısı söz konusu olabilir.

# IDOR (Insecure Direct Object Reference)

Yukarıdaki yazıda web uygulamasının veri tabanı ile kullanıcı arasındaki ilişkiden bahsetmiştik. Kullanıcı veri tabanına web uygulamasının izin verdiği ölçüde erişebiliyor. Örnek olarak web sitelerinin bize çeşitli alanlarda(adres ekleme, satış bilgilerini kaydetme gibi) verdiği izinler gösterilebilir. Burada 'web uygulamasının izin verdiği ölçü' aslında kilit noktadır. Güvenli bir uygulamada kullanıcıların web uygulaması üzerindeki yetkileri sınırlı olmalıdır ki hem kullanabilsinler hem de veri sızmasın. 

Örneğin biz kendi adresimizi web uygulamasına kaydediyoruz ama aynı zamanda bir başkasının adresini görmemeliyiz. Eğer web uygulamasında böyle bir zaafiyet var ise ve kendimizin dışında başkasının adresini de görebiliyorsak  burada IDOR kategorisinde bir zaafiyet olduğunu söyleyebiliriz. En basit haliyle IDOR zaafiyeti budur.