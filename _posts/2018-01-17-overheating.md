---
layout: post
title: Ubuntu 16.04 TLS Isınma Sorunu
subtitle: Nvidia-Coolbits
---

## Isınma Sorunu
Sürücüleri yüklemeden önce ısınma sorunu yaşıyordum. Nvidia sürücüleri yükledikten sonra da ısınma sorunum devam ediyordu Windows kullanırken browserda gezinirken anakart üzerinde olan ekran kartımın sıcaklığının 50°C geçtiğini pek görmedim ama Ubuntu üzerinde browser açtığım zaman fan gürültülü bir çekilde çalışmaya başlıyor sıcaklık 65°C  ile 75°C  arasında değişiyor. Daha sonra biraz araştırınca bunun PowerMizerin ekran kartını basit işlerde bile yüksek performansa zorlamasından kaynaklandığını anladım.Nvidia sürücülerinin kurulumundan sonra hiçbir optimizasyon yapmadan sıcaklık değerim ve genelde kullanılan performans seviyesi aşağıdaki resimlerdeki gibi.

![TemperatureBefore](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/TemperatureBefore2.png)

![PerformanceBefore](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/PerformanceBefore2.png)


Sıcaklıklar böyle olunca [X Server](https://www.x.org/archive/current/doc/man/man5/xorg.conf.5.xhtml) ayarlarını değiştirmek gerekti. Ayarları değiştireceğimiz konfigürasyon dosyasını `/etc/X11/xorg.conf` şeklinde bulabilirsiniz eğer bu dosya oluşturulmamışsa terminale `nvidia-xconfig` yazarak konfigurasyonları içeren dosyayı otomatik olarak oluşturabilirsiniz. `xorg.conf` oluşturulması ile ilgili işinize yarayacak bir [kaynak](https://askubuntu.com/questions/217758/how-to-make-an-xorg-conf-file). 
~~~
Option         "RegistryDwords" "PowerMizerEnable=0x1; PowerMizerDefaultAC=0x3;"
~~~
Üstteki satırı `/etc/X11/xorg.conf` dosyamda "Screen" sekmesine ekleyeceğim. Isterseniz "Device" sekmesine de ekleyebilirsiniz. Bu şekilde yapan kaynak [sayfam](http://z-issue.com/wp/nvidia-linux-drivers-powermizer-coolbits-performance-levels-and-gpu-fan-settings/).
~~~
Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
    Monitor        "Monitor0"
    DefaultDepth    24
    Option         "RegistryDwords" "PowerMizerEnable=0x1; PowerMizerDefaultAC=0x3;"
    SubSection     "Display"
        Depth       24
    EndSubSection
~~~    
satırı ekledikten sonra "Screen" bölümünün son hali bu şekilde oldu. Bu satır bende ısınma sorununu çözdü. Daha ayrıntılı olarak [Linux NVIDIA Sürücüleri](http://us.download.nvidia.com/XFree86/Linux-x86_64/364.19/README/index.html) ve [konfigürasyonlar](http://us.download.nvidia.com/XFree86/Linux-x86_64/364.19/README/xconfigoptions.html).Tabi ekran kartının performansının düşük seviyede tutulması kartı zorlayan işlemlerde işinizi görmeyecektir eklediğimiz satırı kaldırarak ya da xorg ayarlarını biraz daha karıştırarak kendinize uygun bir optimizasyon yapabilirsiniz.
Optimizasyon sonrası sıcaklık ve performans değerlerim.

![TemperatureBefore](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/TemperatureAfter.png)

![PerformanceBefore](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/PerformanceAfter.png)

GPU fan ayarları, frekans değerleri ve voltaj artırımlası ile ilgili [kısa bir yazı.](http://z-issue.com/wp/nvidia-linux-drivers-powermizer-coolbits-performance-levels-and-gpu-fan-settings/) 
