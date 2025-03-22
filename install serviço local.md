
<h4 align="center"> 
    :construction:  Projeto em construção  :construction:
Método criado por GOR INFORMÁTICA suporte (61) 9 96104908
</h4

# Whapichat em modo local com servidor windows para liberar socket
 
# Configurando um servidor windows com Nginx e ssl para DEV

**1. Baixe e instale o Nginx no Windows**

🔘  Baixando o Nginx:

Acesse o site oficial: http://nginx.org/en/download.html
Escolha a versão estável (Stable version).

🔘  Extraia o conteúdo do arquivo ZIP em um diretório, como C:\nginx.

🔘  Abra o Prompt de Comando ou PowerShell.
Navegue até o diretório do Nginx:

Por exemplo:
```
Cd C:\nginx
```

🔘  Inicie o Nginx com:

```
Start nginx
```

Acesse http://localhost no navegador para verificar se o Nginx está funcionando (você verá uma página padrão do Nginx).

**2. Configure o arquivo nginx.conf**

🔘  Navegue até o diretório onde o Nginx foi extraído (por exemplo, C:\nginx).

🔘  Abra o arquivo de configuração nginx.conf localizado em C:\nginx\conf com um editor de texto, como o Notepad++.

🔘  Substitua ou adicione o seguinte ao arquivo:

```
# Configuração para o frontend
Server {
    Listen 80;
    Server_name app.SEUDOMINIO.com.br;

    Location / {
        Proxy_pass http://127.0.0.1:3000; # Porta do frontend
        Proxy_set_header Host $host;
        Proxy_set_header X-Real-IP $remote_addr;
        Proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        Proxy_set_header X-Forwarded-Proto $scheme;
    }
}

# Configuração para a API
Server {
    Listen 80;
    Server_name api.SEUDOMINIO.com.br;

    Location / {
        Proxy_pass http://127.0.0.1:5000; # Porta da API
        Proxy_set_header Host $host;
        Proxy_set_header X-Real-IP $remote_addr;
        Proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        Proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Salve o arquivo.
OBS.: Não se esqueça de trocar o “SEUDOMINIO” pelo domínio que irá usar.

**3. Atualize o arquivo hosts**

Você precisa associar os domínios app.SEUDOMINIO.com.br e api.SEUDOMINIO.com.br ao localhost no arquivo hosts do Windows.

🔘  Abra o arquivo C:\Windows\System32\drivers\etc\hosts em um editor de texto com privilégios de administrador.
  ***** Salve uma cópia do arquivo antes de substituí-lo

🔘  Adicione as seguintes linhas ao final do arquivo:

```
127.0.0.1 app.SEUDOMINIO.com.br
127.0.0.1 api.SEUDIMINIO.com.br
```
Salve o arquivo.

**4. Reinicie o Nginx**
Para reiniciar o Nginx, use os seguintes comandos no Prompt de Comando ou PowerShell:
Pare o Nginx:

```
Nginx -s stop
```
Inicie o Nginx novamente:
```
Start nginx
```
**5. Teste as URLs**
Abra seu navegador e teste:

http://app.SEUDOMINIO.com.br
http://api.SEUDOMINIO.com.br
Se tudo estiver correto, você verá a aplicação rodando localmente.

**6. Configurar HTTPS (opcional)**
Para usar HTTPS no Windows com Nginx:

Gere um certificado autoassinado:

Use o OpenSSL (pode ser baixado separadamente ou já vem com algumas ferramentas de desenvolvimento como *Git Bash*).
Comando para gerar:

```
Openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout selfsigned.key -out selfsigned.crt
```

🔘  Salve os arquivos selfsigned.key e selfsigned.crt em um diretório, como C:\nginx\ssl.
Atualize o arquivo nginx.conf para incluir HTTPS:

```
Server {
    Listen 443 ssl;
    Server_name app.SEUDOMINIO.com.br;

    Ssl_certificate C:/nginx/ssl/selfsigned.crt;
    Ssl_certificate_key C:/nginx/ssl/selfsigned.key;

    Location / {
        Proxy_pass http://127.0.0.1:3000;
        Proxy_set_header Host $host;
        Proxy_set_header X-Real-IP $remote_addr;
        Proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        Proxy_set_header X-Forwarded-Proto $scheme;
    }
}

