<properties 
    pageTitle="Descrição geral da integração da empresa | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Utilizar as funcionalidades de integração da empresa para ativar o processo e integração cenários para empresas com aplicações de lógica" 
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
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Descrição geral do pacote de integração de empresa

## <a name="what-is-the-enterprise-integration-pack"></a>O que é o pacote de integração de empresa?
O pacote de integração da empresa é solução de baseado na nuvem da Microsoft para forma totalmente integrada ativar comunicações do negócio para empresas (B2B). O pacote de utiliza protocolos padrão da indústria incluindo [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)e [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) a troca de mensagens entre parceiros de negócios. Mensagens podem ser protegidas de opcionalmente utilizando encriptação e as assinaturas digitais. 

O pacote de formatos a troca de mensagens eletronicamente por transformará os diferentes formatos para um formato que sistemas dos ambas as organizações podem interpretar e tomar medidas na e permite às organizações que utilizem protocolos diferentes. 

Se estiver familiarizado com o servidor BizTalk ou serviços da Microsoft Azure BizTalk, vai encontrar fáceis de utilizar que funcionalidades da integração da empresa, uma vez que a maioria dos conceitos é semelhante. Uma grande diferença é que integração da empresa utiliza contas integração para simplificar o armazenamento e gestão de artefactos utilizado B2B comunicações. 

Neutros, o pacote de integração da empresa é baseado **contas integração** armazenar todos os erros que podem ser utilizados para estruturar, implementar e manter as suas aplicações B2B. Uma conta de integração que mostra basicamente é um contentor baseado na nuvem, onde pode armazena artefactos como esquemas, parceiros, certificados, mapas e os acordos do. Estes artefactos, em seguida, podem ser utilizados nas aplicações de lógica para construir B2B fluxos de trabalho. Antes de poder utilizar erros numa aplicação lógica, é necessário ligar a sua conta de integração para a sua aplicação de lógica. Depois de ligá-las, a sua aplicação de lógica terá acesso às artefactos a conta de integração.  

## <a name="why-should-you-use-enterprise-integration"></a>Por que motivo devo utilizar o integração da empresa?
- Com a integração da empresa, são poderão armazenar todos os seus artefactos num único local, o que é a sua conta de integração. 
- Pode tirar partido o motor de aplicações de lógica e todas as suas conexões para construir B2B fluxos de trabalho e integrar com aplicações de SaaS festa 3º, aplicações no local, bem como aplicações personalizadas
- Também pode tirar partido funções Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>Como posso começar a integração da empresa?
Pode criar e gerir aplicações de B2B utilizando o pacote de integração de empresa através do estruturador de aplicações de lógica no **portal do Azure**.  

Também pode utilizar o [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "lógica aplicações tópicos do PowerShell") para gerir as suas aplicações de lógica. 

Eis uma descrição geral dos passos que precisa de tomar antes de poder criar aplicações no portal do Azure: ![imagem de descrição geral](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>O que são alguns cenários comuns?

Integração da empresa suporta estes normas da indústria:   

- Editar - intercâmbio de dados Eletrónicos  
- Endereços EAI - integração de aplicações empresariais  

## <a name="heres-what-you-need-to-get-started"></a>Eis o que precisa para começar a utilizar
- Uma subscrição com uma conta de integração do Azure
- Visual Studio 2015 para criar mapas e esquemas
- [Ferramentas de integração da empresa de aplicações do Microsoft Azure lógica para Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Experimente
[Experimente agora](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) implementar uma amostra completamente operacional AS2 enviar e receber lógica app que utiliza as funcionalidades de B2B de lógica de aplicações.

## <a name="learn-more-about"></a>Obter mais informações sobre:
- [Acordos] (./app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre acordos de integração de empresa")
- [Cenários para empresas para empresas (B2B)] (./app-service-logic-enterprise-integration-b2b.md "Saiba como criar aplicações de lógica com funcionalidades B2B")  
- [Certificados] (./app-service-logic-enterprise-integration-certificates.md "Saiba mais sobre os certificados de integração de empresa")
- [Ficheiro simples codificação/descodificar] (./app-service-logic-enterprise-integration-flatfile.md "Saiba como codificar e descodificar conteúdos do ficheiro simples")  
- [Contas de integração] (./app-service-logic-enterprise-integration-accounts.md "Saiba mais sobre contas de integração")
- [Mapas] (./app-service-logic-enterprise-integration-maps.md "Saiba mais sobre a integração da empresa de mapas")
- [Parceiros] (./app-service-logic-enterprise-integration-partners.md "Saiba mais sobre parceiros de integração de empresa")
- [Esquemas] (./app-service-logic-enterprise-integration-schemas.md "Saiba mais sobre esquemas de integração de empresa")
- [Validação de mensagem XML] (./app-service-logic-enterprise-integration-xml.md "Saiba como validar mensagens XML com aplicações de lógica")
- [Transformação de XML] (./app-service-logic-enterprise-integration-transform.md "Saiba mais sobre a integração da empresa de mapas")
- [Conectores de integração de empresa] (../connectors/apis-list.md "Saiba mais sobre conectores de pacote de integração de empresa")



