<properties 
   pageTitle="Criar um processo de B2B na aplicação de serviço de Azure | Microsoft Azure" 
   description="Descrição geral sobre como criar um processo de negócio para empresas" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Criar um processo de B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Cenário de negócio 
Contoso e Adamastor são duas parceiros de negócios. Contoso (o revendedor) envia encomendas de compra para Adamastor (fornecedor) através de uma indústria nível de transporte como AS2. Adamastor armazena todas as encomendas recebidas no seu armazenamento em nuvem. As encomendas de compra são mensagens XML entre estes dois parceiros. Assim que a mensagem é armazenada no armazenamento em nuvem da Northwind processos internos da Northwind alça a ordem a partir desse momento em.
 
Este tutorial objectivo estabelecer como Adamastor pode estabelecer um processo empresarial através do qual pode receber mensagens de (encomendas de compras na XML) a partir do seu parceiro Contoso através do AS2 e, em seguida, persistirem-lo no seu armazenamento em nuvem.


## <a name="capabilities-demonstrated"></a>Capacidades de demonstrado 
Neste tutorial ajuda-o a exibir os as seguintes funcionalidades: 

- **Transportes de mensagem**: O revendedor e fornecedor podem ser diferentes plataformas, mas ainda podem atingir comunicações entre os dois. Neste tutorial são comunicar através de AS2 (aplicação declaração 2). AS2 é uma forma popular de dados de transporte entre parceiros comerciais comunicações de negócio para empresas.
- **Recorrência de dados**: assim que a mensagem foi recebida sobre AS2, em seguida, Adamastor pretende persistirem-lo antes de processamento suplementar. Pode utilizar uma conexão persistir mensagens no seu armazenamento em nuvem. Neste tutorial Blobs do Azure está a ser utilizadas como o armazenamento em nuvem para Adamastor.
- **Criar um processo empresarial**: num fluxo, várias aplicações de API podem ser coser em conjunto para alcançar um resultado de negócio, tal como demonstrado aqui.


## <a name="before-you-begin"></a>Antes de começar
Neste tutorial assume que tenha noções básicas dos serviços de aplicação do Azure, sabe como criar aplicações de API e compor um fluxo de.


## <a name="steps-to-achieve-the-business-scenario"></a>Passos para atingir o cenário de negócio
**Criar e configurar as aplicações de API necessárias**

1. Crie uma instância do **Azure armazenamento Blob conexão**. Isto requer que as credenciais para uma conta de armazenamento do Windows Azure. Certifique-se de que está pronta antes de começar a criar esta.
2. Crie uma instância da **Gestão de parceiro negociação BizTalk**. Isto requer uma base de dados do SQL em branco para a função. Certifique-se de que está pronta antes de começar a criar esta.
3. Crie uma instância do **Conector de AS2**. Isto requer que também uma base de dados do SQL em branco para a função. Certifique-se de que está pronta antes de começar a criar esta. Para além disso, se pretende arquivar mensagens como parte do AS2 processamento, pode fornecer as credenciais para uma BLOBs do Azure durante a sua criação.
4. Configure o serviço TPM (negociação parceiro gestão) que é criado:  
    1. Navegue para a instância do serviço TPM criada como parte dos passos acima.
    2. Utilizar a opção de **parceiros** em *componentes* para **Adicionar** um novo parceiro com o nome **Contoso** e no seu perfil, adicione a identidade AS2 necessária.
    3. Utilizar a opção de **parceiros** em *componentes* para **Adicionar** um novo parceiro denominado **Adamastor** e no seu perfil, adicione a identidade AS2 necessária.
    4. Utilize a opção de **acordos** em *componentes* para o contrato de **Adicionar** um novo AS2 entre Adamastor e Contoso. Adamastor será o parceiro alojado aqui e Contoso será o parceiro de convidado. Conforme adequado configurar iniciar sessão, encriptação, compressão e confirmações durante a criação este contrato. No caso de precisar de certificados ser utilizado, estes podem ser carregados através da opção de **certificados** quando o serviço TPM que é criado de navegação.


## <a name="create-a-flow--business-process"></a>Criar um fluxo de / processo de negócio
1. Crie um novo fluxo em que o primeiro passo é AS2. Arraste e largue o **Conector de AS2** e escolha a instância já criada. Selecione accionador como a funcionalidade:  
    ![][1]  
2. Em seguida arraste e largue **Azure armazenamento Blob conexão** e selecione a instância já criada. Selecione ação como a funcionalidade e dentro desse, selecione **Carregar Blob** como a funcionalidade pretendida. Configure conforme adequado.
3. Agora já criar/implemente o fluxo.


## <a name="message-processing--troubleshooting"></a>Processamento de mensagens e resolução de problemas
1. É altura de testar terminar o fluxo que implementou o podemos. Envie que mensagens de XML moldado na AS2 (de acordo com o contrato de AS2 criado em cima) para o ponto final AS2 estar pela instância AS2Connector que criou. Poderá ter de configurar a autenticação para o ponto final para que seja acessível publicamente.
2. Execução obter informações sobre o fluxo são estar, navegar até o fluxo de e, em seguida, passo a passo para a instância do fluxo que tem a ser executada
3. Para obter informações de processamento de AS2, navegue para a instância AS2Connector envolvida e, em seguida, siga por afastar-se para a parte do controlo. Pode utilizar os filtros envolvidos restringir a vista para as informações que são pretendidas.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
