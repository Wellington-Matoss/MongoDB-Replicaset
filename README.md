# MongoDB ReplicaSet

![MongoDB](https://img.shields.io/badge/MongoDB-7.0-green)
![Docker](https://img.shields.io/badge/Docker-Compatible-blue)

Uma configuração pronta para uso de um cluster MongoDB com replicação (Replica Set) utilizando Docker Compose. Este projeto configura um ambiente MongoDB com três nós em replica set para alta disponibilidade e resiliência de dados.

## 📋 Requisitos

- [Docker](https://www.docker.com/get-started) (versão 20.10+)
- [Docker Compose](https://docs.docker.com/compose/install/) (versão 2.0+)

## 🚀 Estrutura do Projeto

```
.
├── docker-compose.yml    # Configuração dos serviços Docker
├── mongod.env           # Variáveis de ambiente para autenticação
├── mongodb/             # Diretório para dados persistentes
│   ├── data/            # Dados dos nós MongoDB
│   │   ├── mongo1/
│   │   ├── mongo2/
│   │   └── mongo3/
│   └── security/        # Arquivos de segurança
│       └── keyfile.key  # Chave de autenticação (gerada automaticamente)
└── README.md            # Este arquivo
```

## 🔧 Configuração

O cluster MongoDB é composto por:

- **3 instâncias MongoDB** (mongo1, mongo2, mongo3) configuradas como um replica set chamado `rs0`
- Autenticação habilitada com usuário administrador
- Comunicação entre os nós protegida por keyfile
- Portas expostas: 27117, 27118 e 27119

## ⚙️ Início Rápido

1. Clone este repositório:

   ```bash
   git clone https://github.com/seu-usuario/mongodb-replicaset.git
   cd mongodb-replicaset
   ```

2. Inicie o cluster MongoDB:

   ```bash
   docker-compose up -d
   ```

3. Configure o replica set após os contêineres estarem em execução:

   ```bash
   docker exec -it mongo1 mongosh --port 27117 -u admin -p adminpwd --eval '
   rs.initiate({
     _id: "rs0",
     members: [
       { _id: 0, host: "mongo1:27117" },
       { _id: 1, host: "mongo2:27118" },
       { _id: 2, host: "mongo3:27119" }
     ]
   })
   '
   ```

4. Verifique o status do replica set:
   ```bash
   docker exec -it mongo1 mongosh --port 27117 -u admin -p adminpwd --eval 'rs.status()'
   ```

## 🔐 Segurança

- Autenticação habilitada para todos os nós
- Comunicação entre nós protegida com keyfile
- Credenciais padrão:
  - Usuário: `admin`
  - Senha: `adminpwd`

> ⚠️ **IMPORTANTE**: Para ambientes de produção, altere as credenciais padrão no arquivo `mongod.env`.

## 📝 Conectando-se ao Cluster

Para se conectar ao cluster MongoDB como um cliente:

```bash
mongosh "mongodb://admin:adminpwd@localhost:27117,localhost:27118,localhost:27119/admin?replicaSet=rs0"
```

Ou em aplicações, use a string de conexão:

```
mongodb://admin:adminpwd@mongo1:27117,mongo2:27118,mongo3:27119/admin?replicaSet=rs0
```

## 🛡️ Configurações Avançadas

### Alterando para Ambiente de Produção

Para um ambiente de produção, modifique o docker-compose.yml para utilizar caminhos absolutos nos volumes:

```yaml
volumes:
  - /var/lib/mongodb/data/mongo1:/data/db
  - /var/lib/mongodb/security:/etc/mongo
```

### Ajustando o Tamanho do Oplog

O tamanho do oplog está configurado para 600MB. Você pode ajustá-lo modificando o parâmetro `--oplogSize` no arquivo docker-compose.yml.

## 📊 Monitoramento

Você pode monitorar o status do replica set a qualquer momento:

```bash
docker exec -it mongo1 mongosh --port 27117 -u admin -p adminpwd --eval 'rs.status()'
```

## 📄 Licença

Este projeto está licenciado sob a licença MIT - consulte o arquivo LICENSE para obter detalhes.
