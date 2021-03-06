# php-apache-nodejs:7.4-debian

<p align="center">
	<img alt="logo-docker" class="avatar rounded-2" height="150" src="https://avatars2.githubusercontent.com/u/35675959?s=400&u=b1f9ebca6fa8e5be55cb524e16f38b52f2f1dd58&v=4" width="160">
	<br>
	Travis-CI<br>
	<a href="https://travis-ci.org/docker-sources/php-apache-nodejs">
		<img src="https://travis-ci.org/docker-sources/php-apache-nodejs.svg?branch=master" alt="Build Status">
	</a>
</p>

Essa é uma imagem **docker** criada para start simplificado de container PHP + Apache + Nodejs.

As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" presentes em qualquer parte deste repositório devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Imagens disponíveis

Consulte a guia [Tags](https://hub.docker.com/r/fabiojanio/php-apache-nodejs/tags/) no repositório deste projeto no **Docker Hub** para ter acesso a outras versões.

## Pacotes presentes na imagem

 - Apache 2.4.38
 - PHP 7.4.*
 - Composer
 - Node.js 12.*
 - NPM 6.*
 - curl
 - unzip

 > Obs: as versões citadas acima são referentes a compilação atual desta imagem. O script de *build* foi criado de modo a permitir atualizações menores (*minor*) de forma transparente a cada nova *build*.

## Módulos PHP ativos

Lista de módulos ativos presentes na imagem:

 - Core
 - ctype
 - curl
 - date
 - dom
 - exif
 - fileinfo
 - filter
 - ftp
 - gd
 - hash
 - iconv
 - intl
 - json
 - libxml
 - mbstring
 - mysqli
 - mysqlnd
 - openssl
 - pcre
 - PDO
 - pdo_mysql
 - pdo_pgsql
 - pdo_sqlite
 - pgsql
 - Phar
 - posix
 - readline
 - Reflection
 - session
 - SimpleXML
 - soap
 - sockets
 - SPL
 - sqlite3
 - standard
 - tokenizer
 - xml
 - xmlreader
 - xmlwriter
 - Zend OPcache
 - zlib

## Considerações relevantes

 - Porta **80** exposta
 - **php.ini** limpo e definido como *dev* ou *prod*
 - **TIPO_AMBIENTE** define o tipo de container (*dev* ou *prod*)

A variável de ambiente **TIPO_AMBIENTE** PODE assumir o valor **prod** ou **dev**, por padrão assume **prod**. Com base nessa atribuição de valor, flags de erro do PHP e definições especificas do Apache serão aplicadas, consulte o diretório [**config**](https://github.com/docker-sources/php-apache-nodejs/tree/master/config).

**Sugestão**: Você PODE utilizar um volume compartilhado para os arquivos de **VirtualHost** do Apache, essa abordagem pode ser bem vinda em ambiente de desenvolvimento, onde nem sempre faz sentido ter multiplus containers. Para um exemplo prático consultei o arquivo [**docker-compose.yml**](https://github.com/docker-sources/php-apache-nodejs/blob/master/docker-compose.yml).

**ATENÇÃO**: no modo de produção (`TIPO_AMBIENTE=prod`), para otimização de desempenho o recurso `opcache.validate_timestamps` foi definido com valor `= 0`, ou seja, se algum arquivo for alterado este não será revalidado pelo opcache, sendo assim o usuário continuará vendo a versão anterior do arquivo. Para resolver isso você pode agir de duas formas, veja as soluçõe apresentadas no tópico seguinte.

### Revalidar o cache

#### Solução 1

Edite o arquivo: `/usr/local/etc/php/php.ini`
Altere: `opcache.validate_timestamps` de `0` para `1` e salve o arquivo
Recarregue a configuração do apache: `/etc/init.d/apache2 reload`

#### Solução 2

Crie um arquivo php, como por exemplo `opcache-clear.php` e adicione este código a ele:

```
<?php echo opcache_reset(); ?>
```

Agora basta chamar o arquivo no navegador e o cache será esvaziado.

## Start container

Execute essa instrução para montar um volume compartilhado entre *host* e *container*:

```
docker run -v /projeto:/var/www -d -p 80:80 --name nome_do_container fabiojanio/php-apache-nodejs:7.4-debian
```

**Obs**: no lugar de */projeto* você DEVE informar o caminho absoluto do diretório a ser compartilhado com o container.

Opcionalmente você PODE definir a variável **TIPO_AMBIENTE** como **dev**:

```
docker run -v /projeto:/var/www -d -p 80:80 -e TIPO_AMBIENTE=dev --name nome_do_container fabiojanio/php-apache-nodejs
```

Ao omitir **TIPO_AMBIENTE** na criação do container o valor **prod** será automaticamente atribuido.

Após a criação do container é possível se conectar a ele desta forma:

```
docker exec -it nome_do_container /bin/bash
```

## docker-compose.yml

Para subir o ambiente utilizando o docker-compose, efetue o download do arquivo [**docker-compose.yml**](https://github.com/docker-sources/php-apache-nodejs/blob/master/docker-compose.yml), acesse o diretório onde o arquivo foi baixado e execute:

```
docker-compose up -d
```

Neste arquivo os containers estão nomeados como **web** e **mysql**. Para se conectar:

```
docker exec -it nome_do_container /bin/bash
```

## Build (opcional)

Os passos anteriores estão configurados para utilizar a imagem já compilada disponível no **Docker Hub**, entretanto, caso queira compilar sua própria imagem, basta efetuar o download do arquivo [**Dockerfile**](https://github.com/docker-sources/php-apache-nodejs/blob/master/Dockerfile) e executar a instrução:

```
docker build -t nome_da_nova_imagem:nome_da_tag .
```

Posteriormente pode criar o container executando:

```
docker run -v /projeto:/var/www -d -p 80:80 --name nome_do_container nome_da_nova_imagem:nome_da_tag
```

E para conectar ao container executando:

```
docker exec -it nome_do_container /bin/bash
```

## Licença MIT

Para maiores informações, leia o arquivo de [licença](https://github.com/docker-sources/php-apache-nodejs/blob/master/LICENSE) disponível neste repositório.