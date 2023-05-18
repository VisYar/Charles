# Charles
Это приложение, которое представляет собой прокси-сервер, позволяет видеть и регулировать все запросы, которые клиент отправляет на сервер. В нашем случае мы говорим о мобильном приложении, поэтому трафик мобилки мы можем отследить только через Charles/Фиддлер.
Пример: как к примеру мне надо будет посмотреть регистрацию нового пользователя мобильного приложения на сервере? Если в вебе можно посмотреть в девтулах (вкладке network), то в мобилке через Charles (посмотреть общение мобильного приложения с сервером по http/https протоколу, посмотреть запросы, json, в правильные url улетают наши запросы.

| Функциональность Charles | Что будем тестированть | Ссылка |
| ------ | ------ | ------ |
| **1. Breakpoint** — это некая пауза для остановки запроса. Когда появляется запрос из списка, для дальнейшего ручного взаимодействия с параметрами запроса открывается отдельное окно Breakpoint. В нём мы можем вручную изменять запросы и ответы, можем abort-ить запросы (то есть отменять, будем получать 503 status). Удобно использовать эту функцию, когда тестируем API или разные ответы сервера. К примеру запустить Postman, взять url запроса оттуда, вставить в breakpoint, запустить запрос Postman (Send) и и данный запрос появится в Чарлике, а в Постмане запрос будет грузиться до тех пор, пока мы не отработам в Чарлике. | Функция похожа на rewrite, но при отправке запроса он останавливается в Чарлике и я могу редактировать его в реальном времени, приложение ждет ответа от сервера. Когда мы выполняем (Execute) запрос, то в мобильном приложении отобразятся данные, которые я поменяла. | link |
| **2. Rewrite** — это инструмент, позволяющий создавать правила, которые изменяют запросы и ответы, когда те проходят через Charles Proxy. Например, можно добавлять и изменять в заголовках (header rules), в пути (URL), в параметрах (query parameter rules) и в теле запроса или ответа (body rules). Помимо подмен можно добавить или удалить новые заголовки, параметры запроса, когда это нужно. Для этого выбираем подходящее для задачи значение из выпадающего списка Тип (Type). | Здесь я использую подмену серверных данных, приходящих в response body. Меняю бонусы с 0 на 100 в приложении JET, имя (логин) не заданы в приложении меняю на "serg", и e-mail не задано меняю на "testing for AIBY" | link |
| **3. Map Local** — можно использовать данную функцию для подмены ответа сервера целиком, то есть позволяет использовать локальные файлы, словно они являются частью сервера. Можно заготовить json файл, который должен якобы являться ответом сервера, указать путь к файлу в настройках, отправить запрос снова и получить ответ сервера исходя из данных этого файла. | Здесь я подменяю полностью ответ от сервера. То есть я заготовил заранее 2 json файла: один из них просто "about me", который не относится к приложению, тем самым ответ от сервера приходит в таком виде, но в приложении ничего не меняется.2й файл - это уже ответ от сервера, где я меняю данные приложения и и в самом приложении также меняются заданные мной данные | link |
| **4. Throttle Settings** — функция, позволяющая задавать разные параметры скорости соединения с выбранным доменом. То есть можно настроить скорость интернета вручную либо выбрать готовые preset в настройках (3G/4G и др.) |  Throttle - это функция, позволяющая задавать разные параметры скорости соединения с выбранным доменом. То есть с помощью это функции я могу протестировать наше приложение как оно будет реагировать при маленьких скоростях интернета (например лифт/метро и т.д). | link |
| **5. No caching** — предотвращает кэширование |   | link |
| **6. Block Cookies** — заголовок cookies удаляется из запросов |   | link |
| **7. Map Remoteg** — позволяет перенаправлять запросы с одного URL на другой |   | link |


# Charles Traffic capture

## Ex_0:

`Protocol: http
IP: 162.55.220.72
Port: 5005`

## Ex_1:
`Method: GET
EndPoint: /get_method
request url params:
 name: str
 age: int`

response:


    [
        “Str”,
        “Str”
    ]

Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
 ⁃ Подменить url в Charles чтобы в запросе ушло имя которые вы вписали в Postman, а вернулось то, которое вы подставили в Charles.
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/get_method</path>
                <query>name=Tata&amp;age=42</query>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>false</active>
              <name>Ex_01</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/get_method</path>
                      <query>name=Tata&amp;age=42</query>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>9</ruleType>
                  <matchHeader>name</matchHeader>
                  <matchValue></matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newHeader>name</newHeader>
                  <newValue>Not Dmitry</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```

## Ex_2:
`Method: POST
EndPoint: /user_info_3
request form data: 
 name: str
 age: int
 salary: int`

    response: 
    {'name': name,
              'age': age,
              'salary': salary,
              'family': {'children': [['Alex', 24], ['Kate', 12]],
                         'u_salary_1_5_year': salary * 4}}

Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
 ⁃ Подменить body в Charles так чтобы в запросе ушла salary которую вы вписали в Postman, а в u_salary_1_5_year цифра вернулась меньше оригинальной из запроса.
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/user_info_3</path>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>false</active>
              <name>Ex_02</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/user_info_3</path>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>7</ruleType>
                  <matchValue>4000</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>true</matchResponse>
                  <newValue>500</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```

## Ex_3:
`Method: GET
EndPoint: /object_info_1
request url params: 
 name: str
 age: int
 weight: int`

response:

    {'name': name,
              'age': age,
              'daily_food': weight * 0.012,
              'daily_sleep': weight * 2.5}

Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
 ⁃ Подменить параметры запроса в Charles так, чтобы в Postman пришел ответ где другое name, daily_food > weight из запроса, а daily_sleep < weight из запроса.
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/object_info_1</path>
                <query>name=Tata&amp;age=42&amp;weight=63</query>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>false</active>
              <name>Ex_03</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/object_info_1</path>
                      <query>name=Tata&amp;age=42&amp;weight=63</query>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>7</ruleType>
                  <matchValue>{&quot;age&quot;:31,&quot;daily_food&quot;:0.756,&quot;daily_sleep&quot;:157.5,&quot;name&quot;:&quot;Dmitry&quot;}</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>true</matchResponse>
                  <newValue>{&quot;age&quot;:31,&quot;daily_food&quot;:65,&quot;daily_sleep&quot;:59,&quot;name&quot;:&quot;NotDmitry&quot;}</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```

## Ex_4:
`Method: GET
EndPoint: /object_info_3
request url params: 
 name: str
 age: int
 salary: int`

response: 


    {'name': name,
              'age': age,
              'salary': salary,
              'family': {'children': [['Alex', 24], ['Kate', 12]],
                         'pets': {'cat':{'name':'Sunny',
                                         'age': 3},
                                  'dog':{'name':'Luky',
                                         'age': 4}},
                         'u_salary_1_5_year': salary * 4}
              }

Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
- Сделать через Charles так, чтобы сервер вернул 500 код.
- Сделать через Charles так, чтобы сервер вернул 405 код.
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/object_info_3</path>
                <query>name=Tata&amp;age=42&amp;salary=1000</query>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>false</active>
              <name>Ex_04</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/object_info_3</path>
                      <query>name=Tata&amp;age=42&amp;salary=1000</query>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>11</ruleType>
                  <matchValue>200 OK</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newValue>500 ERROR</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>1</replaceType>
                </rewriteRule>
                <rewriteRule>
                  <active>false</active>
                  <ruleType>11</ruleType>
                  <matchValue>200 OK</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newValue>405 ERROR</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```

## Ex_5:
`Method: GET
EndPoint: /object_info_4
request url params: 
 name: str
 age: int
 salary: int`

response: 


    {'name': name,
              'age': int(age),
              'salary': [salary, str(salary * 2), str(salary * 3)]}


Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
 ⁃ Сделать через Charles так, чтобы сервер вернул 405 ошибку.
 ⁃ Подменить salary в request
 ⁃ Подменить (salary * 2) в response
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/object_info_4</path>
                <query>name=Tata&amp;age=42&amp;salary=1000</query>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>false</active>
              <name>Ex_05</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/object_info_4</path>
                      <query>name=Tata&amp;age=42&amp;salary=1000</query>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>11</ruleType>
                  <matchValue>200 OK</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newValue>405 ERROR</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>9</ruleType>
                  <matchHeader>salary</matchHeader>
                  <matchValue></matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newHeader>salary</newHeader>
                  <newValue>1111</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>1</replaceType>
                </rewriteRule>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>9</ruleType>
                  <matchHeader>salary</matchHeader>
                  <matchValue>1000</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>false</matchResponse>
                  <newHeader>salary</newHeader>
                  <newValue>2000</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```

## Ex_6:
`Method: POST
EndPoint: /user_info_2
request form data: 
 name: str
 age: int
 salary: int`

response: 


    {'start_qa_salary': salary,
              'qa_salary_after_6_months': salary * 2,
              'qa_salary_after_12_months': salary * 2.7,
              'qa_salary_after_1.5_year': salary * 3.3,
              'qa_salary_after_3.5_years': salary * 3.8,
              'person': {'u_name': [user_name, salary, age],
                         'u_age': age,
                         'u_salary_5_years': salary * 4.2}
              }

Task:
Сделать и в Rewrite, и в BreakPoint (можно отключить чтобы не стопило на каждом запросе)
 ⁃ Сделать через Charles так, чтобы в Postman вернулся ответ, в котором qa_salary_after_1.5_year переименовано в qa_salary_after_1.5_month
 ⁃ Сделать так чтобы qa_salary_after_3.5_years было меньше qa_salary_after_12_months в response
###### breakpoint:
```xml
...
<breakpoint>
              <location>
                <protocol>http</protocol>
                <host>162.55.220.72</host>
                <port>5005</port>
                <path>/user_info_2</path>
              </location>
              <request>false</request>
              <response>true</response>
              <enabled>false</enabled>
</breakpoint>
...
```
###### rewrite:
```xml
...
<rewriteSet>
              <active>true</active>
              <name>Ex_06</name>
              <hosts>
                <locationPatterns>
                  <locationMatch>
                    <location>
                      <protocol>http</protocol>
                      <host>162.55.220.72</host>
                      <port>5005</port>
                      <path>/user_info_2</path>
                    </location>
                    <enabled>true</enabled>
                  </locationMatch>
                </locationPatterns>
              </hosts>
              <rules>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>7</ruleType>
                  <matchValue>qa_salary_after_1.5_year</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>true</matchResponse>
                  <newValue>qa_salary_after_1.5_month</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
                <rewriteRule>
                  <active>true</active>
                  <ruleType>7</ruleType>
                  <matchValue>3800.0</matchValue>
                  <matchHeaderRegex>false</matchHeaderRegex>
                  <matchValueRegex>false</matchValueRegex>
                  <matchRequest>false</matchRequest>
                  <matchResponse>true</matchResponse>
                  <newValue>1111</newValue>
                  <newHeaderRegex>false</newHeaderRegex>
                  <newValueRegex>false</newValueRegex>
                  <matchWholeValue>false</matchWholeValue>
                  <caseSensitive>false</caseSensitive>
                  <replaceType>2</replaceType>
                </rewriteRule>
              </rules>
</rewriteSet>
...
```
