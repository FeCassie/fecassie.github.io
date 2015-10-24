---
layout: post
title:  "UPX ile Manual Unpack"
date:   2015-10-25 01:37:00
categories: Security
banner_image: "/assets/drilii.jpg"
featured: false
comments: true
---

Merhaba,
Bu yazıda UPX packing ve manual unpacking aşamalarını inceleyeceğiz.
<!--more-->
# UPX Packing&Unpacking  #

## UPX ##

UPX bir pack yazılımıdır.Zararlı yazılımları Anti-Virus gibi uygulamalardan saklamak, analiz işlemlerini zorlaştırmak ya da boyuttan tasarruf sağlamak adına upx ile packleme yöntemi kullanılır.

## Packer ##

Packerlar girdi olarak executable bir program alırlar ve çıktı olarak yeni bir executable dosya oluştururlar.Packlenmiş program unpack edildiği zaman, unpack edilen stub OS'a yüklenir ve daha sonra unpack edilen stub orijinal programın içine eklenir.Packlenmiş program çağrıldığında unpack edilmiş stub işletim sistemi tarafından yüklenir.Daha sonra unpack edilmiş stub orijinal programa eklenir.

Unpack işlemi özetle üç adımdan oluşmaktadır:

- Programın orijinalini unpack ettiğimizde dosya memoryden çalıştırılacaktır.
- Orijinal programın tüm importları çözümlenir.
- Çözülen importlar OEP'e aktarılarak düzenlenir.

Pack&Unpack aşamalarını basit şekilde inceleyelim:

- Orijinal programın packing işleminden önceki hali.Tüm bölümler ve header kısmı görünür halde.Entry Point OEP(Original Entry Point)'i göstermekte.

