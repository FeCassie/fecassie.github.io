---
layout: post
title: "SSH Brute Force Attack via Metasploit"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [backtrack5, kali linux, linux, security, metasploit, ssh]
---

Merhabalar,
Metasploit’teki scanner modülü ile SSH servere yapılan Brute Force saldırısı hakkında bir videolu anlatım yaptım.
Ama önce bazı terimleri konunun yararı açısından incelememizde fayda var.

## SSH Nedir? ##
SSH (Secure Shell) yani Güvenli Kabuk; makinalar arasındaki iletişimi,dosya transferini,
uzak makinalarda komut çalıştırmayı sağlamak için geliştirilmiş güvenli iletşim protokolüdür.

Yani güvensiz kanallar üzerinden güvenli iletişim olanağı sağlar.

Özetle SSH bilgisayar-bilgisayar, bilgisayar-sunucu, sunucu-sunucu bağlantı çeşitleri içerisinde güvenli iletişimi sağlayan bir protokoldür.
Tel-net ile açığa çıkan güvenlik problemlerinin önüne geçmek amacıyla geliştirilmiştir.

## Temel SSH Unsurları Nelerdir? ##

* Authentication = Kimlik Denetimi
* Encryption = Şifreleme
* İntegrity = Bütünlük

SSH çalışma mantığı:

![SSH çalışma mantığı]({{ site.url }}/assets/ssh.jpg)

Eğer SSH şifreniz varsayılan olarak tanımlıysa,şifreyi değiştirmenizi tavsiye ederim.

Hemen bir Terminal(veya Konsol) açıp ;
`passwd` veya `passwd root` komutunu verdikten sonra,
Sizden yeni root şifrenizi girmeniz istenecektir.
Bu şekilde root şifrenizi değiştirebilirsiniz.

Umarım SSH hakkında anlattığım temel bilgiler faydalı olmuştur.
Şimdi Metasploit ile SSH Serverlarına nasıl Brute Force saldırısı yapılır inceleyelebiliriz.

Video şeklinde anlatmayı tercih ettim daha çok faydalı olması için.
İyi seyirler..

<iframe src="//player.vimeo.com/video/37900454" width="500" height="281" frameborder="0" allowfullscreen></iframe>
