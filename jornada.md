# Testes com NextJs :)

## Motivação do projeto

NextJs é um framework voltado para desenvolvedores React feito para facilitar a criação de aplicações.
Estou tentando aprender o NextJs porque já tenho alguma experiencia com React, e acho interessante as otimizações que o framework oferece.
Neste projeto quero tentar aprender mais sobre as configurações e peculiaridades do NextJs. Mas a principal motivação foi eu não conseguir configurar next com websockets e trpc num projeto feito com o create t3-app.

# Passos com o projeto

## 1. .devcontainer

### Contexto

Normalmente eu trabalho numa maquina com ubuntu, mas minha maquina pessoal é windows, então para ter (basicamente) a mesma experiencia de desenvolvimento estou usando devcontainers.
DevContainers é uma extensão para o vscode que junto com o docker e o wsl2 me permite desenvolver numa maquina linux sem sair do meu windows :).
Com o devcontainer eu consigo especificar exatamente o que eu preciso na maquina, e caso bugs estranhos aconteçam, eu posso pedir ajuda mais facilmente porque qualquer pessoa teria a mesma maquina para reproduzir o problema.

---

#### O 1º passo

Iniciar o docker no windows e criar um devcontainer com pela extensão do vscode.
Escolhi o container base do ubuntu na versão jammy.
Adicionei as features(opções adicionais) "node", "typescript", "zsh", "common utils"(para o zsh) e "shell history".
Eu poderia configurar mais algumas coisas nas features, mas preferi deixar no padrão.
Depois de terminar a configuração, o vscode reabre no container de desenvolvimento, o docker começa a criar o container, e depois de alguns segundos/minutos, meu ambiente ubuntu está pronto para o desenvolvimento.
Os ultimos retoques para começar a codar é re-habilitar as extensões relevantes no vscode, porque os containers de desenvolvimento não vem com todas as extensões do seu vscode habilitadas por padrão, então algumas extensões como prettier precisam ser habilitadas manualmente no container. (pra mim esse comportamento é até bem util, pois meu vscode padrão no windows geralmente tem extensões inuteis para alguns projetos de webdev)

---

## 2. Tutorial

