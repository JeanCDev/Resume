# Documentação Técnica do Tinsports

## 1. Visão Geral do Projeto

O **Tinsports** é uma aplicação mobile para gerenciamento de eventos esportivos (principalmente futebol). Permite que usuários criem jogos/partidas, convidem amigos, confirmem participação, avaliem outros jogadores após os jogos, e possui um sistema de assinatura premium (TinsBoss) para funcionalidades avançadas.

### Estrutura do Projeto

```
Tinsports/
├── Tinsports/          # App Mobile (React Native)
│   ├── src/
│   │   ├── screens/    # Telas do aplicativo
│   │   ├── Components/ # Componentes reutilizáveis
│   │   ├── Contexts/   # Contextos React (Auth, Request)
│   │   ├── services/   # Configuração de API
│   │   ├── routes/     # Navegação (Stack + Drawer)
│   │   └── utils/      # Types e funções utilitárias
│   ├── android/        # Projeto Android nativo
│   ├── ios/            # Projeto iOS nativo
│   └── package.json
│
├── Tinsports-API/      # Backend API (Node.js/Express)
│   ├── src/
│   │   ├── controllers/ # Controllers de lógica de negócio
│   │   ├── views/       # Views (tratamento de requisições)
│   │   ├── model/       # Models Mongoose (MongoDB)
│   │   ├── routes/      # Rotas Express
│   │   ├── services/    # Serviços (token, notifications)
│   │   ├── utils/       # Utilitários
│   │   ├── DB/          # Conexão MongoDB
│   │   └── __tests__/  # Testes unitários
│   └── package.json
│
└── Screenshots/        # Screenshots do app
```

---

## 2. Tecnologias Utilizadas

### 2.1 App Mobile (React Native)

| Tecnologia | Versão | Descrição |
|------------|--------|-----------|
| **React Native** | 0.73.3 | Framework principal para desenvolvimento mobile cross-platform |
| **React** | 18.2.0 | Biblioteca de UI |
| **TypeScript** | 5.0.4 | Superset JavaScript com tipagem estática |
| **React Navigation** | 6.x | Biblioteca de navegação (Stack + Drawer) |
| **Axios** | 0.21.1 | Cliente HTTP para requisições à API |
| **Styled Components** | 5.2.1 | Biblioteca de estilização CSS-in-JS |
| **React Native Vector Icons** | 8.1.0 | Ícones (FontAwesome) |
| **React Native Reanimated** | 2.0.0 | Animações de alto desempenho |
| **React Native Gesture Handler** | 1.10.3 | Sistema de gestos |
| **React Native Screens** | 2.18.1 | Otimização de navegação |
| **React Native Safe Area Context** | 3.2.0 | Tratamento de áreas seguras (notch) |
| **AsyncStorage** | 1.12.1 | Armazenamento local key-value |
| **DateTimePicker** | 3.5.2 | Componente de seleção de data/hora |
| **Image Picker** | 4.0.6 | Seleção de imagens da galeria/câmera |
| **Firebase Storage** | 12.8.0 | Armazenamento de imagens |
| **Firebase App** | 12.8.0 | Core do Firebase |
| **Moment Timezone** | 0.5.34 | Manipulação de datas e fusos horários |
| **Google Pay** | 2.1.0 | Pagamentos via Google Pay |

### 2.2 Backend (API)

| Tecnologia | Versão | Descrição |
|------------|--------|-----------|
| **Node.js** | >=18 | Runtime JavaScript server-side |
| **Express** | 4.17.1 | Framework web/server |
| **TypeScript** | 4.0.5 | Superset JavaScript com tipagem |
| **Mongoose** | 5.12.1 | ODM MongoDB |
| **JSON Web Token (JWT)** | 8.5.1 | Autenticação por tokens |
| **Bcrypt** | 5.0.1 | Criptografia de senhas |
| **Nodemailer** | 6.7.2 | Envio de e-mails (SMTP) |
| **Cors** | 2.8.5 | Middleware CORS |
| **Dotenv** | 8.2.0 | Variáveis de ambiente |
| **Moment Timezone** | 0.5.30 | Manipulação de datas |
| **Cron** | 1.8.2 | Agendamento de tarefas |
| **Winston** | 3.4.0 | Logging de aplicação |
| **Swagger Autogen** | 2.9.1 | Geração automática de documentação Swagger |
| **EJS** | 3.1.6 | Template engine |

