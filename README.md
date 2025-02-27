# Introdução ao Docker
Docker é uma plataforma que permite criar, gerenciar e executar aplicações em containers. Containers são como máquinas virtuais leves que compartilham o kernel do sistema operacional, tornando-os rápidos e eficientes.

## Principais Conceitos
- **Imagens:** Modelos "prontos" que servem como base para criar containers.
- **Containers:** Instâncias em execução de uma imagem.
- **Volumes:** Local de armazenamento para dados persistentes.
- **Redes:** Mecanismo para comunicação entre containers.

## Instalação Docker
Para instalação no ambiente Windows, pode ser instalado o [Docker Desktop](https://www.docker.com/products/docker-desktop). Temos abaixo também como realizar a instalação no Linux Ubuntu, que serve também para casos de criação de um ambiente de desenvolvimento, através do WSL por exemplo.

1. Configure o repositório apt do Docker:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Instalar última versão dos pacotes do Docker:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verificar se a instalação ocorreu com sucesso, rodando a imagem do hello-world:
```bash
sudo docker run hello-world
```

## Testando imagem do Nginx
    
- Baixar uma imagem do Nginx:
        
```bash
  docker pull nginx
```
    
- Iniciar um container Nginx(esse comando pode também já ser usado antes do pull, pois se não houver a imagem disponível, será baixado automaticamente).
        
```bash
docker run --name nginx-container -d -p 8080:80 nginx
```
        
- O Nginx estará disponível em [http://localhost:8080](http://localhost:8080/).
    
- Para parar o container:
        
```bash
docker stop nginx-container
```
        
- Para remover o container:
        
```bash
docker rm nginx-container
```

## Comandos Úteis

|Comando|Descrição|
|---|---|
|`docker --version`|Verificar a versão do Docker.|
|`docker pull <imagem>`|Baixar uma imagem do Docker Hub.|
|`docker run <imagem>`|Criar e executar um container.|
|`docker ps`|Listar containers ativos.|
|`docker ps -a`|Listar todos os containers (ativos ou não).|
|`docker stop <nome>`|Parar um container em execução.|
|`docker rm <nome>`|Remover um container.|
|`docker images`|Listar imagens locais.|
|`docker rmi <imagem>`|Remover uma imagem do repositório local.|
|`docker image prune`|Limpar imagens sem referência.|


### Opções comuns do docker run

|Comando|Descrição|
|---|---|
|`-d`|Executa em modo detached (background)|
|`-p [host-port]:[container-port]`|Mapeia portas|
|`-v [host-path]:[container-path]`|Monta volumes|
|`--name [nome]`|Define um nome para o container|
|`-t`|Define uma tag/nome para a imagem durante o build ou execução|


## Subindo Banco de Dados MySQL

```bash
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
```
- `-e MYSQL_ROOT_PASSWORD=root`: Define a senha do usuário `root` no MySQL.
    - O usuário `root` tem privilégios administrativos para criar bancos, tabelas e usuários.
- A senha será solicitada ao conectar ao MySQL (usando `mysql -uroot -p`).
- Esse comando cria um banco de dados funcional e seguro com a senha especificada.

```bash
docker exec -it mysql-container mysql -uroot -p
```
- `docker exec`: Executa comandos em um container ativo.
- `-it`: Permite interação com o terminal dentro do container.
- `mysql -uroot -p`: Abre o cliente MySQL e autentica com o usuário `root`.
    - Ao pressionar Enter, a senha configurada (`root`) será solicitada.

```sql
CREATE DATABASE exemplo;
SHOW DATABASES;
```

## Subindo Banco de Dados MySQL com Volume

```bash
docker run --name mysql-persistent -e MYSQL_ROOT_PASSWORD=root -v mysql_data:/var/lib/mysql -d mysql:latest
```
- **`-v mysql_data:/var/lib/mysql`:**
    - Cria um **volume nomeado** chamado `mysql_data`.
    - Monta o volume no caminho `/var/lib/mysql` do container.
    - Essa configuração **garante que os dados do banco sejam persistidos**, mesmo que o container seja removido.
- Após parar e remover o container, o volume `mysql_data` mantém os dados.

```bash
docker exec -it mysql-persistent mysql -uroot -p
```
    
```sql
CREATE DATABASE persistencia;
```

É possível remover o container criado, e criar um novo com o mesmo volume, o que mantém os dados do banco anterior.
```bash
docker stop mysql-persistent
docker rm mysql-persistent
```

```bash
docker run --name mysql-new -e MYSQL_ROOT_PASSWORD=root -v mysql_data:/var/lib/mysql -d mysql:latest
```

```sql
SHOW DATABASES;
```

## Criando uma imagem e adicionando ao DockerHub

O Docker Hub é o registro público oficial de imagens Docker - um repositório centralizado onde você pode encontrar, compartilhar e distribuir imagens Docker. Funciona de forma similar ao GitHub, mas para imagens Docker.

No repositório temos um projeto simples de conversão de distância, e nele há também um Dockerfile que a partir dele será feita a criação da imagem, com o que é necessário para que o projeto funcione. Abaixo estão os comandos iniciais para criação da imagem a partir do Dockerfile:

```bash
docker build -t conversao -f Dockerfile .
```

```bash
docker build -t douglasmv/conversao:v1 .
```

Para subir imagens no DockerHub, é necessário antes realizar o login da sua conta, que caso não tenha pode ser criada em [https://hub.docker.com/](https://hub.docker.com/). Lembrando que o douglasmv dos comandos, deve ser substituido pela sua conta.

```bash
docker login
```

```bash
docker push douglasmv/conversao:v1
```

É possível também trocar a versão da imagem através de tags, como feito nos comandos abaixo:

```bash
docker tag douglasmv/conversao:v1 douglasmv/conversao:latest
```

```bash
docker push douglasmv/conversao:latest
```

Através do comando abaixo, é possível rodar a aplicação a partir da imagem armazenada no DockerHub. Se tudo ocorrer como esperado, a aplicação de conversão de distância estará disponível em [http://localhost:8080](http://localhost:8080/).

```bash
docker container run -d -p 8080:5000 douglasmv/conversao-distancia:v1
```


## Docker Compose

Docker Compose é uma ferramenta para orquestrar aplicações multi-containers, permitindo definir e executar múltiplos containers Docker de forma declarativa através de um único arquivo YAML. Com ele, você pode configurar todos os serviços, redes e volumes necessários para sua aplicação em um único lugar, facilitando o gerenciamento e deploy de aplicações complexas. Abaixo temos um exemplo de um arquivo:

```yaml
services:
# Aqui a gente bota o nome do serviço (como ele vai aparecer la no terminal depois)
  backend:
    # Caminho para o dockerfile desse serviço
    build: ./nodejs-ping-pong
    # Define o mapeamento de portas que vamos fazer
    ports:
      - "3000:3000"
    # Qual o nome da rede que vamos usar
    networks:
      - app-network

  frontend:
    build: ./frontend
    ports:
      - "4200:4200"
    networks:
      - app-network

# Aqui definimos a rede que vamos usar
networks:
	# Chamando ela de app-network
  app-network:
    driver: bridge
```


## Comandos Docker Compose

|Comando|Descrição|
|---|---|
|`docker-compose up`|Inicia todos os serviços definidos no arquivo docker-compose.yml|
|`docker-compose up --build`|Força o rebuild das imagens antes de iniciar os serviços|
|`docker-compose down`|Para e remove todos os containers, redes e volumes definidos|
|`docker-compose ps`|Lista todos os containers em execução do compose|
|`docker-compose logs`|Exibe os logs de todos os serviços|
|`docker-compose logs [serviço]`|Exibe os logs de um serviço específico|
|`docker-compose stop`|Para todos os serviços sem remover os containers|
|`docker-compose start`|Inicia serviços que foram parados|
|`docker-compose restart`|Reinicia todos os serviços|
|`docker-compose exec [serviço] [comando]`|Executa um comando em um serviço específico|
|`docker-compose run [serviço] bash`|Acessa o terminal bash de um serviço específico|


### Flags comuns:

- `-d` - Executa em modo detached (background)
- `--build` - Força o rebuild das imagens
    
    Quando você executa o `docker-compose build` ou usa a flag `--build`, o Docker Compose irá construir todas as imagens definidas no arquivo docker-compose.yml que têm a instrução 'build' especificada. É similar ao comando `docker build`, mas com algumas diferenças importantes:
    
    - O Docker Compose automaticamente constrói todas as imagens necessárias em um único comando
    - Ele mantém um cache das imagens construídas e só reconstrói o que foi modificado
    - O contexto de build é definido no docker-compose.yml, não sendo necessário especificar o caminho do Dockerfile manualmente
    
    Por exemplo, no nosso docker-compose.yml acima, quando executamos `docker-compose up --build`, ele irá construir automaticamente as imagens tanto do backend quanto do frontend, usando os Dockerfiles especificados em seus respectivos diretórios.
    
- `--force-recreate` - Força a recriação dos containers
- `-f` - Especifica um arquivo compose alternativo