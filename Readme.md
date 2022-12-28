### Включить использование CLI на адресах
*Settings\Srvices\HTTPU параметр Access\CLI* установить *clients*.  
Указать 127.0.0.1 как клиентский адрес.  

### Включить PKI Services
*Settings\Domain\<NameDomain>\Security\SSL\TLS* параметр *PKI Services* установить в *Enabled*.  

### Скрипт для установки ключа, сертификата и цепочки из LE в CGP
```
#!/bin/sh

# файл ключа
le_key=<path to/le-key.pem
# файл сертификата
le_crt=<path to/le-crt.pem
# файл цепочки 
le_chain_crt=<path to/le-chain-crt.pem

domain_name=<domain_name>
postmaster_name=postmaster@${domain_name}
postmaster_password=<password>

# Готовим ключ
private_secure_key=`openssl rsa -in ${le_key} 2> /dev/null | grep -v '\-\-' | tr -d '\n'`

# Добавляем ключ
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{PrivateSecureKey=[${private_secure_key}];}"

# Готовим сертификат
secure_sertificate=`cat ${le_crt=} | grep -B1000 'BEGIN CERTIFICATE' | grep -B1000 'END ' | grep -v '\-\-' | tr -d '\n'`

# Добавляем сертификат
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{SecureCertificate=[${secure_sertificate}];}"

# Готовим цепочку
le_chain_crt=`grep -v '\-\-' ${le_chain_crt} | tr -d '\n'`
# Добавляем цепочку
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{CAChain=[${le_chain_crt}];}"
```

### Расписание
Добавить в расписание обновление по мере подхода срока

## English
## Enable CLI usage on URLs

Settings\Services\HTTP Access\CLI setting set clients.

Specify 127.0.0.1 as the client address.

### Enable PKI Services

Settings\Domain<NameDomain>\Security\SSL\TLS set the PKI Services parameter to Enabled.

### Script to install key, certificate and chain from LE to CGP

```
#!/bin/sh

# key file
le_key=<path to/le-key.pem
# the certificate file
le_crt=<path to/le-crt.pem
# chain file 
le_chain_crt=<path to/le-chain-crt.pem

domain_name=<domain_name>
postmaster_name=postmaster@${domain_name}
postmaster_password=<password>

# The key is ready
private_secure_key=`openssl rsa -in ${le_key} 2> /dev/null | grep -v '\-\-' | tr -d '\n'`

# Adding a Key
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{PrivateSecureKey=[${private_secure_key}];}"

# Preparing a certificate
secure_sertificate=`cat ${le_crt=} | grep -B1000 'BEGIN CERTIFICATE' | grep -B1000 'END ' | grep -v '\-\-' | tr -d '\n'`

# Adding a certificate
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{SecureCertificate=[${secure_sertificate}];}"

# Cooking the chain
le_chain_crt=`grep -v '\-\-' ${le_chain_crt} | tr -d '\n'`
# Adding a chain
curl -u postmaster:$postmaster_password -k 'https://127.0.0.1:9100/cli/' \
--data-urlencode "command=updatedomainsettings ${domain_name} \
{CAChain=[${le_chain_crt}];}"
```
### Schedule
Add an update to the schedule as the deadline approaches