![upx1](http://ferdogan.net/assets/upx1.png)

* Packlenmiş programın hali ise bu şekilde.Unpacking Stub bölümü eklenmiş ve Entry Pointy, Unpacking Stub bölümüne set edilmiş.

![upx2](http://ferdogan.net/assets/upx2.png)

* Program, unpack edildikten ve memory'ye yüklendikten sonra bu hale geliyor.Fakat Entry Point hala Unpacking Stub üzerinde görünüyor.

![upx3](http://ferdogan.net/assets/upx3.png)

* Executable'ın tamamen unpack edildikten sonraki hali gördüğünüz gibi. Entry Point, OEP'i işaret etmekte ve import dosyaları düzeltilmiş haldedir.

![upx4](http://ferdogan.net/assets/upx4.png)

##UPX ile Packing&Unpacking

İlk olarak calc.exe programını upx aracı ile packleyelim, ardından yine upx aracı ile otomatik unpack etmeyi inceleyelim.

Önce UPX aracını indirin daha sonra calc.exe'yi upx aracının olduğu klasöre kopyalayın.

![upx5](http://ferdogan.net/assets/upx5.png)

Pack ederken -1 -9 arasında bir değer belirliyoruz.-9'a doğru sıkıştırma seviyesi artacak.

``` upx.exe -9 calc.exe``` komutuyla calc.exe'yi packleyelim.

![upx6](http://ferdogan.net/assets/upx6.png)

"Packed 1 file." calc.exe'yi packledik.DIE ile packlenmiş calc.exe'yi inceleyelim.

![upx7](http://ferdogan.net/assets/upx7.png)

Farkettiyseniz packer kısmında UPX(3.09) yazmakta.Pack işlemi başarılı olmuş.

Peki unpack etmek istiyoruz ne yapmalıyız ? UPX aracı ile çok basit bir şekilde calc.exe'yi unpack edebiliriz.

``` upx.exe -d -o calc_upx.exe calc.exe``` komutuyla unpack edelim.

![upx62](http://ferdogan.net/assets/upx62.png) 

"Unpacked 1 file" .Tekrar DIE ile bakalım bir değişme var mı ?

![upx8](http://ferdogan.net/assets/upx8.png)

Gördüğünüz gibi packer kısmı ve UPX'e dair bir şey yok.Farklı bir PE aracı olan PeID ile de inceleyelim.Hangisini isterseniz onu kullanabilirsiniz.

![upx9](http://ferdogan.net/assets/upx9.png)

Bakın burada ek bir bilgi edindik.Microsoft Visual C++ ile geliştirilmiş doğal olarak :)

## Manual Unpacking 

UPX ile packlenmiş bir program üzerinde analiz yapmak istiyorsak, programı unpack etmemiz gerekir.Bu bölümde upx manual unpack olayını inceleyelim.

UPX Unpack Aşamaları:

* PUSHAD instruction’ı ile tüm registerlar stack’de saklanır.
* Tüm bölümler unpack kodu ile unpack edilir.
* IAT tablosu fix edilir.
* POPAD instruction’ı ile tüm registerlar eski değerlere yüklenir.
* Klasik bir JMP instruction’ı ile asıl OEP adresine atlanır.

Elimizde UnPackMe.exe adında bir dosya var.DIE ile bakarak UPX ile packlendiğini görebilirsiniz.

![upx10](http://ferdogan.net/assets/upx10.png)

Programı IDA ile açıp "pusha" instruction'ına breakpoint koyup debugger ile incelemeye başlayalım.

![upx11](http://ferdogan.net/assets/upx11.png)

Bu arada breakpoint koyduğumuz instruction adresi yukarıda incelediğimiz Unpacking Stub içindeki Entry Point'i gösteren adres.F8 yaparak pusha instruction'ını atlayalım.

![upx12](http://ferdogan.net/assets/upx12.png)

Ardından ESP'nin yanındaki oka tıklayarak stack'e gidelim.

View > Open subviews > Hex Dump 'a tıklayarak ESP'nin hex dump halini alalım.

```0012FFA4``` adresindeki ilk 4 byte'ı seçip Debugger > Breakpoints > Add breakpoint menüsünden hardware breakpoint koyalım.

![upx13](http://ferdogan.net/assets/upx13.png) 

Aynı menüdeki Breakpoint list'den koyduğumuz breakpointleri görebilirsiniz.

![upx14](http://ferdogan.net/assets/upx14.png) 

Daha sonra F9'a basarak continue process işlemini gerçekleştirelim.F8 ile ilerlediğimizde kendimizi bir döngüde bulmamız lazım.
Breakpoint menüsünden koyduğumuz hardware breakpoint'i kaldılarım.
İsterseniz tek tek ilerleyerek döngüden çıkmaya çalışabilirsiniz ya da sub instruction'a F2 ile breakpoint koyup ardından F9'a basarak sub instruction'ına geçebilirsiniz.

![upx15](http://ferdogan.net/assets/upx15.png) 

F8 ile jmp instruction'ına geldik, peki o jmp bizi nereye dallandıracak tahmin etmişsinizdir.

Bingo! OEP adresini bulduk.

![upx16](http://ferdogan.net/assets/upx16.png) 

```004271B0``` bizim yeni OEP adresimiz.Bir kenara not alın :)

### Sırada ne var ?

IDA şimdilik bizi beklesin.Yine kullanacağımız başka bir araç olan LordPE ile UnPackMe.exe dosyasına ait dump dosyası oluşturalım.IDA'yı kapatmayın, LordPE'yi açın UnPackMe.exe'ye ait process'i bulun.Daha sonra process'e sağ tıklayıp "dump full" seçeneğine tıklayın.
dumped.exe olarak kaydedin.

![upx17](http://ferdogan.net/assets/upx17.png) 

Dumping done yazan bir messagebox karşılayacak sizi.
dumped.exe'yi açmaya çalıştırdığınızda aşağıdaki hatayı alacaksınız.

![upx18](http://ferdogan.net/assets/upx18.png) 

Bunun sebebi Import Address Table(IAT)'ın bozulmuş olması.IAT tablosunu düzeltmemiz gerekiyor.Bu işlemi ImpREC aracı ile yapacağız.
Orijinal programımızı ImpREC ile açın (dumped.exe değil dikkat edin!).

![upx19](http://ferdogan.net/assets/upx19.png) 

Gözünüze bir şey çarptı mı ? OEP adresi bizim bulduğumuz adres değil.
OEP değerini ```000271B0``` olarak değiştirelim.
AutoSearch butonuna basın, program size bir şeyler bulduğunu söyleyecektir.Daha sonra GetImports'a tıklayarak importları ekrana getirin.

![upx20](http://ferdogan.net/assets/upx20.png) 

FixDump butonuna basarak dumped.exe'nin üzerine kaydedin.dumped_.exe olarak kaydedildiğini göreceksiniz.Tıkladığınızda başarıyla çalıştığını göreceksiniz.

DIE ile dumped_.exe'yi inceleyelim.

![upx21](http://ferdogan.net/assets/upx21.png) 

Programımızı UPX'den arındırdık gördüğünüz gibi :)

Bir başka yazıda görüşmek üzere.

