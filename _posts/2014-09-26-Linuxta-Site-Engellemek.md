---
layout: post
title: "Linux'ta Site Engellemek"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [linux]
categories: Linux
---
Merhaba,
Linux'ta belli web sitelerine giriş yapılmasını engelleyebilirsiniz.Bu tarz bir şeyi sanırım sunucularda kullanırsınız veya kişisel bilgisayarınızdan da belli sitelere giriş yapılmasını engelleyebilirsiniz :)

Varsayalım ki engellemek istediğiniz site benim blogum olsun(umarım böyle bir şey olmaz) :) 
Yani sitenin adı ferdogan.net

`sudo vim /etc/hosts` komutuyla hosts dosyasını açıp içine site ismi ve IP adresi eklememiz gerekiyor.

`0.0.0.0 ferdogan.net`

bu satırı ekleyip dosyayı kaydettikten sonra ferdogan.net adresine girmeyi deneyin.
Bağlantı hatası verdiğini göreceksiniz, çünkü ferdogan.net için IP adresini 0.0.0.0 olarak tanımladık.Böyle bir IP adresi yok, bu yüzden bağlantı hatası çıktı karşımıza yani bir nevi engellemiş olduk.

Ayarları tekrar eski haline döndürmek istiyorsanız yine aynı şekilde hosts dosyasının içerisine girip ilgili satırı silebilirsiniz.