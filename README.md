# Exercício Continuous Delivery

Esta é uma aplicação baseada em Node.js utilizada para exercitar conceitos de Continuous Delivery (CD)

\* Based on the work of [Dan Arias](https://twitter.com/getDanArias): <https://auth0.com/blog/create-a-simple-and-stylish-node-express-app>

## 2. Configurando sua conta na Azure

### 2.1 Subscriptions

Você já deve ter uma subscrição criada na sua conta Azure, uma vez que ela foi gerada a partir da sua conta de estudante. Caso não haja nenhuma, utilize [este tutorial](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/create-subscription).

### 2.2 App Services

Precisamos criar um [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan) para o alvo dos nossos deployments.

1. Na tela inicial selecione `App Service` e em seguida `Create`
2. Na tela de criação do Web App, preencha as seguintes informações:
   1. **Resource Group:** `Create new`: continuous-deployment-<seu_usuario>
   2. **Name:** aula-cd-<seu_usuario>
   3. **Runtime stack:** Nodes 14 LTS
3. Clique `Review + create`
4. Clique `Create`

:tada: Pronto, já temos recursos em nuvem para receber nossa aplicação!

Agora precisamos criar um workflow para entregar o nosso app na nuvem. 

### 2.3 Deployment Center

Vamos usar o _wizard_ da Azure para nos ajudar na criação do workflow.

1. No App Service que você acabou de criar, vá na opção `Deployment Center`
2. Em **Source**, selecione `GitHub`
3. Autorize o acesso a sua conta pessoal
4. **Organization:** seu usuário
5. **Repository:** sample-deployment
6. **Branch:** main
7. **Runtime stack:** Node
8. **Version:** Node 14 LTS
9. Clique `Save`

#### :question: O que mudou no seu repositório?

🚨 Nosso app está chegando na nuvem, como queríamos, mas direto no ambiente de produção. Como fazemos para que ele escale pelos ambientes de **Testes/QA** e **Homologação** antes de chegar em produção?

### 2.4 Deployment Slots

Você pode utilizar `Deployment Slots` para isolar ambientes de deployment, realizar testes A/B ou organizar a sua entrega de aplicações com estratégias Blue/Green (veremos em mais detalhes na próxima aula)

1. Selecione a opção `Deployment slots`
2. Você verá que já existe um slot com o *label* `Production`. Vamos criar novos slots. Selecione a opção `Add Slot`
3. **Nome:** aula-cd-<seu_usuario>-QA
4. **Clone settings from:** Do not clone settings
5. Clique `Add`
6. Siga o mesmo procedimento e crie um novo slot chamado `aula-cd-<seu_usuario>-HOM`

## 3. Configurando o deployment para promoção de artefatos

### 3.1 Criando novos ambientes (Environments)

1. No seu repositório no GitHub selecione `Settings` > `Environments`
2. Clique em `New environment` para criar um novo ambiente de deployment
3. Dê o nome de `QA` e clique em `Configure environment`
4. Na seção **Environment secrets**, clique em `Add Secret`
5. **Name:** AZURE_WEBAPP_PUBLISH_PROFILE
6. Agora volte para a sua conta da Azure, vá em **Deployment slots** e selecione o slot de QA (terminado em `QA`)
7. Clique em `Get publish profile`e baixe o arquivo.
8. Abra o arquivo no VSCode e cole o seu conteúdo no campo `Value` do secret que criamos no GitHub
   
Repita o processo processo para criar um novo ambiente chamado `HOM`. Não há problema em os secrets terem o mesmo nome. :question: Por que?

### 3.2 Ajustando o workflow

O nosso workflow agora precisa refletir a capacidade de fazer a promoção do nosso build entre ambientes.