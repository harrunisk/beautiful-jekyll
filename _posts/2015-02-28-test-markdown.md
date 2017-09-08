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
**pkg-config**  sisteminizde muhtemelen kuruludur ama  **apt-get**  komutu eklendiğinden emin olun. **cmake**  OpenCv'i otomatik olarak derlerken kullanacağız. 
~~~
sudo apt-get install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev
~~~
OpenCv görüntü işleme ve bilgisayar görmesi işlemlerinde kullanılan bir kütüphane. OpenCv' nin JPEG, PNG, TIFF, vb bir sürü resim formatını deskteklemesi gerekiyor.  OpenCv yükleme ve çözme işlerini kolaylaştırması için diğer I/O kütüphanelerini kullanır.Gerekli olanları kuracağız:


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
