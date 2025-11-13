
# Teste de Carga com Apache Bench (ab) na Aplicação de Instituições em React

Este tutorial explica como configurar o Apache Bench (ab) e executar um teste de carga na página inicial da aplicação React [Instuições de ensino](https://github.com/JDavi-Dev/Aplicacao-React-IE)

## Pré-requisitos

- **Apache Bench (ab)**: O Apache Bench é uma ferramenta de linha de comando para realizar testes de carga HTTP. Ela vem junto com o Apache HTTP Server, mas pode ser instalada separadamente.
- **Node.js e React**: A aplicação deve já estar configurada e em execução.

## Passo 1: Instalar o Apache Bench

Antes de executar o Apache Bench, você precisa garantir que ele esteja instalado no seu sistema.

### No Linux (Debian/Ubuntu):

```bash
sudo apt update
sudo apt install apache2-utils
```
### No macOS:

O Apache Bench geralmente já vem instalado com o macOS. Para verificar, execute:

```bash
ab -V
```

Se não estiver instalado, você pode instalar via Homebrew:

```bash
brew install apache2
```

### No Windows:

No Windows, o Apache Bench pode ser instalado através do **Cygwin** ou **WSL (Windows Subsystem for Linux)**. A maneira mais fácil de usar Apache Bench no Windows é com o WSL. Recomendamos a instalação do WSL e a configuração de um ambiente Linux.

## Passo 2: Subir a Aplicação React

Certifique-se de que sua aplicação React está rodando corretamente antes de executar o teste de carga. Caso a aplicação não esteja em execução, siga os passos abaixo.

### 2.1 Clonando o repositório

Primeiro, clone o repositório da aplicação:

```bash
git clone https://github.com/JDavi-Dev/Aplicacao-React-IE.git
cd Aplicacao-React-IE
```

### 2.2 Instalando as dependências

Dentro do diretório do projeto, instale as dependências:

```bash
npm install
```

### 2.3 Rodando o servidor de desenvolvimento

Execute a aplicação localmente:

```bash
npm start
```

Isso deve iniciar a aplicação no endereço `http://localhost:3000` (ou outro se configurado de forma diferente).

## Passo 2: Subir sua Aplicação React com Docker e Nginx

Agora, vamos criar um ambiente Docker para rodar sua aplicação React, utilizando o **Nginx** para servir os arquivos estáticos.

### 2.1 Clonando o repositório

Primeiro, clone o repositório da aplicação:

```bash
git clone https://github.com/JDavi-Dev/Aplicacao-React-IE.git
cd Aplicacao-React-IE
```

### 2.2 Instalando as dependências

Dentro do diretório do projeto, instale as dependências do React:

```bash
npm install
```

### 2.3 Criando a build da aplicação

Antes de subir a aplicação com Docker, precisamos criar a versão otimizada para produção da aplicação React. Para isso, execute o seguinte comando:

```bash
npm run build
```

Este comando criará uma pasta `build/` no seu projeto, que contém todos os arquivos estáticos necessários para servir a aplicação.

### 2.4 Subindo a aplicação com Docker e Nginx

Agora vamos usar o **Docker** para rodar a aplicação com o **Nginx**, sem a necessidade de um `Dockerfile`. O processo é simples e consiste em construir a imagem do Nginx, copiando os arquivos da build para o diretório correto no container.

1. **Criar a imagem Docker do Nginx:**

    Utilize o seguinte comando para rodar o Nginx diretamente no Docker e servir os arquivos da sua build:

    ```bash
    docker run --name react-app -v $(pwd)/build:/usr/share/nginx/html:ro -p 80:80 -d nginx
    ```

    Explicando o comando:
    
    - `--name react-app`: Dá um nome ao container (pode ser qualquer nome, mas usamos `react-app` para facilitar).
    
    - `-v $(pwd)/build:/usr/share/nginx/html:ro`: Monta o diretório `build` (onde a aplicação foi construída) como volume no diretório de arquivos estáticos do Nginx (`/usr/share/nginx/html`), com a flag `:ro` para tornar o volume apenas leitura.
    
    - `-p 80:80`: Expõe a porta 80 do container para a porta 80 da sua máquina local (ou outra porta de sua escolha).
    
    - `-d nginx`: Roda o container em segundo plano usando a imagem oficial do Nginx.

2. **Acessando a aplicação:**

    Após rodar o comando, a aplicação estará disponível em `http://localhost`. Você pode acessar o site diretamente no navegador ou usar o Apache Bench para realizar o teste de carga.

## Passo 3: Executando o Apache Bench

Agora que sua aplicação React está rodando no Docker, podemos usar o **Apache Bench** para simular carga na página inicial da aplicação.

### 3.1 Comando básico

O comando básico para executar o teste de carga é o seguinte:

```bash
ab -n [número de requisições] -c [número de conexões simultâneas] [URL]
```

Onde:

- `-n [número de requisições]`: O número total de requisições HTTP a serem enviadas durante o teste.

- `-c [número de conexões simultâneas]`: O número de conexões simultâneas a serem mantidas durante o teste.

- `[URL]`: O URL da página que você deseja testar.

### 3.2 Exemplo de execução

Vamos executar um teste de carga simulando 1000 requisições e 10 conexões simultâneas na página inicial da sua aplicação React.

```bash
ab -n 1000 -c 10 http://localhost:3000/
```

Este comando irá simular 1000 requisições para o endereço `http://localhost:3000/` com 10 conexões simultâneas.

### 3.3 Analisando os resultados

Após a execução do comando, você verá uma série de informações de desempenho, incluindo:

- **Requests per second**: A quantidade de requisições que o servidor conseguiu processar por segundo.

- **Connection Times**: O tempo de resposta médio do servidor.

- **Throughput**: A quantidade total de dados transferidos por segundo.

- **Failures**: O número de requisições que falharam.

Exemplo de saída do Apache Bench:

```
Server Software:        nginx/1.18.0
Server Hostname:        localhost
Server Port:            80

Document Path:          /
Document Length:        10000 bytes

Concurrency Level:      10
Time taken for tests:   5.000 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      10000000 bytes
HTML transferred:       10000000 bytes
Requests per second:    200.00 [#/sec] (mean)
Time per request:       50.000 [ms] (mean)
Time per request:       5.000 [ms] (mean, across all concurrent requests)
Transfer rate:          2000.00 [Kbytes/sec] received
```
## Passo 4: Interpretando os Resultados

A partir dos resultados obtidos, você pode verificar como o servidor está se comportando sob carga:

- **Requests per second**: Este número indica quantas requisições o servidor conseguiu processar por segundo. Quanto maior, melhor.

- **Time per request**: O tempo médio necessário para processar uma requisição. Quanto menor, melhor.

- **Failures**: Se houver falhas, isso indica que o servidor não conseguiu processar algumas requisições corretamente.

- **Throughput**: A quantidade de dados que o servidor consegue transferir por segundo. Esse número pode ser útil para medir a eficiência de transferência de dados.

## Passo 5: Ajustes e Otimizações

Caso você perceba que sua aplicação não está atendendo bem ao teste de carga, considere as seguintes otimizações:

- **Melhorar a performance do servidor**: Verifique a configuração do servidor onde a aplicação está hospedada (seja local ou em produção) para garantir que ele tenha recursos suficientes para lidar com o tráfego.

- **Uso de cache**: Considere implementar mecanismos de cache no lado do servidor para reduzir o tempo de resposta.

- **Otimize os assets da aplicação**: Certifique-se de que sua aplicação React está devidamente otimizada para produção, com arquivos minificados e divididos adequadamente.

## Conclusão

Neste tutorial, você aprendeu a usar o **Apache Bench** para realizar testes de carga na aplicação React, além de configurar um ambiente **Docker** com **Nginx** para servir os arquivos estáticos da aplicação. Isso ajudará a identificar possíveis gargalos de performance antes de colocar sua aplicação em produção.

Se precisar de mais informações ou ajustes no teste, consulte a documentação oficial do Apache Bench!
