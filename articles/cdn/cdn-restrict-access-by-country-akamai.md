<properties
    pageTitle="Restringir o acesso ao conteúdo Azure CDN por país | Microsoft Azure"
    description="Saiba como restringir o acesso ao conteúdo Azure CDN utilizando a funcionalidade de filtragem Geo."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>Restringir o acesso ao conteúdo por país - Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai padrão](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Descrição geral

Quando um utilizador pede o seu conteúdo, por predefinição, o conteúdo é servido, independentemente de onde o utilizador feitas este pedido a partir do. Em alguns casos, poderá querer restringir o acesso ao conteúdo por país. Este tópico explica como utilizar a funcionalidade de **Filtragem Geo** para configurar o serviço para permitir ou bloquear o acesso por país.

> [AZURE.IMPORTANT] Os produtos Verizon e Akamai fornecem a mesma funcionalidade de filtragem geo, mas a interface de utilizador é diferente. Este documento descreve a interface do **Azure CDN padrão de Akamai**para. Para uma filtragem geo com o **Azure CDN padrão/Premium a partir do Verizon**, consulte o artigo [restringir o acesso ao conteúdo por país - Verizon](cdn-restrict-access-by-country.md).

Para obter informações sobre a que se aplicam a configurar este tipo de restrição, consulte a secção [Considerações](cdn-restrict-access-by-country.md#considerations) no final do tópico.  

![Filtragem de país/região](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>Passo 1: Definir o caminho do diretório

Selecione o ponto final no interior do portal e localizar o separador filtragem Geo na navegação à esquerda para encontrar esta funcionalidade.

Quando configurar um filtro de país/região, tem de especificar o caminho relativo para a localização à qual os utilizadores vão ser permitidos ou o acesso negados. Pode aplicar filtragem geo para todos os seus ficheiros com "/" ou selecionada pastas ao especificar caminhos de directório "/ imagens /". Também pode aplicar filtragem geo para um único ficheiro ao especificar o ficheiro e ao deixar o saída a barra invertida final "/ pictures/city.png".

Filtro de caminho do directório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>Passo 2: Definir a ação: bloquear ou permitir

**Bloco:** Os utilizadores de países/regiões especificados não terão acesso aos activos solicitados a partir desse caminho recursiva. Se não existem outras opções de filtragem de país/região tiverem sido configuradas para essa localização, em seguida, todos os outros utilizadores são permitidos acesso.

**Permitir:** Apenas os utilizadores de países/regiões especificados são permitidos acesso aos elementos solicitada a partir desse caminho recursiva.

##<a name="step-3-define-the-countries"></a>Passo 3: Definir os países/regiões

Selecione os países/regiões que pretende bloquear ou permitir para o caminho. Para mais informações, consulte o artigo [Azure CDN de códigos de país/região específicos Akamai](https://msdn.microsoft.com/library/mt761717.aspx).

Por exemplo, a regra de bloqueio /Photos/Estrasburgo/irá filtrar ficheiros, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Códigos de país/região

A funcionalidade de **Filtragem Geo** utiliza os códigos de país para definir os países a partir da qual um pedido de vai ser permitido ou bloqueado para um diretório seguro. Irá encontrar os códigos de país no [Azure CDN de códigos de país/região específicos Akamai](https://msdn.microsoft.com/library/mt761717.aspx). 

##<a id="considerations"></a>Considerações sobre

- Poderá demorar até a alguns minutos para as alterações ao seu país/região filtragem configuração entre em vigor.
- Esta funcionalidade não suporta a carateres universais (por exemplo, ' *').
- A configuração de filtragem geo associada com o caminho relativo será aplicado modo recursivo para esse caminho.
- Pode ser aplicada apenas uma regra para o mesmo caminho relativo (não pode criar vários filtros de país/região que apontem para o mesmo caminho relativo. No entanto, uma pasta pode ter vários filtros de país/região. Este é devido a natureza recursiva dos filtros de país/região. Por outras palavras, pode ser atribuída uma subpasta de uma pasta previamente configurada um filtro de país/região diferente.

