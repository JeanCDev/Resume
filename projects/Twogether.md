# ProfileAdmin2.0 (Twogether)

**Twogether** platform for personal (and couples) finance management, with additional modules unlocked by plan (paywall), including **Cashflow/Revenue**, **Backup/Storage**, **Chatbot**, **AiStudio**, and **Telegram Automation**.

> Note: business rules for modules/limits (Cashflow/Chatbot/AiStudio) are summarized in `llms.txt`.

---

## 1) Project architecture

- **Backend (Node.js + TypeScript + Fastify)**
  - Code in `src/`
  - Entry point: `src/server.ts`
  - Default API port: `3333`
  - Database: **MongoDB** via `mongoose`
  - Authentication: **JWT in a cookie** (`access_token`) via `@fastify/jwt` + `@fastify/cookie`

- **Frontend (React + TypeScript + Vite)**
  - Code in `client/`
  - Routes in `client/src/App.tsx`
  - Default Vite dev server port: `5173`

---

## 2) Run locally (dev) with frontend and backend separated

### 2.1) Requirements

- Node.js (recommended **>= 18**)
- MongoDB accessible (local or Atlas)

### 2.2) Environment variables (backend)

This project **does not** ship with a versioned `.env`. You must create a `.env` at the **repo root** (same folder as the root `package.json`) with at least:

```bash
NODE_ENV=development
UPLOAD_PATH=/uploads
MONGO_DB=profileadmin
ENV_URL=http://localhost:3333
JWT_SECRET_KEY=coloque-um-segredo-aqui
MONGO_URL=mongodb://localhost:27017/profileadmin
JWT_COOKIE_SECRET_KEY=coloque-outro-segredo-aqui
FILE_STORAGE_URL=https://seu-storage.exemplo.com/uploads
```

Important notes:

- `ENV_URL` is used for:
  - CORS (in production)
  - OAuth redirect (`/oauth/google/callback`)
  - building public file URLs in production
- `MONGO_URL` must be a valid Mongo connection string.

### 2.3) Install dependencies

At the repo root:

```bash
npm install
```

### 2.4) Start the backend (port 3333)

```bash
npm run dev
```

### 2.5) Start the frontend (port 5173)

In another terminal:

```bash
npm run client
```

 The frontend will talk to the backend via HTTP using **cookies** (credentials) and automatically adds the `timezone` header (see `client/src/helpers/Connector.ts`).

---

## 2.6) Operational backups (VPS/Linux + Windows)

 This repository includes 2 helper scripts:

 - **VPS/Linux (create/restore/cleanup):** `backup.sh`
 - **Windows (download from VPS):** `backupdownload.bat`

### 2.6.1) VPS/Linux — `backup.sh`

 **What it backs up**

 - **MongoDB database** using `mongodump` from the Docker container named `mongo`.
 - **Uploaded files** by compressing the folder `/usr/share/nginx/html/uploads` from the Docker container named `file-storage`.

 **Where it stores backups**

 - Local folder: `./backups`
 - File names:
   - `db_<DATE>.gz` (Mongo dump archive, gzipped)
   - `files_<DATE>.tar.gz` (uploads tarball)

 **Requirements**

 - Bash (Linux)
 - Docker CLI available to the user running the script
 - Running containers with names:
   - `mongo`
   - `file-storage`

 If your container names differ, edit these variables in `backup.sh`:

 - `MONGO_CONTAINER`
 - `NGINX_CONTAINER`
 - `NGINX_PATH`

 **How to run**

 1) Ensure the script is executable:
 
 ```bash
 chmod +x ./backup.sh
 ```

 2) Run:

 ```bash
 ./backup.sh
 ```

 3) Choose an option:

 - **1) Create New Backup**
   - Generates a timestamp (`YYYY-MM-DD_HH-mm-ss`) and creates:
     - a Mongo dump archive redirected to `./backups/db_<DATE>.gz`
     - a tar.gz of uploads redirected to `./backups/files_<DATE>.tar.gz`

 - **2) Restore Existing Backup**
   - Lists files in `./backups/` and prompts for the `<DATE>`.
   - Restores Mongo with `mongorestore` from `db_<DATE>.gz`.
   - Restores uploads by:
     - clearing the current uploads directory inside the `file-storage` container (`rm -rf /usr/share/nginx/html/uploads/*`)
     - extracting the tarball into the uploads path.

   Important:
   - The restore **deletes current uploads** before extracting, to avoid conflicts.
   - Only run restore when you are sure you want to overwrite the current database/files.

 - **3) Clean/Delete Backups**
   - Option **1**: deletes everything inside `./backups`
   - Option **2**: deletes backups older than 7 days (`find ... -mtime +7 -delete`)

 ### 2.6.2) Windows — `backupdownload.bat` (download backups from VPS)

 This script connects to a VPS via SSH and downloads backup files from a remote folder to a local folder.

 **What it does**

 - Lists files on the VPS (`ssh ... ls -1 <REMOTE_PATH>`)
 - Downloads using `scp`:
   - **Option 1**: downloads all files from the remote folder
   - **Option 2**: downloads a single file (supports wildcards like `db_*.gz`)

 **Requirements**

 - Windows with `ssh` and `scp` available in PATH
   - Typically provided by **Windows OpenSSH Client**.
 - SSH access to the VPS (key or password)

 **Configuration**

 Edit these variables at the top of `backupdownload.bat`:

 - `USER` (e.g. `root`)
 - `IP` (VPS public IP)
 - `REMOTE_PATH` (folder on VPS that contains the backups)
 - `LOCAL_PATH` (destination folder on Windows, default: `./backups_vps`)

 **How to run**

 - Double click `backupdownload.bat` or run in `cmd.exe`.
 - Select one of the options shown.

 ### 2.6.3) Troubleshooting

 - **Permission denied (publickey)**
   - Ensure your SSH key is configured and allowed on the VPS, or that password auth is enabled.

 - **`ssh`/`scp` not found on Windows**
   - Install/enable the Windows OpenSSH Client and reopen the terminal.

 - **Remote path not found**
   - Confirm the `REMOTE_PATH` exists on the VPS and matches the `BACKUP_DIR` used by `backup.sh`.

 - **Container name/path mismatch**
   - Update `MONGO_CONTAINER`, `NGINX_CONTAINER`, and `NGINX_PATH` in `backup.sh` to match your deployment.

