<properties
   pageTitle="Tutorial: Processar faturas EDIFACT utilizando os serviços de Azure BizTalk | Serviços de BizTalk do Microsoft Azure"
   description="Como criar e configurar a aplicação caixa de conexão ou API e utilizá-la numa aplicação lógica na aplicação de serviço do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Processo EDIFACT faturas utilizando os serviços de BizTalk do Azure
Pode utilizar o Portal de serviços de BizTalk para configurar e implementar X12 e os acordos do EDIFACT. Neste tutorial, vamos veja como criar um contrato EDIFACT para o intercâmbio de faturas entre parceiros comerciais. Neste tutorial, é escrito à volta de uma solução de ponto a ponto empresarial que envolvam dois parceiros comerciais Adamastor e Contoso trocar EDIFACT mensagens.  

## <a name="sample-based-on-this-tutorial"></a>Exemplo com base neste tutorial
Neste tutorial escrito à volta de uma amostra, **Enviar EDIFACT faturas utilizando BizTalk os serviços**, que se encontra disponível para transferir a partir da [Galeria de códigos do MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Pode utilizar o exemplo e analisá este tutorial para compreender como exemplo foi criado. Em alternativa, pode utilizar este tutorial para criar o seu próprio solução terra de segurança. Neste tutorial está atribuído no sentido a segunda abordagem para que compreender a forma como esta solução foi criada. Além disso, quanto possível, o tutorial está consistente com a amostra e utiliza os mesmos nomes para artefactos (por exemplo, esquemas, transformações) como utilizados no exemplo.  

>[AZURE.NOTE] Uma vez que esta solução envolve enviar uma mensagem a partir de um bridge endereços EAI para uma bridge de editar,-lo de volta a amostra [BizTalk serviços Bridge interligação de exemplo](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>O que faz a solução?

Nesta solução, Adamastor recebe faturas EDIFACT de Contoso. Estes faturas não estão a ser um formato de editar padrão. Assim sendo, antes de enviar a fatura a Adamastor, devem ser transformado a um documento de fatura (também designado INVOIC) EDIFACT. No recibo, Adamastor tem fatura EDIFACT do processo e devolver uma mensagem de controlo (também designada CONTRL) à Contoso.

![][1]  

Para obter este cenário de negócio, Contoso utiliza as funcionalidades fornecidas com os serviços do Microsoft Azure BizTalk.

*   Contoso utiliza pontes endereços EAI para transformar a fatura original para EDIFACT INVOIC.

*   A bridge endereços EAI, em seguida, envia a mensagem a uma bridge de enviar editar implementada como parte de um contrato configurado no Portal de serviços do BizTalk.

*   Bridge de envio de editar processa a INVOIC EDIFACT e encaminha-o para Adamastor.

*   Depois de receber a fatura, Adamastor devolve um CONTRL de mensagem para o editar recebe bridge implementado como parte do acordo.  

> [AZURE.NOTE] Opcionalmente, esta solução também demonstra como utilizar lotes para enviar as faturas em lotes, em vez de enviar cada fatura em separado.  

Para concluir o cenário, utilizamos filas Bus de serviço para enviar fatura da Contoso para Adamastor ou receber a confirmação da Northwind. Estas filas podem ser criadas utilizando uma aplicação de cliente, está disponível como uma transferência e está incluída no exemplo de pacote que está disponível como parte deste tutorial.  

## <a name="prerequisites"></a>Pré-requisitos

*   Tem de ter um espaço de nomes de serviço Bus. Para obter instruções sobre como criar um espaço de nomes, consulte o artigo [How To: criar ou modificar um espaço de nomes de serviço do serviço Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Diga-nos partem do princípio de que já tem um espaço de nomes de serviço Bus aprovisionado, denominado **edifactbts**.

*   Tem de ter uma subscrição de serviços de BizTalk. Para obter instruções, consulte o artigo [criar um serviço de BizTalk através do portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=302280). Para este tutorial, diga-nos partem do pressuposto que tiver uma subscrição de serviços de BizTalk, denominada **contosowabs**.

*   Registe-se a sua subscrição de serviços de BizTalk no Portal de serviços de BizTalk. Para obter instruções, consulte o artigo [como registar uma implementação do serviço de BizTalk no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Tem de ter o Visual Studio instalada.

*   Tem de ter BizTalk Services SDK instalado. Pode transferir o SDK do [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Passo 1: Criar as filas Bus de serviço  
Esta solução utiliza filas do serviço Bus para mensagens entre parceiros comerciais do exchange. Contoso e Adamastor enviar mensagens para as filas a partir de onde pontes endereços EAI e/ou editar consumam-los. Esta solução, terá de três filas Bus de serviço:

*   **northwindreceive** – Adamastor recebe a fatura da Contoso através desta fila.

*   **contosoreceive** – Contoso recebe a confirmação da Northwind sobre esta fila.

*   todos os **suspensa** – suspensa as mensagens são encaminhadas para esta fila. As mensagens são suspensa se houver falha durante o processamento.

Pode criar estas filas Bus de serviço, utilizando uma aplicação de cliente incluída no pacote de exemplo.  

1.  A partir da localização onde que transferiu da amostra, abra **Tutorial enviar faturas utilizando BizTalk serviços editar Bridges.sln**.

2.  Prima **F5** para compilar e iniciar a aplicação de **Cliente do Tutorial** .

3.  No ecrã, introduza o espaço de nomes, nome emissor e chave emissor serviço Bus ACS.

    ![][2]  
4.  Uma caixa de mensagem pede-lhe que três filas serão criadas no seu espaço de nomes de serviço Bus. Clique em **OK**.

5.  Deixe o cliente de iniciação em execução. Abrir, clique em **Serviço Bus** > **_o espaço de nomes de serviço Bus_** > **filas**e certifique-se de que são criadas as três filas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Passo 2: Criar e implementar o contrato de parceiro negociação
Crie um acordo de parceiro negociação entre Contoso e Adamastor. Um contrato de parceiro negociação define um contrato de comércio entre os parceiros de duas empresas, tal como qual o esquema que mensagem para utilizar, protocolo de mensagens que deseja utilizar, etc. Um contrato parceiro negociação inclui dois pontes de editar, uma para enviar mensagens para parceiros comerciais (denominados a **Editar enviar bridge**) e outra para receber mensagens de parceiros comerciais (chamados a **receber editar bridge**).

No contexto desta solução, bridge de envio de editar corresponde ao lado de enviar o contrato e é utilizado para enviar a fatura EDIFACT a partir da Contoso para Adamastor. Da mesma forma, bridge de receção de editar corresponde ao lado de receção do contrato e é utilizado para receber notificações a partir da Northwind.  

### <a name="create-the-trading-partners"></a>Criar os parceiros comerciais

Para começar, crie parceiros comerciais para Contoso e Adamastor.  

1.  No Portal de serviços BizTalk, no separador de **parceiros** , clique em **Adicionar**.

2.  Na página do novo parceiro, introduza **Contoso** como um nome de parceiro e, em seguida, clique em **Guardar**.

3.  Repita o passo para criar o segundo parceiro, **Adamastor**.  

### <a name="create-the-agreement"></a>Criar o contrato
Os acordos do parceiro negociação são criados entre os perfis de negócio de negociação parceiros. Esta solução utiliza os perfis de parceiro predefinidos que são criados automaticamente quando criámos os parceiros.  

1.  No Portal de serviços BizTalk, clique em **acordos** > **Adicionar**.

2.  Na página de **Definições gerais** o novo acordo, especifique os valores, conforme mostrado na imagem abaixo e, em seguida, clique em **continuar**.

    ![][3]  

    Depois de clicar em **continuar**, dois separadores, **Definições de receber** e **Enviar** ficam disponíveis.

3.  Crie o contrato de enviar entre Contoso e Adamastor. Este contrato controla como Contoso envia a fatura EDIFACT a Adamastor.

    1.  Clique em **definições de envio**.

    2.  Manter os valores predefinidos nos separadores **De entrada do URL**, **transformar**e **Batching** .

    3.  No separador de **protocolo** , na secção de **esquemas** , carregue o esquema de **EFACT_D93A_INVOIC.xsd** . Este esquema está disponível com o pacote de exemplo.

        ![][4]  
    4.  No separador de **transporte** , especifique os detalhes para as filas Bus de serviço. Para o contrato de enviar lado, utilizamos **northwindreceive** fila de espera para enviar a fatura EDIFACT para Adamastor e fila de espera **suspensa** para encaminhar todas as mensagens que falharem durante o processamento e são suspensa. Criou estas filas no **passo 1: criar as serviço Bus filas** (neste tópico).

        ![][5]  

        Em **as definições de transporte > tipo de transporte** e **definições de suspensão de mensagens > tipo de transporte**, selecione Bus de serviço do Azure e forneça os valores tal como apresentado na imagem.

4.  Crie o contrato de receção entre Contoso e Adamastor. Este contrato controla a forma como o Contoso recebe a confirmação da Northwind.

    1.  Clique em **definições de recepção**.

    2.  Manter os valores predefinidos nos separadores de **transporte** e **transformar** .

    3.  No separador de **protocolo** , na secção de **esquemas** , carregue o esquema de **EFACT_4.1_CONTRL.xsd** . Este esquema está disponível com o pacote de exemplo.

    4.  No separador **encaminhar** , crie um filtro para se certificar de que apenas as confirmações da Northwind são encaminhadas para Contoso. Em **Encaminhar definições**, clique em **Adicionar** para criar o encaminhamento filtro.

        ![][6]  
        1.  Forneça os valores para **O nome da regra**, **regra encaminhar**e **destino encaminhar** conforme mostrado na imagem.

        2.  Clique em **Guardar**.

    5.  No separador **encaminhar** novamente, especificar onde confirmações suspensas (confirmações falharem durante o processamento) são encaminhadas para. Definir o tipo de transporte Azure Service bus, encaminhar o tipo de destino para **fila de espera**, tipo de autenticação para **Partilhado assinatura de acesso** (SA), forneça a cadeia de ligação SA para o espaço de nomes de serviço Bus e, em seguida, introduza o nome da fila como **suspensa**.

5.  Por fim, clique em **Implementar** para implementar o contrato. Tenha em atenção os pontos finais onde a enviar e receber acordos implementados.

    *   No separador **Definições de envio** , em **URL de entrada**, tenha em atenção o ponto final. Para enviar uma mensagem a partir da Contoso para Adamastor utilizando bridge de envio de editar, terá de enviar uma mensagem para este ponto final.

    *   No separador **Definições de recepção** , em **transporte**, tenha em atenção o ponto final. Para enviar uma mensagem de Adamastor à Contoso utilizando a editar receber bridge, terá de enviar uma mensagem para este ponto final.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Passo 3: Criar e implementar o projeto BizTalk serviços

No passo anterior, implementado enviar editar e receber acordos para processar EDIFACT faturas e notificações. Estes acordos podem apenas as mensagens de processo que está em conformidade com o esquema de mensagem EDIFACT padrão. No entanto, pelo cenário para esta solução, Contoso envia uma fatura a Adamastor num esquema proprietário sessões. Assim sendo, antes da mensagem é enviada para o bridge de envio de editar,-deve ser transformado a partir do esquema de utilização interno para o esquema de fatura EDIFACT padrão. O project de BizTalk serviços EAI faz que.

Projeto de serviços de BizTalk, **InvoiceProcessingBridge**, que transforma a mensagem também é incluído como parte do exemplo que transferiu. O project inclui os seguintes erros:

*   **INHOUSEINVOICE. XSD** – esquema da fatura sessões que é enviada para Adamastor.

*   **EFACT_D93A_INVOIC. XSD** – esquema da fatura EDIFACT padrão.

*   **EFACT_4.1_CONTRL. XSD** – esquema do aviso EDIFACT que Adamastor envia para Contoso.

*   **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – a transformação que mapeie o esquema de fatura utilização interna para o esquema de fatura EDIFACT padrão.  

### <a name="create-the-biztalk-services-project"></a>Criar o projecto BizTalk serviços
1.  Na solução do Visual Studio, expanda o projeto InvoiceProcessingBridge e, em seguida, abra o ficheiro **MessageFlowItinerary.bcs** .

2.  Clique em qualquer parte na tela e configurar o **URL do serviço de BizTalk** na caixa de propriedade para especificar o nome da sua subscrição BizTalk serviços. Por exemplo, `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  A partir da barra de ferramentas, arraste um **Xml One-way Bridge** para a tela. Defina as propriedades de **Entidade nome** e **Endereço relativa** da ponte para **ProcessInvoiceBridge**. Faça duplo clique **ProcessInvoiceBridge** para abrir a superfície de configuração de bridge.

4.  Dentro da caixa de **Tipos de mensagem** , clique no sinal de adição (**+**) botão para especificar o esquema da mensagem a receber. Uma vez que a mensagem a receber para a bridge endereços EAI é sempre a fatura utilização interna, defina este para **INHOUSEINVOICE**.

    ![][8]  
5.  Clique na forma de **Transformação de Xml** e, na caixa de propriedade, para a propriedade de **mapas** , clique no botão reticências (…****). Na caixa de diálogo **Seleção de mapas** , selecione o ficheiro de transformação de **INHOUSEINVOICE_to_D93AINVOIC** e, em seguida, clique em **OK**.

    ![][9]  
6.  Volte para **MessageFlowItinerary.bcs**e da caixa de ferramentas, arraste um **Ponto final de serviço externos Two-Way** à direita da **ProcessInvoiceBridge**. Defina a propriedade do **Nome da entidade** para **EDIBridge**.

7.  No Explorador de solução, expanda o **MessageFlowItinerary.bcs** e faça duplo clique no ficheiro **EDIBridge.config** . Substitua os conteúdos da **EDIBridge.config** com o seguinte.

    > [AZURE.NOTE] Porque é necessário editar o ficheiro. config? O ponto final de serviço externo que adicionámos para a tela estruturador bridge representa pontes editar que são implementados anterior. Editar pontes são pontes bidirecional, com um enviar e recebem lado. No entanto, o bridge endereços EAI que adicionámos para o estruturador de bridge é uma bridge unidirecional. Sim, para processar os padrões de exchange mensagem diferente das duas pontes, utilizamos um comportamento bridge personalizado, incluindo a sua configuração no ficheiro. config. Para além disso, o comportamento personalizado também processa a autenticação para o ponto final do editar enviar bridge. Este comportamento personalizado está disponível como uma amostra separada na [BizTalk serviços Bridge interligação de exemplo - endereços EAI para editar](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Esta solução volta da amostra.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Atualizar o ficheiro EDIBridge.config para incluir detalhes de configuração

    *   Em _<behaviors>_, forneça o espaço de nomes de ACS e chave associada a subscrição dos serviços de BizTalk.

    *   Em _<client>_, fornecer o ponto final onde o contrato de enviar editar é implementado.

    Guarde as alterações e feche o ficheiro de configuração.

9.  A partir da barra de ferramentas, clique na **conexão** e participar os componentes **ProcessInvoiceBridge** e **EDIBridge** . Selecione a conexão e, na caixa de propriedades, defina a **Condição de filtro** para **Corresponder todas as**. Isto assegura que todas as mensagens processadas pelos bridge endereços EAI são encaminhadas à bridge editar.

    ![][10]  
10.  Guarde alterações a solução.  

### <a name="deploy-the-project"></a>Implementar o projeto

1.  No computador onde a criou o projeto de serviços de BizTalk, transfira e instale o certificado SSL para a sua subscrição BizTalk serviços. A partir de, em serviços BizTalk, clique em **Dashboard**e, em seguida, clique em **Transferir um certificado SSL**. Faça duplo clique sobre o certificado e siga a linha de comandos para concluir a instalação. Certifique-se de que instala o certificado em arquivo de certificados **Autoridades de certificação de raiz fidedigna** .

2.  No Explorador de solução do Visual Studio, com o botão direito do projecto **InvoiceProcessingBridge** e, em seguida, clique em **Implementar**.

3.  Forneça os valores tal como apresentado na imagem e, em seguida, clique em **Implementar**. Pode obter as credenciais de ACS para serviços de BizTalk ao clicar em **Informações de ligação** a partir do dashboard de serviços de BizTalk.

    ![][11]  

    A partir do painel de saída, copie o ponto final de onde a bridge endereços EAI é implementada, por exemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Terá este URL de ponto final mais tarde.  

## <a name="step-4-test-the-solution"></a>Passo 4: Testar a solução


Este tópico, podemos observe como testar a solução, utilizando a aplicação de **Cliente do Tutorial** fornecida como parte da amostra.  

1.  No Visual Studio, prima F5 para iniciar o **Tutorial cliente**.

2.  No ecrã tem de ter os valores pré-povoada do passo onde criámos as filas Bus de serviço. Clique em **seguinte**.

3.  Na janela seguinte, fornecem ACS as credenciais para subscrição dos serviços de BizTalk e os pontos finais onde endereços EAI e editar (receber) pontes são implementadas.

    O ponto final bridge de endereços EAI tinha copiou no passo anterior. Para editar receba ponto final de bridge, no Portal de serviços do BizTalk, vá para o contrato de > definições de recepção > transporte > ponto final.

    ![][12]  
4.  Na janela seguinte, em Contoso, clique no botão **Enviar sessões fatura** . No ficheiro de caixa de diálogo Abrir, abra o ficheiro INHOUSEINVOICE.txt. Examinar o conteúdo do ficheiro e, em seguida, clique em **OK** para enviar a fatura.

    ![][13]  
5.  Em alguns segundos a fatura é recebida no Adamastor. Clique na ligação de **Mensagem de vista** para ver a fatura recebida pelo Adamastor. Repare como a fatura recebida pelo Adamastor é no esquema EDIFACT padrão enquanto que lhe foi enviada pelo Contoso foi um esquema de sessões.

    ![][14]  
6.  Selecione a fatura e, em seguida, clique em **Enviar confirmação**. Na caixa de diálogo que aparece, repare que o ID de intercâmbio é o mesmo na fatura recebida e o aviso a ser enviada. Clique em OK na caixa de diálogo de **Confirmação de enviar** .

    ![][15]  
7.  Em alguns segundos, a confirmação com êxito é recebida no Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Passo 5 (opcional): fatura EDIFACT enviar em lotes 
Pontes BizTalk serviços editar também suporta lotes de mensagens a enviar. Esta funcionalidade é útil para receber parceiros que preferem para receber um lote de mensagens (determinado critério da reunião) em vez de mensagens individuais.

O aspecto mais importante ao trabalhar com secções é a versão real do lote, também denominado os critérios de lançamento. Os critérios de lançamento podem ser baseados na forma como o parceiro receção pretende receber mensagens. Se lotes estiver ativada, o bridge de editar não a enviar a mensagem a enviar para o parceiro de receção, até que os critérios de lançamento for cumprida. Por exemplo, um critério lotes com base na mensagem tamanho distribuições um lote de apenas quando «n» mensagens são enviadas em batches. Um critério lote pode também ser baseados no tempo, por exemplo, que é enviado um lote de cada vez fixo diariamente. Esta solução, vamos tentar os critérios baseada em tamanho da mensagem.

1.  No Portal de serviços BizTalk, clique no contrato que criou anteriormente. Clique em definições de envio > lotes > Adicionar lote.

2.  Nome do lote, introduza **InvoiceBatch**, forneça uma descrição e, em seguida, clique em **seguinte**.

3.  Especificar um critério lote, que define as mensagens que devem ser enviadas em batches. Nesta solução, podemos batch todas as mensagens. Por isso, selecione utilizar definições opção avançada e introduza **1 = 1**. Esta é uma condição que esteja sempre true e, consequentemente, todas as mensagens vai ser enviadas em batches. Clique em **seguinte**.

    ![][17]  
4.  Especificar um critério de lançamento do lote. A caixa de lista pendente, selecione **MessageCountBased**e **contar**, especifique **3**. Isto significa que um lote de três mensagens será enviado para Adamastor. Clique em **seguinte**.

    ![][18]  
5.  Reveja o resumo e, em seguida, clique em **Guardar**. Clique em **Implementar** a implementar o contrato.

6.  Voltar para o **Cliente de iniciação**, clique em **Enviar sessões fatura**e siga os pedidos para enviar a fatura. Vai notar que não fatura é recebida no Adamastor porque o tamanho do lote não for cumprido. Repita este passo mais duas vezes, para que tenha três fatura mensagens enviadas para Adamastor. Isto satisfizer os critérios de lançamento do lote de 3 mensagens e deverá agora visualizar uma fatura em Adamastor.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

