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

> Günümüzde **XML** veri formatı e ticaret sitelerinden, uluslararası bankacılık sistemi Swift'e kadar geniş bir alanda   kullanılmaktadır.

## XML DTD (XML Document Type Definition(XML Belge Tipi Belirleme))

Bir XML Belgesinin yapısını ve kurallarını belirler. XML dosyasının nasıl yazılacağını gösteren bir rehber gibi düşünebiliriz Örneğin aşağıda bir XML dosyası ve onun beslendiği bir **DTD** var;

```XML
<?xml version="1.0"?>
<!DOCTYPE person SYSTEM "person.dtd"> <!--Burada XML belgesinin beslendiği bir döküman-->
<person>
  <name>Ali</name>
  <age>30</age>
</person>
```
Yukarıdaki XML dosyasında yukarıda da örneklediğimiz ali isimli birinin verileri ve bir DTD dosyası var. Yukarıda çağrılan DTD dosyası da aşağıda;

```DTD
<!ELEMENT person (name, age)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT age (#PCDATA)>
```