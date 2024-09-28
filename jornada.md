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

#### O 10º passo

Renderização estatica e dinamica.
Com a renderização estatica, a busca de dados acontece no servidor, na hora do build ou na revalidação dos dados. Quando um usuario acessa o app, ele vê o resultado no cache. As vantagens são o site ser mais rapido, ser mais leve no servidor, e ter um SEO um pouco melhor porque os dados do site estão carregados no servidor.
Um site estatico é bom para blogs ou quando não temos muita interação do usuario com dados de um banco de dados ou com outros usuarios.
O oposto da renderização estatica, é a dinamica. Com ela toda vez que a pagina é renderizada no servidor, os dados são atualizados para o usuario. As vantagens são ter dados em tempo real, conteudo especifico para cada usuario, e informação sobre a requisão (como cookies).

##### Simulando buscas de dados lentas

O dashboard de exemplo, deve ser dinamico.
Vamos simular uma busca de dados no banco lenta para ver o que acontece na pagina.
No `data.ts` descomentamos o `console.log` e `setTimeout` dentro do `fetchRevenue()`

```TSX
export async function fetchRevenue() {
  try {
    // We artificially delay a response for demo purposes.
    // Don't do this in production :)
    console.log('Fetching revenue data...');
    await new Promise((resolve) => setTimeout(resolve, 3000));

    const data = await sql<Revenue>`SELECT * FROM revenue`;

    console.log('Data fetch completed after 3 seconds.');

    return data.rows;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}
```

Agora a pagina `http://localhost:3000/dashboard/` demora mais para carregar. E com isso, podemos ver que com a renderização dinamica, a velocidade que a pagina carrega é a mesma que o componente mais lento.

#### O 11º passo

Mostrando alguma coisa enquanto os dados são carregados.
O proximo passo no tutorial é sobre o streaming de dados.
Uma tecnica para carregar "pedaços" da interface sem bloquear a pagina inteira.

Implementando o `loading.tsx`
No `app/dashboard`, criamos um arquivo `loading.tsx`

```TSX
export default function Loading() {
  return <div>Loading...</div>;
}
```

Agora a pagina `http://localhost:3000/dashboard/` abre imediatamente com um texto indicando o carregamento.

Para melhorar a experiencia no loading, o tutorial pede para colocar um `skeleton`, para dar uma ideia da forma da interface enquanto estamos carregando.

```TSX
import DashboardSkeleton from '@/app/ui/skeletons';

export default function Loading() {
  return <DashboardSkeleton />;
}
```

Um bug acontece nesse loading. Como ele esta num nivel acima, ele vai ser aplicado para `invoices` e `dustomers`.
Para corrigir isso, usamos `route groups`, criamos uma pasta `app/dashboard/(overview)` e colocamos o `loading.tsx` e o `page.tsx` nela.
Usar `()` no next não cria uma url `/dashboard/(overview)`, ele mantem o `/dashboard`. Esse grupo garante que o `loading.tsx` só seja aplicado ao `page.tsx`. Os grupos tambem podem ser usados para separar um app em sessões ou por times.

##### Loading em componentes dentro da pagina

Até agora colocamos um loading na pagina inteira, mas podemos usar loadings em componentes dentro da pagina.
Primeiro vamos remover `fetRevenue()` no `dashboard/(overview)/page.tsx`

```TSX
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data'; // remove fetchRevenue

export default async function Page() {
  const revenue = await fetchRevenue() // delete this line
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();

  return (
    // ...
  );
}
```

Depois importamos o `<Suspense />` do React, e colocamos ele envolvendo o `<RevenueChart />`. O fallback vai ser o `<RevenueChartSkeleton />`

```TSX
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data';
import { Suspense } from 'react';
import { RevenueChartSkeleton } from '@/app/ui/skeletons';

export default async function Page() {
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();

  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```

e depois, atualizamos o `RevenueChart`

