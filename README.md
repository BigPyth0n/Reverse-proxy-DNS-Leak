# Reverse-proxy-DNS-Leak
> برای مدیریت دو سرور داخلی و خارجی و تانل زدن بین اونها و همچنین مشکل DNS Leak

> در مرحله اول
یه کلود از ابر آروان تهیه کردم و روی اون بعد از اپدیت و اپگرید پنل ثنایی رو نصب کردم به   [آدرس](https://github.com/MHSanaei/3x-ui) و بعد از نصب یه زیر دامنه برای اون تنظیم کردم تا بتونم SSL رو روش نصب کنم بعد از نصب و کانفیگ مسیر پنل و نصب SSL و استفاده از توکن امنیتی برای ورود شروع
کردم به تنظیم برای تانلینگ :
الف -  از قسمت ورودی ها یه ورودی ساختم به نام portal-turkie و پروتکل vless و پورت مثلا 38541 و راه اتصال رو TCP گذاشتم و TLS رو فعال کردم و uTLS رو گذاشتم chrome و استفاده از گواهی پنل رو زدم و همچنین SNI رو گذاشتم همون زیر نام زیر دامنه ای که براش گرفتم arvanvps.hisec.eu.org و افزودن یه ورودی دیگه لازم داریم برای مدیریت کاربران با نام bigpython-turkie پروتکل vless و پورت 39638 با اتصال TCP و همچنین TSL با SNI arvanvps.hisec.eu.org و uTLS chrome و استفاده از گواهی پنل و افزودن به تب پیکربندی ایکس ری میریم و قسمت معکوس و افزودن معکوس نوع رو میزاریم پورتال برچسب رو میزاریم reverse-turkie و دامنه رو میزاریم روی turkie.xui در قسمت اتصال میانی تیک inbound-38541 رو میزنیم و در قسمت ورودی تیک inbound-39638 رو میزنیم و دکمه افزودن معکوس و ذخیره و ریستارت پنل در قسمت ورودی ها دو تا ورودی داریم الان اونی که اسمش رو گذاشتیم portal-turkie رو باز میکنیم و از قسمت کیوآر کانفیگ رو کپی میکنیم.
  

> در محله دوم
یه سرور مجازی از ترکیه گرفتم با احتساب اینکه فیلتر نباشه و لوکیشن هم دقیق در تست ها ترکیه رو نشون بده و اون رو هم همونطور مثل سرور بالا مراحل نصب تا پنل و گواهی و غیره رو رفتم
 1. در قسمت پیکربندی ایکس ری در تب خروجی ها دکمه افزودن خروجی رو میزنیم در پنچره باز شده اون کانفیگی رو که از سرور ایران و ورودی portal-turkie گرفته بودیم رو در تب JSON در قسمت Link پیسن میکنیم و دکمه ادیت رو میزنیم و تنظیمات در پنچره زیرش نمایش داده میشود و در تب Form دکمه افزودن خروجی رو میزنیم
2. حالا مجدد در تب معکوس افزودن معکوس رو میزنیم نوع رو پل انتخاب میکنیم و برچسب رو reverse-turkie و دامنه رو turkie.xui (نکته: دامنه ها حتما باید یکی باشند) و اتصال میانی رو اون کانفیگی که ساخته شده رو انتخاب میکنیم در اینجا با نام portal-tulie-ny4evdpim بود و خروجی رو از نوع direct و افزودن معکوس و ذخیره و ریستارت پنل در اینجا کارمون با پنل ترکیه تموم شد.

> در مرحله سوم
در پنل ایران از قسمت ورودی bigpython-turkie رو انتخاب میکنیم و میتونم کاربر اضافه کنم و در کلاینت استفاده کنم

> در محله چهارم که مربوط به DNS Leak است این مراحل رو در سرور مجازی ترکیه رفتم
 1. اول با دستور ```nano  /etc/hosts``` مطمئن شدم که هاست سرور مجازی در این فایل است یا خیر اگر نباشد خطای ```sudo: unable to resolve host 7mnrckrh.vm: Name or service not known``` رو میده بنابراین این نام دامنه رو بهش اضافه کردم که شد
```
127.0.0.1 localhost
127.0.1.1 ubuntu
127.0.0.1 7mnrckrh.vm
# The following lines are desirable for IPv6 capable hosts'
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
و ذخیره کردم

 2. حالا مراحل نصب resolvconf رو پیش میریم با دستور 
```
sudo apt install resolvconf
```

   3) با دستور زیر resolvconf را استارت می کنیم.
```sudo systemctl start resolvconf.service```
```sudo systemctl enable resolvconf.service```
   4) با دستور زیر فایل resolv.conf.d/head را ویرایش کرده و  dns های مورد نظر را وارد می کنیم.
```sudo nano /etc/resolvconf/resolv.conf.d/head```
   5) در صفحه باز شده dns ها را وارد کردم
```   
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
# 127.0.0.53 is the systemd-resolved stub resolver.
# run "systemd-resolve --status" to see details about the actual nameservers.
nameserver 78.189.87.241
nameserver 77.223.128.222
nameserver 1.1.1.1
nameserver 8.8.4.4
```
 6. با دستورهای زیر dns ها را در resolvconf آپدیت می کنیم
``sudo resolvconf --enable-updates ``
``sudo resolvconf -u``
 7.  سرویس resolvconf را مجددا راه اندازی میکنیم
```sudo systemctl restart resolvconf.service```
```sudo systemctl restart systemd-resolved.service```


 8.  با دستور زیر dns ها را چک میکنیم
```cat /etc/resolv.conf```
که خروجی اون میشه این :
```
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
# 127.0.0.53 is the systemd-resolved stub resolver.
# run "systemd-resolve --status" to see details about the actual nameservers.
nameserver 78.189.87.241
nameserver 77.223.128.222
nameserver 1.1.1.1
nameserver 8.8.4.4
nameserver 127.0.0.1
search 7mnrckrh.vm
```






[منابع](https://www.youtube.com/watch?v=3p_rP1XPOPw)/n
[منابع](https://www.youtube.com/watch?v=jCg6Yci7a5U)


   
