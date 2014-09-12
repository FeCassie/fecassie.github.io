---
layout: post
title: "Metagoofil Belge Toplama Aracı"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [backtrack5, metagoofil, linux, security, footprinting, kali]
---

Merhabalar,

Bu gece Backtrack5 ile birlikte gelen bilgi toplama araçlarından biri olan Metagoofil isimli araçtan söz edeceğim.

Metagoofil,web sitelerinden belge toplamaya yarayan bir araçtır.İstenilen domain ve ona ait olan subdomainler içerisinde istenilen formattaki dosya tiplerini bulabilir.
Belge araması yaparken Google arama motorunu kullanır.
Word(.doc),Excel(.xls),Sunum Dosyaları(.pdf,.ppt,.odp) dosya formatlarını destekler.

Programı kullanmaya başlamadan önce bir terminal açıp şu komutu vermeliyiz :

`apt-get install extract`

Bu işlemi belgelerin extract edilmesi için yapıyoruz.

Evet şimdide işimize yarayacak olan Metagoofil parametrelerinin ne anlama geldiğini inceleyelim:

* -d : Tarama yapılacak olan domain adı bu parametreyle girilir.

    `/metagoofil.py -d hedefsite.com`
    
* -t : İndirilecek dosya formatını bu parametreyle belirtiyoruz.

    `./metagoofil.py -d hedefsite.com -t doc,pdf`

* -l : Tarama sonuçlarındaki limiti belirler.
  
    `./metagoofil.py -d hedefsite.com -t doc,pdf -l 100`

* -n : İndirilecek dosyaların limitini belirler.

    `./metagoofil.py -d hedefsite.com -t doc,pdf -l 100 -n 50`

* -o : Çalışma alanı.Yani toplanan belgelerin bir klasör altında toplanmasını sağlar.

    `./metagoofil.py -d hedefsite.com -t doc,pdf -l 100 -n 50 -o ferdogan`

* -f : Dosyanın çıktı formatını belirler.

    `./metagoofil.py -d hedefsite.com -t doc,pdf -l 100 -n 50 -o ferdogan -f fecassie.html`


Evet şimdi bütün bu bahsettiklerimizi videoda uygulayalım.İyi seyirler...

<iframe src="//player.vimeo.com/video/37350062" width="500" height="375" frameborder="0" allowfullscreen></iframe>