---

## 3) Authentication and login

### 3.1) Username/password login

Endpoints:

- `POST /auth/login`
- `POST /auth/register`
- `POST /auth/validate`
- `POST /auth/logout`

The backend sets an `access_token` cookie containing the JWT.

### 3.2) Google login (OAuth)

Backend routes:

- `GET /oauth/status`
- `GET /oauth/google/authorize`
- `GET /oauth/google/callback`

#### Where to configure in the system (screen)

As admin, open the login configuration menu in the system. Enable SSO and fill the OAuth client id and secret.

Screen (admin):

- `client/src/Screens/LoginRegisterConfiguration/index.tsx`

Saved fields:

- `oauth.active` (enables SSO)
- `oauth.clientId`
- `oauth.clientSecret`
- `oauth.provider` (currently **google**)

**Redirect URI** used by the system:

- `${ENV_URL}/oauth/google/callback`

So, in dev usually:

- `http://localhost:3333/oauth/google/callback`

---

## 4) Global settings (System Configuration)

The project centralizes integrations and feature toggles in **SystemConfiguration**.

- Model: `src/Model/SystemConfiguration.ts`
- Schema: `src/schemas/SystemConfiguration.ts`
 - Routes (admin-only): `src/routes/systemConfig.ts`
  - `GET /system-config`
  - `PUT /system-config`

This configuration includes:

- **OAuth** (Google SSO)
- **Stripe** (payments)
- **Chatbot API keys** (OpenAI/Gemini)
- `allowRegistration` and `defaultLicense`

---

## 5) Payments (Stripe)

### 5.1) How Stripe works in this project

- Stripe is enabled by `SystemConfiguration.stripe.enabled`.
- The backend creates a **Checkout Session** using a Stripe **Product** and its `default_price`.
- The backend receives Stripe events via webhook and updates:
  - payments (`Payment`)
  - the user's plan / validity
  - family behavior (members)

Main routes:

- `POST /payments/create-link` (authenticated)
  - body: `{ "planId": "..." }`
  - returns `{ url }` to open Stripe Checkout

- `POST /payments/webhook/stripe` (Stripe webhook)
  - validates the signature with `stripe.webhookSecret`
  - processes events such as:
    - `checkout.session.completed`
    - `invoice.payment_succeeded`
    - `customer.subscription.updated`
    - `customer.subscription.deleted`
    - `invoice.payment_failed`

Code:

- Routes: `src/routes/payments.ts`
- Implementation: `src/Model/StripeModel.ts`

### 5.2) Where to configure Stripe in the system (screen)

Open Payment Methods configuration, enable Stripe, and fill the API key and webhook secret.

Screen (admin):

- `client/src/Screens/PaymentConfiguration/index.tsx`

Fields:

- `stripe.enabled`
- `stripe.apiKey` (e.g. `sk_live_...` / `sk_test_...`)
- `stripe.webhookSecret` (e.g. `whsec_...`)

### 5.3) How to create paid plans and link them to Stripe

#### Where to manage plans (admin screen)

Open Plans configuration and create a new plan or edit an existing one with payment settings (Stripe): the Stripe Product ID and (optionally) a promotion (coupon).

- `client/src/Screens/PlanList/index.tsx`
- Admin API:
  - `POST /plans/admin/create`
  - `POST /plans/admin/update`
  - `POST /plans/admin/list`
  - `POST /plans/admin/toggle`
  - `DELETE /plans/admin/delete/:_id`

Relevant plan fields:

- `priceCents` (value used internally and for payments history)
- `periodDays` (plan duration)
- `recurring` (defines checkout mode: `subscription` vs `payment`)
- `paymentGateway` (currently `stripe`)
- `paymentLink` (**required for Stripe**) – in this project it is the **Stripe Product ID**
- Promotions:
  - `isPromotional`
  - `promotionCodeId` (Stripe Promotion Code ID)
  - `originalValueCents` (reference “strikethrough” value)

#### Step by step (Stripe Dashboard)

1) Create a **Product** in Stripe.
2) Inside the Product, create a **Price**.
   - If the plan in the system has `recurring=true`, the Price must be recurring.
   - If `recurring=false`, the Price can be one-time.
