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

