# BrBox APP

Plataforma de catálogo de jogos com sistema de recomendações por Inteligência Artificial.

## Visão Geral do Projeto

O **BrBox** é uma aplicação completa que consiste em três projetos principais:

1. **BRBOX_APP** - Aplicativo mobile (React Native)
2. **BRBOX_APP_API** - Backend REST API (Node.js/TypeScript)
3. **BRBOX_APP_IA_RECCOMENDER** - Sistema de recomendações com IA (Python)
4. **BRBOX_USER_MOCKER** - Utilitário para simular dados de usuários

---

## Tecnologias Utilizadas

### Frontend (Mobile)
- **React Native** 0.68.2
- **React Navigation** (Stack)
- **Axios** para requisições HTTP
- **AsyncStorage** para armazenamento local

### Backend (API)
- **Node.js** 16.x
- **TypeScript**
- **Express.js**
- **TypeORM** (ORM para PostgreSQL)
- **PostgreSQL** (Banco de dados)
- **JWT** (Autenticação)
- **Nodemailer** + **Google APIs** (Envio de e-mails)

### Sistema de Recomendação (IA)
- **Python** 3.x
- **Pandas**
- **Surprise** (Biblioteca de filtragem colaborativa)

---

## Estrutura do Repositório

```
BrBox_APP/
├── BRBOX_APP/                    # Aplicativo React Native
│   ├── src/                      # Código fonte
│   ├── android/                  # Configurações Android
│   ├── ios/                      # Configurações iOS
│   └── package.json
│
├── BRBOX_APP_API/                # Backend API
│   ├── src/
│   │   ├── Controller/           # Controladores (lógica de negócio)
│   │   ├── Model/               # Entidades do banco de dados
│   │   ├── View/                # Retornos das APIs
│   │   ├── Routes/              # Definição de rotas
│   │   ├── Middleware/          # Middlewares (auth, etc)
│   │   ├── services/            # Serviços (mailer, recomendação, etc)
│   │   └── app.ts              # Ponto de entrada
│   ├── views/                   # Templates de e-mail (EJS)
│   ├── .env                    # Variáveis de ambiente
│   └── package.json
│
├── BRBOX_APP_IA_RECCOMENDER/    # Sistema de IA
│   ├── collaborativeFilteringRec.py
│   ├── demographicFilteringRec.py
│   ├── main.py
│   ├── requirements.txt
│   └── algorithms/
│
└── BRBOX_USER_MOCKER/           # Simulador de usuários
    ├── index.js
    └── package.json
```

---

## Variáveis de Ambiente

Crie um arquivo `.env` na pasta `BRBOX_APP_API/` com as seguintes variáveis:

### Configuração do Servidor
```env
PORT=80
```

### Configuração do Banco de Dados (PostgreSQL)
```env
DATABASE_HOST="127.0.0.1"
DATABASE_PORT=5432
DATABASE_USERNAME="postgres"
DATABASE_PASSWORD="sua_senha_aqui"
DATABASE_DATABASE="brbox"
```

### Autenticação
```env
TOKEN_SECRET="sua_chave_secreta_aqui"
```

### Usuário Administrador (criado automaticamente na primeira execução)
```env
ADMIN_EMAIL='administrator@administrator.com'
ADMIN_USER='Administrator'
ADMIN_PASSWORD='123'
```

### Tipos de Avaliação
```env
AVALIATION_GOOD="Up vote"
AVALIATION_NEUTRAL="Neutral vote"
AVALIATION_BAD="Down vote"
```

### Configuração de E-mail (SMTP)
```env
MAIL_HOST='smtp.seudominio.com'
MAIL_PORT=587
MAIL='seu_email@seudominio.com'
MAIL_PASS="sua_senha"
MAIL_TO='destino@seudominio.com'
```

