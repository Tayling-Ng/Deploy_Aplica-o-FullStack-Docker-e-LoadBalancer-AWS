# Deploy de Aplicação com Docker e Load Balancer na AWS

## Descrição da Atividade

Implementação de dois Frontends, um Backend, e um Banco de Dados MySQL em Docker na AWS, com balanceamento de carga.

## Estrutura do Projeto

-   Frontend 1: Nginx configurado na `porta 83` da instância EC2.
-   Frontend 2: Nginx configurado na `porta 84` da instância EC2.
-   Backend: Conecta com os frontends na rede `idade-network`.
-   Banco de Dados: Container MySQL com credenciais configuradas via `secrets`.

## Objetivo da Atividade

-   Acessar a aplicação do `Frontend 1` na porta `83`.
-   Acessar a aplicação do `Frontend 2` na porta `84`.
-   Acessar a aplicação pelo endereço `DNS do balanceador de carga`.

## Passo a Passo

### **AWS**

1. **Criar Security Group**

    - Regras de Entrada:
        - Liberar acesso nas portas:
            - `22` (SSH)
            - `80` (HTTP)
            - `83` (Frontend 1)
            - `84` (Frontend 2)
        - Permitir o tráfego de qualquer endereço IP
            - `0.0.0.0/0`

2. **Criar Instância**

    - Imagem: `Ubuntu`
    - Tipo: `t2.micro`
    - Criar a `chave.pem`
    - Escolher o `SG` criado no passo 1

3. **Criar Load Balancer**

    - Tipo: `Application Load Balancer (ALB)`
    - Esquema: Voltado para internet
    - Tipo de endereço IP: IPV4
    - Escolher o Grupo de Destino criado no passo 4
    - Listener: `Porta 80`
    - No mínimo 2 zonas de disponibilidade (1 tem que ser a mesma da EC2)
    - Escolher o `SG` criado no passo 1

4. **Criar Target Group**
    - Tipo de Destino: Instâncias
    - Porta: 80
    - Tipo de endereço IP: IPV4
    - Versão do Protocolo: HTTP1
    - Escolher a instância criada no passo 2
    - Incluir como pendente as portas 83 (Frontend 1) e 84 (Fronted 2)

### **Terminal Local**

1. **Conectar a instância via SSH**

    - `ssh -i "<caminho da chave>\chave.pem" ubuntu@<IP público da instância>`

2. **Instalar Docker na instância**

    - Documentação: https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

    ```
    # Adicionar a chave GPG oficial do Docker:

    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc


    # Adicionar o repositório às fontes do Apt:

    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    ```

    ```
    # Instalar e rodar o Docker Compose

    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

3. **Upload da aplicação na instância**

    - No terminal local

        `scp -i "<caminho local da chave.pem>" -r "<caminho local da aplicação>" ubuntu@<IP público da instância>:~/`

4. **Disponibilizar a aplicação em Docker Compose**

    - No terminal da instância navegue até o diretório da aplicação:
        - Iniciar o Docker Compose: `sudo docker compose up -d`
        - Verificar os containers: `sudo docker ps`
        - Verificar logs dos contêineres: `sudo docker compose logs`

5. **Acessar a aplicação**

    - Frontend 1: `<IP público da instância>:83`
    - Frontend 2: `<IP público da instância>:84`
    - DNS do Load Balancer: `<Endereço DNS do ALB>`

    ![Acessando a Aplicação](https://github.com/Tayling-Ng/Deploy_Aplicacao-Docker-LoadBalancer-AWS/raw/main/acessando-aplicacao.gif)
