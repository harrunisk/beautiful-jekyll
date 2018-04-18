---
layout: post
title: Elasticsearch'de Wifi Paket Analizi 
subtitle: Tshark Logstash Filebeat Kibana Elasticsearch
---


Tshark'ın Wireshark konsol arayüzü ya da Wireshark'ın tsharkın grafik arayüzü olan hali olduğu söylenebilir. 
Tshark ile yakalanan wireless paketlerinin bazı özelliklerinin analizini Elasticsearch ve araçları yardımı ile yapacağım.
Kullanacağım araçlar Tshark, ElasticSearch ,Kibana, Logstash ve Filebeat olacak. Elasticsearch, Kibana, Logstash ve Filebeat 
kurulumu için [bu link](https://www.elastic.co/products). Ya da docker kullanarak da halledebilirsiniz. Tshark filtreler kullanarak paketleri yakalayacak csv türünde 
dosyaya yazacak. Filebeat dosyayı dinleyecek Logstash'e aktaracak. Logstash burada yeniden filtreleme yapacak ve
Elasticsearch'e aktaracak. Kibana kullanarak analizlerimi yapacağım.
Mimarimiz aşağıdaki şekilde olacak:  
![Architecture](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/ArchitectureBlog.png)  
Wifi paketlerinin analizde kullanacağım filtreler:  

| Filtre        | Açıklama           |   
| ------------- |:-------------:| -----:|
| _ws.col.Time      | Zaman Bilgisi |
| wlan.fc.type      | Çerçeve Türü |   
| wlan.fc.type_subtype      | Çerçeve Alt Türü      |   
| radiotap.dbm_antsignal | Sinyal Gücü(RSSI)      |     
| frame.len      | Çerçeve Boyutu      |   
| radiotap.datarate | Veri Akış Hızı      |   


wlan.fc.type filtresinden gelecek değerler 0, 1, ya da 2 olacak. Bu değerlerin karşılığı:        

| Değer        | Sonuç           |   
| ------------- |:-------------:| -----:|   
| wlan.fc.type==0      | Yönetim Çerçevesi |   
| wlan.fc.type==1      | Kontrol Çerçevesi      |   
| wlan.fc.type==2 | Veri Çerçevesi      |    

wlan.fc.subtype filtresinden gelecek değerler 0 ile 47 arasında değişecek bunlardan bazıları aşağıdaki tabloda kapsamlı olarak [buradan](https://dalewifisec.wordpress.com/2014/04/29/wireshark-802-11-display-filters-2/) ulaşabilirsiniz.  

| Değer        | Sonuç           |   
| ------------- |:-------------:| -----:|   
| wlan.fc.type_subtype==8      | Beacon |   
| wlan.fc.type_subtype==27      | Request To Send      |   
| wlan.fc.type_subtype==28 | Clear To Send      |