```TSX
import { generateYAxis } from '@/app/lib/utils';
import { CalendarIcon } from '@heroicons/react/24/outline';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';

// ...

export default async function RevenueChart() { // Make component async, remove the props
  const revenue = await fetchRevenue(); // Fetch data inside the component

  const chartHeight = 350;
  const { yAxisLabels, topLabel } = generateYAxis(revenue);

  if (!revenue || revenue.length === 0) {
    return <p className="mt-4 text-gray-400">No data available.</p>;
  }

  return (
    // ...
  );
}
```

Agora o tutorial pede para implementar o mesmo tipo de logica para o `<LatestInvoices>`.
Depois de repetir a logica para `<LatestInvoices>`, vamos implementar a mesma logica para os `<Card>`.
Para evitar um efeito de "pop" que poderia ficar estranho, então vamos colocar todos os cards dentro do mesmo suspense
No `dashboard/(overview)/page.tsx`, vamos deletar os `<Card>`, deletar a função `fetchCardData()`, importar o `<CardWrapper />`, importar o `<CardsSkeleton />`, e envolver o `<CardWrapper />` no suspense.

```TSX
import CardWrapper from '@/app/ui/dashboard/cards';
// ...
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
  CardsSkeleton,
} from '@/app/ui/skeletons';

export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Suspense fallback={<CardsSkeleton />}>
          <CardWrapper />
        </Suspense>
      </div>
      // ...
    </main>
  );
}
```

No `app/ui/dashboard/cards.tsx`, vamos importar a função `fetchCardData()` e usar dentro do `<CardWrapper/>`(e descomentar as linhas comentadas no componente)

```TSX
// ...
import { fetchCardData } from '@/app/lib/data';

// ...

export default async function CardWrapper() {
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();

  return (
    <>
      <Card title="Collected" value={totalPaidInvoices} type="collected" />
      <Card title="Pending" value={totalPendingInvoices} type="pending" />
      <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
      <Card
        title="Total Customers"
        value={numberOfCustomers}
        type="customers"
      />
    </>
  );
}
```

#### O 12º passo

Combinando renderização estatica e dinamica. A pré renderização parcial(PPR).(função experimental no next 14)
A pré renderização parcial usa o Suspense do React para exibir um componente enquanto outro carrega. O fallback do Suspense vai ser colocado no html inicial, junto com a parte estatica da pagina. O conteudo estatico fica pre renderizado no servidor e a parte dinamica carrega quando o usuario acessa a rota.
Apenas envolver um componente num Suspense não deixa o componente em si dinamico. O Suspense é usado como uma linha entre codigo estatico e dinamico.
Para habilitar o PPR, adicionamos a opção no `next.config.mjs`

```mjs
/** @type {import('next').NextConfig} */

const nextConfig = {
  experimental: {
    ppr: "incremental",
  },
};

export default nextConfig;
```

O `incremental` permite usar o PPR em rotas especificas.
Agora adicionamos o `experimental_ppr` no `app/dashboard/layout.tsx`

```TSX
import SideNav from '@/app/ui/dashboard/sidenav';

export const experimental_ppr = true;

// ...
```

Podemos acabar não vendo uma diferença na performance em desenvolvimento, mas em produção deve dar para perceber.

#### O 13º passo

Buscas e paginação. No `/dashboard/invoices` vamos adicionar buscas e paginação
Dentro do `dashboard/invoices/page.tsx` colocamos

```TSX
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';
import { Suspense } from 'react';

export default async function Page() {
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      {/*  <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense> */}
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```

No `app/ui/search.tsx` vamos ver:

- `"use client"`, porque vamos usar hooks e event listeners
- `<input>`, o campo de busca
  Vamos criar uma função `handleSearch`, e adicionar um `onChange` no `<input>`.

```TSX
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';

export default function Search({ placeholder }: { placeholder: string }) {
  function handleSearch(term: string) {
    console.log(term);
  }

  return (
    <div className="relative flex flex-1 flex-shrink-0">
      <label htmlFor="search" className="sr-only">
        Search
      </label>
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
        onChange={(e) => {
          handleSearch(e.target.value);
        }}
      />
      <MagnifyingGlassIcon className="absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
    </div>
  );
}
```

