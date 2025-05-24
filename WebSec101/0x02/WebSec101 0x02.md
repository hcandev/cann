<h1 align="center">IDOR ZAFİYETLERİ</h1>

# Web Uygulamasının Bazı Çalışma Mantıkları

Bir web uygulaması kullanıcıdan girdiler alarak çalışır. Yani bizler web sayfasıyla etkileşime geçer ve onun veri tabanına girdiler yollarız. Çoğu web uygulamasında etkileşime geçebileceğimiz çok fazla alan vardır. Örneğin bir alışveriş sitesinde kendi adresimizi ya da ödeme bilgilerimizi kaydedebiliriz. Bu işlemleri yaparken de aslında uygulamanın veri tabanına çok fazla direktif veririz.Çoğu web uygulamasında da kullanıcının etkileşime geçebildiği çok fazla alan olması sebebiyle bu web uygulamalarının veri tabanında karmaşık ve katmanlı yapılar oluşur. Bu katmanlı ve karmaşık yapılar birbirleri ile iç içe geçmiş olduğundan zaafiyete açık hale gelebilir ve veri sızıntısı söz konusu olabilir.

# IDOR (Insecure Direct Object Reference)

### IDOR NE DEMEK