### OAuth Google (Gmail API)
```env
OAUTH_CLIENT_ID="seu_client_id"
OAUTH_CLIENT_SECRET="seu_client_secret"
OAUTH_AUTH_CODE="codigo_auth"
OAUTH_REFRESH_TOKEN="token_refresh"
OAUTH_ACCESS_TOKEN="token_access"
```

### Serviço de Recomendação (IA)
```env
RECCOMENDER='http://localhost:9000'
```

---

## Como Configurar e Rodar os Projetos

### 1. Configuração do Banco de Dados

1. Instale o **PostgreSQL** em sua máquina
2. Crie um banco de dados chamado `brbox`
3. Configure as variáveis de ambiente no arquivo `.env`

### 2. Backend API (BRBOX_APP_API)

```bash
# Acesse a pasta da API
cd BRBOX_APP_API

# Instale as dependências
npm install
# ou
yarn install

# Execute em modo desenvolvimento
npm run dev
```

**Scripts disponíveis:**
- `npm run dev` - Executa em modo desenvolvimento (TypeScript)
- `npm run build` - Compila o projeto
- `npm start` - Executa o projeto compilado

### 3. Aplicativo Mobile (BRBOX_APP)

```bash
# Acesse a pasta do app
cd BRBOX_APP

# Instale as dependências
npm install

# Execute no Android
npm run android

# Execute no iOS
npm run ios

# Inicie o Metro bundler
npm start
```

**Para gerar APK debug:**
```bash
# Gere o bundle
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res

# Compile o APK
cd android
./gradlew assembleDebug
```

O APK será gerado em: `android/app/build/outputs/apk/debug/app-debug.apk`

### 4. Sistema de Recomendação IA (BRBOX_APP_IA_RECCOMENDER)

```bash
# Acesse a pasta da IA
cd BRBOX_APP_IA_RECCOMENDER

# Crie um ambiente virtual (opcional)
python -m venv venv
# Ative no Windows: venv\Scripts\activate
# Ative no Linux/Mac: source venv/bin/activate

# Instale as dependências
pip install -r requirements.txt

# Execute o servidor
python main.py
```

O servidor IA rodar em `http://localhost:9000`

**Dependências Python:**
- pandas (manipulação de dados)
- scikit-learn (algoritmos de ML)
- scikit-surprise (sistema de recomendação)
- psycopg2 (conexão PostgreSQL)

### 5. Simulador de Usuários (BRBOX_USER_MOCKER)

Este utilitário cria dados de teste automaticamente:

1. Cria 20 usuários mock
2. Cria 25 tags de teste
3. Associa tags aleatórias aos jogos

```bash
# Acesse a pasta do mocker
cd BRBOX_USER_MOCKER

# Instale as dependências
npm install

# Execute
npm run run
```

**Nota:** Certifique-se de que a API está rodando em `http://localhost:8080` antes de executar o mocker.

---

## Sistema de Criação de Usuários

### Criação Automática (Administrador)

Na primeira execução da API, um usuário administrador é criado automaticamente com as credenciais definidas nas variáveis de ambiente:

- **E-mail:** `ADMIN_EMAIL` (padrão: `administrator@administrator.com`)
- **Usuário:** `ADMIN_USER` (padrão: `Administrator`)
- **Senha:** `ADMIN_PASSWORD` (padrão: `123`)

### Criação de Novos Usuários (via API)

**Endpoint:** `POST /user/create`

```json
{
  "username": "novo_usuario",
  "email": "usuario@email.com",
  "password": "senha123",
  "confirm_password": "senha123"
}
```

**Resposta de sucesso (200):**
```json
{
  "id": 1,
  "username": "novo_usuario",
  "email": "usuario@email.com",
  "auth_token": "eyJhbGciOiJIUzI1..."
}
```

### Login de Usuário

**Endpoint:** `POST /user/login`

```json
{
  "email": "usuario@email.com",
  "password": "senha123"
}
```

### Recuperação de Senha

