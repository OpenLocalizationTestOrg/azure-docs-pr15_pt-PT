<properties 
    pageTitle="Conectores de negócio para empresas e Apps de API nas aplicações de lógica | Microsoft Azure" 
    description="Saiba como criar e configurar conectores de editar, EDIFACT, AS2 e TPM; arquitetura de microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Conectores de negócio para empresas e API Apps

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Lógica aplicações inclui muitos BizTalk API as aplicações que são o essencial ambientes de integração. Estas aplicações de API são baseadas no conceitos e ferramentas utilizadas no servidor BizTalk, mas agora estão disponíveis como parte das aplicações de lógica. 

Uma categoria destas aplicações de API são as aplicações de API Business Business (B2B). Utilizar estas aplicações de API B2B, pode facilmente adicionar parceiros, criar acordos e fazer tudo à medida que iria no local com o editar, AS2 e EDIFACT.  

Estas aplicações de API B2B fornecem capacidades de "Accionador" e "Ação". Um accionador inicia uma instância nova com base num evento específico, como a chegada de um X12 mensagem a partir de um parceiro. Uma ação assemelha-se o resultado, depois de receber um X12 da mensagem, em seguida, enviar a mensagem através do AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>O que é uma conexão de Business-para-Business ou API aplicações
A funcionalidade de Business-para-Business (B2B) inclui existente e previamente concebidos aplicações API que permitem empresas diferentes, divisões, aplicações, e assim sucessivamente para comunicar através de AS2, editar e EDIFACT. 

As aplicações de API B2B incluem: 

Conexão ou aplicações de API | Descrição
--- | ---
Gestão de parceiro negociação BizTalk | Uma aplicação de API que cria relações de negócio para empresas (B2B) utilizando parceiros e os acordos. Estas relações utilizam o AS2, EDIFACT e X12 protocolo.<br/><br/>A aplicação de API TPM é o requisito de base de conector AS2 e o X12 ou EDIFACT API aplicações. 
Conector AS2 | Um conector que recebe e envia mensagens utilizando o transporte AS2. A conexão transportes de dados em segurança e fiável através da Internet.
BizTalk EDIFACT | Uma aplicação de API que recebe e envia mensagens utilizando EDIFACT. EDIFACT frequentemente também é referido como UN/EDIFACT (das Nações Unidas/electrónica Interchange para administração de dados, Commerce e transporte) e é bastante utilizado entre sectores.
BizTalk X12 | Uma aplicação de API que recebe e envia mensagens utilizando o X12 protocolo. X12 é geralmente também designado como ASC X 12 (aprovados normas Comissão X12) e é bastante utilizado entre sectores. 


Utilizar estas aplicações de API, pode concluir editar diferentes mensagens tarefas. Por exemplo, utilizando o conector de AS2, pode segura receber e enviar diferentes tipos de mensagens (editar, XML, ficheiro simples, e assim sucessivamente) para um cliente, uma divisão dentro da sua empresa como recursos humanos, ou qualquer pessoa que utiliza AS2. 

Pode criar aplicações de API tantas à medida que pretende e criá-los facilmente. Também pode reutilizar uma única aplicação API dentro de várias cenários ou fluxos de trabalho.

Pode fazê-lo sem escrever qualquer código. Vamos começar. 


## <a name="requirements-to-get-started"></a>Requisitos para começar a utilizar
Quando cria B2B API aplicações, existem alguns recursos necessários. Estes itens têm de ser criados por si antes de poder ser utilizadas dentro de outras aplicações de API. Estes requisitos incluem: 

