# ELK Stack no Debian

Este repositório contém um guia passo a passo para instalar e configurar o ELK Stack (Elasticsearch, Logstash e Kibana) em um sistema Debian. O ELK Stack é uma solução poderosa para coleta, processamento, armazenamento e visualização de logs.

## Pré-requisitos

- Um sistema Debian (testado no Debian 12).
- Acesso root ou permissões de superusuário (`sudo`).
- Conexão com a internet para baixar pacotes.

## Passos de Instalação

### 1. Atualize o sistema
Certifique-se de que o sistema está atualizado:

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Instale o Java
```bash
sudo apt-get install openjdk-17-jdk
```
Para ver o caminho da pasta do java:

```bash
sudo update-alternatives --config java
```
Para configurar a variável de ambiente:

```bash
sudo nano .bashrc
```

```bash
JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export JAVA_HOME
export PATH=$PATH:$JAVA_HOME
```
Verifique a versão do Java
```bash
java --version
```
### 3. Adicione o repositório do Elasticsearch
Adicione a chave GPG e o repositório oficial da Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
```
### 4. Instale o Elasticsearch

```bash
sudo apt install elasticsearch -y
```
Inicie e habilite o serviço:
```bash
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```
### 5. Configure o Elasticsearch
Edite o arquivo de configuração:
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
```
Altere as seguintes linhas:
```bash
network.host: 0.0.0.0
discovery.seed_hosts: ["127.0.0.1"]
cluster.initial_master_nodes: ["127.0.0.1"]
```
Reinicie o Elasticsearch:
```bash
sudo systemctl restart elasticsearch
```
### 6. Instale o Logstash
```bash
sudo apt install logstash -y
```
Inicie e habilite o serviço:
```bash
sudo systemctl start logstash
sudo systemctl enable logstash
```
### 7.Configure o Logstash
Crie um arquivo de configuração para o Logstash:
```bash
sudo nano /etc/logstash/conf.d/logstash.conf
```
```bash
input {
  file {
    path => "/var/log/*.log"
    start_position => "beginning"
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
  stdout { codec => rubydebug }
}
```
Reinicie o Logstash
```bash
sudo systemctl restart logstash
```
### 8.Instale o Kibana
```bash
sudo apt install kibana -y
```
Inicie e habilite o serviço
```bash
sudo systemctl start kibana
sudo systemctl enable kibana
```

### 9. Configure o Kibana
Edite o arquivo de configuração:
```bash
sudo nano /etc/kibana/kibana.yml
```
Altere as seguintes linhas:
```bash
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
```
Reinicie o Kibana
```bash
sudo systemctl restart kibana
```
Acesse o Kibana no navegador:
```bash
http://<IP_DA_SUA_MAQUINA>:5601
```
Substitua <IP_DA_SUA_MAQUINA> pelo endereço IP da máquina onde o Kibana está instalado.