### 2.3 Banco de Dados

| Tecnologia | Descrição |
|------------|-----------|
| **MongoDB** (MongoDB Atlas) | Banco de dados NoSQL principal |
| **Mongoose** | ODM para modelagem de dados |

### 2.4 Ferramentas de Desenvolvimento

| Tecnologia | Descrição |
|------------|-----------|
| **Jest** | Framework de testes |
| **Supertest** | Testes de API HTTP |
| **ts-node-dev** | Execução TypeScript em desenvolvimento |
| **ESLint** | Análise estática de código |
| **Prettier** | Formatação de código |

---

## 3. Como Rodar o Projeto Localmente

### 3.1 Pré-requisitos

- **Node.js** >= 18
- **npm** ou **yarn**
- **MongoDB** (local ou Atlas)
- **Android Studio** (para emulador Android) ou **Xcode** (para iOS)
- **Java JDK** 11+

### 3.2 Configuração do Backend (API)

1. Navegue para o diretório da API:
   ```bash
   cd Tinsports-API
   ```

2. Instale as dependências:
   ```bash
   npm install
   # ou
   yarn install
   ```

3. Configure as variáveis de ambiente:
   ```bash
   # O arquivo .env já existe, mas você pode criar um .env.local para desenvolvimento:
   cp .env .env.local
   ```

4. Edite o arquivo `.env.local` com suas configurações:
   ```env
   PORT=3000
   ENVIRONMENT='dev'
   MONGO_URL="sua_url_mongodb"
   TOKEN_SECRET="seu_token_secreto"
   MAIL_HOST='smtp.seu-email.com'
   MAIL_PORT=587
   E_MAIL='seu@email.com'
   MAIL_PASS='sua_senha'
   ```

5. Inicie o servidor em modo de desenvolvimento:
   ```bash
   npm run dev
   # ou
   yarn dev
   ```

   O servidor estará disponível em `http://localhost:3000`

6. Para gerar a documentação Swagger:
   ```bash
   npm run gen-doc
   ```

### 3.3 Configuração do App Mobile

1. Navegue para o diretório do app:
   ```bash
   cd Tinsports
   ```

2. Instale as dependências:
   ```bash
   npm install
   # ou
   yarn install
   ```

3. Configure a URL da API:
   - Edite o arquivo `env.ts`:
   ```typescript
   export default {
     apiUrl: "http://SEU_IP_LOCAL:3000/",  // Para emulador Android use 10.0.2.2
     publishableKey: 'pk_test_...',
     gatewayMerchantId: '...',
     merchantName: 'TinSoft',
   }
   ```

4. Execute o app no Android:
   ```bash
   npm run android
   # ou
   npx react-native run-android
   ```

5. Execute o app no iOS:
   ```bash
   npm run ios
   # ou
   npx react-native run-ios
   ```

6. Para iniciar o Metro bundler:
   ```bash
   npm start
   # ou
   npx react-native start
   ```

---

## 4. Como Criar a Build

### 4.1 Build Android (APK)

1. Configure as variáveis de ambiente Android SDK:
   ```bash
   export ANDROID_HOME=/caminho/para/Android/Sdk
   export PATH=$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/tools
   ```

2. Execute o build de release:
   ```bash
   cd android
   ./gradlew assembleRelease
   # ou no Windows
   gradlew.bat assembleRelease
   ```

3. O APK será gerado em:
   ```
   android/app/build/outputs/apk/release/app-release.apk
   ```

4. Para build de debug:
   ```bash
   ./gradlew assembleDebug
   # APK em: android/app/build/outputs/apk/debug/app-debug.apk
   ```

### 4.2 Build iOS

1. Execute:
   ```bash
   xcodebuild -workspace ios/Tinsports.xcworkspace -scheme Tinsports -configuration Release archive
   ```

