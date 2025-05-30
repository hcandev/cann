<h1 align="center">XML Parserlar ve XML External Entity (XXE) Injection</h1>


# XML (Extensible Markup Language)

Genişletilebilir İşaretleme Dili. Verileri düzenli, taşınabilir ve okunabilir şekilde saklamak ya da paylaşmak için kullanılan bir veri formatıdır. Örn;

```
<user>
  <name>Ali Yılmaz</name>
  <age>30</age>
  <department>IT</department>
</user>
```

## XML Parsing(XML Ayrıştırma)

XML belgesinin okunması ve verilerin buradan yapılandırılmış bir biçimde çıkarılması işlemidir. Örneğin yukarıdaki örneği **python** ya da başka bir programlama diliyle dışarı çıkarabiliriz.  


## XML Nerede?

![alt text](image.png)

Görselde iki tane web uygulaması örnek veriliyor. Burada bu iki web uygulamasının da birbirinden farklı olduğu varsayılmış ve bu bilgilere dayanarak iki uygulama da etkileşim halindedir. Ancak bu iki uygulamanın etkileşim halinde olabilmesi için ortak bir protokol kullanması gerekir(görselde bu protocol **http**dir.) Aynı zamanda Data(veri)'nın da iki uygulamanın anlayabileceği bir formatta olması gerekir. İşte burada da **XML** devreye girmektedir. **Yalnız, bir web uygulamasının **XML**'i kullanabilmesi için onu parse(ayrıştma) etmesi gerekir. **

> ***Günümüzde **XML** veri formatı e ticaret sitelerinden, uluslararası bankacılık sistemi Swift'e kadar geniş bir alanda kullanılmaktadır. ***