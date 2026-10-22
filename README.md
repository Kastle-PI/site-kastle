# Site Kastle

Site institucional da equipe **Kastle**, formada por estudantes do curso de Desenvolvimento de Software Multiplataforma da Fatec de Registro (2023–2026). A página apresenta a equipe, sua missão, visão e valores, além de divulgar o **Learny** — aplicativo gamificado de ensino de inglês voltado para crianças com TEA (Transtorno do Espectro Autista).

## Sumário

- [Visão geral](#visão-geral)
- [Stack](#stack)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Como executar](#como-executar)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Scripts disponíveis](#scripts-disponíveis)
- [Funcionalidades](#funcionalidades)
- [Equipe](#equipe)

## Visão geral

Landing page de página única (single-page) com navegação por âncoras, contendo:

1. **Banner** com vídeo de fundo e identidade visual da Kastle.
2. **Equipe** — cards dos integrantes.
3. **Sobre nós / Serviços** — apresentação da equipe e dos serviços.
4. **Missão, Visão e Valores**.
5. **Portfólio (Learny)** — apresentação do aplicativo desenvolvido pela equipe.
6. **Contatos** — informações, redes sociais dos integrantes e formulário de contato com envio de e-mail via SMTP.

## Stack

- [Next.js 16](https://nextjs.org) (App Router)
- [React 19](https://react.dev)
- [TypeScript 5](https://www.typescriptlang.org)
- [Tailwind CSS 4](https://tailwindcss.com) (via `@tailwindcss/postcss`)
- [Nodemailer](https://nodemailer.com) para envio do formulário de contato
- [Swiper](https://swiperjs.com) para carrosséis
- [React Icons](https://react-icons.github.io/react-icons/)
- [`@next/third-parties`](https://nextjs.org/docs/app/building-your-application/optimizing/third-party-libraries) para integração com Google Analytics
- Fonte [Montserrat](https://fonts.google.com/specimen/Montserrat) via `next/font`

## Estrutura do projeto

```
site-kastle/
├── app/
│   ├── api/
│   │   └── contact/route.js     # Endpoint POST do formulário (Nodemailer)
│   ├── globals.css              # Estilos globais (Tailwind)
│   ├── layout.tsx               # Layout raiz + Google Analytics
│   └── page.tsx                 # Página principal (todas as seções)
├── components/
│   ├── CustomAlert.tsx          # Alerta visual de feedback do formulário
│   ├── InfoSection.tsx          # Bloco de texto + imagem reutilizável
│   ├── Members.tsx              # Cards da equipe
│   └── Navbar.tsx               # Barra de navegação com dropdown
├── constants/
│   └── index.ts                 # Itens do menu, membros e cores
├── public/
│   ├── icons/                   # Ícones (instagram, linkedin, success, error...)
│   ├── images/                  # Imagens (fatec, ftx, mockup, logo-learny...)
│   └── videos/                  # banner-video.mp4
├── next.config.ts
├── tsconfig.json
├── eslint.config.mjs
└── package.json
```

## Pré-requisitos

- **Node.js** 22 ou superior
- **npm**, **yarn**, **pnpm** ou **bun**

## Como executar

```bash
# 1. Instalar dependências
npm install

# 2. Criar o arquivo .env.local (ver seção abaixo)

# 3. Subir o servidor de desenvolvimento
npm run dev
```

Acesse [http://localhost:3000](http://localhost:3000).

Para gerar o build de produção:

```bash
npm run build
npm run start
```

Para testar e publicar no Cloudflare Workers:

```bash
npm run preview
npm run deploy
```

## Variáveis de ambiente

Crie um arquivo `.env.local` na raiz de `site-kastle/` com as variáveis abaixo. Elas são usadas pelo endpoint de contato (`app/api/contact/route.js`) e pelo Google Analytics (`app/layout.tsx`).

```env
# SMTP — envio do formulário de contato
SMTP_HOST=smtp.gmail.com
SMTP_PORT=465
SMTP_USER=seu-email@exemplo.com
SMTP_PASSWORD=sua-senha-de-app

# Google Analytics (opcional)
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

Observações:

- A porta `465` usa TLS implícito; `587` usa STARTTLS. O código define `secure` automaticamente com base na porta.
- No Cloudflare Workers, configure uma porta SMTP suportada pelo provedor, como `465` ou `587` (a porta `25` não é permitida para conexões TCP de saída).
- Para o Gmail, utilize uma **Senha de App** (não a senha da conta).
- Se `NEXT_PUBLIC_GA_ID` não estiver definida, o componente do GA simplesmente não é renderizado.

## Scripts disponíveis

| Script | Descrição |
| --- | --- |
| `npm run dev` | Sobe o servidor de desenvolvimento em `localhost:3000`. |
| `npm run build` | Gera o build de produção. |
| `npm run start` | Inicia o servidor em produção (após o build). |
| `npm run preview` | Gera o bundle OpenNext e executa uma prévia no runtime local do Workers. |
| `npm run deploy` | Gera o bundle OpenNext e publica no Cloudflare Workers. |
| `npm run upload` | Envia uma nova versão do Worker sem ativá-la. |
| `npm run lint` | Executa o ESLint. |

## Deploy no Cloudflare Workers

O workflow `.github/workflows/deploy-production.yml` executa lint, verificação TypeScript e deploy em cada push para `main`. Também pode ser iniciado manualmente em GitHub → Actions → Deploy production.

Configure os secrets no repositório GitHub em **Settings → Secrets and variables → Actions**:

- `CLOUDFLARE_API_TOKEN`: token de deploy específico para este projeto, com permissão de publicar Workers na conta Cloudflare correta.
- `CLOUDFLARE_ACCOUNT_ID`: Account ID da conta Cloudflare onde o Worker será publicado.

Depois do primeiro deploy, abra **Cloudflare → Workers & Pages → `site-kastle` → Settings → Variables and Secrets** e adicione as variáveis de runtime:

| Nome | Tipo |
| --- | --- |
| `SMTP_HOST` | Variable |
| `SMTP_PORT` | Variable (`465` ou `587`) |
| `SMTP_USER` | Secret |
| `SMTP_PASSWORD` | Secret |

O `SMTP_USER` também é usado como endereço destinatário. Nunca inclua credenciais SMTP ou o token da Cloudflare no repositório. Para habilitar o Google Analytics em produção, adicione `NEXT_PUBLIC_GA_ID` em **Settings → Secrets and variables → Actions → Variables** no GitHub; o workflow a injeta durante o build.

## Funcionalidades

- **Navegação por âncoras** entre as seções (`#home`, `#equipe`, `#sobreNos`, `#portifolio`).
- **Navbar responsiva** com menu dropdown de links úteis (Manual da Marca, GitHub, Artigo Científico).
- **Efeito de scroll** que altera o estilo da Navbar após a rolagem inicial.
- **Formulário de contato** com estado de loading, validação básica e feedback via `CustomAlert` (sucesso ou erro).
- **API Route** (`/api/contact`) que envia o conteúdo do formulário por e-mail via Nodemailer.
- **Google Analytics** carregado condicionalmente quando `NEXT_PUBLIC_GA_ID` estiver presente.
- **Layout responsivo** com breakpoints `md` e `lg` do Tailwind.

## Equipe

- **João Marcos Alecsandro Kirimis** — [Instagram](https://www.instagram.com/joaokirimis/) · [LinkedIn](https://www.linkedin.com/in/joão-marcos-alecsandro-kirimis-443218213)
- **Jorge Hashiguchi** — [Instagram](https://www.instagram.com/jooj_hashiguchi/) · [LinkedIn](https://www.linkedin.com/in/jorge-hahsiguchi)
- **Guilherme Leandro Martins** — [Instagram](https://www.instagram.com/guiix_33/) · [LinkedIn](https://www.linkedin.com/in/guilherme-leandro-martins)
- **Gabriel Yoshimitsu Cunha Shimabukuro** — [Instagram](https://www.instagram.com/gabriel_yoshimitsu/) · [LinkedIn](https://www.linkedin.com/in/gabriel-yoshimitsu)

**Contato:** jorge.hashiguchi2005@gmail.com · +55 (13) 99682-8069