Depois vamos importar `useSearchParams` do `next/navigation`

```TS
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams } from 'next/navigation';

export default function Search() {
  const searchParams = useSearchParams();

  function handleSearch(term: string) {
    console.log(term);
  }
  // ...
}
```

Dentro do `handleSearch` criamos um novo `URLSearchParams`

```TS
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams } from 'next/navigation';

export default function Search() {
  const searchParams = useSearchParams();

  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
  }
  // ...
}
```

`URLSearchParams` é uma api da web, que provê metodos para manipular parametros de URLs.
O proximo passo é setar os parametros ao digitar ou apagar quando limpamos campo de busca.

```TSX
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams } from 'next/navigation';

export default function Search() {
  const searchParams = useSearchParams();

  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
  }
  // ...
}
```

Agora podemos usar o `useRouter` e `usePathname` para atualizar a URL.

```TSX
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams, usePathname, useRouter } from 'next/navigation';

export default function Search() {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const { replace } = useRouter();

  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }
}
```

Para manter o input sincronizado com o valor da url

```TSX
<input
  className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
  placeholder={placeholder}
  onChange={(e) => {
    handleSearch(e.target.value);
  }}
  defaultValue={searchParams.get('query')?.toString()}
/>
```

Agora no `app/dashboard/invoices/page.tsx` podemos atualizar para ver a tabela

```TSX
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { Suspense } from 'react';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';

export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;

  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```

O table vai exibir conteudos com base na query da url

```TSX
// ...
export default async function InvoicesTable({
  query,
  currentPage,
}: {
  query: string;
  currentPage: number;
}) {
  const invoices = await fetchFilteredInvoices(query, currentPage);
  // ...
}
```

Agora vamos adicionar um debounce para evitar disparar buscas a cada tecla digitada

```zsh
pnpm i use-debounce
```

No `<Search>`, vamos importar o `useDebouncedCallback`

```TSX
// ...
import { useDebouncedCallback } from 'use-debounce';

// Inside the Search Component...
const handleSearch = useDebouncedCallback((term) => {
  console.log(`Searching... ${term}`);

  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```

A proxima parte é adicionar a paginação.
No `dashboard/invoices/page.tsx`

```TSX
// ...
import { fetchInvoicesPages } from '@/app/lib/data';

export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string,
    page?: string,
  },
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;

  const totalPages = await fetchInvoicesPages(query);

  return (
    // ...
  );
}
```

Depois vamos passar `totalPages` para o `<Pagination/>`

```TSX
// ...

export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;

  const totalPages = await fetchInvoicesPages(query);

  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        <Pagination totalPages={totalPages} />
      </div>
    </div>
  );
}
```

No `app/ui/invoices/pagination.tsx`

```TSX
'use client';

import { ArrowLeftIcon, ArrowRightIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Link from 'next/link';
import { generatePagination } from '@/app/lib/utils';
import { usePathname, useSearchParams } from 'next/navigation';

export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;

  // ...
}
```

Descomentamos o componente, e depois adicionamos a função `createPageURL`

```TSX
'use client';

import { ArrowLeftIcon, ArrowRightIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Link from 'next/link';
import { generatePagination } from '@/app/lib/utils';
import { usePathname, useSearchParams } from 'next/navigation';

export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;

  const createPageURL = (pageNumber: number | string) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', pageNumber.toString());
    return `${pathname}?${params.toString()}`;
  };

  // ...
}
```

A ultima coisa nas buscas e paginações é resetar a pagina quando editamos o filtro na busca

```TSX
'use client';

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { usePathname, useRouter, useSearchParams } from 'next/navigation';
import { useDebouncedCallback } from 'use-debounce';

export default function Search({ placeholder }: { placeholder: string }) {
  const searchParams = useSearchParams();
  const { replace } = useRouter();
  const pathname = usePathname();

  const handleSearch = useDebouncedCallback((term) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', '1');
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }, 300);

```

