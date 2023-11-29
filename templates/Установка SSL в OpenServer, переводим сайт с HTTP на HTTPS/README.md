# Установка SSL в OpenServer, переводим сайт с HTTP на HTTPS

Редактируем файл переписывая данным содержимым OSPanel\modules\http\ВашаВерсияPHP\conf\generate.bat

```
@echo off

set OPENSSL_CONF=%~dp0..\conf\openssl.cnf
..\bin\openssl req -x509 -sha256 -newkey rsa:2048 -nodes -days 5475 -keyout rootCA.key -out rootCA.crt -subj "/CN=cite.by/"
..\bin\openssl req -newkey rsa:2048 -nodes -days 5475 -keyout server.key -out server.csr -subj  "/CN=cite.by/"
..\bin\openssl x509 -req -sha256 -days 5475 -in server.csr -extfile v3.txt -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out server.crt
..\bin\openssl dhparam -out dhparam.pem 2048

```

Вместо `cite.by` подставьте своё название сайта (не обязательно)!

В той же папке OSPanel\modules\http\ВашаВерсияPHP\conf\создаем текстовый файл под названием `v3.txt` c содержимым (в dns прописываем название сайта):

```
nsComment = "Open Server Panel Generated Certificate"

basicConstraints = CA:false

subjectKeyIdentifier = hash

autorityKeyIdentifier = keyid,issuer

keyUsage = nonRepudiation, digitalSignature, keyEncipherment

subjectAltName = @alt_names 

[alt_names]

DNS.1 = site.by
```

Вместо `cite.by` подставьте своё название сайта! Так же можно прописать нужные вам домены на новой строчке по типу:

`
DNS.1 = вашсайт1.ру
DNS.2 = вашсайт2.ру
DNS.3 = вашсайт3.ру
DNS.4 = вашсайт4.ру
`

Запускаем `generate.bat`. Ждем завершения, зависит от производительности компьютера. 

Копируем из той же папки созданные файлы `rootCA.crt`, `rootCA.key`, `rootCA.srl`, `server.key`, `server.cst`, `server.crt`, `dhparam.pem` в "OSPanel\userdata\config\cert_files" c заменой.

ВАЖНО: рекомендую перед заменой файлов сделать резервные файлы которые заменяются.

Так же копируем файл "OSPanel\userdata\config\Apache-ВашаВерсияApache_vhost.conf в папку с нужным вам сайтом "OSPanel\domains\ВашСайт" (Это делать не обязательно, так как они по умолчанию тянутся в папку, где лежит сертификат)

Устанавливаем сертификаты: запускаем файл rootCA.crt и устанавливаем строго в "доверенные корневые центры сертификации" и второй файл server.crt и устанавливаем строго в "личное".

Перезапустить браузер и OSPanel.

P.S. Для тех кто использует CMS (Content management system, к примеру WordPress) учтите при установке на http адрес сайта сохраняется в БД и вам следует обновить в ручную\плагинами адрес сайта на https