3) Copy the **Product ID** (e.g. `prod_...`).
4) In the system admin, open **PlanList** and fill:
   - `paymentGateway = Stripe`
   - `paymentLink = <Product ID (prod_...)>`
5) (Optional) Promotion:
   - Create a **Coupon** and then a **Promotion Code** in Stripe.
   - Copy the promotion code id (e.g. `promo_...`) and save it in `promotionCodeId`.

#### How the backend uses it

When creating the session:

- the system calls `stripe.products.retrieve(plan.paymentLink)`
- it uses `product.default_price` as the purchase Price

This means:

- your Product must have a `default_price` configured in Stripe.

### 5.4) Payment callback in the frontend

After checkout, Stripe redirects to:

- `http://localhost:3333/payment/callback?status=success|cancel|failed`

The frontend has a screen that only notifies the result to the main tab via `BroadcastChannel`:

- `client/src/Screens/PaymentCallback/index.tsx`

---

## 6) AI integrations (Chatbot + AiStudio)

### 6.1) Supported providers

- **OpenAI** (SDK `openai`)
- **Google Gemini** (SDK `@google/genai`)

Keys are also stored in the database via `SystemConfiguration.chatbot`.

### 6.2) Where to configure the keys (screen)

As admin, open the AI integration screen, enable the AI provider and fill the API key.

- `client/src/Screens/AiConfiguration/index.tsx`

Fields:

- `chatbot.openAIApiKey`
- `chatbot.openAIApiActive`
- `chatbot.geminiAIApiKey`
- `chatbot.geminiAIApiActive`

The backend exposes:

- `GET /chatbot/providers` so the frontend can know which providers are enabled.

### 6.3) Chatbot (module)

Open the Chatbot menu with a non-admin user

- Screen: `client/src/Screens/Chatbot/`
- API: `src/routes/chatbot.ts`
  - `POST /chatbot/chat`
  - `GET /chatbot/messages`
  - `GET /chatbot/usage`

Access can be limited by license/plan (validated in the backend).

### 6.4) AiStudio (module)

Open the Studio menu with a non-admin user

- Screen: `client/src/Screens/AiStudio/`
- API: `src/routes/aiStudio.ts`
  - `POST /aistudio/generate` (generates PDF/IMAGE/EXCEL)
  - Contexts CRUD: `/aistudio/context/*`
  - Sharing: `/aistudio/sharewith`, `/aistudio/sharedwith`, `/aistudio/unshare`
  - `GET /aistudio/usage`

---

## 7) Main modules (screens) and how to use them

Frontend routes are in `client/src/App.tsx`.

### 7.1) Landing / Login / Register

- `/` LandingPage
- `/login` Login
- `/register` Register

### 7.2) Home

- `/` (signed in)

### 7.3) Revenue (Cashflow)

- `/revenue`
- Backend: `src/routes/revenue.ts`

Main features:

- create/edit/delete “boards”
- add income/outcome entries
- list items, totals, and filters by date/quickSearch
- categories per board
- share a board with other users (`sharewith`)

### 7.4) Backup / Storage

- `/backup`
- Backend: `src/routes/backup.ts`

Main features:

- create folders
- upload files (multipart)
- rename, delete
- sharing (`sharewith`)
- also view shared items (`/backup/shared/get`)

Important:

- uploads use `FILE_STORAGE_URL` to PUT the binary.

### 7.5) Family (invites)

- `/family`
- `/invite`
- Backend: `src/routes/family.ts`

Features:

- create/update family
- generate invite link (JWT token)
- accept/reject
- remove member

### 7.6) Telegram Automation

- `/telegramAutomation`
- `/telegramAutomationList`
- Backend: `src/routes/telegramautomation.ts`

Features:

- send an immediate message
- schedule messages with intervals (job scheduler)
- list and delete messages

You will need:

- `botToken` from BotFather
- `chatId` from the chat/recipient

### 7.7) Support

- `/support` (user)
- `/support-admin` (admin)
- Backend: `src/routes/support.ts`

User:

- create a ticket
- list my tickets
- cancel a ticket

Admin:

- list all tickets with filters/pagination
- view a ticket
- change status + add a response

---

## 8) Administration: Licenses, Plans and Limits (Paywall)

### 8.1) Licenses

- Screen (admin): `/licenseList`
- Backend: `src/routes/licenses.ts`
  - Model: `src/Model/LicenseModel.ts`

Licenses are used for:

- menu permissions
- limits and access to modules (Chatbot/AiStudio/Storage/Family etc.) through the linked special configuration

#### How to configure in the UI (step by step)

Open the Licenses screen as admin

Screen: `client/src/Screens/LicenseList/index.tsx`

1) Click **Add license**.
2) Fill **Name**.
3) (Optional) Select a **Special configuration** (combo).
   - This links `License.specialConfigId` (ref. `SpecialConfig`).
4) Check the menus that this license can access.
   - In the current UI, toggling a menu adds/removes the `menuCode` inside `menusPermission`.
   - Technical note: the `permissions` object (create/read/update/delete) exists in the type, but the UI currently stores them as `false`.
5) Click **Save**.

Edit/delete:

- To edit, select exactly **1** item in the list and use the floating **edit** button.
- To delete, select 1+ items and use the floating **trash** button.

Technical references:

- **Routes**:
  - `POST /licenses/createlicense`
  - `POST /licenses/updatelicense`
  - `POST /licenses/licenseslist`
  - `DELETE /licenses/deletelicense/:_id`
