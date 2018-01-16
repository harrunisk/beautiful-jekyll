---
layout: post
title: Ubuntu Ekran Kartı Yükleme ve Isınma Sorunu
subtitle: Sli-Nvidia-Intel-Coolbits
---

Window 10 ve Ubuntu 16.04 LTS beraber kullanırken 128 ssd alanı yeterli gelmediği için Windows 10'u sistemden kaldırarak Ubuntu 16.04 LTS ile devam etmeye karar verdim. İlk iş olarak Nvidia ekran kartı sürücülerini yükledim. Yükleme adımları için değişik türde bir çok kaynak var. Kurulumları adım adım aşağıda anlattım.

## Adım-1 : Sisteminizde Bulunan Kartları Bulun
Aşağıdaki kod parçacığını terminale koplayarak sisteminizde ekran kartı olarak neler olduğunu bulabilirsiniz. Eğer Nvidia ve Intel beraber kullanıyorsanız ve bunu optimize etmek istiyorsanız yazının ileriki bölümünde bunlar var. Eğer benim gibi Sli teknolojisi kullanıyorsanız Sli nasıl optimize edeceğim bunlarda yazının ilerleyen bölümlerinde.

~~~
lspci -k | grep -A 2 -i "VGA"
~~~
![GraphicCards](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/GraphicCards.png)