1. **Esqueci a senha:** `POST /user/forgot_password`
   ```json
   { "email": "usuario@email.com" }
   ```

2. **Nova senha:** `POST /user/retrieve_password`
   ```json
   {
     "email": "usuario@email.com",
     "new_password": "nova_senha",
     "confirm_new_password": "nova_senha",
     "code": "1234"
   }
   ```

---

## Sistema de Tradução

O BrBox suporta múltiplos idiomas. A estrutura de tradução é gerenciada no frontend através de arquivos de idioma e no backend através de tabelas no banco de dados.

### Como funciona:

1. **Banco de Dados:** Tags, gêneros e valores de jogos são armazenados com descrições em diferentes idiomas
2. **Frontend:** O app detecta o idioma do dispositivo e carrega as traduções correspondentes
3. **API:** Retorna os dados com base no idioma solicitado

### Idiomas suportados:
- Português (pt-BR) - Padrão
- Inglês (en-US)
- Outros idiomas podem ser adicionados nas tabelas correspondentes

---

## Sistema de Recomendações com IA

O sistema de recomendações utiliza **Filtragem Colaborativa** para sugerir jogos aos usuários.

### Como funciona:

1. **Coleta de Dados:** A API coleta as avaliações dos usuários (tags, valores, contagens de votos)
2. **Envio para IA:** Os dados são enviados para o serviço Python
3. **Processamento (Python):**
   - Usa a biblioteca **Surprise** com algoritmo **SVD** (Singular Value Decomposition)
   - Analisa padrões de avaliações similares entre usuários
   - Prediz notas para jogos não avaliados pelo usuário
4. **Retorno:** A API recebe as recomendações e as exibe ao usuário

### Arquitetura do Sistema de IA:

```
BRBOX_APP_API ──────> axios POST ──────> BRBOX_APP_IA_RECCOMENDER (Python)
                                                    │
                                          CollaborativeFilter (SVD)
                                                    │
                                          Retorna predições
```

### Algoritmos utilizados:

- **Collaborative Filtering (Filtragem Colaborativa):** Baseado em SVD
- **Demographic Filtering:** Opcional, baseado em perfis demográficos

### Configuração do Endpoint:

A variável de ambiente `RECCOMENDER` define a URL do serviço de IA:
```env
RECCOMENDER='http://localhost:9000'
```

---

## Arquitetura da API

A API segue o padrão **MVC** (Model-View-Controller):

```
Request → Routes → Controller → Model → Banco de Dados
                ↓
              View (retorno JSON)
```

### Estrutura de Rotas:

- `/user` - Gerenciamento de usuários
- `/game` - Gerenciamento de jogos
- `/sugestion` - Sugestões

### Autenticação:

A API usa **JWT** (JSON Web Tokens). O token deve ser enviado no header:
```
Authorization: Bearer <token>
```

---

## Funcionalidades Principais

### Usuários
- [x] Cadastro/Login
- [x] Recuperação de senha
- [x] Atualização de perfil
- [x] Níveis de acesso (admin/usuário)

### Jogos
- [x] Listagem de jogos
- [x] Avaliação com tags
- [x] Gêneros e modos de jogo
- [x] Plataforma (Steam, Oficial, etc)
- [x] Links externos
- [x] Imagens
- [x] Tempo de jogo

### Sistema de Recomendação
- [x] Recomendação personalizada por IA
- [x] Top jogos avaliados
- [x] Lista de favoritos (Watchlist)

---

## Endpoints da API

### Usuários

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| POST | `/user/create` | Não | Criar novo usuário |
| POST | `/user/login` | Não | Login de usuário |
| POST | `/user/forgotPassword` | Não | Solicitar recuperação de senha |
| POST | `/user/retrievePassword` | Não | Definir nova senha |
| GET | `/user/:id` | JWT | Obter dados do usuário |
| PUT | `/user/update` | JWT | Atualizar perfil |
| POST | `/user/destroy` | JWT | Excluir conta |
| GET | `/user/` | Admin | Listar todos os usuários |