#### O 14º passo

Editando dados.
Vamos usar server actions para rodar codigo assincrono no servidor sem precisar de uma API que editaria os dados no banco.
No React, podemos usar o atributo `action` na tag `<form>` para disparar uma função. Essa função recebe um `FormData` com os valores dos inputs do formulario.
Por exemplo

```TSX
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    'use server';

    // Logic to mutate data...
  }

  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```

Uma vantagem da server action num server component é que os forms funcionam mesmo em navegadores com javascript desabilitado.

Agora vamos criar novos `invoices`.
Vamos criar um form, uma server action, que pegar os dados do form, depois vamos validar, preparar, inserir o dados no banco(lidando com erros) e por fim vamos revalidar o cache e redirecionar o usuario.
Na pasta `app/dashboard/invoices` adicionamos a pasta `app/dashboard/invoices/create` com um `page.tsx`

```TSX
import Form from '@/app/ui/invoices/create-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchCustomers } from '@/app/lib/data';

export default async function Page() {
  const customers = await fetchCustomers();

  return (
    <main>
      <Breadcrumbs
        breadcrumbs={[
          { label: 'Invoices', href: '/dashboard/invoices' },
          {
            label: 'Create Invoice',
            href: '/dashboard/invoices/create',
            active: true,
          },
        ]}
      />
      <Form customers={customers} />
    </main>
  );
}
```

Agora vamos criar a server action.
Na `app/lib` vamos criar o `app/lib/actions.ts` e vamos adicionar o `"use server"`

```TSX
'use server';
```

(daria para colocar `"use server"` dentro das funções na propria pagina, mas colocamos num arquivo separado para ficar mais organizado)
No `app/lib/actions.ts` vamos criar a função

```TSX
'use server';

export async function createInvoice(formData: FormData) {}
```

No `app/ui/invoices/create-form.tsx` vamos adicionar a action

```TSX
import { customerField } from '@/app/lib/definitions';
import Link from 'next/link';
import {
  CheckIcon,
  ClockIcon,
  CurrencyDollarIcon,
  UserCircleIcon,
} from '@heroicons/react/24/outline';
import { Button } from '@/app/ui/button';
import { createInvoice } from '@/app/lib/actions';

export default function Form({
  customers,
}: {
  customers: customerField[];
}) {
  return (
    <form action={createInvoice}>
      // ...
  )
}
```

No `app/lib/actions.ts` vamos pegar os dados do form

```TSX
'use server';

export async function createInvoice(formData: FormData) {
  const rawFormData = {
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  };
  // Test it out:
  console.log(rawFormData);
}
```

No `app/lib/actions.ts` vamos adicionar o `zod` para validar e converter os tipos dos dados

```TSX
'use server';

import { z } from 'zod';

const FormSchema = z.object({
  id: z.string(),
  customerId: z.string(),
  amount: z.coerce.number(),
  status: z.enum(['pending', 'paid']),
  date: z.string(),
});

const CreateInvoice = FormSchema.omit({ id: true, date: true });

export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
}
```

Adicionamos o dado no banco

```TSX
import { z } from 'zod';
import { sql } from '@vercel/postgres';

// ...

export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];

  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;
}
```

Depois revalidamos os dados da rota e redirecionamos

```tsx
"use server";

import { z } from "zod";
import { sql } from "@vercel/postgres";
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";

// ...

export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get("customerId"),
    amount: formData.get("amount"),
    status: formData.get("status"),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split("T")[0];

  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;

  revalidatePath("/dashboard/invoices");
  redirect("/dashboard/invoices");
}
```

Atualizando um `invoice`.
Vamos criar uma rota dinamica com um `id`, ler o `invoice`, pre popular os dados do formulario e atualizar no banco.

No `app/dashboard/invoices` vamos criar a pasta `app/dashboard/invoices/[id]`. O `[]` indica que a rota é dinamica, e podemos acessar o `id` na url. Dentro dessa nova pasta vamos criar um arquivo `page.tsx` dentro de uma pasta `edit`

