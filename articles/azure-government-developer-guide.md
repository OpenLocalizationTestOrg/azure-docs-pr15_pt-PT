<properties 
    pageTitle="Guia do Azure administração pública" 
    description="Este procedimento fornece uma comparision das funcionalidades e orientações sobre como desenvolver aplicações para administração pública do Azure" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guia de programador do Microsoft Azure administração pública 

<p> Administração pública do Microsoft Azure é uma física e instância isolada de rede do Microsoft Azure.  Este guia os programadores irá fornecer detalhes sobre as diferenças que os programadores de aplicações e administradores seriam necessário interagir e trabalhar com estes regiões separadas do Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Neste tópico


+ [Descrição geral](#Overview)
+ [Orientações para programadores](#Guidance)
+ [Funcionalidades atualmente disponíveis no Microsoft Azure administração pública](#Features)
+ [Mapeamento de ponto final](#Endpoint)
+ [Próximos passos](#next)


## <a name="Overview"></a>Descrição geral

Administração pública do Microsoft Azure é uma instância separada do serviço do Microsoft Azure endereçar as necessidades de segurança e conformidade da organismos federais dos EUA, estado e administração local e os respetivos fornecedores de soluções. Administração pública Azure oferece física e de rede isolamento de implementações do Governo não dos Estados Unidos e fornece filtrada EUA pessoal. 

A Microsoft fornece um número de ferramentas para criar e implementar aplicações de nuvem da Microsoft global Microsoft Azure (serviço "Global") e serviços de administração pública do Microsoft Azure.

Quando criar e implementar as aplicações para os programadores de Governo dos serviços do Azure, por oposição ao serviço Global, precisa de saber as diferenças dos duas serviços chave.  Especificamente à volta de instalação e configuração do seu ambiente de trabalho de programação, a configurar os pontos finais, escrever aplicações e implementá-los como serviços para administração pública do Azure.

As informações neste documento resume as diferenças e as informações disponíveis no site(http://www.azure.com/gov "Azure Governo") [Governo do Azure]e a [Biblioteca técnica do Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") no MSDN os suplementos. Informações oficiais também poderão estar disponíveis em muitas outras localizações tais como a [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Centro de documentação do Azure](https://azure.microsoft.com/documentation/) e no [blogues Azure] (https://azure.microsoft.com/blog/ "Blogues Azure" /). 

Este conteúdo destina-se a parceiros e os programadores que estão a implementar para administração pública do Microsoft Azure.



## <a name="Guidance"></a>Orientações para programadores
Uma vez que a maioria dos conteúdos técnicos que está disponível neste momento assume que aplicações estão a ser desenvolvidas para o serviço Global, em vez de para administração pública do Microsoft Azure, é importante para si assegurar que os programadores são em atenção as diferenças chaves para aplicações desenvolvidas à alojado no Azure Governo.

- Em primeiro lugar, existem serviços e diferenças entre funcionalidades das, isto significa que determinadas funcionalidades que estão no regiões específicas do serviço Global poderão não estar disponíveis no Azure Governo.

- Em segundo lugar, para funcionalidades que são oferecidas no Azure Governo, existem diferenças de configuração de serviço Global.  Por conseguinte, devem consultar o seu código de exemplo, configurações e os passos para se certificar de que estão criação e execução dentro do ambiente de serviços em nuvem Azure administração pública.


## <a name="Features"></a>Funcionalidades atualmente disponíveis no Microsoft Azure administração pública
Administração pública Azure atualmente tem os seguintes serviços disponíveis em IOWA administração pública-nos e administração pública-nos VIRGINIA regiões:

- Máquinas virtuais
- Serviços em nuvem
- Armazenamento
- O Active Directory
- Programador
- Funcionamento em rede virtual
- Base de dados SQL
- Ficheiros do Azure
- Serviços de multimédia
- Gestor de tráfego
- Serviço Bus
- StorSimple
- Redis Cache
- Cópia de segurança do Azure
- Automatização
- ExpressRoute
- etc.

Outros serviços estão disponíveis e serão adicionados mais serviços de forma contínua.  Para a lista mais recente dos serviços, consulte o artigo a [página de regiões](https://azure.microsoft.com/regions/#services) que irá realçar cada região disponíveis e dos seus serviços.  

Atualmente,-nos Iowa administração pública e -nos administração pública Virginia são os centros de dados Azure Governo de suporte.  Consulte a página de regiões acima para os centros de dados atual e os serviços disponíveis.

## <a name="Endpoint"></a>Mapeamento de ponto final

Utilize a tabela seguinte para orientá-lo ao mapear públicos Microsoft Azure e base de dados SQL os pontos finais para pontos finais do Azure Governo específicos.


Tipo de serviço|Azure público|Azure administração pública
---|---|---
Portal de gestão|manage.windowsazure.com|manage.windowsazure.us
Geral|*. windows.net|*. usgovcloudapi.net
Principais|*. core.windows.net|*. core.usgovcloudapi.net
Cluster|*. cloudapp.net|*. usgovcloudapp.net
Armazenamento de BLOBs|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Armazenamento de fila de espera|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Armazenamento de tabela|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Gestão de serviços|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Base de dados SQL|*. database.windows.net|*. database.usgovcloudapi.net
Ponto final de balanceamento de carga de processador|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Para a autenticação de processador através do Azure AD, referência [Autenticar pedidos de Gestor de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Próximos passos

Se estiver interessado em aprender mais e sobre Azure Governo fórum tirar partido algumas das ligações em baixo.

- **[Inscrever-se para uma versão de avaliação](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Adquirir e aceder a administração pública do Azure](http://azure.com/gov)**

- **[Descrição geral de administração pública Azure](/azure-government-overview)**

- **[Blogue do Azure administração pública](http://blogs.msdn.com/b/azuregov/)**

- **[Conformidade Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
