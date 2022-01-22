
# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис
  
  - недостаточно кавычек в строке 9 
 1: { "info" : "Sample JSON output from our service\t",
 2:     "elements" :[
 3:         { "name" : "first",
 4:         "type" : "server",
 5:         "ip" : 7175 
 6:         },
 7:         { "name" : "second",
 8:         "type" : "proxy",
 9:         "ip": "71.78.22.43"
10:         }
11:     ]
12: }

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:

```python

import socket as s
import time as t
import datetime as dt
import json
import yaml

# set variables 
i     = 1
wait  = 2 # интервал проверок в секундах
srv   = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init  = 0
fpath = "F:\devops-netology\python\\" #путь к файлам конфигов
flog  = "F:\devops-netology\python\error.log" #путь к файлам логов

# start script workflow
print('*** start script ***')
print(srv)
print('********************')

while 1 == 1 : # для бесконечного цикла, else  установить условие i >= чилу треуемых итераций
  for host in srv:
    is_error = False 
    ip = s.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1: # выведем ошибку, если нет инициализации или есть иниц. и не первый шаг
        is_error=True
        # вывод ошибок в файл
        with open(flog,'a') as fl:
          print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip,file=fl)
        #******************************************
        # решение 4.3 - п2
        #vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
        #  в отдельные файлы
        # json
        with open(fpath+host+".json",'w') as jsf:
          json_data= json.dumps({host:ip})
          jsf.write(json_data) 
        # yaml
        with open(fpath+host+".yaml",'w') as ymf:
          yaml_data= yaml.dump([{host : ip}])
          ymf.write(yaml_data) 
    # в один общий файл     
    if is_error:
      data = []  
      for host in srv:  
        data.append({host:ip})
      with open(fpath+"services_conf.json",'w') as jsf:
        json_data= json.dumps(data)
        jsf.write(json_data)
      with open(fpath+"services_conf.yaml",'w') as ymf:
        yaml_data= yaml.dump(data)
        ymf.write(yaml_data)
        #^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      srv[host]=ip
  i+=1 # счетчик итераций для отладки
  if i >=50 : # число итераций для выхода из цикла для отладки
    break
  t.sleep(wait) # задержка на итерации  

```


### Вывод скрипта при запуске при тестировании:
```
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
0

```

### json-файл(ы), который(е) записал ваш скрипт:
```json

файл drive.google.com.json
{"drive.google.com": "74.125.205.194"}

файл mail.google.com.json
{"mail.google.com": "216.58.210.165"}

файл google.com.json
{"google.com": "216.58.210.142"}

общий файл 
[{"drive.google.com": "216.58.210.142"}, {"mail.google.com": "216.58.210.142"}, {"google.com": "216.58.210.142"}]


```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml

файл drive.google.com.yaml
- drive.google.com: 74.125.205.194

файл mail.google.com.yaml
- mail.google.com: 216.58.210.165

файл google.com.yaml
- google.com: 216.58.210.142

общий файл 

- drive.google.com: 216.58.210.142
- mail.google.com: 216.58.210.142
- google.com: 216.58.210.142

```

