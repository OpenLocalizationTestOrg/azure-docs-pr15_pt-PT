<properties 
    pageTitle="Saiba como criar um contrato AS2 para o pacote de integração de empresa" 
    description="Saiba como criar um contrato AS2 para o pacote de integração de Enterprise | Aplicação de serviço do Microsoft Azure" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-as2"></a>Integração da empresa com AS2

## <a name="create-an-as2-agreement"></a>Criar um contrato AS2
Para que possa utilizar as funcionalidades de empresa nas aplicações de lógica, primeiro tem de criar acordos. 

### <a name="heres-what-you-need-before-you-get-started"></a>Eis o que precisa antes de começar
- Uma [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua subscrição do Azure  
- Pelo menos mais duas [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração  

>[AZURE.NOTE]Ao criar um contrato, o conteúdo no ficheiro contrato tem de corresponder o tipo de acordo.    


Depois de ter [criado uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionado parceiros](./app-service-logic-enterprise-integration-partners.md), pode criar um contrato seguindo estes passos:  

### <a name="from-the-azure-portal-home-page"></a>Na home page de portal Azure

Depois de registo para o [Azure portal](http://portal.azure.com "Azure portal"):  
1. Selecione **Procurar** a partir do menu à esquerda.  

>[AZURE.TIP]Se não vir a ligação **Procurar** , poderá ter de expandir o menu pela primeira vez. Faça o seguinte ao selecionar a ligação **Mostrar o menu de** que está localizada na parte superior esquerda do menu fechado.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escreva *integração* na caixa de pesquisa de filtro, em seguida, selecione **Contas de integração** a partir da lista de resultados.       
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. No pá de **Contas de integração** aberto, selecione a conta de integração no qual irá criar o contrato. Se não vir qualquer integração contas listas, [criar um primeiro](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4.  Selecione o mosaico **acordos** . Se não vir os acordos dispor em mosaico, adicioná-la pela primeira vez.   
![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
5. Selecione o botão **Adicionar** na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Introduza um **nome** para o contrato, em seguida, selecione o **Parceiro de anfitrião**, **Identidade de anfitrião**, **Parceiro de convidado**, **Identidade de convidado**, na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

Aqui estão alguns detalhes que pode ser úteis quando configurar as definições para o contrato de: 
  
|Propriedade|Descrição|
|----|----|
|Parceiro de anfitrião|Um contrato necessita de um sistema anfitrião e o convidado parceiro. O parceiro de anfitrião representa a organização que está a configurar o contrato.|
|Identidade do anfitrião|Um identificador para o parceiro de anfitrião. |
|Parceiro de convidado|Um contrato necessita de um sistema anfitrião e o convidado parceiro. O parceiro de convidado representa a organização que está a fazer negócio com o parceiro de anfitrião.|
|Identidade de convidado|Um identificador para o parceiro de convidado.|
|Definições de recepção|Estas propriedades aplicam a todas as mensagens recebidas por um contrato|
|Definições de envio|Estas propriedades aplicam a todas as mensagens enviadas por um contrato|  
Vamos continuar:  
7. Selecione **Definições de recepção** para configurar como mensagens recebidas através deste contrato estão a ser processada.  
 
 - Opcionalmente, pode substituir as propriedades da mensagem a receber. Para executar esta tarefa, selecione a caixa de verificação **substituir as propriedades da mensagem** .
  - Selecione a caixa de verificação **deve ser assinada mensagem** se pretender para exigir todas as mensagens recebidas para ter sessão iniciada. Se selecionar esta opção, também terá de selecionar o **certificado** que será utilizada para validar a assinatura nas mensagens.
  - Opcionalmente, pode requerer mensagens sejam encriptados também. Para executar esta tarefa, selecione a caixa de verificação **mensagem deve ser encriptada** . Que precisa, em seguida, selecione o **certificado** que será utilizada para codificar as mensagens a receber.
  - Também pode pedir mensagens para ser comprimido. Para executar esta tarefa, selecione a caixa de verificação **mensagem deve ser comprimida** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

Se pretender obter mais informações sobre a receber que as definições de ativar, consulte a tabela abaixo.  

|Propriedade|Descrição|
|----|----|
|Substituir as propriedades da mensagem|Selecione esta opção para indicar que podem ser substituídas propriedades em mensagens recebidas |
|Mensagem deve ser assinada|Ativar esta opção para requerer mensagens a ser assinado digitalmente|
|Mensagem deve ser encriptada|Ative esta opção para requerer mensagens sejam encriptados. Mensagens encriptadas não serão rejeitadas.|
|Mensagem deve ser comprimida|Ative esta opção para requerer mensagens para ser comprimido. Mensagens comprimidos não serão rejeitadas.|
|MDN texto|Esta é uma predefinição MDN sejam enviadas para o remetente da mensagem|
|Enviar MDN|Ative esta opção para permitir MDNs sejam enviadas.|
|Enviar MDN com a sessão iniciada|Ative esta opção para requerer MDNs de ser assinados.|
|Algoritmo de Microfone||
|Enviar MDN assíncrona|Ative esta opção para requerer mensagens sejam enviadas modo assíncrono.|
|URL|Este é o URL para o qual as mensagens serão enviadas.|
Agora, vamos continuar:  
8. Selecione **Definições de envio** para configurar a forma como as mensagens enviadas através deste contrato não para ser resolvido.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

Se pretender obter mais informações sobre o que enviar as definições de ativar, consulte a tabela abaixo.  

|Propriedade|Descrição|
|----|----|
|Activar a assinatura de mensagem|Selecione esta caixa de verificação para ativar todas as mensagens enviadas a partir do contrato de ser assinados.|
|Algoritmo de Microfone|Selecione o algoritmo para utilizar na assinatura de mensagem|
|Certificado|Selecione o certificado para utilizar na assinatura de mensagem|
|Ativar a encriptação de mensagens|Selecione esta caixa de verificação para encriptar todas as mensagens enviadas a partir deste contrato.|
|Algoritmo de encriptação|Selecione o algoritmo de encriptação para utilizar em encriptação de mensagens|
|Desdobrar cabeçalhos de HTTP|Selecione esta caixa de verificação para desdobrar o cabeçalho do tipo de conteúdo de HTTP para uma única linha.|
|Pedido de MDN|Ativar esta caixa de verificação Pedir um MDN para todas as mensagens enviadas a partir deste contrato|
|Pedir MDN com a sessão iniciada|Ativar para pedir que todos os MDNs enviado para este contrato tem sessão iniciada|
|Pedir MDN assíncrona|Ativar para pedir MDN assíncrona sejam enviadas para o presente contrato|
|URL|O URL para o qual serão enviadas MDNs|
|Ativar NRR|Selecione esta caixa de verificação para ativar não repudiação recibo|
Vamos quase terminados!  
9. Selecione o mosaico **acordos** no pá a conta de integração e irá ver o contrato recentemente adicionado listado.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

