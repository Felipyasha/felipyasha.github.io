# Meu Portfólio

Portfólio profissional desenvolvido com **Angular 21**, **Tailwind CSS v4** e **SSR**, focado em código limpo, TypeScript estrito, componentes standalone e identidade visual moderna (dark theme, glassmorphism, animações).

**Autor:** Felipe Nascimento — Dev Front End  
**Título do site:** Felipe Nascimento - Dev Front

---

## Índice

- [Funcionalidades](#funcionalidades)
- [Stack tecnológica](#stack-tecnológica)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Consumo da API EmailJS](#consumo-da-api-emailjs)
- [Scripts disponíveis](#scripts-disponíveis)
- [Desenvolvimento](#desenvolvimento)
- [Build](#build)
- [Testes](#testes)
- [Deploy](#deploy)
- [Arquitetura](#arquitetura)
- [Recursos adicionais](#recursos-adicionais)

---

## Funcionalidades

- **Página inicial (single-page)** com seções: Início, Sobre, Habilidades, Projetos, Experiência e Contato
- **Navegação por âncoras** com header fixo e destaque da seção ativa no scroll
- **Menu responsivo** (hambúrguer) para mobile
- **Listagem de projetos** com cards e link para página de detalhes (case study)
- **Página de detalhe do projeto** (`/project/:id`) com descrição completa, tecnologias, features e galeria
- **Formulário de contato** com validação (Reactive Forms) e envio via **EmailJS**
- **SSR (Server-Side Rendering)** com Express e hidratação no cliente
- **SEO** e performance com orçamentos de bundle e lazy loading onde aplicável
- **Tema escuro**, animações (blob, fade-in, etc.) e estética glassmorphism
- **Testes unitários** com Vitest

---

## Stack tecnológica

| Categoria        | Tecnologia |
|------------------|------------|
| Framework        | Angular 21 (standalone, signals, SSR) |
| Linguagem        | TypeScript 5.9 (strict mode) |
| Estilização      | Tailwind CSS v4, PostCSS |
| Ícones           | Devicon, Material Symbols (Google Fonts) |
| E-mail           | EmailJS (Browser) |
| Testes           | Vitest, jsdom |
| Deploy           | angular-cli-ghpages (GitHub Pages) |
| Ferramentas      | Angular CLI 21, Prettier |

---

## Estrutura do projeto

```
meu-portfolio/
├── public/                 # Assets estáticos (imagens, favicon)
├── src/
│   ├── app/
│   │   ├── components/     # Header, Footer
│   │   ├── models/         # Interfaces (Project, Skill, Experience, Lore)
│   │   ├── pages/          # Páginas e seções
│   │   │   ├── home-page/  # Container da home (todas as seções)
│   │   │   ├── home/       # Hero / apresentação
│   │   │   ├── about/      # Sobre mim
│   │   │   ├── skills/     # Habilidades
│   │   │   ├── projects/   # Lista e detalhe de projetos
│   │   │   ├── experience/ # Experiência profissional
│   │   │   └── contact/    # Formulário de contato
│   │   ├── services/       # ProjectService, EmailService
│   │   ├── app.config.ts
│   │   ├── app.routes.ts
│   │   └── app.ts
│   ├── environments/       # environment.ts (gitignore), environment.example.ts
│   ├── styles.css         # Estilos globais + tema Tailwind v4
│   ├── main.ts
│   ├── main.server.ts
│   └── server.ts          # Express para SSR
├── angular.json
├── package.json
├── tsconfig.json
└── README.md
```

---

## Pré-requisitos

- **Node.js** 20.x ou superior (recomendado LTS)
- **npm** 11.x (ou compatível com `packageManager` do `package.json`)

---

## Instalação

1. Clone o repositório:

```bash
git clone https://github.com/Felipyasha/meu-portfolio.git
cd meu-portfolio
```

2. Instale as dependências:

```bash
npm install
```

3. Configure o ambiente (obrigatório para o formulário de contato):

```bash
cp src/environments/environment.example.ts src/environments/environment.ts
```

Edite `src/environments/environment.ts` e preencha as chaves do EmailJS (veja [Variáveis de ambiente](#variáveis-de-ambiente)).

---

## Variáveis de ambiente

O projeto usa um arquivo de ambiente **não versionado** (`src/environments/environment.ts`). Use o exemplo:

- **Arquivo de exemplo:** `src/environments/environment.example.ts`
- **Arquivo real:** `src/environments/environment.ts` (criar a partir do exemplo)

Estrutura esperada:

```ts
export const environment = {
  production: false,
  emailjs: {
    publicKey: 'SUA_PUBLIC_KEY',
    serviceId: 'SEU_SERVICE_ID',
    templateId: 'SEU_TEMPLATE_ID'
  }
};
```

As chaves são obtidas no [painel do EmailJS](https://dashboard.emailjs.com/). Sem esse arquivo configurado, o envio de e-mail do formulário de contato falhará.

---

## Consumo da API EmailJS

O formulário de contato envia mensagens usando a **API do EmailJS** diretamente pelo navegador, sem backend próprio. O consumo é feito no serviço `EmailService` (`src/app/services/email.service.ts`).

### Fluxo

1. **Inicialização** — No construtor do `EmailService`, a SDK é inicializada com a chave pública:
   ```ts
   emailjs.init(environment.emailjs.publicKey);
   ```

2. **Envio** — O método `sendEmail(formData: ContactForm)` monta os parâmetros do template e chama a API:
   - `serviceId` e `templateId` vêm do `environment` (configurados no painel do EmailJS).
   - Os campos do formulário (nome, e-mail, mensagem) são mapeados para `templateParams` (`from_name`, `from_email`, `message`, `to_name`, `reply_to`).

3. **Resposta** — A chamada `emailjs.send(...)` retorna uma Promise; o serviço considera sucesso quando `response.status === 200` e devolve `true` ou `false` para o componente, que usa signals para loading e feedback (sucesso/erro).

### Onde é usado

- O componente **Contact** (`pages/contact`) usa o `EmailService` ao submeter o formulário (Reactive Forms). Os dados validados são enviados via `sendEmail()`; o template do EmailJS define o assunto, o corpo do e-mail e o destinatário no painel.

### Observações

- A **Public Key** fica exposta no cliente; o EmailJS usa **Service ID** e **Template ID** e restringe no painel quais domínios podem enviar, mantendo o uso seguro para formulários de contato.
- Para personalizar campos ou texto do e-mail, altere o template no [dashboard do EmailJS](https://dashboard.emailjs.com/) e, se necessário, os nomes das chaves em `templateParams` no `EmailService`.

---

## Scripts disponíveis

| Comando            | Descrição |
|--------------------|-----------|
| `npm start`        | Sobe o servidor de desenvolvimento em `http://localhost:4200` |
| `npm run build`    | Build de produção (saída em `dist/`) |
| `npm run watch`    | Build em modo desenvolvimento com watch |
| `npm test`         | Roda os testes unitários (Vitest) |
| `ng e2e`           | Testes e2e (configurar framework conforme necessidade) |
| `ng deploy`        | Deploy para GitHub Pages (angular-cli-ghpages) |

Para rodar o servidor SSR localmente após o build:

```bash
npm run build
npm run serve:ssr:meu-portfolio
```

---

## Desenvolvimento

```bash
npm start
```

Acesse **http://localhost:4200**. A aplicação recarrega automaticamente ao alterar os arquivos de código.

---

## Build

Build de produção (otimizado, com budgets configurados em `angular.json`):

```bash
ng build
```

Artefatos em `dist/`. Para apenas o cliente (sem SSR), ajuste `outputMode` em `angular.json` se necessário.

---

## Testes

Testes unitários com **Vitest**:

```bash
ng test
```

Arquivos de teste: `*.spec.ts` junto aos componentes e serviços (ex.: `email.service.spec.ts`, `project.service.spec.ts`, `contact.spec.ts`).

---

## Deploy

Deploy para **GitHub Pages** com o builder `angular-cli-ghpages`:

```bash
ng deploy
```

Configure no `angular.json` o `baseHref` para o repositório (ex.: `/meu-portfolio/`) se a aplicação não estiver na raiz do domínio.

---

## Arquitetura

### Rotas

| Rota            | Componente   | Descrição |
|-----------------|-------------|-----------|
| `''`            | `HomePage`  | Página única com todas as seções (lazy loaded) |
| `project/:id`   | `ProjectDetail` | Detalhe do projeto (case study) |

Scroll com âncoras (`#about`, `#skills`, `#projects`, `#experience`, `#contact`) e `scrollPositionRestoration` habilitado no router.

### Componentes principais

- **app-header:** Navegação fixa, menu mobile, botão “Voltar” na rota de detalhe, detecção da seção ativa no scroll.
- **app-footer:** Rodapé com links/redes.
- **Home:** Hero com apresentação (slides: “Felipe Nascimento” e “S-Rank Archer Developer”).
- **About, Skills, Experience, Contact:** Seções da home.
- **ProjectList:** Lista de projetos (dados do `ProjectService`).
- **ProjectDetail:** Página de detalhe por `id` (roteamento por `project/:id`).

### Serviços

- **ProjectService:** Lista e detalhe de projetos (fonte de dados em memória; pode ser trocada por API).
- **EmailService:** Envio do formulário de contato via EmailJS (`environment.emailjs`).

### Modelos (interfaces)

- `Project` — projetos (id, title, thumbnail, description, fullDescription, technologies, githubUrl, status, client, duration, features, processSteps, gallery).
- `Skill`, `SkillCard` — habilidades.
- `ExperienceCard` — experiências profissionais.
- `LoreCard` — seção “Sobre mim”.
- `ContactForm` — dados do formulário de contato (EmailService).

### Configuração da aplicação

- **app.config.ts:** `provideRouter` (com in-memory scrolling e anchor scrolling), `provideClientHydration(withEventReplay())`, tratamento global de erros.
- **Tailwind v4:** tema em `src/styles.css` com `@theme` (keyframes: blob, fade-in, spin-slow, bounce-slow, gradient) e estilos base (smooth scroll, fundo escuro).

---

## Recursos adicionais

- [Angular CLI — Overview e referência de comandos](https://angular.dev/tools/cli)
- [Angular Docs](https://angular.dev)
- [Tailwind CSS v4](https://tailwindcss.com/docs)
- [EmailJS](https://www.emailjs.com/docs/)
- [Vitest](https://vitest.dev/)

---

## Licença

Projeto privado. Entre em contato para uso ou referência.