Vou seguir o [tutorial do Next.js](https://nextjs.org/learn/dashboard-app), criar um dashboard financeiro simplificado.
Ele vai ter:

- uma homepage
- uma pagina de login
- uma pagina de dashboard protegida com autenticação
- e a habilidade para usuarios de criar, editar e deletar pagamentos
  O dashboard tambem vai ter um banco de dados.

#### O 2º passo

Criar o app usando o create-next-app@latest

```zsh
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example" --use-pnpm
```

Abrir o projeto

```zsh
cd nextjs-dashboard
```

###### O conteudo do projeto

/app: Contem todas as rotas, componentes e logica do app. É aqui que vou trabalhar seguindo o tutorial
/app/lib: Contem funções usadas no app, como utils para fazer o fetch de dados.
/app/ui: Contem todos os componentes de UI do app.
/public: Contem todos os assets staticos do app como imagens.
Config Files: Arquivos de configuração como next.config.js na raiz do app. A maioria dos arquivos foi criado pelo create-next-app. Provavelmente não vou precisar alterar seguindo o tutorial

#### O 3º passo

Rodar instalar as dependencias

```zsh
pnpm i
```

E depois o

```zsh
pnpm dev
```

E agora o app vai estar rodando no http://localhost:3000, sem nenhum estilo.

#### O 4º passo

Estilizar.
Dentro do `nextjs-dashboard/app/ui`, tem o arquivo `global.css`. Geralmente o css global é importado num componente bem alto na arvore do app, como o [root layout](https://nextjs.org/docs/app/building-your-application/routing/pages#root-layout-required).
No `app/layout.tsx`, importo o css

```TSX
import '@/app/ui/global.css';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

###### Algo interessante sobre o 4º passo

O projeto do tutorial usa tailwind, e o `create-next-app` pergunta se você quer usar tailwind para ja deixar tudo configurado.
Uma coisa interessante sobre o tutorial é que ele é interativo. Ele tem algumas perguntas sobre o que você acabou de ler e fazer.
Nesse ponto do tutorial, ele pediu para adicionar uma div estilizada com tailwind acima da tag `p`, para testar o taiwind, e logo em seguida teve uma pergunta sobre o que essa div mostrava na tela.

##### CSS Modules

Uma outra opção que o tutorial sugere caso não queira usar Tailwind é o CSS Modules. Ele permite criar estilização de css num escopo de componente, e lida os nomes de classes evitando colisões de nomes.
O tutorial dá um exemplo que faz a mesma coisa que a div com taiwind mas usando CSS Modules.
Criando no `app/ui`, o arquivo `home.module.css` e colocando o seguinte:

```CSS
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

e no `app/page.tsx`, importamos o estilo e colocamos a div

```TSX
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import styles from '@/app/ui/home.module.css';

export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      <div className={styles.shape} />
    // ...
  )
}
```

##### clsx

clsx é uma lib que foi recomendada pelo tutorial para estilização condicional.
EX

```TSX
import clsx from 'clsx';

export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

#### O 5º passo

o capitulo 3 do tutorial do next.js, otimizando fontes e imagens.
O next quando usamos o `next/font`, salva a fonte usada nos assets staticos do site.

Seguindo o tutorial, no `app/ui`, criamos o `fonts.ts`.
Importamos a fonte `Inter`, do `next/font/google` para ser a fonte primaria do app

```TS
import { Inter } from 'next/font/google';

export const inter = Inter({ subsets: ['latin'] });
```

E no `<body>` do `app/layout.tsx`:

```TSX
import "@/app/ui/global.css";
import { inter } from "@/app/ui/fonts";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

Adicionando a fonte no body, ela vai ser aplicada no app inteiro.
Ao abrir o app agora, posso ver o `Inter` e `Inter_Fallback` estão aplicados.

O componente `<Image>`, adiciona algumas otimizações para exibir imagens.
No `app/page.tsx`, importamos o `<Image>` do `next/image`

```TSX
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';

export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    </div>
    //...
  );
}
```

#### O 6º passo

Criar layouts e paginas.
O Next.js usa um sistema de rotas baseado em arquivos. Cada pasta representa uma parte da url.
Podemos criar interfaces diferentes para cada pagina usando o `layout.tsx` e o `page.tsx`. O `page.tsx` é um caso especial no next, porque é o arquivo que exporta o que aparece na rota.

Seguindo o tutorial, agora vamos criar a rota `/dashboard`.
Primeiro criamos a pasta `app/dashboard`, e depois criamos o `app/dashboard/page.tsx`

```TSX
export default function Page() {
  return <p>Dashboard Page</p>;
}
```

Depois de criar a primeira pagina da pasta `dashboard`, o tutorial pede para criar mais duas paginas, `customers` e `invoices`.

A proxima parte do tutorial é sobre o `layout.tsx`. Ele é um arquivo especial porque vai ser usado em volta de todas as paginas na mesma pasta. Para mostrar esse comportamento o tutorial pede para criar um layout com a navegação do `dashboard`

```TSX
import SideNav from '@/app/ui/dashboard/sidenav';

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

Um dos beneficios do `layout.tsx`, é que na navegação apenas o ""filhos"" do layout são atualizados.

#### O 7º passo

Navegando entre as paginas.
Normalmente usamos tags `<a>` para navegação, toda vez que clicamos num link a pagina atualiza. No Next existe um componente `<Link>`, ele tem a vantagem de não ter que necessariamente atualizar a pagina inteira, ele permite uma navegação no client.
No `app/ui/dashboard/nav-links.tsx` trocamos as tags `<a>` pelo componente `<Link>`

```TSX
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';

// ...

export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

###### Mostrando a url atual

Uma feature comum em interfaces é exibir a url atual em breadcrumbs.
O next disponibiliza o hook `usePathname` para esse tipo de coisa.
Como `usePathname`é um hook, precisamos transformar o lugar onde ele vai ser usado num `Client Component` colocando a string `"use client"` no topo do arquivo.
No `app/ui/dashboard/nav-links.tsx` importamos o hook

```TSX
'use client';

import {
  UserGroupIcon,
  HomeIcon,
  InboxIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';

// ...
```

E em seguida

```TSX
export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```

E agora podemos usar a url para colocar o link da url atual em azul

```TSX
'use client';

import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';

// ...

export default function NavLinks() {
  const pathname = usePathname();

  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              'flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3',
              {
                'bg-sky-100 text-blue-600': pathname === link.href,
              },
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

#### O 8º passo

Preparar um banco de dados.
O tutorial vai usar um banco PostgresSQL usando o `@vercel/postgres`.
Primeiro o tutorial pede para criar um repositorio no github, subir o app no github, criar uma conta de hobby na vercel, e fazer o deploy do app. Agora toda vez que subir um commit, ele vai para o deploy.

A proxima parte é criar o banco postgres.
Na vercel, vamos para o `Dashboard`, aba `Storage`. Depois criamos o banco postgres.
Depois de conectar, vamos para a aba `.env.local` e copiamos os secrets,
criamos o `.env` no projeto e colamos os secrets.
**Tenha certeza que o `.gitignore` está ignorando o `.env`**

Depois rodamos

```zsh
pnpm i @vercel/postgres
```

##### Fazendo o seed

O `app/seed/route.ts` tem um route handler do next, comentado. Agora podemos descomentar ele.
Entrando em `http://localhost:3000/seed`, o seed será feito. Depois podemos ver os dados no banco na vercel.

#### O 9º passo

Buscando dados.
O tutorial apresenta algumas maneiras de buscar dados.

Usando uma API. Geralmente usamos essa maneira quando trabalhamos com um serviço de terceiro que prove uma API, ou quando estamos buscando dados para o client e não queremos expor secrets para bancos de dados ou outros serviços.
No next, podemos criar APIs com route handlers.

Queryes no banco. Geralmente usamos essa maneira quando criamos uma API que interage com o banco de dados, ou sem usar uma API, com server components do react, fazendo a busca no servidor, sem risco de export secrets.

##### Usando server components para buscar dados

Por padrão, apps no next usam server components do react.
Buscar dados em server components tem as vantagens de

- suportarem promises em componentes, ou seja podemos evitar o uso de useEffect e useState para buscar dados
- são executados no servidor e apenas o resultado vai para o client
- podemos fazer buscas no banco sem precisar criar uma API

##### Continuando o tutorial

No tutorial vamos usar o `vercel postgres SDK` e `SQL` para interagir com o banco.

No `app/lib/data.ts`, essa função permite fazer buscas no banco

```TSX
import { sql } from '@vercel/postgres';

// ...
```

Podemos chamar `sql` num server component, mas para deixar mais facil de navegar pelos componentes, o tutorial agrupou as queries no `data.ts`.

Agora vamos buscar os dados para o dashboard.
No `app/dashboard/page.tsx`

```TSX
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';

export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        {/* <Card title="Collected" value={totalPaidInvoices} type="collected" /> */}
        {/* <Card title="Pending" value={totalPendingInvoices} type="pending" /> */}
        {/* <Card title="Total Invoices" value={numberOfInvoices} type="invoices" /> */}
        {/* <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        /> */}
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        {/* <RevenueChart revenue={revenue}  /> */}
        {/* <LatestInvoices latestInvoices={latestInvoices} /> */}
      </div>
    </main>
  );
}
```

Agora o componente é async, isso permite que usemos `await` para buscar dados, e tambem temos componentes comentados(para evitar erros).

Vamos buscar os dados para o `<RevenueChart />`. Importamos `fetchRevenue` do `data.ts`

```TSX
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue();
  // ...
}
```

Precisamos descomentar o codigo no `app/ui/dashboard/revenue-chart.tsx`

Importando os dados para `<LatestInvoices />`,

```TSX
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue, fetchLatestInvoices } from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  // ...
}
```

Precisamos descomentar o codigo no `app/ui/dashboard/latest-invoices`

Agora o tutorial pede para seguir os exemplos e importar os dados para descomentar os `<Card>`