- **Class**: `LicenseModel`

### 8.2) Special Config (plan limits)

- Screen (admin): `/specialConfigList`
- Backend: `src/routes/specialConfig.ts`
  - Model: `src/Model/SpecialConfigModel.ts`

Special configurations include:

- `paywallActive`
- `chatbotAccess` + `chatbotMonthlyRequests`
- `aiStudioAccess` + `aiStudioMonthlyRequests`
- `storageAccess` + `storageLimitMB`
- Revenue board limits (with/without custom currency)

#### How to configure in the UI (step by step)

Open the Special Config screen as admin

Screen: `client/src/Screens/SpecialConfigList/index.tsx`

1) Click **Add special configuration**.
2) Fill **Name**.
3) Set limits:
   - **Boards without custom currency** → `revenueBoardsWithoutCustomCurrency`
   - **Boards with custom currency** → `revenueBoardsWithCustomCurrency`
   - **Family members limit** → `familyMembersLimit`
4) Enable/disable modules and (when enabled) their limits:
   - **Paywall active** → `paywallActive`
   - **Storage access** → `storageAccess`
     - **Storage limit (MB)** → `storageLimitMB`
   - **Chatbot access** → `chatbotAccess`
     - **Monthly chatbot requests** → `chatbotMonthlyRequests`
   - **Studio access** → `aiStudioAccess`
     - **Monthly Studio requests** → `aiStudioMonthlyRequests`
5) Click **Save**.

Edit/delete:

- To edit, use the row actions menu (`...`).
- To delete, use the row actions menu (`...`) and choose delete.

Technical references:

- **Routes**:
  - `POST /special-config/create`
  - `POST /special-config/update`
  - `POST /special-config/list`
  - `DELETE /special-config/delete/:_id`
- **Class**: `SpecialConfigModel`

#### Recommended order (linking)

Create first:

1) `SpecialConfig` (limits)
2) `License` (menus) pointing to `specialConfigId`
3) `Plan` pointing to `licenseId`

### 8.3) UserPlan (user view)

Open the Plans screen as a non-admin user

- Route: `/userPlan`
- File: `client/src/Screens/UserPlan/index.tsx`

It shows:

- current plan
- validity
- unlocked menus
- limits (special config)
- cards to change plan
- cancel button (when allowed)

### 8.4) Plans (admin) — creation and linking to license/Stripe

Screen (admin): `/planList`

- UI: `client/src/Screens/PlanList/index.tsx`
- Backend: `src/routes/plans.ts`
  - Model: `src/Model/PlanModel.ts`

#### How to create/edit in the UI (step by step)

Open the Plans menu as admin and edit/create a plan

1) Click **New plan**.
2) Fill:
   - **Name** → `name`
   - **Price (cents)** → `priceCents` (e.g. $7.99 = `799`)
   - **Period (days)** → `periodDays`
   - **Original value (optional)** → `originalValueCents` (used to show a “strikethrough” reference value)
3) Toggle the flags according to your product rules:
   - **Auto renewal** → `recurring`
   - **Refundable** → `refundable`
   - **New users only** → `newUsersOnly`
   - **Promotion active** → `isPromotional`
   - **Block user from changing plan** → `lockUserChange`
   - **Cancel immediately** → `cancelImmediately`
4) In **Gateway**, select `Stripe` → `paymentGateway`.
5) In **Link / Identifier**, fill `paymentLink`.
   - Important: in the backend (`StripeModel.createCheckoutSession`) this field is treated as the **Stripe Product ID** (`prod_...`).
6) (Optional) In **Promotion code**, fill `promotionCodeId` (Stripe Promotion Code ID `promo_...`).
7) Select the **License** (this defines which menus and limits the plan unlocks).
8) (Optional) Add **Timezones**.
   - If there are timezone-specific plans, the backend prioritizes plans whose `timezones` contains the user's timezone.
9) Click **Continue/Save**.

Technical notes:

- **Admin routes**:
  - `POST /plans/admin/create`
  - `POST /plans/admin/update`
  - `POST /plans/admin/list`
  - `POST /plans/admin/toggle`
  - `DELETE /plans/admin/delete/:_id`
- **Class**: `PlanModel`

---

## 9) Terms / i18n

Texts are provided by `TermProvider`.

There is a terms management screen available **only in development**:

- `/termManager`

The backend also restricts `termsRoutes` so it does not run outside `development`.

---

## 10) Quick setup checklist (admin)

After starting the project and signing in as admin:

1) **Configure Login/Register**
   - Screen: `LoginRegisterConfiguration`
   - (Optional) enable registration and set `defaultLicense`
   - (Optional) enable Google SSO and save `clientId`/`clientSecret`

2) **Configure AI (OpenAI/Gemini)**
   - Screen: `AiConfiguration`
   - enable provider and save the API key

3) **Configure Stripe**
   - Screen: `PaymentConfiguration`
   - enable and save `apiKey` + `webhookSecret`

4) **Create Special Configs** (limits)
   - Screen: `SpecialConfigList`

5) **Create Licenses** and link to Special Config
   - Screen: `LicenseList`

6) **Create Plans** and link them in Stripe (`paymentLink = prod_...`)
   - Screen: `PlanList`

---

## 11) Data modeling (MongoDB) — full schemas

