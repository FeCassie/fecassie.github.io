---
layout: post
title: "Linux'ta DNS Değiştirmek"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [linux]
categories: Linux
---
Merhaba,

Engellenmiş sitelere girmek için vs. DNS değiştirme,VPN veya proxy kullanımı gibi farklı yöntemler var.Bende sizlere linuxta kolay bir şekilde nasıl DNS değiştirildiğini göstermeye karar verdim.
DNS ayarlarını yapmak için etc dizini altında bulunan resolv.conf isimli konfigürasyon dosyasının içeriğini değiştirmemiz gerekiyor.
Default olarak dosyanın içinde şöyle bir şey yazması gerek:
    `nameserver 127.0.0.1`

Bu alanı kullanmak istediğiniz DNS değerlerine göre düzenlemeniz gerekiyor.Bunun için google dns veya opendns kullanabilirsiniz.
Sık kullanımı açısından Google DNS ayarlamalarını kullanalım.
Önce terminal ekranı açalım ve resolv.conf dosyasını root yetkisiyle açalım.Ben editör olarak vim kullanıyorum, sizde kolaylık açısından kullandığınız editörle düzenleyebilirsiniz. 
    
    `~$ sudo vim /etc/resolv.conf`
    
İçindeki nameserver kısmında google dns'e göre düzenlemeyi aşağıdaki gibi yapalım:
    
    `nameserver 8.8.8.8`
    `nameserver 8.8.4.4`
    
OpenDNS ayarlarını kullanmak içinse:

    `nameserver 208.67.222.222`
    `nameserver 208.67.220.220`
    
şeklinde resolv.conf dosyasını düzenleyelim ve ayarları kaydedip editörü kapatalım.(vim'de :wq komutunu kullanabilirsiniz.)

Fakat bu ayarlamalar tek kullanımlık oluyor.Yani siz linux sisteminizi yeniden başlattığınızda DNS ayarları tekrar eski haline dönecektir.
Bunun kalıcı olması için:

    `chattr +i /etc/resolv.conf`
    
Son olarak bu komutla da yaptığımız ayarlar kalıcı hale geldi.
**FeCassie**