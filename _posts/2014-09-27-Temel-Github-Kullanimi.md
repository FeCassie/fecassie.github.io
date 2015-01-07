---
layout: post
title: "Git Kurulumu ve Kullanımı(Başlangıç)"
author: fatih_erdogan
modified: fatih_erdogan
excerpt: ""
tags: [linux,github]
categories: Linux
---
Merhabalar,
Birkaç arkadaşımın da isteğiyle bugün Git kurulumu ve temel kullanımı hakkında bir yazı yazmaya karar verdim.Platform olarak Linux üzerinde anlattım, ilerleyen yazılarda windows üzerinde kurulumunu da anlatabilirim.Her neyse, belki daha önce duymuş olabilirsiniz, Github yazılımcıların sosyal platformu olarak geçer.Ekip halinde projeler geliştirmeye veya kişisel projelerimizi bize online ortamda barındırma imkanı sağlar.<!--more-->


Neyse fazla uzatmadan başlayalım...

## Terimler ##
* <font color="red">repository (kod deposu): </font>Tüm kodlarımızın barındırıldığı depoya verilen genel isim.Proje dizininizde git init komutu çalıştırılarak o dizinde bir repository oluşturabilirsiniz.Birazdan hepsine değineceğiz.

* <font color="red">commit (taahhüt): </font>Bir dosyada yaptığınız değişikliklerin kalıcı olması için gereken taahhüt.Böylece dosyada yaptığınız değişiklikler repository'nize kaydedilir.

## Kurulum ve Yapılandırma ##

* İlk olarak [github.com](http://github.com/)'a üye olup kendi hesabınızı açmak gerekmekte.Bu kısımda çok zorlanılmayacağını düşündüğüm için çok üstünde durmadan geçiyorum.

* Terminal ekranı açalım ve aşağıdaki komutu yazalım.
	* `sudo apt-get install git-core curl`

  Bu komutla git'i linux sistemimize kurduk.Şimdi sıra global ayarlara geldi.

* Yine aşağıdaki komutları terminalden kendinize göre düzenleyerek sırayla girin.
	* `git config --global user.name "FeCassie"`
	* `git config --global user.email fatiherdogan94@gmail.com`

![ilk adım]({{ site.url }}/assets/git1.png)

* Bu adımda kendi bilgisayarımızı Github'a tanıtmak için ssh anahtarı oluşturuyoruz.Oluşturduğumuz ssh anahtarlarından id_rsa.pub olanı github'da tanıtarak bu adımı da halletmiş oluyoruz.
İlk adımda sorulan soruyu enter'a basarak geçin.Sonrasında bizden parola isteyecek.Bu alanı uygun parolayla doldurun.

	* `ssh-keygen` komutuyla ssh anahtarını oluşturuyoruz.

* Ardından oluşturduğumuz ssh anahtarını kopyalayalım
	
	* `cd ~` komutu ile home dizinine geçiş yapalım.
	
	* `gedit .ssh/id_rsa.pub` komutu ile dosyayı açın ve tümünü seçip kopyalayın.

![id_rsa]({{ site.url }}/assets/git2.png)

* Evet şimdi kopyaladığımız key'i github hesabımıza ekleyelim. "settings" butonuna tıklayın, sonra SSH keys bölümüne girin, daha sonra "Add SSH key" butonuna basarak aşağıda gösterdiğim gibi ssh anahtarınızı ilgili alana kopyalayın.Ardından "Add key" butonuna tıklayarak işlemi tamamlayın.

![git]({{ site.url }}/assets/git3.png)


## Depo Oluşturmak ve Dosya Göndermek ##

* Öncelikle depo oluşturmak için Github'ta Repositories kısmından New butonuna tıklayarak istediğiniz depo ismini girin ve Create repository butonuna tıklayın.

![git]({{ site.url }}/assets/git4.png)	


* Daha sonra terminalden:
	* `mkdir ferdogan`
	* `cd ferdogan`
	* `git init` komutu ile .git dosyalarını oluşturalım.
	* `touch README.md`
	* `git add README.md`
	* `git commit -m "ilk"`
	* `git remote add origin git@github.com:FeCassie/ferdogan.git`
	* `git push origin master`

![git]({{ site.url }}/assets/git5.png)

* Evet şimdi depomuza ilk dosyamızı eklemek için aşağıdakileri yapalım.
	* `cd ferdogan`
	* `ls`
	* `vim mini_program.py`  siz gedit editörünü de kullanabilirsiniz.
	* `git add mini_program.py`
	* `git commit -a -m "ilk programım"`
	* `git push origin master`

![git]({{ site.url }}/assets/git6.png)


* Şimdi hesabınızdan Repositories kısmına gelerek depomuzun oluştuğunu ve içinde minik programımızın olduğunu görebilirsiniz.

![git]({{ site.url }}/assets/git7.png)

![git]({{ site.url }}/assets/git8.png)


Evet bir yazının daha sonuna geldik.Elimden geldiği kadarıyla github kurulumunu, depo oluşturup içine dosya veya projelerimizi göndermeyi anlatmaya çalıştım.Temel seviyede olması nedeniyle depo klonlama, ekip çalışması, pull&push gibi konulara değinmedim.Belki bir başka yazıda o konulara da değinebilirim.

Umarım faydalı olmuştur.Bir başka yazıda görüşmek üzere...

{% highlight bash %}
cd ~/Desktop
{% endhighlight %}

