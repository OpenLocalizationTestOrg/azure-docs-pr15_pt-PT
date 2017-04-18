<properties 
   pageTitle="Criar um contrato de parceiro negociação no Azure de aplicação de serviço | Microsoft Azure" 
   description="Criar negociação os acordos do parceiro" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Criar um contrato de parceiro negociação   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Parceiros comerciais são as entidades envolvidos na B2B comunicações (empresas para empresas). Quando dois parceiros estabelecer uma relação, isto é referido como um *contrato*. O contrato definido é baseado no comunicação as duas parceiros vontade alcançar e é protocolo ou transporte específico. Os vários B2B protocolos e transportes suportados pelo serviço de aplicação do Azure incluem:

- AS2 (aplicação instrução 2)
- EDIFACT (das Nações Unidas eletrónica intercâmbio de dados para a administração, Commerce e transporte (UN/EDIFACT))
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>BizTalk API aplicações que suportam B2B cenários
As seguintes aplicações de API ativar estas capacidades utilizando uma experiência avançada e intuitiva no Portal do Azure:


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk negociação gestão de parceiro (TPM)
- Criação e gestão de parceiros, perfis e identidades
- Armazenamento e gestão de esquemas de editar
- Armazenamento e gestão de certificados (utilizado em AS2 protocol)
- Criação e gestão de AS2 acordos
- Criação e gestão de EDIFACT acordos (inclui lotes no lado enviar)
- Criação e gestão de X12 acordos (inclui lotes no lado enviar)

![][1]


## <a name="as2-connector"></a>Conector AS2
- Executa AS2 acordos, tal como foi definido na instância TPM API aplicação relacionada
- Superfícies AS2 processamento/controlar informações para resolução de problemas


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Executa EDIFACT acordos, tal como foi definido na instância TPM API aplicação relacionada
- Analisa as informações de processamento/rastreio EDIFACT para resolução de problemas
- Fornece gestão do Estado de lotes (Iniciar e parar), tal como definido no EDIFACT acordos na instância TPM API aplicação relacionada


## <a name="biztalk-x12"></a>BizTalk X12
- Executa X12 acordos tal como foi definido na instância TPM API aplicação relacionada 
- Superfícies X12 processamento/rastreio informações para resolução de problemas
- Fornece a gestão do Estado de lotes (Iniciar e parar), tal como foi definido no X12 acordos na instância TPM API aplicação relacionado

Como mencionado anteriormente, AS2, X 12 e EDIFACT API aplicações requerem uma aplicação de API TPM para funcionar como esperado.


## <a name="getting-started"></a>Introdução
Para criar negociação os acordos do parceiro:

1. Crie uma instância da **Gestão de parceiro negociação BizTalk** conexão. Isto requer uma base de dados do SQL em branco para a função. Antes de iniciar certifique-se de ter uma base de dados em branco disponível e pronto a utilizar.
2. Carregar esquemas e certificados conforme necessário pelos acordos. Fazê-lo a instância TPM criado e afastar-se para a parte 'Esquemas' e/ou 'Certificados' de navegação
3. Navegue para a instância TPM criada e passo para a parte de **parceiros**
4. Crie parceiros conforme pretender. Também edite perfis conforme adequado e adicione as identidades necessárias
5. Agora, utilize a peça de **acordos** para criar contratos. Quando cria um contrato, tem de selecionar o protocolo que será utilizado. As opções de configuração restantes são baseadas no protocolo que selecionou.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
