# Projeto pet clinic

##Task 1:

antes de iniciarmos precisamos preparar o ambiente instalando softwares que vamos utilizar, abaixo a lista necessaria:
Git, VisualStudio Code, Docker.

## Rodando o petclinic local

antes de iniciarmos precisamos preparar o ambiente instalando softwares que vamos utilizar, abaixo a lista necessaria:
Git, Visual Studio Code, Docker.


```
1- Utilizando o Visual Studio Code vamos abrir o terminal dentro do programa ou podemos abrir um terminal no windows, linux ou MacOS.
vamos navegar ate o caminho onde queremos realizar o clone do repositorio.

2- Vamos realizar o clone do repositorio no github com o comando:

git clone https://github.com/spring-projects/spring-petclinic

3- após realizarmos o clone vamos testar localmente o funcionamento:
  3.1 - vamos navegar até a pasta onde clonamos o projeto
  3.2 - dentro da pasta vamos rodar o comando:
  mvnw package
  3.3 - por ultimo caso não funcione você pode executar usando o maven com o comando:
  mvnw spring-boot:run

```

Após isso vc deve conseguir abrir o projeto através do: http://localhost:8080

##Criando uma imagem docker do nosso aplicativo
1- criaremos um arquivo docker file:
```
#Build PetAPP
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
EXPOSE 8080
```
2- depois que criarmos o docker file vamos buildar essa imagem local do nosso petclinic para o docker com o comando:
```
docker build -t <nomedaimagemquevocequer>:tagquevocequiser .
```
após rodar este comando teremos uma imagem local no docker e precisaremos testar.
```
docker run --rm -it -p 8080:8080 <nomedaimagem>:tag
```
obs: o banco de dados tem que ser o banco local instalado no host pois não há comunicação com o docker, só após a página abrir no localhost:8080 que iremos subir as imagens para o docker.

a parte de testes locais foi finalizada

## Agora vamos adicionar informações ao docker compose para automatizarmos o processo manual e criar as instancias no docker.



## Configuração do docker compose
### nesta etapa o arquivo docker compose automatizará a criação das duas instancias para o docker, ao final desta parte o arquivo docker compose podera ser utilizado para criação das instancias de forma automatizada.

1- criando o arquivo docker compose que irá conter nossa imagem que fizemos a build nos passos anteriores e a crianção de uma instancia Mysql:
```
version: "3"

services:
      
  petclinic:
    image: xisplico/petclinic:webapp 
    restart: always
    ports:
      - 8080:8080
    environment:
      - MYSQL_ROOT_PASSWORD=senhadoroot
      - MYSQL_USER=petclinic
      - MYSQL_PASSWORD=petclinic
      - MYSQL_DATABASE=petclinic
      - MYSQL_TCP_PORT=4407
    networks:
      - net-backend
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=
      - MYSQL_ALLOW_EMPTY_PASSWORD=true
      - MYSQL_USER=petclinic
      - MYSQL_PASSWORD=petclinic
      - MYSQL_DATABASE=petclinic
    volumes:
      - "./conf.d:/etc/mysql/conf.d:ro"
    networks:
      - net-backend

networks: 
  net-backend:
```
2- feito isso vamos rodar o comando:
docker-compose up 

este comando irá ler o arquivo docker compose e executar as instruções para criação das instancias e configuração definidas no arquivo.

3- em nossa máquina vamos digitar:
http://localhost:8080

se a aplicação aparecer esta tudo certo.

##Enviando a imagem para o docker hub.

1- criamos um repositorio no docker hub

2- rodamos o comando:
```
docker tag <nomedorepolocal>:tag <nomedorepodockerhub>:tag
```
3- para enviar a imagem para nosso repositorio remoto rodamos o comando:
```
docker push <nomedorepodockerhub>:tag
```
