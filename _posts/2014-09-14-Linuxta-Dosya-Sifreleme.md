---
layout: post
title: "Linux'ta Dosya Şifreleme"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [linux, cryptography, openssl]
---

Merhaba,
Bu gece ilgimi çeken bir konuyu sizlerle paylaşmak istedim.Peki ne bu? Dosya Şifreleme!.Dosyaları şifrelemek, verileri saklamak vs. çoğu insanın ilgisini çekiyordur mutlaka.Benimde sadece TrueCrypt yazılımıyla şifreleme konusunda bilgim olmuştu, sonra işin linux kısmını araştırınca benzer şeylerin burada da yapıldığını gördüm ve bununla ilgili bir yazı yazmaya karar verdim.
<!--more-->
### Peki nasıl olacak bu? ###

Linux dağıtımlarında default olarak bulunan, SSL ve TSL protokollerinin açık kaynaklı bir uygulaması olan <font color="green">openssl</font>'i dosyalarınızı şifrelemek veya hash değerlerini almakta kullanabilirsiniz.

### Tamam anladım, hadi başlayalım! ###

Sisteminizde openssl'in hangi versiyonda çalıştığını öğrenmek için `~$ openssl version` komutunu verebilirsiniz.

* Şifreleme algoritmalarını listelemek için
	`~$ openssl ciphers -v`

* İlk olarak des3 algoritmasıyla sifrelebeni.txt dosyasını şifreleyelim.Şifreleme mantığı, belirlenen algoritma ve kendi belirlediğimiz bir anahtar eşliğiyle oluşuyor.Nasıl yani?
Şifreleme için bir algoritmaya ve sadece bize ait bir anahtara ihtiyacımız var.Bu anahtar ile dosyayı hem şifreleyebilir hem de tekrar şifrelenmiş dosyayı açabiliriz.Şifrelediğimiz dosyayı açarken de bu anahtar bize lazım olacak.
Şifrelenmiş dosya açılsa bile anahtar bilinmediği sürece dosyanın içindeki gerçek veriye ulaşılamayacaktır.
	`~$ openssl enc -des3 -in sifrelebeni.txt -k anahtar` komutuyla dosyamızı belirlediğimiz anahtarı kullanarak des3 algoritması ile şifreledik.
![des3 şifreleme]({{ site.url }}/assets/1.png)

Şifrelenmiş içeriği bir dosyaya yazmak istersek `~$ openssl enc -des3 -in sifrelebeni.txt -out sifrelendi.des3 -k anahtar` komutunu kullanabiliriz.

![des3 şifreleme2]({{ site.url }}/assets/2.png)

* Eğer dosya değilde komple bir dizini şifrelemek istiyorsak tar, openssl ve dd komutlarını birlikte kullanmamız gerekiyor.

`~$ tar -zcf - dizin/ | openssl des3 -salt -k anahtar | dd of=dizin.des3`
![des3 şifreleme3]({{ site.url }}/assets/3.png)

Şifrelenmiş dizini çözmek için ise;
`~$ dd if=dizin.des3 | openssl des3 -d -k anahtar | tar xzf -`

![des3 şifreleme4]({{ site.url }}/assets/4.png)


Görüldüğü üzere linux'ta dosyalar bu şekilde şifreleniyor ve şifreleri çözülebiliyor.Tabi bunların hepsini yaparken bir anahtar belirlememiz, çözerken de o anahtarı bilmemiz şart.
Bir başka yazıda görüşmek üzere..

[Fatih ERDOGAN](http://ferdogan.net) ([@FeCassie](https://twitter.com/FeCassie))

