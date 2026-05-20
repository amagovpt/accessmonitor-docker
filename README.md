# accessmonitor-docker
AccessMonitor Docker

Para além do servidor da ARTE, I.P, com a _<span lang="en">dockerização</span>_, o AccessMonitor passa a poder ser instalado onde quiser. Na sua máquina local, na sua rede local, na sua intranet, como apoio ao seu <abbr title="Content Management System">CMS</abbr>, ... . Desta forma o AccessMonitor ganha graus de liberdade para avaliar ainda mais páginas, mesmo as que ainda não estão publicadas na Internet.

## Instalar uma imagem docker do AccessMonitor numa máquina local

As instruções abaixo pressupõem que já tem uma instalação de Docker a correr na máquina onde pretende instalar o AccessMonitor.

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

### Clonar o repositório `accessmonitor-docker`

De dentro da pasta `projetos/` vamos clonar o repositório `accessmonitor-docker`:

```bash
git clone https://github.com/amagovpt/accessmonitor-docker.git
```

O comando `git clone` cria automaticamente uma nova pasta com o nome do repositório - a chamada pasta de projeto.

### Criar e configurar o `.env`

De seguida vamos entrar na pasta do projeto `accessmonitor-docker`, vamos criar o ficheiro de ambiente `.env` com as propriedades descritas no `.env.example` e configurar as variáveis de acordo com as requisitos da máquina hospedeira.

```bash
# entrar na pasta do projeto
cd accessmonitor-docker
# criar o ficheiro ´.env´ a partir do ´.env.example´
cp .env.example .env
# editar e configurar as variáveis do ´.env´ com o editor ´vim´ ou outro qualquer
vim .env﻿
```

Definido o `.env` estamos prontos para:

### 1. Construir a imagem Docker

```bash
docker build -t accessmonitor-docker .
```

### 2. Executar o container

```bash
docker run --env-file .env -p 3000:3000 accessmonitor-docker
```

Com isto é fortemente provável que o AccessMonitor apareça pela porta 3000! :-)

```bash
http://localhost:3000
```