No `app/ui/invoices/buttons.tsx` vamos atualizar o link

```TSX
import { PencilIcon, PlusIcon, TrashIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';

// ...

export function UpdateInvoice({ id }: { id: string }) {
  return (
    <Link
      href={`/dashboard/invoices/${id}/edit`}
      className="rounded-md border p-2 hover:bg-gray-100"
    >
      <PencilIcon className="w-5" />
    </Link>
  );
}
```

No `app/dashboard/invoices/[id]/edit/page.tsx`, vamos colocar

```TSX
import Form from '@/app/ui/invoices/edit-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchCustomers } from '@/app/lib/data';

export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  return (
    <main>
      <Breadcrumbs
        breadcrumbs={[
          { label: 'Invoices', href: '/dashboard/invoices' },
          {
            label: 'Edit Invoice',
            href: `/dashboard/invoices/${id}/edit`,
            active: true,
          },
        ]}
      />
      <Form invoice={invoice} customers={customers} />
    </main>
  );
}
```

Depois vamos buscar os dados

```TSX
import Form from '@/app/ui/invoices/edit-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchInvoiceById, fetchCustomers } from '@/app/lib/data';

export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);
  // ...
}
```

Agora passamos o id para a server action
No `app/ui/invoices/edit-form.tsx`

```TSX
// ...
import { updateInvoice } from '@/app/lib/actions';

export default function EditInvoiceForm({
  invoice,
  customers,
}: {
  invoice: InvoiceForm;
  customers: CustomerField[];
}) {
  const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);

  return (
    <form action={updateInvoiceWithId}>
      <input type="hidden" name="id" value={invoice.id} />
    </form>
  );
}
```

Precisamos do `bind` para garantir que os valores passado para a server actions estão `encoded`

No `app/lib/actions.ts` vamos criar a função `updateInvoice`

```TS
// Use Zod to update the expected types
const UpdateInvoice = FormSchema.omit({ id: true, date: true });

// ...

export async function updateInvoice(id: string, formData: FormData) {
  const { customerId, amount, status } = UpdateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  const amountInCents = amount * 100;

  await sql`
    UPDATE invoices
    SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
    WHERE id = ${id}
  `;

  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

Deletando um `invoice`.
No `app/ui/invoices/buttons.tsx` vamos colocar um form em volta do botão de deletar com a ação para deletar

```TSX
import { deleteInvoice } from '@/app/lib/actions';

// ...

export function DeleteInvoice({ id }: { id: string }) {
  const deleteInvoiceWithId = deleteInvoice.bind(null, id);

  return (
    <form action={deleteInvoiceWithId}>
      <button type="submit" className="rounded-md border p-2 hover:bg-gray-100">
        <span className="sr-only">Delete</span>
        <TrashIcon className="w-4" />
      </button>
    </form>
  );
}
```

No `app/lib/actions.ts` vamos criar a função `deleteInvoice`

```TS
export async function deleteInvoice(id: string) {
  await sql`DELETE FROM invoices WHERE id = ${id}`;
  revalidatePath('/dashboard/invoices');
}
```

#### O 15º passo

Lidar com possiveis erros ao salvar dados.
Vamos criar um `error.tsx` dentro do `app/dashboard/invoices`

```TSX
'use client';

import { useEffect } from 'react';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Optionally log the error to an error reporting service
    console.error(error);
  }, [error]);

  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Something went wrong!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={
          // Attempt to recover by trying to re-render the invoices route
          () => reset()
        }
      >
        Try again
      </button>
    </main>
  );
}
```

Enquanto o `error.tsx` é util para pegar todos os erros, outra maneira de lidar com erros é usar a função `notFound`.

No `dashboard/invoices/[id]/edit/page.tsx`

```TSX
import { fetchInvoiceById, fetchCustomers } from '@/app/lib/data';
import { updateInvoice } from '@/app/lib/actions';
import { notFound } from 'next/navigation';