2. Ou através do Xcode:
   - Abra `ios/Tinsports.xcworkspace`
   - Selecione Product > Archive

### 4.3 Build da API (Backend)

1. Compile o TypeScript:
   ```bash
   cd Tinsports-API
   npm run build
   # ou
   yarn build
   ```

2. Os arquivos compilados estarão em `dist/`

3. Para iniciar a API em produção:
   ```bash
   npm start
   # ou
   node ./dist/app.js
   ```

---

## 5. Modelos de Dados

### 5.1 User (Usuário)

```typescript
{
  _id: string;           // ID único do usuário
  name: string;          // Nome completo
  email: string;        // E-mail (único)
  pass: string;         // Senha criptografada (bcrypt)
  last_pass: string;   // Senha anterior
  reputation: number;   // Reputação (0-100)
  photo: string;        // URL da foto de perfil
  premium: boolean;     // Status premium (TinsBoss)
  code: string;         // Código de confirmação de e-mail
  confirmed: boolean;  // E-mail confirmado
  deletedAt: string;    // Soft delete
}
```

### 5.2 Game (Jogo/Evento)

```typescript
{
  _id: string;           // ID único do jogo
  name: string;         // Nome do evento
  type: string;         // Tipo de esporte (futebol, basquete, etc)
  location: string;     // Local do evento
  date: Date;           // Data do evento
  hour: string;         // Horário (HH:mm)
  description: string;  // Descrição opcional
  value: number;        // Valor da inscrição (se pago)
  host_ID: string;      // ID do criador/anfitrião
  finished: boolean;    // Jogo encerrado
  recurrence: boolean; // Recorrência semanal (apenas premium)
  deletedAt: string;    // Soft delete
}
```

### 5.3 GameList (Lista de Participantes)

```typescript
{
  _id: string;           // ID único
  game_ID: string;       // ID do jogo
  user_ID: string;       // ID do usuário
  confirmed: boolean;    // Confirmação de participação
}
```

### 5.4 Friend (Amizade)

```typescript
{
  _id: string;           // ID único
  user_ID: string;       // ID do usuário que recebeu o convite
  friend_ID: string;     // ID do amigo
  confirmed: boolean;    // Solicitação aceita
}
```

---

## 6. Todas as Chamadas de API

### 6.1 Autenticação

| Método | Endpoint | Descrição | Autenticação | Corpo da Requisição |
|--------|----------|-----------|--------------|---------------------|
| POST | `/login` | Login de usuário | Não | `{email, pass}` |
| POST | `/register/user` | Criar novo usuário | Não | `{name, email, pass, confPass}` |
| POST | `/code` | Confirmar código de e-mail | Não | `{code, email}` |
| POST | `/resend-code` | Reenviar código de confirmação | Sim | `{}` |
| POST | `/forgot-pass` | Esqueci minha senha | Não | `{email}` |
| PUT | `/change-pass` | Alterar senha | Sim | `{pass}` |

### 6.2 Usuários

| Método | Endpoint | Descrição | Autenticação |
|--------|----------|-----------|--------------|
| GET | `/user/:id` | Buscar usuário por ID | Sim |
| GET | `/register/user/:name` | Buscar usuários por nome | Sim |
| PUT | `/register/user` | Atualizar dados do usuário | Sim |
| PUT | `/register/photo` | Atualizar foto de perfil | Sim |
| POST | `/register/user/update-reputation` | Atualizar reputação | Sim |
| POST | `/premium` | Tornar-se premium | Sim |
| POST | `/not-premium` | Cancelar premium | Sim |
| DELETE | `/register/user` | Deletar conta | Sim |

### 6.3 Jogos/Eventos

| Método | Endpoint | Descrição | Autenticação |
|--------|----------|-----------|--------------|
| GET | `/games/home` | Jogos do usuário e amigos | Sim |
| GET | `/games/:_id` | Detalhes de um jogo | Sim |
| POST | `/games` | Criar novo jogo | Sim |
| PUT | `/games` | Atualizar jogo | Sim |
| DELETE | `/games/:_id` | Deletar jogo | Sim |

### 6.4 Amigos

