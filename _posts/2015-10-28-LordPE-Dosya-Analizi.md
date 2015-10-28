# LordPE ile Dosya Analizi  #

Merhaba,
Bu yazıda LordPE ve strings araçlarını kullanarak, basit bir şekilde çalışan bir uygulamayı analiz ederek bilgi edinmeye çalışacağız.
<!--more-->

Bir önceki yazıda UPX ile Manual Unpack etmeyi incelemiştik.Dolayısıyla Packing-Packed gibi kavramlara da değinmiştik.

Zararlı yazılımlar geliştirilirken genellikle packlenerek veya kaynak kodu obfuscated edilerek statik analiz zorlaştırılır.Bunu çözümlemek için, zararlı yazılımı çalıştırmak ve yarattığı RAM görüntüsünü analiz etmek gerekir.(Ram görüntüsünden kastettiğimiz şey sisteme verdiği zarar ve yüklediği yük anlamında.)
Analiz edeceğimiz program message.exe.
Açılışta bizden parola istiyor.Yanlış parola girdiğimizde Fail! çıktısı veriyor.

![L1](http://ferdogan.net/assets/L1.png)

DIE ile inceleyelim.

![L2](http://ferdogan.net/assets/L2.png)

Program MPRESS ile packlenmiş.Bu yazıdaki amacımız unpack etmek olmadığı için bu konuya değinmiyorum.
Programın içindeki kelimelere bakalım.Bunun için SysInternal aracı olan strings.exe'den yararlanacağız.

![L3](http://ferdogan.net/assets/L3.png)

Programda bizden istenen parolaya dair anlamlı bir string'e rastlamadık.Hatta "Enter the password:" yazısına bile rastlamadık.
Bu durum bize programın RAM içinde çalışan message.exe olmasa bile okunabilir stringlerin olduğunu düşündürmeli.

LordPE ile programın RAM'deki imajını dump edelim. 

![L4](http://ferdogan.net/assets/L4.png)

dump full'e tıklayarak dump alalım.Programın olduğu dizede dumped.exe oluşmuş olması lazım.
Ardından aynı şekilde dumped.exe nin içindeki stringlere strings.exe aracı ile bakalım.

![L5](http://ferdogan.net/assets/L5.png)

Hemen gözünüze çarpmıştır, parola açık bir şekilde önümüzde :)

![L6](http://ferdogan.net/assets/L6.png)

Gördüğünüz gibi "Password correct!" mesajına başarılı bir şekilde ulaştık.

Başka bir yazıda görüşmek üzere.