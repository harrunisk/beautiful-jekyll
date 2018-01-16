---
layout: post
title: Ubuntu Ekran Kartı Yükleme, Çift Ekran Kartı Ayarları ve SLI
subtitle: Sli-Nvidia-Intel-Coolbits-Overlock
---

Window 10 ve Ubuntu 16.04 LTS beraber kullanırken 128 ssd alanı yeterli gelmediği için Windows 10'u sistemden kaldırarak Ubuntu 16.04 LTS ile devam etmeye karar verdim. İlk iş olarak Nvidia ekran kartı sürücülerini yükledim. Yükleme adımları için değişik türde bir çok kaynak var. Kurulumları adım adım aşağıda anlattım.

## Adım-1 : Sisteminizde Bulunan Kartları Bulun
Aşağıdaki kod parçacığını terminale koplayarak sisteminizde ekran kartı olarak neler olduğunu bulabilirsiniz. Eğer Nvidia ve Intel beraber kullanıyorsanız ve bunu optimize etmek istiyorsanız yazının ileriki bölümünde bunlar var. Eğer benim gibi Sli teknolojisi kullanıyorsanız Sli nasıl optimize edeceğim bunlarda yazının ilerleyen bölümlerinde.

~~~
lspci -k | grep -A 2 -i "VGA"
~~~
![GraphicCards](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/GraphicCards.png)

Sistemimde bir tane anakart üzerine yerleştirilmiş GeForce GT 650M ve UltraBuy teknolojisi ile harici bir tane daha GeForce GT 650M var oyunlarda 2 tane ekran kartının olması size 2 kat performans vermiyor ama Sli destekleyen oyunlarda 1.6 kat kadar performans artışı veriyor o da GeForce GT 765M ile hemen hemen aynı performansı veriyor. Tabi şu an için çok eski bir ekran kartı Nvidia'nın 1000 serisi yada Amd'nin yeni nesil kartları kartları ile  gerek mimari gerek enerji tüketimi olsun karşılaştırılamaz.
## Adım-2 : Nvidia Sürücülerinin Kurulması
### Yöntem-1

Ubuntuyu kurduğunuz zaman açık kaynak olan Nouveau sürücüsü ile geliyor ama bu sürücüden tam verim alamıyorsunuz. Bu yüzden uygun sürücüleri kurmak için `sofware-properties-gtk`  kuracağız.Kurmak için terminale:
~~~
sofware-properties-gtk
~~~
Aslında bu da otomatik olarak geliyor Windows tuşuna basıp `Additional Drivers` yazıp bakarsanız `Software & Updates` içinde  sekmeye erişebilirsiniz. Aşağıdaki gibi bir ekranla karşılaşınca daha önce herhangi bir driver yüklemediyseniz sizde `Using X.Org X server--Nouveau display driver from xserver-xorg-videonouveau(open source)` seçili olarak gelmesi gerekir. Buradan Nvidia'nın son sürümünü seçip Apply Changes'a tıklarsanız sizin için otomatik olarak sürücüyü yükleyecektir. Yüklemeden sonra yeniden başlatmanız gerekebilir.

![AdditionalDrivers](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/AdditionalDrivers.png)

### Yöntem-2

Terminali açıp aşağıdaki kodu işletirseniz sisteminiz için en uygun grafik sürücüsünü size verecektir.
 ~~~
 sudo ubuntu-drivers devices
~~~
![Recommended](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/Recommended.png)

Tavsiye edilen sürümü `apt-get` ile terminalden de yükleyebilirsiniz.
 ~~~
sudo apt-get install nvidia-390
~~~

Yüklemeden sonra `Additional Drivers` sekmesine geldiğiniz zaman terminalden yüklediğiniz sürücünün seçili olması lazım eğer seçili değilse yüklediğiniz sürücüyü seçerek `Apply Changes` demeniz yeterli.
## Nvidia Sürücülerinin Kaldırılması

Sürücülerde bazen konfigurasyonlar yaparken hatalar ile karşılaşabiliyoruz aşağıdaki kod ile  sürücüleri tamamen kaldırabiliriz:
 ~~~
sudo apt purge nvidia-*
~~~

## Nvidia-Intel Grafik Kartlarından Birinin Seçimi

Sürücüyü yükledikten sonra bilgisayarı yeniden başlatınca Nvidia ayarlar konsoluna erişmek için windows tuşuna basıp `NVIDIA X Server Settings` yazarak ya da terminale
 ~~~
 nvidia-settings
~~~
yazarak ayarlara erişebiliriz. Burada açılan uygulamada yandaki seçmelerden 'PRIME PROFILES' sekmesinden Nvidia ya da Intel Kartlarından birisini seçebilirsiniz.

Terminalden Intel kartı seçmek için:
 ~~~
 sudo prime-select intel
~~~

Terminalden Nvidia kartı seçmek için:

 ~~~
 sudo prime-select nvidia
~~~

Hangi kartın seçili olduğunu görmek için terminale aşağıdaki kod parçacığını yazarak erişebilirsiniz:
 ~~~
 sudo prime-select query 
~~~

## Nvidia Sli Aktif Edilmesi
Sli teknolojisi kullanan arkadaşlar oyunlarda verim almak için Sli'ı aktif etmeleri gerekiyor bunun için aşağıdaki kod satırı yeterli:
 ~~~
sudo nvidia-xconfig --sli=On
~~~



