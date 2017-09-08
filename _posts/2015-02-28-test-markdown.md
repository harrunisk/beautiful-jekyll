---
layout: post
title: Ubuntu 16.04'de OpenCv Kurulumu
subtitle: Adım Adım Kurulum
---

Yazıya başlamadan önce Python 2.7 ve Python 3.5.2 Ubuntu 16.04 içinde hazır olarak kurulu olduğunu belirteyim.  
* Python 2.7.12'e konsoldan `Python` komutunu kullanarak ulaşabilirsiniz.  
* Python 3.5.2'e konsoldan `Python3` komutunu kullanarak ulaşabilirsiniz.

Python 2.7 Ubuntu tarafından kullanılan varsayılan versiyon. Python 3'e geçmek gibi planlar var ama bunun için daha zaman var gibi gözüküyor. Bu yazıda OpenCv' nin kurulumu her iki versiyon içinde yapılacak ama sizin hangi versiyon için yapacağınıza karar vermeniz gerekiyor aksi halde bir sürü hata ile karşılaşmanız kaçınılmaz. Ama tavsiye olarak yeni başlıyorsanız Python 3 ile başlamanız daha yararlı olacaktır.  Python versiyonları arasındaki farkları anladığınızda Python 2.7 kodunu Python 3'e taşımak çok da zor değil.

## Adım-1 : OpenCv Bağımlılıklarının Kurulumu:  
Tüm adımlarda konsol kullanacağımız için Ubuntu konsolu açıp öncelikle güncelleme ve  yükseltmelerinizi yapın.  
~~~
sudo apt-get update 
sudo apt-get upgrade
~~~
Bazı geliştirme araçlarını kuralım:
~~~
sudo apt-get install build-essential cmake pkg-config
~~~
`pkg-config`  sisteminizde muhtemelen kuruludur ama  `apt-get`  komutu eklendiğinden emin olun. `cmake`  OpenCv'i otomatik olarak derlerken kullanacağız.   
OpenCv görüntü işleme ve bilgisayar görmesi işlemlerinde kullanılan bir kütüphane. OpenCv' nin JPEG, PNG, TIFF, vb bir sürü resim formatını deskteklemesi gerekiyor.  OpenCv yükleme ve çözme işlerini kolaylaştırması için diğer I/O kütüphanelerini kullanır.Gerekli olanları kuracağız:
~~~
sudo apt-get install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev
~~~
Şu an resimleri yüklemek için gerekli olan kütüphaneleri yükledik. Video ve kameradan gelen veri akışları için kütüphaneleri yükleyelim:
~~~
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev 
sudo apt-get install libxvidcore-dev libx264-dev
~~~
OpenCv kısıtlı sayıda GUI(Graphical User Interface) aracı ile geliyor. Bunlardan bazıları ekranımızda bir resmi görüntülemeye yarayan (`cv2.imshow`),  tuş basışlarını beklemek için (`cv2.waitkey`), fare hareketlerini kaydetmek için (`cv2.setMouseCallback`) ve diğer kaydırma butonu gibi basit GUI elemanları. OpenCv ile çok kapsamlı GUI uygulamaları yapmayı beklemeyin sadece basit uygulamalar ve kodumuzu debug etmek için kullanacağız.  

OpenCv'nin GUI işlerini halleden modül (`highgui`) GTK  kütüphanesine dayanır, bu kütüphaneyi aşağıdaki komut ile kuracağız:
~~~
sudo apt-get install libgtk-3-dev
~~~
Daha sonra OpenCv içinde değişik fonksiyonları (matris işlemleri vb.) optimize eden kütüphaneleri kuracağız:  
~~~
sudo apt-get install libatlas-base-dev gfortran
~~~
İlk adımı her iki Python sürümünün geliştirme başlıklarını ve kütüphanelerini kurarak bitireceğiz:
~~~
sudo apt-get install python2.7-dev python3.5-dev
~~~
Bu başlıkları ve kütüphaneleri kurmadığınız taktirde 4.adımda  `cmake`  Python derleyicileri ve kütüphaneleri için otomatik olarak uygun değerleri atayamayacak.
## Adım-2:OpenCv Kaynak Kodu İndir
Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](http://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