Schemas live in `src/schemas/*` (Mongoose). Below is a summary of the **full fields** per collection.

### 11.1) User (`src/schemas/User.ts`)

- `admin`: boolean (default `false`)
- `changePassword`: boolean (default `false`)
- `name`: string (required)
- `login`: string (required, unique)
- `email`: string (unique, sparse, optional)
- `password`: string (required)
- `permission`: ObjectId (user license)
- `planId`: ObjectId ref `Plan` (current plan)
- `validUntil`: Date (optional)
- `willExpire`: boolean (default `false`)
- `family`: ObjectId ref `Family` (optional)
- `sso`: boolean (default `false`)
- `isNewUser`: boolean (default `true`)
- `createdAt`: Date
- `updatedAt`: Date
- `canceledAt`: Date (optional)

### 11.2) License (`src/schemas/License.ts`)

- `name`: string (required)
- `specialConfigId`: ObjectId ref `SpecialConfig` (optional)
- `menusPermission`: array (default `[]`)
  - items in the shape `{ menuCode: number, permissions: { read/create/update/delete: boolean } }`
- `createdAt`: Date
- `updatedAt`: Date

### 11.3) SpecialConfig (`src/schemas/SpecialConfig.ts`)

- `name`: string (required)
- `paywallActive`: boolean
- `chatbotAccess`: boolean
- `chatbotMonthlyRequests`: number (optional)
- `aiStudioAccess`: boolean
- `aiStudioMonthlyRequests`: number (optional)
- `revenueBoardsWithCustomCurrency`: number (optional)
- `revenueBoardsWithoutCustomCurrency`: number (optional)
- `familyMembersLimit`: number (optional)
- `storageAccess`: boolean
- `storageLimitMB`: number (optional)
- `createdAt`: Date
- `updatedAt`: Date

### 11.4) Plan (`src/schemas/Plan.ts`)

- `name`: string (required)
- `active`: boolean (default `true`)
- `priceCents`: number (optional)
- `periodDays`: number (optional)
- `recurring`: boolean (default `false`)
- `refundable`: boolean (default `false`)
- `paymentLink`: string (optional) — used as **Stripe Product ID** (`prod_...`) when `paymentGateway='stripe'`
- `timezones`: string[] (optional)
- `newUsersOnly`: boolean (default `false`)
- `isPromotional`: boolean (default `false`)
- `promotionCodeId`: string (optional) — Stripe Promotion Code ID (`promo_...`)
- `paymentGateway`: `'stripe'` (optional)
- `lockUserChange`: boolean (default `false`)
- `cancelImmediately`: boolean (default `false`)
- `originalValueCents`: number (optional)
- `licenseId`: ObjectId ref `License` (optional)
- `createdAt`: Date
- `updatedAt`: Date

### 11.5) Payment (`src/schemas/Payment.ts`)

- `gateway`: string (required) (e.g. `stripe`)
- `paymentToken`: string (required) (gateway token/identifier)
- `chargedCents`: number (required)
- `isFirstPayment`: boolean (default `false`)
- `checkoutSessionId`: string (optional)
- `userId`: ObjectId ref `User` (required)
- `planId`: ObjectId ref `Plan` (required)
- `type`: `'payment' | 'subscription'` (required)
- `status`: `'paid' | 'past_payment' | 'failed' | 'canceled' | 'pending_cancellation' | 'refunded'` (required)
- `periodEnd`: Date
- `createdAt`: Date
- `updatedAt`: Date (updated in `pre('save')`)

### 11.6) SystemConfiguration (`src/schemas/SystemConfiguration.ts`)

- `allowRegistration`: boolean
- `defaultLicense`: ObjectId ref `License`
- `defaultPlanId`: ObjectId ref `Plan` (optional)
- `stripe` (optional)
  - `apiKey`: string
  - `enabled`: boolean
  - `webhookSecret`: string
- `oauth` (optional)
  - `active`: boolean
  - `provider`: string
  - `clientId`: string
  - `clientSecret`: string
  - `redirectUri`: string
  - `tokenUri`: string
  - `authorizationUri`: string
  - `userInfoUri`: string
  - `userNode`: string
  - `emailNode`: string
  - `scopes`: string
- `chatbot` (optional)
  - `openAIApiKey`: string
  - `geminiAIApiKey`: string
  - `openAIApiActive`: boolean
  - `geminiAIApiActive`: boolean
- `createdAt`: Date
- `updatedAt`: Date

### 11.7) RevenueBoard (`src/schemas/RevenueBoard.ts`)

- `default`: boolean
- `userId`: ObjectId ref `User` (required)
- `parentRevenueBoardId`: ObjectId ref `RevenueBoard` (default `null`)
- `closingDay`: number
### 11.8) IncomeOutcome (`src/schemas/IncomeOutcome.ts`)

- `userId`: ObjectId ref `User` (required)
- `revenueBoardId`: ObjectId ref `RevenueBoard` (required)
- `parentIncomeOutcomeId`: ObjectId ref `IncomeOutcome` (default `null`)
- `name`: string (required)
- `income`: boolean
- `value`: number (required)
- `categoryId`: ObjectId ref `Category` (default `null`)
- `paymentMethod`: number (default `1`)
- `installments`: number (default `null`)
- `date`: Date (default `Date.now`)
- `hidden`: boolean (default `false`)
- `installmentData`: `{ current: number }` (default `null`)
- `values`: array
  - `{ _id, value, name, createdAt }`