export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);

  if (!invoice) {
    notFound();
  }

  // ...
}
```

Criamos `app/dashboard/invoices/[id]/edit/not-found.tsx`

```TSX
import Link from 'next/link';
import { FaceFrownIcon } from '@heroicons/react/24/outline';

export default function NotFound() {
  return (
    <main className="flex h-full flex-col items-center justify-center gap-2">
      <FaceFrownIcon className="w-10 text-gray-400" />
      <h2 className="text-xl font-semibold">404 Not Found</h2>
      <p>Could not find the requested invoice.</p>
      <Link
        href="/dashboard/invoices"
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
      >
        Go Back
      </Link>
    </main>
  );
}
```

#### O 16º passo

Melhorando acessibilidade.
O next inclui o plugin `eslint-plugin-jsx-a11y` para alertar sobre erros de acessibilidade como tags de imagem sem texto `alt`.
Adicionamos o script no `package.json`

```JSON
"scripts": {
    "build": "next build",
    "dev": "next dev",
    "start": "next start",
    "lint": "next lint"
},
```

e rodamos

```zsh
pnpm lint
```

Depois disso podemos ver os pontos que poderiam ser melhorados no app.

##### Validações no cliente e no servidor

Atualmente a criação de `invoice` não tem validações no formulario. Podemos clicar para criar um invoice sem preencher o form, e isso gera um erro.
A forma mais simples de resolver seria colocar um required no form
No `app/ui/invoices/create-form.tsx`

```TSX
<input
  id="amount"
  name="amount"
  type="number"
  placeholder="Enter USD amount"
  className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
  required
/>
```

Outra seria validar no servidor.
No `app/ui/invoices/create-form.tsx`

```TSX
'use client';

// ...
import { useActionState } from 'react';
```

E usamos o hook

```TSX
// ...
import { useActionState } from 'react';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const [state, formAction] = useActionState(createInvoice, initialState);

  return <form action={formAction}>...</form>;
}
```

O initialState pode ser qualquer coisa que queremos, vamos usar um objeto com duas chaves vazias

```TSX
// ...
import { createInvoice, State } from '@/app/lib/actions';
import { useActionState } from 'react';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const initialState: State = { message: null, errors: {} };
  const [state, formAction] = useActionState(createInvoice, initialState);

  return <form action={formAction}>...</form>;
}
```

No `app/lib/actions.ts`

```TS
const FormSchema = z.object({
  id: z.string(),
  customerId: z.string({
    invalid_type_error: 'Please select a customer.',
  }),
  amount: z.coerce
    .number()
    .gt(0, { message: 'Please enter an amount greater than $0.' }),
  status: z.enum(['pending', 'paid'], {
    invalid_type_error: 'Please select an invoice status.',
  }),
  date: z.string(),
});
```

e atualizamos o `createInvoice`

```TS
export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};

export async function createInvoice(prevState: State, formData: FormData) {
```

depois mudamos o `parse` do zod e o `safeParse`

```TS
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // ...
}
```

O `safeParse` retorna um objeto com um `success` ou um `error`, isso ajuda a validar sem usar um try/catch.
Agora atualizamos a validação

```TS
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }
   const { customerId, amount, status } = validatedFields.data;
  // ...
}
```

Agora no `app/ui/invoices/create-form.tsx` podemos acessar o erro no `state`

```TSX
<form action={formAction}>
  <div className="rounded-md bg-gray-50 p-4 md:p-6">
    {/* Customer Name */}
    <div className="mb-4">
      <label htmlFor="customer" className="mb-2 block text-sm font-medium">
        Choose customer
      </label>
      <div className="relative">
        <select
          id="customer"
          name="customerId"
          className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
          defaultValue=""
          aria-describedby="customer-error"
        >
          <option value="" disabled>
            Select a customer
          </option>
          {customers.map((name) => (
            <option key={name.id} value={name.id}>
              {name.name}
            </option>
          ))}
        </select>
        <UserCircleIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500" />
      </div>
      <div id="customer-error" aria-live="polite" aria-atomic="true">
        {state.errors?.customerId &&
          state.errors.customerId.map((error: string) => (
            <p className="mt-2 text-sm text-red-500" key={error}>
              {error}
            </p>
          ))}
      </div>
    </div>
    // ...
  </div>
