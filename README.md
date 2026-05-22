# accessmonitor-docker (_backend_ ou _<abbr title="Application Programming Interface" lang="en">API</abbr>_ AccessMonitor)
AccessMonitor Docker

Com a dockerização, o AccessMonitor pode agora ser instalado em qualquer ambiente: localmente, na rede interna da organização ou integrado noutras aplicações. Desta forma, o AccessMonitor pode avaliar mais páginas, incluindo páginas ainda não publicadas na Internet.

## Instalar uma imagem docker do AccessMonitor numa máquina local

As instruções abaixo pressupõem que já tem o Docker instalado na máquina onde pretende correr o contentor do _AccessMonitor_.

Esta instalação disponibiliza apenas o **_backend_ do _AccessMonitor_**. No final da instalação ficará disponível um _endpoint_ da API (`/amp/eval/`) para executar avaliações de acessibilidade web. Com este _endpoint_ pode integrar as avaliações de acessibilidade em qualquer aplicação web.

Eis um exemplo de como pode, no final do presente processo de instalação, invocar a API para efetuar uma avaliação de uma página web:

```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://192.168.1.164" \
  "http://192.168.1.74:3000/amp/eval/$ENCODED"
```

Esta solicitação à API do AccessMonitor devolve como resposta um ficheiro JSON com todo o output da avaliação. Algo como:

```textplain
HTTP/1.1 200 OK
Content-Type: application/json
...
```

**NOTA**: O exemplo anterior é um caso real de chamada da API que pressupõe que o contentor do _AccessMonitor_ está instalado na máquina 192.168.1.74 e que o estamos a chamar, para produzir avaliações, a partir da máquina 192.168.1.164 da rede local. Pode ser a rede do escritório ou mesmo a rede _wifi_ de casa. No exemplo solicita-se a avaliação da _<span lang="en">homepage</span>_ `https://www.arte.gov.pt`. 

**Vamos então começar a instalação. :-)**

Estrutura de pastas:

```plaintext
projetos/
└── accessmonitor-docker/
```

Pode criar a pasta `projetos/` e entrar:

```bash
mkdir projetos
cd projetos
```

### 1. Clonar o presente repositório `accessmonitor-docker`

De dentro da pasta `projetos/` vamos clonar o repositório `accessmonitor-docker`:

```bash
git clone https://github.com/amagovpt/accessmonitor-docker.git
```

O comando `git clone` cria automaticamente uma nova pasta com o nome do repositório - a chamada pasta de projeto.

### 2. Criar e configurar o `.env`

De seguida entramos na pasta do projeto `accessmonitor-docker`, onde vamos criar o ficheiro de ambiente `.env` com as propriedades descritas no `.env.example` e configurar as variáveis de acordo com os requisitos da máquina hospedeira.

```bash
# entrar na pasta do projeto
cd accessmonitor-docker
# criar o ficheiro ´.env´ a partir do ´.env.example´
cp .env.example .env
# editar e configurar as variáveis do ´.env´ com o editor ´vim´ ou outro qualquer
vim .env﻿
```

A estrutura de variáveis do ficheiro `.env` tem este aspeto:

```bash
## Environment variables for AccessMonitor Docker container
NODE_ENV=
## Referer header value to allow requests to the API (e.g., from a frontend app)
REFERER=
## Comma-separated list of IP ranges to block (e.g., "192.168.0.0/24,10.0.0.0/8")
IP_BLACKLIST_RANGES=""
```

#### Variáveis do `.env`

- `NODE_ENV`  
  Define o ambiente de execução. Para produção utilize `production`.
- `REFERER`  
  Define a origem autorizada a efetuar pedidos à API.
- `IP_BLACKLIST_RANGES`  
  Lista de IPs ou gamas de IPs bloqueados.

Assim, para uma instalação em que a solicitação das avaliações será feita na mesma máquina (`localhost`) onde se encontra o contentor do AccessMonitor a configuração mais provável será:

```bash
## Environment variables for AccessMonitor Docker container
NODE_ENV=production
## Referer header value to allow requests to the API (e.g., from a frontend app)
REFERER=http://localhost
## Comma-separated list of IP ranges to block (e.g., "192.168.0.0/24,10.0.0.0/8")
IP_BLACKLIST_RANGES=""
```


Para uma instalação na mesma rede em que o contentor do _AccessMonitor_ seja instalado na máquina 192.168.1.74 (**é só um exemplo**) e esta máquina seja usada a partir da máquina (REFERER) 192.168.1.164 então teremos uma configuração do tipo:

```bash
## Environment variables for AccessMonitor Docker container
NODE_ENV=production
## Referer header value to allow requests to the API (e.g., from a frontend app)
REFERER=http://192.168.1.164
## Comma-separated list of IP ranges to block (e.g., "192.168.0.0/24,10.0.0.0/8")
IP_BLACKLIST_RANGES=""
```
Definido o `.env` estamos prontos para avançar na instalação do contentor do _AccessMonitor_.

### 3. Construir a imagem Docker do AccessMonitor

```bash
docker build -t accessmonitor-docker .
```

### 4. Executar o _container_

```bash
docker run --env-file .env -p 3000:3000 accessmonitor-docker
```

Após executar o comando anterior, o AccessMonitor deverá ficar disponível na porta 3000.

```bash
http://localhost:3000
```

O endpoint do AccessMonitor está agora disponível para integração nas suas aplicações e fluxos de desenvolvimento.

Exemplos de chamadas ao _endpoint_ via Terminal:

**Exemplo 1:** contentor do _AccessMonitor_ instalado em localhost e avaliações solicitadas a partir de localhost. 

```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://localhost" \
  "http://localhost:3000/amp/eval/$ENCODED"
```

Ou assim:

**Exemplo 2:** contentor do _AccessMonitor_ instalado em 192.168.1.74 e avaliações solicitadas a partir de 192.168.1.164. No exemplo solicita-se a avaliação da _homepage_ `https://www.arte.gov.pt`.  

```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://192.168.1.164" \
  "http://192.168.1.74:3000/amp/eval/$ENCODED"
```


