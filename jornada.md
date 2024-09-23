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