### Jogos

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/game` | Não | Listar jogos |
| GET | `/game/:id` | Não | Obter jogo específico |
| POST | `/game` | Admin | Criar jogo |
| PUT | `/game/:id` | Admin | Atualizar jogo |
| DELETE | `/game/:id` | Admin | Excluir jogo |

### Tags e Avaliações

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/tag` | JWT | Listar tags |
| POST | `/tag` | JWT | Criar tag |
| GET | `/value` | JWT | Listar valores de avaliação |
| POST | `/tagValue` | JWT | Criar avaliação com tags |
| POST | `/tagValueFormat` | JWT | Nova formato de avaliação |

### Géneros e Modos

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/genre` | JWT | Listar gêneros |
| POST | `/genre` | Admin | Criar gênero |
| GET | `/mode` | JWT | Listar modos de jogo |
| POST | `/mode` | Admin | Criar modo |

### Plataformas e Modelos de Negócio

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/platform` | Admin | Listar plataformas |
| POST | `/platform` | Admin | Criar plataforma |
| GET | `/businessModel` | JWT | Listar modelos de negócio |
| POST | `/businessModel` | JWT | Criar modelo de negócio |

### Watchlist e Sugestões

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/watchlist` | JWT | Listar favoritos |
| POST | `/watchlist` | JWT | Adicionar à lista |
| DELETE | `/watchlist/:id` | JWT | Remover da lista |
| GET | `/suggestion` | JWT | Obter recomendações IA |

### Utilitários

| Método | Endpoint | Autenticação | Descrição |
|--------|----------|--------------|-----------|
| GET | `/gameUtils` | Não | Utilitários de jogos |
| GET | `/gameTime` | JWT | Tempos de jogo |

---

## Esquema do Banco de Dados

### Entidades Principais

#### User (Usuário)
```
id: number (PK)
username: string
email: string (unique)
Password: string (hash bcrypt)
createdDate: Date
```

#### Admin (Administrador)
```
id: number (PK)
user_id: number (FK -> User)
```

#### Game (Jogo)
```
id: number (PK)
name: string
DLC: boolean
linkList_id: number (FK -> ExternalLinkList)
imageList_id: number (FK -> ImageList)
tagList_id: number (FK -> TagValueList)
businessModelList_id: number (FK -> BusinessModelList)
createdDate: Date
```

#### Genre (Gênero)
```
id: number (PK)
name: string
description: string
```

#### Mode (Modo de Jogo)
```
id: number (PK)
name: string
```

#### Platform (Plataforma)
```
id: number (PK)
name: string
```

#### Tag (Tag)
```
id: number (PK)
name: string
description_positive: string
description_negative: string
```

#### TagValue (Valor da Tag)
```
id: number (PK)
tag_id: number (FK -> Tag)
value_id: number (FK -> Value)
user_id: number (FK -> User)
```

#### Value (Valor de Avaliação)
```
id: number (PK)
name: string
```

#### TagValueList (Lista de Tags)
```
id: number (PK)
```

#### ImageList (Lista de Imagens)
```
id: number (PK)
```

#### ExternalLinkList (Lista de Links)
```
id: number (PK)
```

#### BusinessModelList (Modelo de Negócio)
```
id: number (PK)
```

#### Score (Pontuação)
```
id: number (PK)
game_id: number (FK -> Game)
```

#### Watchlist (Lista de Favoritos)
```
id: number (PK)
user_id: number (FK -> User)
game_id: number (FK -> Game)
```

#### Code (Código de Recuperação)
```
id: number (PK)
user_id: number (FK -> User)
code: string (hash bcrypt)
```

---

## Middlewares de Autenticação

A API possui dois tipos de autenticação:

### 1. `Auth.user` (Usuário Logado)
Verifica se o token JWT é válido. Permite acesso a rotas de usuário.

### 2. `Auth.admin` (Administrador)
Verifica se o token JWT pertence a um administrador. Permite acesso a rotas administrativas.

### Estrutura do Token JWT
```json
{
  "id": 1,
  "username": "admin",
  "email": "admin@teste.com",
  "admin": true
}
```

---

## Fluxo de Dados do Sistema de Recomendação

### 1. Coleta de Dados na API
```typescript
// Consulta SQL que coleta dados de avaliações
const values = await AppDataSource.query(`
  SELECT 
    u.id AS userId,
    g.id AS gameId,
    t.id AS tagId,
    t.name AS tag,
    v.id AS score,
    vote.countV AS vote_count,
    t.description_positive AS overview
  FROM game g
  INNER JOIN tag_value_list_tag_values_tag_value tvltvtv ON ...
`);
```

### 2. Envio para Serviço IA
```typescript
const data = {
  user: userId,
  games: totalGames,
  values: values
};
const ret = await axios.post(process.env.RECCOMENDER, data);
```

### 3. Processamento na IA (Python)
```python
# Usa SVD para calcular predições
svd = SVD()
trainset = data.build_full_trainset()
svd.fit(trainset)

