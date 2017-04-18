<properties
 pageTitle="Os modelos da aplicação lógica | Microsoft Azure"
 description="Saiba como utilizar os modelos da aplicação lógica previamente criados para o ajudar a começar a utilizar"
 authors="kevinlam1"
 manager="dwrede"
 editor=""
 services="app-service\logic"
 documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="klam"/>

# <a name="logic-app-templates"></a>Modelos de aplicação de lógica

## <a name="what-are-logic-app-templates"></a>Quais são os modelos da aplicação lógica

Um modelo de aplicação lógica é uma aplicação de lógica pré-concebidos que pode utilizar para rapidamente começar a criar o seu próprio fluxo de trabalho. 

Estes modelos são uma boa forma de descobrir padrões diferentes que podem ser construídas utilizando aplicações lógica. Pode utilizar estes modelos como-está ou modificá-las para se ajustar ao seu cenário.

## <a name="overview-of-available-templates"></a>Descrição geral dos modelos disponíveis

Existem muitos modelos disponíveis atualmente publicados na plataforma de aplicação lógica. Algumas categorias de exemplo, bem como o tipo de conexões utilizados no-las, está indicado abaixo.

### <a name="enterprise-cloud-templates"></a>Modelos de nuvem da empresa
Modelos de que se integram Dynamics CRM, Salesforce, caixa, BLOBs do Azure e outros conectores às suas necessidades de nuvem da empresa. Alguns exemplos do que posso fazer com estes modelos incluem organizar as oportunidades potenciais e criar cópias de segurança dos seus dados de ficheiros empresarial.

### <a name="enterprise-integration-pack-templates"></a>Modelos de pacote de integração de empresa
Configurações do VETER (validar, extrair, transformar, enriquecer, encaminhar) tubagens, receber um X12 Editar documento sobre AS2 e transformá-lo para XML, como bem como a mensagem X12 e AS2 movimentação.

### <a name="protocol-pattern-templates"></a>Modelos do protocolo padrão
Estes modelos de consistir em lógica das aplicações do que contêm os padrões de protocolo como resposta de pedido de através de HTTP, bem como integrações através de FTP e SFTP. Utilize estes à medida que existam ou como base para a criação de padrões de protocolo mais complexos.  

### <a name="personal-productivity-templates"></a>Modelos de produtividade pessoal
Padrões para o ajudar a melhorar a produtividade pessoal incluem modelos que definir lembretes diários, transformar trabalho importante itens em listas de acções a fazer e automatizar tarefas compridas para baixo até um passo de aprovação único utilizador.

### <a name="consumer-cloud-templates"></a>Modelos do consumidor na nuvem
Modelos de simples que se integram com os serviços de redes sociais como Twitter, margem e correio eletrónico, finalmente capaz de reforçar iniciativas de marketing de redes sociais. Estes também incluem modelos como copiar turvo, que podem ajudar a aumentar a produtividade guardando o tempo gasto nas tarefas repetitivas tradicionais. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Como criar uma aplicação de lógica através de um modelo 

Para começar a utilizar um modelo de aplicação lógica, vá para o Estruturador da aplicação lógica. Se estiver a introduzir o estruturador ao abrir uma aplicação de lógica existente, a aplicação de lógica carrega automaticamente na vista de estruturador. No entanto, se estiver a criar uma nova aplicação de lógica, será apresentada no ecrã abaixo.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

A partir deste ecrã, pode escolher quer iniciar com uma aplicação de lógica em branco ou a um modelo pré-concebidos. Se selecionar um dos modelos, são-lhe fornecidos com informações adicionais. Neste exemplo, vamos utilizar o modelo *quando é criado um novo ficheiro no Dropbox, copie-o para o OneDrive* .  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Se optar por utilizar o modelo, selecione apenas o botão de *Utilize este modelo* . Lhe vai ser pedido para iniciar sessão na sua contas baseadas no quais conectores utiliza o modelo. Ou, se tiver estabelecido anteriormente uma ligação com estes conectores, pode selecionar continuar conforme visto abaixo.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

Depois de estabelecer a ligação e selecionar *continuar*, a aplicação de lógica abre-se na vista do estruturador de.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

No exemplo acima, tal como acontece com muitos modelos, alguns dos campos de propriedade obrigatória poderão ser preenchido dentro de conectores; No entanto, ainda alguns poderão exigir um valor antes de ser capaz de corretamente implementar a aplicação de lógica. Se tentar implementar sem ter de introduzir alguns dos campos em falta, será notificado com uma mensagem de erro.

Se pretender voltar para o Visualizador de modelo, selecione o botão de *modelos* na barra de navegação superior. Ao mudar novamente para o Visualizador de modelo, perderá qualquer progresso não guardado. Antes de mudar para o Visualizador de modelo, verá uma mensagem de aviso notificá-lo desta.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Como implementar uma aplicação de lógica criada a partir de um modelo

Assim que tiver carregado o modelo e efetuadas todas as alterações pretendidas, selecione guardar botão no canto superior esquerdo. Este procedimento guarda e publica a sua aplicação de lógica.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Se pretender obter mais informações sobre como adicionar mais passos para um modelo de aplicação lógica existente ou fazer edições em geral, leia mais em [criar uma aplicação de lógica](app-service-logic-create-a-logic-app.md).