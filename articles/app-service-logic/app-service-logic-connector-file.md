<properties
    pageTitle="Utilizando o conector de ficheiro nas aplicações de lógica | Aplicação de serviço do Microsoft Azure"
    description="Como criar e configurar o conector de ficheiro ou a aplicação de API e utilizá-la numa aplicação lógica na aplicação de serviço do Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Começar a trabalhar com o conector de ficheiro e adicioná-lo para a sua aplicação de lógica
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Ligar a um sistema de ficheiros para carregar, transferir e muito mais aos seus ficheiros num computador anfitrião. Podem acionar lógica aplicações com base numa variedade de origens de dados e conectores de oferta para obter e processar dados. Pode adicionar o conector de ficheiro para os seus dados de fluxo de trabalho e o processo de negócio como parte de uma aplicação de lógica este fluxo de trabalho. 

O conector de ficheiro utiliza o Gestor de ligação híbrido para conectividade de híbrido para o sistema de ficheiros do anfitrião.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Criar uma conexão de ficheiro para a sua aplicação de lógica ##
Para utilizar o conector de ficheiro, necessita de criar primeiro uma instância da aplicação de API do conector de ficheiro. Isto pode ser feito da seguinte forma:

1.  Abra o Azure Marketplace utilizando o + novo opção no lado esquerdo do Portal do Azure.
2.  Procure "ficheiro conexão".
3.  Selecione **A conexão de ficheiro (pré-visualização)** nos resultados da pesquisa.
4.  Selecione o botão **Criar**
5.  Configure o conector de ficheiro da seguinte forma:  
![][1]

    - **Nome** - atribua um nome para a conexão de ficheiro
    - **Definições do pacote**
        - **Pasta raiz** - especifique o caminho da pasta raiz no seu computador do anfitrião. Por ex. D:\FileConnectorTest
        - **Cadeia de ligação do serviço Bus** - fornecer uma cadeia de ligação de Bus de serviço. Certifique-se de que o espaço de nomes do serviço bus é do tipo padrão e não básicas para permitir a utilização de serviço Bus retransmissão.  Reencaminhamento de Bus do serviço é utilizado para ligar para o Gestor de ligação híbrido.
    - **Plano de serviço de aplicação** - selecionar ou criar um plano de serviço de aplicação
    - **Preços camada** - selecionar uma definição de preços camada para a conexão
    - **Grupo de recursos** - selecionar ou criar um grupo de recursos onde a conexão deve residem
    - **Subscrição** - escolha uma subscrição que pretende que este conector ser criada em
    - **Localização** - selecione a localização geográfica onde pretende que a conexão para ser implementada

4. Clique em criar. Será criada uma nova conexão de ficheiro

## <a name="configure-hybrid-connection-manager"></a>Configurar o Gestor de ligação híbrido ##
Quando a instância da aplicação de API estiver criada, navegue para o seu dashboard.  Isto pode ser feito ao clicar em Procurar > API aplicações > selecione a conexão de ficheiro API aplicação.  A partir daqui, o Gestor de ligação híbrido tem de ser configurado.
Para obter mais informações sobre a configuração e o Gestor de ligação híbrido de resolução de problemas, consulte [utilizar o Gestor de ligação híbrido].

## <a name="using-the-file-connector-in-your-logic-app"></a>Utilizando o conector de ficheiro na sua aplicação de lógica ##
Assim que a sua aplicação API for criada, agora pode usar o conector de ficheiro como uma ação para a sua aplicação de lógica. Para executar esta tarefa, tem de:

1.  Criar uma nova aplicação de lógica e selecione o grupo de recursos mesmo que tem o conector de ficheiro. Siga as instruções para [criar uma nova aplicação de lógica].

2.  Abra "Accionadores e ações" da aplicação de lógica criadas para abrir as aplicações de lógica Designer e configurar o seu fluxo.

3.  O conector de ficheiro apareceriam na secção "API aplicações neste grupo de recursos" na Galeria de no lado direito.

4.  Pode largar a aplicação de API do conector de ficheiro para o editor de ao clicar em "Connector do ficheiro". conector do ficheiro expõe um accionador e 4 ações:  
![][5]

6.  Cada um dos seguintes procedimentos expõe determinadas propriedades. A imagem abaixo apresenta as propriedades para o accionador e obter a ação de ficheiro:  
![][6]

7. Assim que estes estão configuradas, accionador e ação podem ser utilizado no seu fluxo. Da mesma forma, outras ações podem ser configuradas também.

> [AZURE.NOTE] O accionador ficheiro irá eliminar o ficheiro depois de leu com êxito a partir da pasta.

## <a name="file-connector-rest-apis"></a>Conector REST APIs de ficheiros ##
Para utilizar o conector fora de uma aplicação de lógica, podem ser utilizadas APIs do resto exposto pela conexão. Pode ver este definições de API utilizando Procurar -> Api aplicação -> a conexão de ficheiro. Agora, clique na lente de definição de API na secção de resumo para ver todos os APIs expostas por este conector:  
![][7]

Detalhes das APIs podem ser encontrados em [definição API do conector de ficheiro].

## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de trabalho de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se pretender começar com aplicações do Azure lógica antes de inscrever-se para uma conta do Azure, aceda a [lógica Experimente a aplicação](https://tryappservice.azure.com/?appservice=logic), onde imediatamente pode criar uma aplicação de lógica starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

Ver a referência Swagger REST API [API aplicações referência e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Também pode rever a segurança estatísticas e controlo de desempenho ao conector. Consulte o artigo [Gerir e monitorizar o seu incorporada aplicações API e conexão](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Criar uma nova aplicação de lógica]: app-service-logic-create-a-logic-app.md
[Definição de API do conector de ficheiro]: https://msdn.microsoft.com/library/dn936296.aspx
[Utilizar o Gestor de ligação híbrido]: app-service-logic-hybrid-connection-manager.md