</form>
```

#### O 17º passo

Criando uma rota de login.
Vamos criar uma pasta `app/login` e um arquivo `page.tsx` nela

```TSX
import AcmeLogo from '@/app/ui/acme-logo';
import LoginForm from '@/app/ui/login-form';

export default function LoginPage() {
  return (
    <main className="flex items-center justify-center md:h-screen">
      <div className="relative mx-auto flex w-full max-w-[400px] flex-col space-y-2.5 p-4 md:-mt-32">
        <div className="flex h-20 w-full items-end rounded-lg bg-blue-500 p-3 md:h-36">
          <div className="w-32 text-white md:w-36">
            <AcmeLogo />
          </div>
        </div>
        <LoginForm />
      </div>
    </main>
  );
}
```

Vamos usar o NextAuth para lidar com a autenticação.

```zsh
pnpm i next-auth@beta
```

(estamos usando a versão beta porque ela é compativel com o next 14)

Agora vamos gerar uma chave para a aplicação. Essa chave vai ser usada para encriptar cookies.

```zsh
openssl rand -base64 32
```

Pegamos a chave e salvamos no .env

```.env
AUTH_SECRET=your-secret-key
```

(para adicionar a autenticação em produção vamos precisar setar essa variavel na vercel)

O proximo passo é criar o arquivo `auth.config.ts` na raiz do projeto para configurar o NextAuth

```ts
import type { NextAuthConfig } from "next-auth";

export const authConfig = {
  pages: {
    signIn: "/login",
  },
} satisfies NextAuthConfig;
```

Depois adicionamos um middleware para proteger as rotas

```TS
import type { NextAuthConfig } from 'next-auth';

export const authConfig = {
  pages: {
    signIn: '/login',
  },
  callbacks: {
    authorized({ auth, request: { nextUrl } }) {
      const isLoggedIn = !!auth?.user;
      const isOnDashboard = nextUrl.pathname.startsWith('/dashboard');
      if (isOnDashboard) {
        if (isLoggedIn) return true;
        return false; // Redirect unauthenticated users to login page
      } else if (isLoggedIn) {
        return Response.redirect(new URL('/dashboard', nextUrl));
      }
      return true;
    },
  },
  providers: [], // Add providers with an empty array for now
} satisfies NextAuthConfig;
```

Criamos um arquivo `middleware.ts` na raiz do projeto e importamos `authConfig`

```TS
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';

export default NextAuth(authConfig).auth;

export const config = {
  // https://nextjs.org/docs/app/building-your-application/routing/middleware#matcher
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
};
```

e um arquivo `auth.ts` na raiz do projeto

```TS
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
});
```

Adicionamos um array de `providers`, que são nossos metodos de login, no `NextAuth`

```TS
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
import Credentials from 'next-auth/providers/credentials';

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [Credentials({})],
});
```

Podemos validar os inputs da autorização com o zod

```TS
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
import Credentials from 'next-auth/providers/credentials';
import { z } from 'zod';

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);
      },
    }),
  ],
});
```

depois de validar, criamos uma função `getuser` para buscar o usuario do banco

```TS
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import { authConfig } from './auth.config';
import { z } from 'zod';
import { sql } from '@vercel/postgres';
import type { User } from '@/app/lib/definitions';

async function getUser(email: string): Promise<User | undefined> {
  try {
    const user = await sql<User>`SELECT * FROM users WHERE email=${email}`;
    return user.rows[0];
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw new Error('Failed to fetch user.');
  }
}

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);

        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
        }

        return null;
      },
    }),
  ],
});
```

e agora podemos usar o `bcrypt.compare` para checar a senha

```TS
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import { authConfig } from './auth.config';
import { sql } from '@vercel/postgres';
import { z } from 'zod';
import type { User } from '@/app/lib/definitions';
import bcrypt from 'bcrypt';

