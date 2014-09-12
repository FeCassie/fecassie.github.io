---
layout: post
title: "TheHarvester ile Footprinting"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [backtrack5, kali linux, linux, security, footprinting, theharvester]
---
Merhaba,

Backtrack ile hedef sistem hakkında bilgi toplamanın farklı araçlarla birçok yolu vardır.
Daha önceki yazımda yine Backtrack içerisinde bulunan Metagoofil aracıyla hedef sistemden nasıl pdf,doc vs. gibi belge toplayabileceğimizi anlatmıştım.
[Metagoofil ile Belge Toplama](http://fecassie.github.io/Metagoofil-Belge-Toplama-Aracı/)

Şimdi ise belge değilde daha farklı bilgi edinme yöntemlerinden bahsedeceğim.
Öncelikle bilgi toplamanın önemine biraz değinmek istiyorum.
Hedef sistem hakkında bilgi toplamanın; zafiyet tespit etme,portların durumunu,firewall ları,çalışan işletim sistemlerini ve IPS denilen saldırı tespit sistemlerinin keşfedilmesi kadar önemli olduğunu düşünüyorum.

Peki bunlardan farklı ne şekilde bilgi toplayabilirim?
Hedef sistemdeki dökümantize edilmiş dosyaları bularak,hangi subdomainlerin hangi sanal host ların olduğunu tespit ederekte hedef sistem hakkında bilgi sahibi olabilirsiniz.
Bu sayedede hedef sistem içindeki dosya akışı hakkında bilgi sahibi olabilirsiniz.
Hedef sistemdeki kullanıcıların eposta adreslerini bularak,sisteme keşfettiğiniz eposta adreslerinden saldırmayı deneyebilirsiniz.
Bu şekilde verilebilecek daha birçok örnek var..

## theHarvester nedir?? ##

theHarvester; hedef sistemdeki Kullanıcı adlarını,Eposta hesaplarını,Hostname leri ve Subdomain leri bulup raporlayan bir araçtır.
Bilgi toplarken aynı Metagoofil aracı gibi Google,Bing,Pgp,Linkedin gibi arama motorlarından yararlanır.
Google,Bing ve Pgp Eposta,Hostname ve Subdomain bilgilerini bulurken Linkedin aracı sadece username bilgilerini bulmaktadır.


## theHarvester'ı nasıl kurabilirim?? ##

theHarvester’ı Linux sistemlere yüklemek için önce bir terminal(konsol) açıyoruz.Ardından aşağıdaki komutları yazıyoruz : 

`wget https://theharvester.googlecode.com/files/theHarvester-ng-2.0.tar` = Bu komutla programın son sürümünü indiriyoruz.

`tar xvf theHarvester-ng-2.0.tar` = Bu komutla indirdiğimiz programı tar halinden çıkartıyoruz.

`cd theHarvester-ng-2.0.tar` = Programın kurulu olduğu dizine geçiyoruz.

`chmod +x theHarvester.py` = Programın sorunsuz çalışması için çalışma izni vermemiz gerekmekte.

`./theHarvester.py` = Bu komutlada programımızı çalıştırıyoruz.

theHarvester kurulumunu Linux Mint12 üzerinde anlattım :

<iframe src="//player.vimeo.com/video/37825822" width="500" height="281" allowfullscreen></iframe>


## theHarvester kullanımı ##

Evet kurulumun ardından theHarvester aracının kullanımından bahsedelim.
Kurulumu LinuxMint 12 üzerinde,Anlatımı ise Backtrack üzerinde yapıyoruz.

* Bir Terminal açıyoruz.ve bu komutla theHarvester’ın olduğu dizine gidiyoruz.

  `cd /pentest/enumeration/theharvester`

* Daha sonra programımız açılıyor.
Programın kullanımı çok basit.Genel arama komutu şu şekildedir :

  `./theHarvester.py -d yahoo.com -b google -l 600 -v`

Bu komutu kendinize göre düzenleyebilirsiniz.Zaten programın ana ekranındada örnekler mevcut.

-d parametresi; hedef siteyi
-b parametresi; kullanılacak arama motorunu
-l parametresi; limit değerini
-f parametresi; tarama sonucunu html veya xml olarak kaydetmeyi
-v parametresi ise DNS çözümlemesini belirtir.


Şimdi anlattıklarımızı video ile özetleyip konuyu noktalayalım.
İyi seyirler.

<iframe src="//player.vimeo.com/video/37834893" width="500" height="281" allowfullscreen></iframe>