- `createdAt`: Date
- `updatedAt`: Date

### 11.9) Category (`src/schemas/Category.ts`)

- `name`: string (required)
- `income`: boolean (required)
- `userId`: ObjectId ref `User` (required)
- `defaultHidden`: boolean (default `false`)
- `revenueBoardId`: ObjectId ref `RevenueBoard` (optional)

### 11.10) Backup/Storage (`Folder` / `File`)

Folder (`src/schemas/Folder.ts`):

- `name`: string (required)
- `parentFolderId`: ObjectId ref `Folder` (default `null`)
- `userId`: ObjectId ref `User` (required)
- `sharedWith`: ObjectId[] ref `User`
- `createdAt`: Date
- `updatedAt`: Date

File (`src/schemas/File.ts`):

- `name`: string (required)
- `path`: string (required)
- `url`: string (required)
- `parentFolderId`: ObjectId ref `Folder` (default `null`)
- `userId`: ObjectId ref `User` (required)
- `extension`: string
- `size`: number (required)
- `sharedWith`: ObjectId[] ref `User`
- `createdAt`: Date
- `updatedAt`: Date

### 11.11) Chatbot (`Conversation`, `ChatMessage`, `ChatbotUsage`)

Conversation (`src/schemas/Conversation.ts`):

- `name`: string (required)
- `messageCount`: number (default `0`)
- `createdAt`: Date
- `updatedAt`: Date
- `lastMessagePeriod`: string (YYYY-MM)
- `lastMessageAt`: Date
- `summaries`: `{ date: Date, text: string }[]`
- `type`: `'chatbot' | 'revenue'`
- `userId`: ObjectId

ChatMessage (`src/schemas/ChatMessage.ts`):

- `message`: string
- `llmModel`: string
- `createdAt`: Date
- `userId`: ObjectId
- `chatbot`: `'gpt' | 'gemini'`
- `role`: `'user' | 'assistant' | 'file-info'`
- `conversationId`: ObjectId ref `Conversation`

ChatbotUsage (`src/schemas/ChatbotUsage.ts`):

- `count`: number
- `period`: string
- `resetAt`: Date (optional)
- `createdAt`: Date
- `updatedAt`: Date
- `userId`: ObjectId ref `User`

### 11.12) AiStudio (`AiStudioContext`, `AiStudioUsage`)

AiStudioContext (`src/schemas/AiStudioContext.ts`):

- `userId`: string (index)
- `title`: string
- `placeholder`: string
- `content`: string
- `sharedWith`: ObjectId[]
- `createdAt`: Date
- `updatedAt`: Date

AiStudioUsage (`src/schemas/AiStudioUsage.ts`):

- `count`: number
- `period`: string
- `resetAt`: Date (optional)
- `createdAt`: Date
- `updatedAt`: Date
- `userId`: ObjectId ref `User`

### 11.13) SupportTicket (`src/schemas/SupportTicket.ts`)

- `userId`: ObjectId ref `User`
- `userName`: string
- `userEmail`: string
- `message`: string
- `adminResponse`: string (default `null`)
- `status`: `'open' | 'in_progress' | 'completed' | 'cancelled'`
- `createdAt`: Date (timestamps)
- `updatedAt`: Date (timestamps)

### 11.14) TelegramMessage (`src/schemas/TelegramMessage.ts`)

- `message`: string
- `chatId`: string
- `botToken`: string
- `imageUrl`: string (optional)

### 11.15) WebhookEvent (`src/schemas/WebhookEvent.ts`)

- `eventId`: string (unique, index)
- `type`: string
- `createdAt`: Date (timestamps)
- `updatedAt`: Date (timestamps)

---

## 12) API Reference (Fastify) — request examples (curl)

Notes:

- Routes are registered in `src/server.ts` with prefixes (e.g. `/user`, `/auth`, `/plans`, etc.).
- Authenticated routes require the `access_token` cookie.
- In the examples below, I use `-b "access_token=..."` (cookie) and `-H "timezone: ..."` when needed.

### 12.1) Auth (`src/routes/auth.ts`) — prefix `/auth`

Login:

```bash
curl -X POST http://localhost:3333/auth/login \
  -H "Content-Type: application/json" \
  -d '{"login":"admin","password":"123456","timezone":"America/Sao_Paulo"}' \
  -c cookies.txt
```

Validate (refresh cookie):

```bash
curl -X POST http://localhost:3333/auth/validate \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"timezone":"America/Sao_Paulo"}'
```

### 12.2) User (admin) (`src/routes/user.ts`) — prefix `/user`

List users:

```bash
curl -X POST http://localhost:3333/user/listusers \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"page":1}'
```

Create user (admin):

```bash
curl -X POST http://localhost:3333/user/newuser \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"name":"New User","login":"newuser","password":"123456","permission":"<LICENSE_ID>"}'
```

Update user (admin):

```bash
curl -X POST http://localhost:3333/user/updateuser \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"_id":"<USER_ID>","name":"Updated User","login":"newuser","permission":"<LICENSE_ID>","password":""}'
```

### 12.3) Licenses (admin) (`src/routes/licenses.ts`) — prefix `/licenses`

List:

```bash
curl -X POST http://localhost:3333/licenses/licenseslist \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"page":1}'
```

