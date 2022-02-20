1. Работа c HTTP через телнет.

Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80
отправьте HTTP запрос
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
В ответе укажите полученный HTTP код, что он означает?

```
HTTP/1.0 301 Moved Permanently
Connection: Close
X-Tracking-Ref: <0.13257.2075>
X-Execution-Time: 149853
Cache-Control: no-cache, no-store, must-revalidate
Location: https://stackoverflow.com/questions
x-request-guid: d5354079-28fe-47ed-a70a-54b93ca1413d
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Date: Thu, 17 Feb 2022 08:15:43 GMT
Via: 1.1 varnish
X-Served-By: cache-hhn4029-HHN
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1645085743.173156,VS0,VE78
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=8da06b60-1829-928a-899f-0004fd81e917; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly
```


301 Moved Permanently — запрошенный документ был окончательно перенесен на новый URI, указанный в поле Location заголовка. 

2. Повторите задание 1 в браузере, используя консоль разработчика F12.

откройте вкладку Network
отправьте запрос http://stackoverflow.com
найдите первый ответ HTTP сервера, откройте вкладку Headers
укажите в ответе полученный HTTP код.


```
Request URL: https://stackoverflow.com/
Request Method: GET
Status Code: 200 
Remote Address: 151.101.129.69:443
Referrer Policy: no-referrer-when-downgrade
```

проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?

Страница полностью загрузилась за 1.1 сек. Самый долгий запрос - начальная загрузка страницы 400 мс

приложите скриншот консоли браузера в ответ.

https://github.com/user74445/devops-netology/blob/main/11.JPG

3. Какой IP адрес у вас в интернете?
```
vagrant@vagrant:~$ dig @resolver4.opendns.com myip.opendns.com +short
77.40.61.12
```
4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois
```
vagrant@vagrant:~$ whois 77.40.61.12 | grep ^descr
descr:          xDSL dynamic pools
descr:          Rostelecom networks

vagrant@vagrant:~$ whois 77.40.61.12 | grep ^origin
origin:         AS12389
```
5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
```
vagrant@vagrant:~$ traceroute 8.8.8.8 -I
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2 [*]  0.347 ms  0.312 ms  0.218 ms
 2  192.168.0.1 [*]  2.447 ms  11.446 ms  12.043 ms
 3  100.106.0.1 [*]  13.703 ms  14.297 ms  14.295 ms
 4  188.254.25.244 [AS12389]  27.918 ms  28.603 ms  28.602 ms
 5  87.226.146.174 [AS12389]  14.871 ms  14.870 ms  14.869 ms
 6  87.226.183.89  [AS12389]  28.591 ms  18.339 ms  38.859 ms
 7  5.143.253.245  [AS12389]  22.858 ms  38.977 ms  38.952 ms
 8  108.170.250.130 [AS15169]  29.450 ms  30.231 ms  38.164 ms
 9  209.85.255.136 [AS15169]  55.913 ms  56.424 ms  56.419 ms
10  209.85.254.20 [AS15169]  56.412 ms  56.406 ms  56.372 ms
11  209.85.254.135 [AS15169]  56.364 ms  56.358 ms  56.291 ms
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  8.8.8.8 [AS15169]  35.353 ms  32.115 ms 
```
Пакет проходит через AS - AS12389, AS15169
```
$ grep org-name <(whois AS12389)
org-name:       PJSC Rostelecom
$ grep OrgName <(whois AS15169)
OrgName:        Google LLC
```

6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?
```
vagrant@vagrant:~$ mtr 8.8.8.8 -znrc 1
Start: 2022-02-19T08:59:51+0000
HOST: vagrant                     Loss%   Snt   Last   Avg  Best  Wrst StDev
  1. AS???    10.0.2.2             0.0%     1    0.3   0.3   0.3   0.3   0.0
  2. AS???    192.168.0.1          0.0%     1    1.9   1.9   1.9   1.9   0.0
  3. AS???    100.106.0.1          0.0%     1    1.8   1.8   1.8   1.8   0.0
  4. AS12389  188.254.25.244       0.0%     1    2.5   2.5   2.5   2.5   0.0
  5. AS12389  87.226.146.174       0.0%     1    1.9   1.9   1.9   1.9   0.0
  6. AS12389  87.226.183.89        0.0%     1   15.3  15.3  15.3  15.3   0.0
  7. AS12389  5.143.253.245        0.0%     1   15.8  15.8  15.8  15.8   0.0
  8. AS15169  108.170.250.130      0.0%     1   15.3  15.3  15.3  15.3   0.0
  9. AS15169  209.85.255.136       0.0%     1   28.9  28.9  28.9  28.9   0.0
 10. AS15169  209.85.254.20        0.0%     1   31.9  31.9  31.9  31.9   0.0
 11. AS15169  209.85.254.135       0.0%     1   32.1  32.1  32.1  32.1   0.0
 12. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 13. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 14. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 15. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 16. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 17. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 18. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 19. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 20. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 21. AS15169  8.8.8.8              0.0%     1   31.2  31.2  31.2  31.2   0.0
```
Наибольшая задержка на 11 хопе

7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig
```
vagrant@vagrant:~$ dig +short NS dns.google
ns1.zdns.google.
ns2.zdns.google.
ns3.zdns.google.
ns4.zdns.google.
```
8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig
```
$ for ip in `dig +short A dns.google`; do dig -x $ip | grep ^[0-9].*in-addr; done
8.8.8.8.in-addr.arpa.	18561	IN	PTR	dns.google.
4.4.8.8.in-addr.arpa.	21274	IN	PTR	dns.google.
```

dns.google
