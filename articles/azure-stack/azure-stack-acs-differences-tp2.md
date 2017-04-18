
<properties
    pageTitle="Armazenamento Azure consistentes: as diferenças e considerações | Microsoft Azure"
    description="Compreenda as diferenças de armazenamento do Windows Azure e outras considerações de implementação de armazenamento Azure consistentes."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Armazenamento Azure consistentes: as diferenças e considerações

Armazenamento Azure consistentes é o conjunto de serviços em nuvem armazenamento no Microsoft Azure pilha. Fornece Azure consistentes armazenamento blob, tabela, fila de espera e funcionalidade de gestão de conta com semântica Azure consistentes. Este artigo resume as diferenças de armazenamento Azure consistentes conhecidas com o armazenamento do Windows Azure. Resuma também outras considerações a ter em conta quando implementar a versão de pré-visualizar atualmente publicamente disponível do Microsoft Azure pilha.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Diferenças conhecidas

Esta versão de pré-visualização técnica do armazenamento Azure consistentes não tiver paridade de funcionalidade de 100% com armazenamento do Windows Azure para as versões de API que são suportadas. Conhecidos diferenças entre funcionalidades das incluem o seguinte:

-   Determinados tipos de conta de armazenamento ainda não estão disponíveis, por exemplo, padrão\_RAGRS e padrão\_GRS.

-   Premium\_contas de armazenamento LRS podem ser aprovisionadas. No entanto, não existem atualmente limites de desempenho ou garantias.

-   Funcionalidade do Azure ficheiros ainda não está disponível.

-   Obter página intervalos API não suporta a obtenção de páginas que diferem entre instantâneos dos blobs de página.

-   Obter página intervalos API Devolve páginas que tenham 4 KB de granularidade.

-   Chave partição e linha na implementação do Azure consistentes tabela de armazenamento estão limitados a 400 caracteres (ou seja, 800 bytes) no tamanho cada.

-   Nomes de BLOBs na aplicação de serviço armazenamento Blob Azure consistentes estão limitados a 880 carateres (ou seja, 1760 bytes) de tamanho.

-   A implementação de armazenamento Azure consistentes de dados de utilização de armazenamento de inquilino relatórios fornece metros de utilização de armazenamento são idênticos no Azure, com a mesma semântica e unidades. Atualmente, no entanto, o indicador de utilização do armazenamento transações não inclui as transações de IaaS e o indicador de utilização de transferência de dados não diferencia a utilização da largura de banda pelo tráfego de rede interna ou externa para uma região Azure pilha.

-   Existem algumas diferenças no âmbito da funcionalidade para gestão de armazenamento. Por exemplo, não pode alterar o tipo de conta ou ter domínios personalizados. Além disso, apenas consistência ao nível do API é disponibilizada para o prémio\_LRS tipo de conta de armazenamento.

## <a name="deployment-considerations"></a>Considerações de implementação

-   **Teste.** Não implemente armazenamento Azure consistentes em ambientes de produção utilizar a versão atual do Microsoft Azure pilha pré-visualização técnica. Esta versão se destinar apenas para fins de avaliação no ambiente de teste.

-   **Desempenho**. A versão do Microsoft Azure pilha pré-visualização técnica do armazenamento Azure consistentes não está totalmente optimizado de desempenho.

-   **Escalabilidade.** A versão do Microsoft Azure pilha pré-visualização técnica do armazenamento Azure consistentes não está optimizada para escalabilidade.

## <a name="version-support-considerations"></a>Considerações de suporte de versão

As seguintes versões são suportadas com esta versão de pré-visualização de armazenamento Azure consistentes:

> Biblioteca de cliente do armazenamento Azure: [Microsoft Azure armazenamento 6. x .NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Azure serviços de armazenamento de dados: [2015-04-05 REST API versão](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Serviços de gestão de armazenamento Azure: [2015-05-01-pré-visualização do](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Próximos passos

-   [Introdução ao Azure consistentes armazenamento](azure-stack-storage-overview.md)