# Prediz notas para todos os jogos
for i in range(gameAmount):
    predicted = svd.predict(userId, i + 1)
    self.predicted.append(predicted)
```

### 4. Retorno das Recomendações
```json
[
  { "user": 1, "game": 1, "est": 4.5 },
  { "user": 1, "game": 2, "est": 3.8 },
  ...
]
```

---

## Carregamento de Jogos da Steam

A API inclui um serviço de carregamento de jogos da Steam que é executado automaticamente na inicialização:

```typescript
// src/services/loadGames/steam/index.ts
class SteamLoader {
  async Run() {
    // Busca jogos da API pública da Steam
    // Popula o banco de dados com jogos encontrados
  }
}
```

Este serviço:
1. Consulta a API pública da Steam
2. Cria registros de jogos no banco de dados
3. Associa gêneros, modos e plataformas

---

## Envio de E-mails

O sistema utiliza **Nodemailer** para envio de e-mails com templates EJS:

### Templates disponíveis:
- `forgotPass.ejs` - Recuperação de senha

### Configuração SMTP
```env
MAIL_HOST='smtp.seudominio.com'
MAIL_PORT=587
MAIL='seu_email@seudominio.com'
MAIL_PASS="sua_senha"
```

### OAuth Google (Alternativo)
```env
OAUTH_CLIENT_ID="..."
OAUTH_CLIENT_SECRET="..."
OAUTH_REFRESH_TOKEN="..."
```

---

## Fluxo de Inicialização da API

Ao iniciar a API, o serviço de inicialização (`src/services/initialization.ts`) executa:

1. **Verifica usuários existentes**
   - Se não existirem, cria o usuário administrador padrão

2. **Inicializa valores de avaliação**
   - Cria: "Up vote", "Neutral vote", "Down vote"

3. **Inicializa plataformas**
   - Cria: "official", "steam"

4. **Carrega jogos da Steam**
   - Popula o banco com jogos da API da Steam

5. **Inicializa scores**
   - Cria registros de pontuação para cada jogo

---

## Estrutura do Frontend (React Native)

### Pastas Principais

```
BRBOX_APP/src/
├── components/          # Componentes reutilizáveis
│   ├── Button/
│   ├── GameCard/
│   ├── Header/
│   ├── TagCard/
│   ├── ImageCarousel/
│   └── ...
├── contexts/           # Contextos React (Estado global)
│   ├── Auth.tsx       # Context de autenticação
│   ├── Game.tsx       # Context de dados de jogos
│   ├── Theme.tsx      # Context de tema
│   └── Request.tsx    # Context de requisições
├── pages/             # Telas do aplicativo
│   ├── Home/
│   ├── Login/
│   ├── Register/
│   ├── GameInfo/
│   ├── Suggestion/
│   ├── Reccomended/
│   ├── Search/
│   └── ...
├── routes/            # Navegação
│   ├── auth.routes.tsx
│   ├── app.routes.tsx
│   └── index.tsx
└── App.tsx           # Componente principal
```

### Contextos Principais

#### AuthContext (`src/Contexts/Auth.tsx`)
Gerencia o estado de autenticação do usuário:
- Login/Logout
- Dados do usuário logado
- Token JWT
- Verificação de admin

#### GameContext (`src/Contexts/Game.tsx`)
Gerencia dados dos jogos:
- Lista de jogos
- Jogo selecionado
- Tags e avaliações

#### ThemeContext (`src/Contexts/Theme.tsx`)
Gerencia o tema visual:
- Tema claro/escuro
- Cores personalizadas

#### RequestContext (`src/Contexts/Request.tsx`)
Gerencia requisições HTTP:
- Configuração de headers
- Tratamento de erros
- Token de autenticação

### Principais Telas

| Tela | Descrição |
|------|-----------|
| Login | Tela de login |
| Register | Tela de cadastro |
| Home | Lista de jogos |
| GameInfo | Detalhes de um jogo |
| Search | Busca de jogos |
| Suggestion | Avaliação de jogos |
| Reccomended | Recomendações da IA |
| Profile | Perfil do usuário |
| Watchlist | Lista de favoritos |
| Admin (AddGame) | Adicionar jogos (admin) |
| Admin (TagRegister) | Registrar tags (admin) |

### Navegação

O app utiliza **React Navigation** com duas rotas principais:

1. **Rotas de Autenticação** (`auth.routes.tsx`)
   - Login
   - Register
   - ChangePassword
   - ForgotPassword

2. **Rotas do App** (`app.routes.tsx`)
   - Home (Tabs com BottomMenu)
   - Search
   - Profile
   - GameInfo
   - Suggestion
   - Reccomended
   - Watchlist

---

## Configurações de Segurança

- Senhas hasheadas com **bcrypt** (salt=10)
- Tokens JWT com chave secreta configurável
- Rotas administrativas protegidas
- Validação de e-mails duplicados

---

## Problemas Comuns

### Erro de conexão com banco de dados
- Verifique se o PostgreSQL está rodando
- Confirme as credenciais no `.env`

### Erro no bundle React Native
- Delete a pasta `node_modules` e rode `npm install` novamente
- Limpe o cache: `react-native start --reset-cache`

### IA não responde
- Verifique se o servidor Python está rodando na porta 9000
- Confirme a variável `RECCOMENDER` no `.env`

---

## Configurações Avançadas

### Executando em Produção

#### API
```bash
# Build
npm run build

# Copie o .env para a pasta build
cp .env build/

# Execute
npm start
```

#### React Native (APK)
```bash
# Gere o bundle de produção
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res

# Build release
cd android
./gradlew assembleRelease
```

### Variáveis de Ambiente em Produção

Recomenda-se alterar os valores padrão:
```env
TOKEN_SECRET="gerar_uma_chave_segura_aleatoria"
ADMIN_PASSWORD="senha_forte_segura"
DATABASE_PASSWORD="senha_forte_do_banco"
```

### Conexão Remota

Para conectar o app mobile à API em servidor remoto:

1. Altere a URL base no app (geralmente em `src/Contexts/Request.tsx`)
2. Configure o CORS na API
3. Use HTTPS em produção

---

## Contribuição

1. Fork o projeto
2. Crie uma branch (`git checkout -b feature/nova-feature`)
3. Commit suas mudanças (`git commit -m 'Add nova feature'`)
4. Push para a branch (`git push origin feature/nova-feature`)
5. Crie um Pull Request

---

## Licença

MIT License

---

## Autor

JL Software
