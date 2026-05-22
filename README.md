# accessmonitor-docker
AccessMonitor Docker

Para além do servidor da ARTE, I.P, com a _<span lang="en">dockerização</span>_, o AccessMonitor passa a poder ser instalado onde quiser. Na sua máquina local, na sua rede local, na sua intranet, como apoio ao seu <abbr title="Content Management System">CMS</abbr>, ... . Desta forma o AccessMonitor ganha graus de liberdade para avaliar ainda mais páginas, mesmo as que ainda não estão publicadas na Internet.

## Instalar uma imagem docker do AccessMonitor numa máquina local

As instruções abaixo pressupõem que já tem o Docker instalado na máquina onde pretende correr o _container_ do _AccessMonitor_.

Esta instalação disponibiliza o **_backend_ do _AccessMonitor_**. No final da instalação fica com um _endpoint_ da API (`/amp/eval/`) que executa uma avaliação de acessibilidade web. Com este _endpoint_ pode integrar as avaliações de acessibilidade em qualquer aplicação web.

Eis um exemplo de como pode invocar a API para validar a _<span lang="en">homepage</span>_ `https://www.arte.gov.pt`:

```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://192.168.1.164" \
  "http://192.168.1.74:3000/amp/eval/$ENCODED"
```

Esta solicitação à API do AccessMonitor produz como resposta um ficheiro JSON com todo o output do relatório de acessibilidade web AccessMonitor. Algo como:

```textplain
HTTP/1.1 200 OK
Content-Type: application/json
...
```

**NOTA**: O exemplo anterior é um caso real de chamada da API que pressupõe que o _container_ do _AccessMonitor_ está instalado na máquina 192.168.1.74 e que o estamos a chamar, para produzir avaliações, a partir da máquina 192.168.1.164 da rede local. Pode ser a rede do escritório ou mesmo a rede _wifi_ de casa. No exemplo solicita-se a avaliação da _<span lang="en">homepage</span>_ `https://www.arte.gov.pt`. 

**Vamos começar a instalar?! :-)**

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

### 1. Clonar o repositório `accessmonitor-docker`

De dentro da pasta `projetos/` vamos clonar o repositório `accessmonitor-docker`:

```bash
git clone https://github.com/amagovpt/accessmonitor-docker.git
```

O comando `git clone` cria automaticamente uma nova pasta com o nome do repositório - a chamada pasta de projeto.

### 2. Criar e configurar o `.env`

De seguida vamos entrar na pasta do projeto `accessmonitor-docker`, vamos criar o ficheiro de ambiente `.env` com as propriedades descritas no `.env.example` e configurar as variáveis de acordo com os requisitos da máquina hospedeira.

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

Variáveis e significado:
- A variável `NODE_ENV" define as configurações a aplicar a um determinado ambiente. Pode usar o valor `production`.
- A variável `REFERER` define a máquina a partir da qual se vai solicitar as avaliações. Pode ser a mesma (localhost) ou então uma qualquer outra máquina. Esta acaba por ser uma variável de segurança que protege a nossa API de ser chamada de uma máquina não autorizada para o fazer.
- A variável `IP_BLACKLIST_RANGES` bloqueia determinados IPs de efetuarem avaliações. Para já podemos deixar o valor vazio. 

Assim, para uma instalação em que a solicitação das avaliações será feita na mesma máquina (`localhost`) onde se encontra o _container_ do AccessMonitor a configuração mais provável será:

```bash
## Environment variables for AccessMonitor Docker container
NODE_ENV=production
## Referer header value to allow requests to the API (e.g., from a frontend app)
REFERER=http://localhost
## Comma-separated list of IP ranges to block (e.g., "192.168.0.0/24,10.0.0.0/8")
IP_BLACKLIST_RANGES=""
```


Para uma instalação na mesma rede em que o _container_ do _AccessMonitor_ seja instalado na máquina 192.168.1.74 (**é só um exemplo**) e esta máquina seja usada a partir da máquina (REFERER) 192.168.1.164 então teremos uma configuração do tipo:

```bash
## Environment variables for AccessMonitor Docker container
NODE_ENV=production
## Referer header value to allow requests to the API (e.g., from a frontend app)
REFERER=http://192.168.1.164
## Comma-separated list of IP ranges to block (e.g., "192.168.0.0/24,10.0.0.0/8")
IP_BLACKLIST_RANGES=""
```
Definido o `.env` estamos prontos para avançar na instalação do _container_ do AccessMonitor.

### 3. Construir a imagem Docker do AccessMonitor

```bash
docker build -t accessmonitor-docker .
```

### 4. Executar o container

```bash
docker run --env-file .env -p 3000:3000 accessmonitor-docker
```

Com isto é fortemente provável que o AccessMonitor apareça pela porta 3000! :-)

```bash
http://localhost:3000
```

Acaba de ficar com o _endpoint_ do _AccessMonitor_ na sua mão para o integrar nos seus desenvolvimentos.

Exemplos de chamadas ao _endpoint_ via Terminal:

**Exemplo 1:** _container AccessMonitor_ instalado em localhost e avaliações solicitadas a partir de localhost. 

```bash
```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://localhost" \
  "http://localhost:3000/amp/eval/$ENCODED"
```
```

Ou assim:

**Exemplo 2:** _container AccessMonitor_ instalado em 192.168.1.74 e avaliações solicitadas a partir de 192.168.1.164. No exemplo solicita-se a avaliação da _homepage_ `https://www.arte.gov.pt`.  

```bash
```bash
URL="https://www.arte.gov.pt"
ENCODED=$(echo -n "$URL" | base64)

curl -i \
  -H "Referer: http://192.168.1.164" \
  "http://192.168.1.74:3000/amp/eval/$ENCODED"
```
```