| Método | Endpoint | Descrição | Autenticação |
|--------|----------|-----------|--------------|
| GET | `/friend` | Lista de amigos e convites | Sim |
| GET | `/friend/gameList/:gameId` | Verificar convite de jogo | Sim |
| POST | `/friend` | Enviar convite de amizade | Sim |
| POST | `/friend/confirm/:_id` | Confirmar amizade | Sim |
| DELETE | `/friend/:_id` | Remover amigo | Sim |

### 6.5 Lista de Jogos (Convites)

| Método | Endpoint | Descrição | Autenticação |
|--------|----------|-----------|--------------|
| GET | `/game-list/invite` | Lista de convites recebidos | Sim |
| POST | `/game-list` | Convidar usuário para jogo | Sim |
| POST | `/game-list/invite-confirmation` | Confirmar convite de jogo | Sim |
| DELETE | `/game-list/:_id` | Remover convite | Sim |

---

## 7. Todas as Telas do Aplicativo

### 7.1 Fluxo de Autenticação

#### 7.1.1 Home Screen (`/screens/Home`)
- **Descrição**: Tela inicial que apresenta o app antes do login
- **Funcionalidades**:
  - Exibe imagem "hero" e logo do app
  - Botão "Entrar" para navegar para tela de login
  - Botão "Inscreva-se" para navegar para registro
- **Navegação**: Login, Register

#### 7.1.2 Login Screen (`/screens/Login`)
- **Descrição**: Tela de autenticação
- **Funcionalidades**:
  - Campo de email
  - Campo de senha (ou recuperação de senha)
  - Botão "Entrar" para autenticação
  - Link "Esqueci minha senha" para recuperação
- **Validações**:
  - Campos obrigatórios
  - Credenciais inválidas mostram modal de erro
- **Autenticação**: POST `/login`
- **Navegação**: Main (após sucesso)

#### 7.1.3 Register Screen (`/screens/Register`)
- **Descrição**: Tela de criação de conta
- **Funcionalidades**:
  - Campos: Nome, Email, Senha, Confirmar Senha
  - Validação de correspondência de senhas
  - Verificação de caracteres especiais
- **Autenticação**: POST `/register/user`
- **Navegação**: Main (após registro)

### 7.2 Telas Principais (App Logado)

#### 7.2.1 Main Screen (`/screens/Main`)
- **Descrição**: Tela principal do app após login - Dashboard de jogos
- **Funcionalidades**:
  - Abas de navegação: "Seus", "Amigos", "Convites"
  - Lista de jogos do usuário
  - Lista de jogos de amigos
  - Lista de convites de jogos recebidos
  - Pull-to-refresh para atualizar dados
  - Botão flutuante (+) para criar novo evento
  - Limitação de 5 jogos para usuários não-premium
- **Dados carregados**: GET `/games/home`
- **Navegação**: CreateEvent, GameInfo, Profile, SearchFriend, Premium

#### 7.2.2 CreateEvent Screen (`/screens/CreateEvent`)
- **Descrição**: Tela de criação/edição de jogos
- **Funcionalidades**:
  - Campos: Nome, Tipo de partida, Local, Data, Hora
  - Checkbox "Evento pago?" com campo de valor (R$)
  - Campo de descrição
  - Para premium: Checkbox "Repetir semanalmente" (recorrência)
- **Date Picker**: Seleção de data via componente nativo
- **Time Picker**: Seleção de hora via componente nativo
- **Validações**:
  - Todos os campos obrigatórios
  - Data não pode ser anterior à atual
  - Valor máximo de R$ 99,99
- **Criação**: POST `/games`
- **Edição**: PUT `/games`
- **Navegação**: Main (após salvar)

#### 7.2.3 GameInfo Screen (`/screens/GameInfo`)
- **Descrição**: Detalhes de um jogo específico
- **Funcionalidades**:
  - Exibe informações completas do jogo
  - Lista de participantes confirmados
  - Para anfitrião não-finalizado: Botão "Convide amigos"
  - Para anfitrião finalizado: Botão "Avaliar participantes"
  - Avaliação de participantes (pagou/não pagou, participou/não participou)
