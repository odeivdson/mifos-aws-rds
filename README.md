# Tomcat7 + Mifos 18.0.3 + RDS

__Requisitos Tomcat:__
- java 8
- tomcat 7
- ubuntu 14
- mysql 5.5



### 1 - Instale o Java no servidor:

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python-software-properties 
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:webupd8team/java 
sudo apt-get update 
sudo apt-get install oracle-java8-installer
```

2 - Faça o setup de seu AWS RDS

3 - Crie as bases de dados 
```bash
mysql -uUserRDS -p -h HostRDS
create database `mifosplatform-tenants`;
create database `mifostenant-default`;
exit 
```

4 - Faça upload do repositório para o diretório /usr/share/

```bash
cd /usr/share
git clone https://github.com/odeivdson/mifos-aws-rds.git
```

5 - Carregue os dados iniciais nas bases de dados  `mifosplatform-tenants` e `mifostenant-default`

    5.1 - Edite o arquivo /usr/share/tomcat7/conf/mifosplatform-tenants.sql (linha 94) e ajuste as credenciais de acesso ao banco de dados RDS.
    6.2 - Edite o arquivo /usr/share/tomcat7/conf/mifostenant-default.sql (linha 285) e insira seu UserRDS

```bash
mysqldump -uUserRDS -p -h HostRDS mifosplatform-tenants < /usr/share/tomcat7/conf/mifosplatform-tenants.sql
mysqldump -uUserRDS -p -h HostRDS mifostenant-default < /usr/share/tomcat7/conf/mifostenant-default.sql

rm /usr/share/tomcat7/conf/mifosplatform-tenants.sql /usr/share/tomcat7/conf/mifostenant-default.sql
```

7 - Edite o arquivo /usr/share/tomcat7/conf/server.xml (linhas 20,21 e 22) e acrescentes suas credenciais RDS.

8 - Crie o certificado SSL local (senha xyx123, caso escolha uma senha diferente, será necessário ajustar o arquivo server.xml)

```bash
sudo keytool -genkey -keyalg RSA -alias tomcat -keystore /usr/share/tomcat.keystore
```

9 - Mova o arquivo de inicialização do tomcat para o diretorio  /etc/init.d/ e ajuste as permissões de acesso

```bash
mv /usr/share/conf/tomcat7 /etc/init.d/

sudo chmod 755 /etc/init.d/tomcat7

sudo ln -s /etc/init.d/tomcat7 /etc/rc1.d/K99tomcat
sudo ln -s /etc/init.d/tomcat7 /etc/rc2.d/S99tomcat
``` 

10 - Crie um diretorio para os reports (pentahoReports):

```bash
sudo -i 
cd /root 
mkdir .mifosx
mv /usr/share/tomcat7/conf/mifosx /root/.mifosx 
```

11 - Inicialize o tomcat

```bash
 sudo /etc/init.d/tomcat7 start
```

12 - Veja os logs do tomcat

```bash
tail -f /usr/share/tomcat7/logs/catalina.out 
```


### ** Seu servidor estará acessível via https://IPdoServidor:8443

    username: mifos
    password: password


Informações adicionais:

- Teste de Plataforma @ https://[public DNS]/fineract-provider/api/v1/offices?tenantIdentifier=default&pretty=true

- Documentação da API @ https://[public DNS]/api-docs/apiLive.htm


__Referências:__

- https://mifos.org/