// ...

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        // ...

        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
          const passwordsMatch = await bcrypt.compare(password, user.password);

          if (passwordsMatch) return user;
        }

        console.log('Invalid credentials');
        return null;
      },
    }),
  ],
});
```

Agora precisamos alterar o formulario de login.
No `actions.ts`, criamos uma função `authenticate`

```TS
'use server';

import { signIn } from '@/auth';
import { AuthError } from 'next-auth';

// ...

export async function authenticate(
  prevState: string | undefined,
  formData: FormData,
) {
  try {
    await signIn('credentials', formData);
  } catch (error) {
    if (error instanceof AuthError) {
      switch (error.type) {
        case 'CredentialsSignin':
          return 'Invalid credentials.';
        default:
          return 'Something went wrong.';
      }
    }
    throw error;
  }
}
```

No `app/ui/login-form.tsx`

```TSX
'use client';

import { lusitana } from '@/app/ui/fonts';
import {
  AtSymbolIcon,
  KeyIcon,
  ExclamationCircleIcon,
} from '@heroicons/react/24/outline';
import { ArrowRightIcon } from '@heroicons/react/20/solid';
import { Button } from '@/app/ui/button';
import { useActionState } from 'react';
import { authenticate } from '@/app/lib/actions';

export default function LoginForm() {
  const [errorMessage, formAction, isPending] = useActionState(
    authenticate,
    undefined,
  );

  return (
    <form action={formAction} className="space-y-3">
      <div className="flex-1 rounded-lg bg-gray-50 px-6 pb-4 pt-8">
        <h1 className={`${lusitana.className} mb-3 text-2xl`}>
          Please log in to continue.
        </h1>
        <div className="w-full">
          <div>
            <label
              className="mb-3 mt-5 block text-xs font-medium text-gray-900"
              htmlFor="email"
            >
              Email
            </label>
            <div className="relative">
              <input
                className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
                id="email"
                type="email"
                name="email"
                placeholder="Enter your email address"
                required
              />
              <AtSymbolIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
            </div>
          </div>
          <div className="mt-4">
            <label
              className="mb-3 mt-5 block text-xs font-medium text-gray-900"
              htmlFor="password"
            >
              Password
            </label>
            <div className="relative">
              <input
                className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
                id="password"
                type="password"
                name="password"
                placeholder="Enter password"
                required
                minLength={6}
              />
              <KeyIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
            </div>
          </div>
        </div>
        <Button className="mt-4 w-full" aria-disabled={isPending}>
          Log in <ArrowRightIcon className="ml-auto h-5 w-5 text-gray-50" />
        </Button>
        <div
          className="flex h-8 items-end space-x-1"
          aria-live="polite"
          aria-atomic="true"
        >
          {errorMessage && (
            <>
              <ExclamationCircleIcon className="h-5 w-5 text-red-500" />
              <p className="text-sm text-red-500">{errorMessage}</p>
            </>
          )}
        </div>
      </div>
    </form>
  );
}
```

O logout seria feito no `ui/dashboard/sidenav.tsx`

```TSX
import Link from 'next/link';
import NavLinks from '@/app/ui/dashboard/nav-links';
import AcmeLogo from '@/app/ui/acme-logo';
import { PowerIcon } from '@heroicons/react/24/outline';
import { signOut } from '@/auth';

export default function SideNav() {
  return (
    <div className="flex h-full flex-col px-3 py-4 md:px-2">
      // ...
      <div className="flex grow flex-row justify-between space-x-2 md:flex-col md:space-x-0 md:space-y-2">
        <NavLinks />
        <div className="hidden h-auto w-full grow rounded-md bg-gray-50 md:block"></div>
        <form
          action={async () => {
            'use server';
            await signOut();
          }}
        >
          <button className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3">
            <PowerIcon className="w-6" />
            <div className="hidden md:block">Sign Out</div>
          </button>
        </form>
      </div>
    </div>
  );
}
```
