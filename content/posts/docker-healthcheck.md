---
title: "Docker Healthcheck"
date: 2020-05-14
draft: false
categories: ["infra", "2020", "devops"]
tags: ["docker"]
---

## Introdução
Enquanto revia alguns assuntos de um curso de docker (Docker Mastery com o Bret Fisher), esbarrei em um tópico que muito me interessou: healthcheck built-in no docker, e é sobre isso que gostaria que escrever um pouco sobre!

A partir da versão 1.12 do docker engine, foi adicionado um recurso de *healthcheck*, que basicamente é uma instrução que diz para o docker como testar o status de um container, bem útil para verificar, por exemplo, se um servidor web está travado em um looping sem receber novas conexões, mas o processo continua executando normalmente.

Esse recurso é suportado tanto em dockerfile, quanto no docker run ou ainda em um compose/stack.

## Como funciona

Ao habilitar esse recurso, podem ser retornados os seguintes status:
* `starting`: container iniciando, ainda não passou pela checagem;
* `healthly`: container passou no check, está 'saudável'
* `unhealthy`: container não passou no check (ou seja, está com algum problema!)

Esses resultados podem ser consultados ao executar o comando `docker container ls` ou `docker inspect` (onde é possível ver os últimos 5 checks).


### No dockerfile
A instrução `HEALTHCHECK` pode ser usada de duas formas:
* `HEALTHCHECK NONE` : healthcheck desabilitado
* `HEALTHCHECK [OPTIONS] CMD command`: healthcheck habilitando executando o `command` dentro do container

```
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
  
# opcoes que podem ser usadas:
#--interval=DURATION (default: 30s)
#--timeout=DURATION (default: 30s)
#--start-period=DURATION (default: 0s)
#--retries=N (default: 3)
```
Saída com o comando `docker container ls`:
```
CONTAINER ID        IMAGE                         COMMAND                  CREATED              STATUS                                                   NAMES
185d2efa35c8        tuxpilgrim/health:1.0         "nginx -g 'daemon of…"   About a minute ago   Up About a minute (healthy)   
```
Saída com o comando `docker container inspect`:
```
"Health": {
                "Status": "healthy",
                "FailingStreak": 0,
                "Log": [
                    {
                        "Start": "2020-05-14T14:55:17.830271812Z",
                        "End": "2020-05-14T14:55:18.136926673Z",
                        "ExitCode": 0,
                        "Output": "  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current\n                                 Dload  Upload   Total   Spent    Left  Speed\n\r  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<!DOCTYPE html>\n<html>\n<head>\n<title>Welcome to nginx!</title>\n<style>\n    body {\n        width: 35em;\n        margin: 0 auto;\n        font-family: Tahoma, Verdana, Arial, sans-serif;\n    }\n</style>\n</head>\n<body>\n<h1>Welcome to nginx!</h1>\n<p>If you see this page, the nginx web server is successfully installed and\nworking. Further configuration is required.</p>\n\n<p>For online documentation and support please refer to\n<a href=\"http://nginx.org/\">nginx.org</a>.<br/>\nCommercial support is available at\n<a href=\"http://nginx.com/\">nginx.com</a>.</p>\n\n<p><em>Thank you for using nginx.</em></p>\n</body>\n</html>\n\r100   612  100   612    0     0  27818      0 --:--:-- --:--:-- --:--:-- 27818\n"
                    },

```
### Na CLI, usando `docker run`

Usando os parâmetros `--health-*`, exemplo com container do *elastic*:

```
docker run \
 -- health-cmd="curl -f localhost:9200/_cluster/health || false" \
 -- helth-interval=5s \
 -- health-retries=3 \
 -- health-timeout=2s \
 -- healt-start-period=15s \
 elasticsearch:2
```

### Em um compose/stack

Usando o bloco de instruções `healthcheck`:

```
version: '3.4' # a versão mínima pra usar o healthcheck é 2.1
services:
 web:
  image: nginx:1.13
  healthcheck:
   test: ["CMD", "curl", "-f", "http://localhost"]
   interval: 1m30s
   timeout: 10s
   retries:3
   start_period:1m # versão mínima para usar é a 3.4
 
```
Caso esse compose seja usado em *swarm mode*, o *swarm* vai tentar recriar as *tasks* com status *unhealth*.


## Conclusão

Um caso de uso bem prático, seria verificar o status de um container executando *PostgreSQL* usando uma ferramenta nativa schamada *pg_isready*:

```
FROM postgres

# especificar o usuário com -U para evitar erros nos logs

HEALTHCHECK --interval=5s --timeout=3s
CMD pg_isready -U postgres || exit 1
```


Algumas observações que são importante ressaltar:
* Esse recurso **NÃO** substitui uma ferramenta de monitoramento, funciona apenas como uma opção extra pra verificar o status do container
* Se for usar o `curl` (por exemplo), verificar se já vem com a imagem que vai usar, ou se precisa instalar 




