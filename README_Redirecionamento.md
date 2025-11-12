
# Como Corrigir o Redirecionamento de Páginas em uma Aplicação ReactJS com Nginx

Quando se utiliza o Nginx para servir uma aplicação ReactJS em produção, é comum enfrentar problemas de redirecionamento. Isso acontece porque, ao tentar acessar diretamente uma URL que não seja a página inicial (exemplo: `/about`, `/profile`, etc.), o servidor Nginx pode não encontrar esse caminho fisicamente no servidor, resultando em um erro 404.

O que precisamos fazer é configurar o Nginx para sempre redirecionar qualquer requisição para o `index.html` da aplicação React, permitindo que o React Router ou outro mecanismo de roteamento gerencie a navegação.

## Pré-requisitos

- Você deve já ter o Nginx instalado e configurado.
- Sua aplicação React deve estar criada e compilada (build) com `npm run build` ou `yarn build`.
- O código da aplicação React deve ser servido corretamente pelo Nginx.

## Passo 1: Criando a build da aplicação React

Primeiramente, se ainda não tiver gerado a build de produção da aplicação React, use o comando abaixo:

```bash
npm run build
# ou
yarn build
```
Isso criará uma pasta chamada `build/`(ou `dist/`) dentro do diretório da sua aplicação, contendo os arquivos estáticos necessários para a execução em produção.

## Passo 2: Configuração do Nginx

Agora, você precisa configurar o Nginx para servir a aplicação React corretamente. Vamos editar o arquivo de configuração do Nginx.

### 2.1 - Localize o arquivo de configuração do Nginx

Normalmente, o arquivo de configuração do Nginx está localizado em:

- `/etc/nginx/nginx.conf` (configuração principal)

- `/etc/nginx/sites-available/default` (configuração de site específico)

Se você configurou o Nginx para um site específico, o arquivo será o correspondente ao seu domínio ou endereço IP. Aqui, vamos supor que a configuração seja no arquivo `default`.

### 2.2 - Configuração básica do Nginx

Abra o arquivo de configuração do Nginx no seu editor de texto preferido:

```bash
sudo nano /etc/nginx/sites-available/default
```

Dentro do bloco `server { ... }`, você precisa garantir que a configuração esteja assim:

```bash
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/your-react-app/build; # Caminho para o diretório da build do React
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Se precisar de configurações adicionais para outros arquivos ou rotas, insira aqui

    error_page 404 /index.html;
}
```
Explicação dos parâmetros:

- **listen 80;**: Define que o Nginx escutará na porta 80 (HTTP).

- **server_name example.com www.example.com**
;: Substitua `example.com` pelo seu domínio ou IP.

- **root /var/www/html/your-react-app/build;**: Defina o caminho onde a build da sua aplicação React está localizada.

- **index index.html;**: A página inicial da sua aplicação React é o `index.html` gerado pelo comando de build.

- **location / { try_files $uri $uri/ /index.html; }**: A chave aqui é o `try_files`. Isso tenta primeiro localizar o arquivo solicitado. Se não encontrar, ele redireciona para `index.html` (o que é necessário para que o React Router funcione corretamente).

- **error_page 404 /index.html;**: Em caso de erro 404 (página não encontrada), o Nginx redireciona para `index.html`.

### 2.3 - Teste a configuração do Nginx

Antes de aplicar as alterações, sempre teste se a configuração do Nginx está correta. Use o seguinte comando:

```bash
sudo nginx -t
```

Se o Nginx estiver configurado corretamente, você verá uma mensagem como:

```bash
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 2.4 - Reinicie o Nginx

Se o teste for bem-sucedido, reinicie o Nginx para aplicar as configurações:

```bash
sudo systemctl restart nginx
```

Ou, se você estiver usando o comando `service`:

```bash
sudo service nginx restart
```

## Passo 3: Verificação da Aplicação React

Agora, ao acessar a aplicação React em seu navegador e tentar navegar para diferentes rotas, o Nginx deve redirecionar corretamente para o `index.html` quando não encontrar o arquivo solicitado, permitindo que o React Router faça o roteamento no lado do cliente.

Por exemplo:
- Se você acessar `http://example.com/about`, o Nginx vai servir o `index.html`, e o React Router gerenciará o redirecionamento para a página "Sobre".
- Se você acessar uma URL inexistente, o Nginx vai redirecionar para a página inicial da aplicação React.