### 12.4) Special Config (admin) (`src/routes/specialConfig.ts`) — prefix `/special-config`

List:

```bash
curl -X POST http://localhost:3333/special-config/list \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"page":1,"quickSearch":""}'
```

### 12.5) Plans (`src/routes/plans.ts`) — prefix `/plans`

List user plans (and current plan):

```bash
curl -X POST http://localhost:3333/plans/list \
  -H "Content-Type: application/json" \
  -H "timezone: America/Sao_Paulo" \
  -b cookies.txt \
  -d '{"page":1}'
```

Cancel plan (user):

```bash
curl -X POST http://localhost:3333/plans/cancel \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{}'
```

### 12.6) Payments (`src/routes/payments.ts`) — prefix `/payments`

Generate checkout link (Stripe):

```bash
curl -X POST http://localhost:3333/payments/create-link \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"planId":"<PLAN_ID>"}'
```

Stripe webhook:

```bash
curl -X POST http://localhost:3333/payments/webhook/stripe \
  -H "stripe-signature: <STRIPE_SIGNATURE>" \
  -H "Content-Type: application/json" \
  --data-binary '@event.json'
```

### 12.7) OAuth (`src/routes/oauth.ts`) — prefix `/oauth`

Status:

```bash
curl http://localhost:3333/oauth/status
```

### 12.8) System Config (admin) (`src/routes/systemConfig.ts`) — prefix `/system-config`

Fetch config:

```bash
curl http://localhost:3333/system-config \
  -b cookies.txt
```

Save config (partial example):

```bash
curl -X PUT http://localhost:3333/system-config \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"allowRegistration":true,"stripe":{"enabled":true}}'
```

### 12.9) Revenue (`src/routes/revenue.ts`) — prefix `/revenue`

Create board:

```bash
curl -X POST http://localhost:3333/revenue/createrevenueboard \
  -H "Content-Type: application/json" \
  -H "timezone: America/Sao_Paulo" \
  -b cookies.txt \
  -d '{"name":"My board","groupByMonth":true,"inputsAndOutputs":true}'
```

### 12.10) Backup (`src/routes/backup.ts`) — prefix `/backup`

Create folder:

```bash
curl -X POST http://localhost:3333/backup/createfolder \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"folderName":"Docs"}'
```

### 12.11) Category (`src/routes/category.ts`) — prefix `/category`

Create category:

```bash
curl -X POST http://localhost:3333/category/createcategory \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"name":"Groceries","income":false,"defaultHidden":false}'
```

### 12.12) Chatbot (`src/routes/chatbot.ts`) — prefix `/chatbot`

Chat:

```bash
curl -X POST http://localhost:3333/chatbot/chat \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"model":"gpt-4o-mini","description":"Hello","chatbot":"gpt","attachments":[]}'
```

### 12.13) AiStudio (`src/routes/aiStudio.ts`) — prefix `/aistudio`

Generate content:

```bash
curl -X POST http://localhost:3333/aistudio/generate \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"prompt":"Generate a report","action":"PDF"}'
```

### 12.14) Telegram (`src/routes/telegramautomation.ts`) — prefix `/telegram`

Send immediate message:

```bash
curl -X POST http://localhost:3333/telegram/save-telegram-message \
  -H "Content-Type: application/json" \
  -H "timezone: America/Sao_Paulo" \
  -b cookies.txt \
  -d '{"message":"Hello","chatId":"<CHAT_ID>","botToken":"<BOT_TOKEN>"}'
```

### 12.15) Support (`src/routes/support.ts`) — prefix `/support`

Create ticket:

```bash
curl -X POST http://localhost:3333/support/ticket \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"name":"Jean","email":"jean@example.com","message":"I need help"}'
```

### 12.16) Terms (dev only) (`src/routes/termsRoutes.ts`) — prefix `/api`

```bash
curl http://localhost:3333/api/getTerms
```

---

## 13) Paywall in the frontend — actual flow (`PaywallProvider`)

Main file:

- `client/src/Contexts/PaywallContext/index.tsx`

### 13.1) When the paywall appears

- The paywall only runs when `user.specialConfig.paywallActive === true`.
- Open flow:
  - `openPlansDialog(reason?)`
  - Fetches plans from `POST /plans/list` and fills:
    - `plans` (list)
    - `userPlan` (current plan, if any)

### 13.2) Internal states and errors

- `plansOpen`: controls the `PlansDialog` modal.
- `plansLoading`: loading during plans fetch and checkout creation.
- `plansError`: string (term key) error; it is set via `getErrorMessage(e)`.
- `reason`: why the paywall opened:
  - `no_access`
  - `limit_reached`
  - `storage_limited`
  - `revenue_limit`

### 13.3) Plan migration (Stripe)

- When a plan is selected in `PlanCard`, the provider calls:
  - `POST /payments/create-link` with `{ planId }`
- The backend returns `{ url }` and the frontend runs:
  - `window.open(url, '_blank')`

### 13.4) Cancellation

- `cancelPlan(immediately?)` opens a confirm (`useConfirmAlert`) and, if confirmed:
  - calls `POST /plans/cancel`
- On error, the provider:
  - sets `plansError`
  - triggers a toast with `sonner.error(getTerm(plansError))`

---

## 14) UsersList (admin) — create users and manually assign licenses