- **Dados carregados**: GET `/games/:id`
- **Convites**: POST `/game-list`
- **Confirmação**: POST `/game-list/invite-confirmation`
- **Avaliação**: POST `/register/user/update-reputation`
- **Navegação**: Main, InviteUsersModal

#### 7.2.4 Profile Screen (`/screens/Profile`)
- **Descrição**: Perfil do usuário
- **Funcionalidades**:
  - Foto de perfil (editável)
  - Nome do usuário
  - Métrica de reputação (0-100)
  - Opções de navegação:
    - Lista de amigos
    - Convites de jogos
    - Editar perfil
    - Ver jogos de amigos (se perfil de outro usuário)
    - Sair
- **Dados carregados**: GET `/user/:id`
- **Edição de perfil**: PUT `/register/user`
- **Foto**: PUT `/register/photo`
- **Navegação**: FriendsList, InviteList, EditProfileModal

#### 7.2.5 SearchFriend Screen (`/screens/SearchFriend`)
- **Descrição**: Busca e envio de convites de amizade
- **Funcionalidades**:
  - Campo de busca por nome
  - Lista de resultados de busca
  - Botão "Adicionar amigo" em cada resultado
  - Filtra o próprio usuário da lista
- **Busca**: GET `/register/user/:name`
- **Envio de convite**: POST `/friend`

#### 7.2.6 FriendsList Screen (`/screens/FriendsList`)
- **Descrição**: Lista de amigos e convites de amizade
- **Funcionalidades**:
  - Abas: "Amigos", "Convites"
  - Lista de amigos confirmados
  - Lista de convites recebidos
  - Ações: Confirmar convite, Remover amigo
- **Dados carregados**: GET `/friend`
- **Confirmação**: POST `/friend/confirm/:id`
- **Remoção**: DELETE `/friend/:id`
- **Navegação**: Main, Profile

#### 7.2.7 InviteList Screen (`/screens/InviteList`)
- **Descrição**: Lista de convites de jogos recebidos
- **Funcionalidades**:
  - Lista de convites de jogos
  - Informações do jogo: Anfitrião, nome, data, hora, local
  - Botões: Confirmar, Recusar
- **Dados carregados**: GET `/game-list/invite`
- **Confirmação**: POST `/game-list/invite-confirmation`
- **Recusa**: DELETE `/game-list/:id`

#### 7.2.8 Premium Screen (`/screens/Premium`)
- **Descrição**: Tela de assinatura TinsBoss
- **Funcionalidades**:
  - Exibe benefícios do premium
  - Opções de assinatura:
    - Mensal: R$ 9,99
    - Trimestral: R$ 34,99
    - Anual: R$ 99,99
  - Integração com Google Pay (Stripe)
- **Benefícios**:
  - Cadastrar eventos ilimitados
  - Cadastrar eventos recorrentes
- **Pagamento**: Google Pay (SDK react-native-google-pay)

---

## 8. Componentes Principais

### 8.1 Componentes de UI

| Componente | Descrição |
|------------|-----------|
| **GameCard** | Card de exibição de jogo na lista |
| **UserCard** | Card de usuário com foto, nome, reputação |
| **InviteCard** | Card de convite de jogo |
| **Header** | Header com botão de voltar |
| **Input** | Campo de entrada estilizado com label |
| **Loading** | Spinner de carregamento |
| **Tab** | Abas de navegação |
| **Badge** | Badge informativo (tipo, valor, horário) |
| **Checkbox** | Checkbox estilizado |
| **Button** | Botão padronizado |

### 8.2 Modais

| Modal | Descrição |
|-------|-----------|
| **GenericMessageModal** | Modal genérico de mensagens/-confirmação |
| **CodeConfirmationModal** | Modal de confirmação de código de e-mail |
| **InviteUsersModal** | Modal para convidar usuários para jogo |
| **EditProfileModal** | Modal de edição de perfil |
| **UserPhotoModal** | Modal de alteração de foto de perfil |
| **EvaluationModal** | Modal de avaliação de participantes |

---

## 9. Contextos e Estados