Requisito de registo | Descrição
--- | ---
Base de dados Azure SQL | Armazena os itens de B2B incluindo parceiros, esquemas, certificados e agreeements. Cada uma das aplicações de API B2B requer a sua própria base de dados do SQL Azure. <br/><br/>**Nota** Copie a cadeia de ligação para esta base de dados.<br/><br/>[Criar uma base de dados do Azure SQL](../sql-database/sql-database-get-started.md)
Contentor de armazenamento de BLOBs Azure | Arquivos de propriedades da mensagem quando AS2 arquivo está ativado. Caso não precise AS2 mensagem arquivo, um contentor de armazenamento não é necessária. <br/><br/>**Nota** Se estiver a activar o arquivo, copie a cadeia de ligação ao armazenamento Blob do.<br/><br/>[Sobre contas de armazenamento Azure](../storage/storage-create-storage-account.md)
Espaço de nomes do serviço Bus e os seus valores chave | Armazena X12 e EDIFACT lotes dados. Se já não precisa de lotes, um espaço de nomes de Bus de serviço não é necessária.<br/><br/>**Nota** Se estiver a activar o processamento em lotes, copie estes valores.<br/><br/>[Criar um espaço de nomes do serviço Bus](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM instância | Uma instância de BizTalk negociação parceiro gestão (TPM) é necessária para criar um conector de AS2 e X12 ou EDIFACT API aplicação. Quando cria a aplicação de API TPM, está a criar a instância TPM. <br/><br/>**Nota** Sabe o nome da sua aplicação de API TPM. 


## <a name="create-the-api-apps"></a>Crie aplicações a API
Aplicações de API B2B podem ser criadas utilizando o portal do Azure ou REST APIs. 


### <a name="create-the-api-apps-using-rest-apis"></a>Criar as aplicações de API REST APIs a utilizar
[Consulte a documentação sobre como utilizar a API REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Criar aplicações de API B2B no Portal do Azure
No portal do Azure, pode criar aplicações de API B2B quando criar aplicações de lógica, aplicações Web ou aplicações Mobile. Em alternativa, pode criar um utilizando a sua própria pá. Ambas as opções estão fácil por isso depende seu necessidades ou preferências. Alguns utilizadores preferem criar todas as aplicações de API de B2B pela primeira vez com as respectivas propriedades específicas. Em seguida, crie as aplicações do aplicações/Mobile Apps/Web lógica e adicionar as aplicações de API B2B que criou.  

Os passos seguintes criam aplicações API do B2B ao utilizar o pá API aplicações.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Criar BizTalk negociação parceiro gestão (TPM) API aplicações

> [AZURE.NOTE] Uma instância de BizTalk negociação parceiro gestão (TPM) é necessária para criar um conector de AS2 e X12 ou EDIFACT API aplicação. Quando cria a aplicação de API TPM, está a criar a instância TPM.

Os passos seguintes criam a instância TPM:

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **API aplicações** lista todas as aplicações de API existente e conectores. Também pode **Procurar** para as aplicações de API B2B específico.
2. Selecione **BizTalk negociação gestão de parceiro**. No pá novo, selecione **Criar**. 
3. Introduza as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Introduza um nome para a instância TPM. Por exemplo, pode nome- *AccountsPayableTPM*.
Definições do pacote | Introduza a ADO.NET **Cadeia de ligação de base de dados** para a base de dados do SQL Azure que criou. <br/><br/>Quando copia a cadeia de ligação, a palavra-passe não é adicionada a cadeia de ligação. Certifique-se de que introduza a palavra-passe na cadeia de ligação.
Plano de serviço de aplicação | Listas do seu plano de pagamento. Pode alterá-lo se precisar de mais ou menos recursos.
Preços de camadas | Propriedade só de leitura que listas da categoria, comparar na sua subscrição do Azure. 
Grupo de recursos | Criar um novo ou utilizar um grupo existente. Todas as aplicações de API e conectores para a sua lógica de aplicações, Web Apps e as aplicações móveis devem estar no mesmo grupo de recursos. <br/><br/>[Utilizar grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica esta propriedade. 
Subscrição | Propriedade só de leitura que apresenta a sua subscrição atual.
Localização | A localização geográfica que aloja o seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar a aplicação de API B2B ao seu estibordo (a home page).

4. Selecione **Criar**. 

Quando a aplicação de API TPM (TPM instância) estiver criada, em seguida, pode criar ligação AS2 e/ou o X12 ou EDIFACT API aplicações. 


#### <a name="create-the-as2-connector"></a>Criar o conector de AS2

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **API aplicações** lista todas as aplicações de API existente e conectores. Também pode **Procurar** para as aplicações de API B2B específico.
2. Selecione **AS2 conexão**. No pá novo, selecione **Criar**. 
3. Introduza as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Introduza um nome para o conector de AS2. Por exemplo, pode nome- *AS2Connector*.
Definições do pacote | Introduza as definições específicas para essa aplicação API, como o nome da instância TPM. <br/><br/>Consulte o artigo [Adicionar definições do pacote AS2](#AddAS2Conn) neste tópico para as propriedades específicas. 
Plano de serviço de aplicação | Listas do seu plano de pagamento. Pode alterá-lo se precisar de mais ou menos recursos.
Preços de camadas | Propriedade só de leitura que listas da categoria, comparar na sua subscrição do Azure. 
Grupo de recursos | Criar um novo ou utilizar um grupo existente. [Utilizar grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica esta propriedade. 
Subscrição | Propriedade só de leitura que apresenta a sua subscrição atual.
Localização | A localização geográfica que aloja o seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar a aplicação de API B2B ao seu estibordo (a home page).

    **<a name="AddAS2Conn"></a>Conector AS2 definições do pacote**

    Propriedade | Descrição
--- | --- 
Cadeia de ligação de base de dados | Introduza a cadeia de ligação ADO.NET para a base de dados do SQL Azure que criou. Quando copia a cadeia de ligação, a palavra-passe não é adicionada a cadeia de ligação. Certifique-se de que introduza a palavra-passe na cadeia de ligação antes de os colar.
Ativar o arquivo para mensagens recebidas | Opcional. Ative esta propriedade armazenar as propriedades da mensagem de uma mensagem de AS2 recebida recebido a partir de um parceiro. 
Cadeia de ligação de armazenamento de Blobs do Azure  | Introduza a cadeia de ligação para o contentor de armazenamento de Blobs do Azure que criou. Quando arquivo está ativado, os codificado e descodificadas as mensagens são armazenadas neste contentor de armazenamento.
Nome da instância TPM | Introduza o nome da **Gestão de parceiro negociação BizTalk** API aplicação que criou anteriormente. Quando cria o conector de AS2, este conector executa apenas os acordos AS2 dentro esta instância específica do TPM.

4. Selecione **Criar**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Criar aplicações de API X12 ou EDIFACT

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **API aplicações** lista todas as aplicações de API existente e conectores. Também pode **Procurar** para as aplicações de API B2B específico.
2. Selecione **BizTalk X 12** ou **BizTalk EDIFACT**. No pá novo, selecione **Criar**. 
3. Introduza as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Introduza um nome para a aplicação de API B2B. Por exemplo, pode nome- *EDI850APIApp*.
Definições do pacote | Introduza as definições específicas para essa aplicação API, como o nome da instância TPM. <br/><br/>Consulte o artigo [X12 ou definições do pacote EDIFACT](#AddX12) neste tópico para as propriedades específicas. 
Plano de serviço de aplicação | Listas do seu plano de pagamento. Pode alterá-lo se precisar de mais ou menos recursos.
Preços de camadas | Propriedade só de leitura que listas da categoria, comparar na sua subscrição do Azure. 
Grupo de recursos | Criar um novo ou utilizar um grupo existente. [Utilizar grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica esta propriedade. 
Subscrição | Propriedade só de leitura que apresenta a sua subscrição atual.
Localização | A localização geográfica que aloja o seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar a aplicação de API B2B ao seu estibordo (a home page).

    **<a name="AddX12"></a>Definições do pacote de aplicações de API de X12 e EDIFACT**  

    Propriedade | Descrição
--- | --- 
Cadeia de ligação de base de dados | Introduza a cadeia de ligação ADO.NET para a base de dados do SQL Azure que criou. Quando copia a cadeia de ligação, a palavra-passe não é adicionada a cadeia de ligação. Certifique-se de que introduza a palavra-passe na cadeia de ligação antes de os colar.
Espaço de nomes do serviço Bus | Introduza o espaço de nomes de serviço Bus que criou. Obrigatório apenas quando lotes está ativado. 
Nome de tecla de acesso partilhado serviço Bus espaço de nomes | Introduza o espaço de nomes do serviço Bus tecla de acesso que criou. Obrigatório apenas quando lotes está ativado. 
Valor de chave do Access partilhado serviço Bus espaço de nomes | Introduza o espaço de nomes do serviço Bus valor de chave de acesso que criou. Obrigatório apenas quando lotes está ativado. 
Nome da instância TPM | Introduza o nome da **Gestão de parceiro negociação BizTalk** API aplicação que criou anteriormente. Quando criar o X12 ou EDIFACT API aplicações, esta aplicação API executa apenas os acordos X12/EDFIACT dentro esta instância específica do TPM.

4. Selecione **Criar**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Adicionar o seu parceiros, acordos, certificados e esquemas 
No portal do Azure, abra a sua aplicação de API TPM. Na secção **componentes** , adicione o seu parceiros, acordos, certificados e esquemas. 

Também pode adicionar acordos às conexões AS2, X12 aplicações API e EDIFACT API aplicações. 


## <a name="monitor-your-api-apps"></a>Monitorize as suas aplicações API
No portal do Azure, abra a sua aplicação de API TPM. Na secção de **operações** , pode ver operações de gestão de diferentes. Por exemplo, pode:

- Eventos informativo de vista e de erro
- Ver a contagem de utilização e o tópico da memória do processo de trabalho (w3wp)
- Ver os registos do servidor web e de aplicação

Mais no [Monitor as suas aplicações de lógica](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Adicionar as aplicações de API para a sua aplicação 
Serviço de aplicação do Microsoft Azure expõe tipos de outra aplicação que podem utilizar estas aplicações de API B2B. Pode criar um novo ou adicionar as suas aplicações de API B2B existente a lógica de aplicações, aplicações Mobile ou um Web Apps. 

Dentro da sua aplicação, basta selecionar automaticamente as aplicações de API B2B a partir da Galeria adiciona-lo para a sua aplicação.  

> [AZURE.IMPORTANT] Para adicionar conectores e Apps API que criou anteriormente, crie a lógica de aplicações, aplicações Mobile ou Web Apps no mesmo grupo de recursos. 

Os passos seguintes adicionar as aplicações de API B2B a lógica de aplicações, aplicações Mobile ou aplicações Web: 

1. No portal do Azure Startboard (home page da), vá para o **Marketplace**e a pesquisa para a sua lógica, Mobile ou Web Apps. 

    Se estiver a criar uma nova aplicação, procure lógica de aplicações, aplicações Mobile ou aplicações Web. Selecione a aplicação e na pá novo, selecione **Criar**. [Criar uma aplicação de lógica](app-service-logic-create-a-logic-app.md) lista os passos. 

2. Abra a sua aplicação e selecione **Accionadores e ações**. 

3. A partir da **Galeria**, selecione a aplicação, API B2B, que adiciona automaticamente para a sua aplicação. Também pode criar uma nova aplicação de API B2B.

    > [AZURE.IMPORTANT] O conector de AS2 e X12, EDIFACT API aplicações necessitam de uma instância de TPM. Para se estiver a criar novas aplicações de API B2B, criar a aplicação de API TPM pela primeira vez e, em seguida, crie o conector de AS2, X12 API aplicação ou EDIFACT API aplicação. 

4. Selecione **OK** para guardar as alterações. 

>[AZURE.NOTE] Para começar a utilizar com o Azure lógica aplicações antes de inscrever-se para uma conta Azure, [Experimente aplicações lógica](https://tryappservice.azure.com/?appservice=logic). Pode criar uma aplicação de lógica starter curto imediatamente. Sem cartões de crédito necessários; sem compromissos.

## <a name="more-b2b-resources"></a>Mais recursos B2B

[Criar um processo de B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Criar um contrato de parceiro negociação](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[O que são conexões e BizTalk API aplicações](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>Leia mais sobre aplicações de lógica e Web Apps
[O que são lógica aplicações?](app-service-logic-what-are-logic-apps.md)<br/>
[Sites e aplicações Web no Azure de aplicação de serviço](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Conectores mais

[Lista de aplicações API e conectores](app-service-logic-connectors-list.md)<br/><br/>
[O que são conexões e BizTalk API aplicações](app-service-logic-what-are-biztalk-api-apps.md) 