Screen:

- `client/src/Screens/UsersList/index.tsx`

Admin can:

- **List users** (pagination)
- **Create a local user** (username/password)
- **Edit user**
- **Delete users**
- **Manually assign a license** via `permission` (which references `License._id`)

### 14.1) How to use in the UI (step by step)

1) Open the **Users** menu as admin.
2) Click **Add user**.
3) Fill:
   - **Name** (`name`)
   - **Login** (`login`)
   - **Password** and **Confirm password** (local user only)
   - **License** (combo) → `permission`
4) Click **Save**.

Edit/delete:

- Select 1 item → floating **edit** button.
- Select 1+ items → floating **trash** button.

### 14.2) Endpoints used

- `POST /user/listusers`
- `POST /user/newuser`
- `POST /user/updateuser`
- `DELETE /user/deleteuser/:_id`
- `POST /licenses/licenseslist` (to populate the licenses combo)

---

## 15) Authentication (JWT) + Licenses (menus) — how permissions are validated

This section documents how the backend validates:

- **JWT (cookie `access_token`)** to authenticate the user.
- **Admin account** to unlock administrative routes.
- **User license (`License.menusPermission`)** to unlock each user menu/module.

### 15.1) JWT: how validation works

File: `src/helpers/authenticate.ts`

- The backend expects the `access_token` cookie.
- The token is verified via `req.jwt.verify(...)`.
- If missing or expired, it returns `401` with `{ message: '100282' }`.
- When valid, the backend sets `req.user` with the JWT payload.

### 15.2) Admin vs regular user: route validations

There are 2 main protection patterns:

1) **Admin routes (require `user.admin === true`)**
   - Example: `src/routes/user.ts` blocks everything for non-admins, except `updateuserself` and `profile`.
   - Other admin routes usually call `authValidation` and then check `user.admin` (via `UserModel.getUserInfo()`).

2) **Module routes (require `License.menusPermission`)**
   - Validation happens via `UserModel.getValidatePermission(userId, menuCode)`.
   - Important: **the check is based on the presence of `menuCode`**, not `read/create/update/delete`.
     - In other words, today the backend treats “has access” as having the `menuCode` entry inside `menusPermission`.

### 15.3) How a menu is linked to a license (menuCode)

Frontend:

- Regular user menus are defined in `client/src/components/Sidebar/helper/allMenusList.ts`.
- Each item uses `title` as a **term code** (e.g. `100093`) and this is also used as the **menuCode**.
- At runtime, the UI blocks access when the user is not admin and does not have the `menuCode` in `menusPermission`.

Backend:

- `License.menusPermission[]` stores objects `{ menuCode, permissions }`.
- `User.permission` references the license.
- `UserModel.getValidatePermission(userId, menuCode)` checks whether the license contains the `menuCode`.

### 15.4) Table: menuCode -> term (UI) -> backend validation -> routes

The terms below come from:

- `client/src/term/pt_br.json`
- `client/src/term/en_us.json`

| menuCode | Term (pt_br) | Term (en_us) | How the backend validates | Routes (prefix) |
|---:|---|---|---|---|
| `100093` | Cashflow | Cashflow | `getValidatePermission(userId, 100093)` | `revenue/*` and `category/*` |
| `100085` | Backup | Backup | `getValidatePermission(userId, 100085)` to list/usage and `validateStorageAccess()` (SpecialConfig) for operations that consume storage | `backup/*` |
| `100270` | Família | Family | `getValidatePermission(userId, 100270)` | `family/*` |
| `100070` | Telegram | Telegram | `getValidatePermission(userId, 100070)` | `telegram/*` |
| `100356` | Chatbot | Chatbot | `validateChatbotAccess()` (SpecialConfig) | `chatbot/*` |
| `100556` | Studio | Studio | `validateAiStudioAccess()` (SpecialConfig) | `aistudio/*` |

Additional menus that appear in the UI:

- `100094` (**Perfil / Profile**) is “always visible” in the frontend and does not depend on a license to appear.
- `100476` (**Suporte / Support**) is currently protected in the backend only by JWT (`authValidation`), without license checks.
- `100369` (**Planos / Plans**) appears in user and admin menus (frontend). In the backend, access to `/plans/*` is mainly controlled by authentication and plan/payment business rules.

### 15.5) Administration menus (adminMenus)

Administration menus are defined in `client/src/components/Sidebar/helper/allMenusList.ts` as `adminMenus`.
These menus depend on `user.admin === true` (frontend) and, in the backend, routes usually also check `user.admin`.

| Term (code) | Screen (frontend) | Example related routes |
|---:|---|---|
| `100078` (Users) | `UsersList` | `/user/*` (admin) |
| `100102` (Licenses) | `LicenseList` | `/licenses/*` |
| `100323` (Special configuration) | `SpecialConfigList` | `/special-config/*` |
| `100240` (Login configuration) | `LoginRegisterConfiguration` | `/system-config` and `/oauth/*` |
| `100346` (Chatbot integrations) | `AiConfiguration` | `/system-config` and `/chatbot/*` (usage) |
| `100434` (Payment methods) | `PaymentConfiguration` | `/system-config` and `/payments/*` |
| `100369` (Plans) | `PlanList` | `/plans/*` (admin + user) |
| `100510` (Support administration) | `SupportAdmin` | `/support/admin/*` |

---