### 9.1 AuthContext (`/Contexts/Auth.tsx`)
Gerencia o estado de autenticação:
- `user`: Dados do usuário logado
- `signed`: Boolean de verificação de login
- `temporaryToken`: Token temporário
- `loading`: Estado de carregamento
- `signIn()`: Função de login
- `register()`: Função de registro
- `signOut()`: Função de logout
- `checkLogin()`: Verifica se usuário está logado

### 9.2 RequestContext (`/Contexts/Request.tsx`)
Gerencia requisições HTTP:
- `get()`: Requisição GET
- `post()`: Requisição POST
- `put()`: Requisição PUT
- `destroy()`: Requisição DELETE
- `uploadPhoto()`: Upload de foto via Firebase Storage

---

## 10. Estrutura de Rotas

### 10.1 Rotas de Autenticação (auth.routes.tsx)
```
AuthStack:
├── Home     -> Tela inicial
├── Login    -> Login
└── Register -> Cadastro
```

### 10.2 Rotas do App (app.routes.tsx)
```
AppStack (Drawer + Stack):
├── Main (Drawer)
│   ├── Home (Tela principal)
│   └── Sair (Logout)
├── GameInfo    -> Detalhes do jogo
├── CreateEvent -> Criar/editar evento
├── Profile     -> Perfil do usuário
├── SearchFriend -> Busca de amigos
├── FriendsList -> Lista de amigos
├── InviteList  -> Lista de convites
└── Premium     -> Assinatura TinsBoss
```

---

## 11. Configurações de Ambiente

### 11.1 App Mobile (env.ts)
```typescript
{
  apiUrl: "http://3.137.136.205:3000/",  // URL da API
  publishableKey: 'pk_test_...',         // Chave Stripe
  gatewayMerchantId: 'BCR2DN4T5CFODI3F', // ID Merchant Google Pay
  merchantName: 'TinSoft'                // Nome do merchant
}
```

### 11.2 Backend (.env)
```env
PORT=3000
ENVIRONMENT='dev'
MONGO_URL=...
TOKEN_SECRET="P3n15L0n6o"
MAIL_HOST='smtp.umbler.com'
MAIL_PORT=587
E_MAIL='tinsports@tinsoft.com.br'
MAIL_PASS='u#e+23LZLd'
```

---

## 12. Funcionalidades Principais

### 12.1 Sistema de Jogos
- Criação de eventos esportivos
- Definição de tipo, local, data, hora
- Eventos pagos com valor
- Eventos recorrentes (premium)
- Encerramento de jogos pelo anfitrião

### 12.2 Sistema de Convites
- Convite de amigos para jogos
- Confirmação/recusa de convites
- Lista de participantes

### 12.3 Sistema de Amizades
- Busca de usuários por nome
- Envio de solicitações
- Aceitação/recusa de solicitações
- Lista de amigos

### 12.4 Sistema de Reputação
- Avaliação de participantes após jogo
- Criteria: pagou/não pagou, participou/não participou
- Reputação de 0-100

### 12.5 Sistema Premium (TinsBoss)
- Jogos ilimitados
- Eventos recorrentes
- Assinatura via Google Pay

### 12.6 Autenticação
- Login por email/senha
- Cadastro com confirmação de email
- Recuperação de senha
- JWT token

---

## 13. Testes

### 13.1 Executando Testes

**Backend (API):**
```bash
cd Tinsports-API
npm test
```

**App Mobile:**
```bash
cd Tinsports
npm test
```

---

## 14. Deployment

### 14.1 Backend (Heroku/AWS)
1. Build: `npm run build`
2. Deploy para Heroku/AWS EC2
3. Configurar variáveis de ambiente
4. Conectar ao MongoDB Atlas

### 14.2 App Mobile
1. Build de release Android (.apk)
2. Build de release iOS (.ipa)
3. Publicação: Google Play Store / Apple App Store

---

## 15. Autores

- **Jean Carlos Gomes**
- **Lucas Fellipe Mondini Pereira**
- **Dominick Brasileiro**

---

## 16. Licença

ISC License

---

*Documentação gerada em Fevereiro de 2026*