Server {
    Listen 443 ssl;
    Server_name api.SEUDOMINIO.com.br;

    Ssl_certificate C:/nginx/ssl/selfsigned.crt;
    Ssl_certificate_key C:/nginx/ssl/selfsigned.key;

    Location / {
        Proxy_pass http://127.0.0.1:5000;
        Proxy_set_header Host $host;
        Proxy_set_header X-Real-IP $remote_addr;
        Proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        Proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Reinicie o Nginx novamente.

Agora você pode acessar sua aplicação com HTTPS


<h4 align="center"> 
    Izing local
</h4

**1.         - Instalar Dependências Necessárias**

*Certifique-se de ter os seguintes softwares instalados em sua máquina:*

•        Docker: Para criar e gerenciar os containers do banco de dados.
•        Node.js: Para executar o backend e o frontend.
•        npm: O gerenciador de pacotes do Node.js.
•        Git: Para clonar os repositórios.

**2.        Criar os Bancos de Dados com Docker**

•      Inicie o Docker.
•      Criar um container PostgreSQL: Execute o seguinte comando no terminal:

```
docker run --name postgresql-whapichat -e POSTGRES_USER=whapichat -e POSTGRES_PASSWORD=password -e TZ="America/Sao_Paulo" --restart=always -p 5432:5432 -v /data:/var/lib/postgresql/data2 -d postgres
```

**2.3.        **Criar um container Redis: Execute o seguinte comando:**

```
docker run -e TZ="America/Sao_Paulo" --name redis-whapichat -p 6379:6379 -d --restart=always redis:latest redis-server --appendonly yes --requirepass "password"
```

**2.4.        **Criar um container RabbitMQ: Execute o seguinte comando:**
```
docker run -d --name rabbitmq-whapichat -p 5672:5672 -p 15672:15672 --restart=always --hostname rabbitmq -v /data:/var/lib/rabbitmq rabbitmq:3-management-alpine

**3.        Configurando o .env**

.env backend

```#NODE_ENV=prod

# ambiente
NODE_ENV=dev

# URL do backend para construção dos hooks
BACKEND_URL=http://localhost

# URL do front para liberação do cors
FRONTEND_URL=http://localhost:8080

# Porta utilizada para proxy com o serviço do backend
PROXY_PORT=3100

# Porta que o serviço do backend deverá ouvir
PORT=3100

# conexão com o banco de dados
DB_DIALECT=postgres
DB_TIMEZONE=-03:00
DB_PORT=5432
POSTGRES_HOST=localhost
POSTGRES_USER=whapichat
POSTGRES_PASSWORD=password
POSTGRES_DB=postgres

# Chaves para criptografia do token jwt
JWT_SECRET=DPHmNRZWZ4isLF9vXkMv1QabvpcA80Rc
JWT_REFRESH_SECRET=EMPehEbrAdi7s8fGSeYzqGQbV5wrjH4i

# Dados de conexão com o REDIS
IO_REDIS_SERVER=localhost
IO_REDIS_PASSWORD=’password’
IO_REDIS_PORT=’6379’
IO_REDIS_DB_SESSION=’2’
REDIS_URI=redis://:password@localhost:6379

#CHROME_BIN=/usr/bin/google-chrome
#CHROME_BIN=/usr/bin/google-chrome-stable
#CHROME_BIN=/usr/bin/chromium-browser
#CHROME_BIN=/usr/bin/vivaldi
#CHROME_BIN=null
CHROME_BIN=C:\Program Files\Google\Chrome\Application\chrome.exe

# tempo para randomização da mensagem de horário de funcionamento
MIN_SLEEP_BUSINESS_HOURS=1000
MAX_SLEEP_BUSINESS_HOURS=2000

# tempo para randomização das mensagens do bot
MIN_SLEEP_AUTO_REPLY=400
MAX_SLEEP_AUTO_REPLY=600

# tempo para randomização das mensagens gerais
MIN_SLEEP_INTERVAL=200
MAX_SLEEP_INTERVAL=500

# dados do RabbitMQ / Para não utilizar, basta comentar a var AMQP_URL
# RABBITMQ_DEFAULT_USER=admin
# RABBITMQ_DEFAULT_PASS=123456
AMQP_URL=’amqp://guest:guest@localhost:5672?connection_attempts=5&retry_delay=5’

# api oficial (integração em desenvolvimento)
API_URL_360=https://graph.facebook.com/v14.0
API_META_TOKEN=”EAAHsG0SAAjsBOxItYJwYq0kUGZCXmIePoBHgZBy1ccq4V7uwIqDrBnDMCpYee8Vapmaoek6lariBF4UaW0F0Ep0JnDXReZCbumnTnkGu1at28sNzr72MSDYLGbUwLftzDQLE6PxD5kRWNE6MtK8c8fBc49f9qA94Fk1Yst8D3VTBgcblLQjT8IVMRYqILPgWJP5rcsqYtaPhe2ts2F2tqcZACDxXYq4ECwZBF0X4I0yyxkliLiWGH”

# usado para mostrar opções não disponíveis normalmente.
ADMIN_DOMAIN=izing.io

# Dados para utilização do canal do facebook
FACEBOOK_APP_ID=3237415623048660
FACEBOOK_APP_SECRET_KEY=3266214132b8c98ac59f3e957a5efeaaa13500

# Limitar Uso do Izing Usuario e Conexões
USER_LIMIT=99
CONNECTIONS_LIMIT=99
```


.env front
```VUE_URL_API=’http://localhost:3100’
VUE_FACEBOOK_APP_ID=’23156312477653241’
```



## Instalação do BACKEND:

```
Cd backend
Npm install
Npm run build
Npx sequelize db:migrate
Npx sequelize db:seed:all
Npm start
Ou
Pm2 start dist/server.js
```


## Instalação do FRONTEND:

```
Npm install
```
No Windows (cmd ou PowerShell):

```
Set NODE_OPTIONS=--openssl-legacy-provider ; npm install ; npx quasar dev
```

No Linux/Mac:

```
Export NODE_OPTIONS=--openssl-legacy-provider”
```
```
Npm install -g @quasar/cli
Npx quasar build -P -m pwa
Npx quasar dev
```
Usuario: admin@whapichat.com.br
Senha: 123456

