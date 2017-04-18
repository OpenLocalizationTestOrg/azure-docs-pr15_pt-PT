<properties
    pageTitle="Restringir o acesso ao conteúdo Azure CDN por país | Microsoft Azure"
    description="Saiba como restringir o acesso ao conteúdo Azure CDN utilizando a funcionalidade de filtragem Geo."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="casoper"/>

#<a name="restrict-access-to-your-content-by-country---verizon"></a>Restringir o acesso ao conteúdo por país - Verizon

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai padrão](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Descrição geral

Quando um utilizador pede o seu conteúdo, por predefinição, o conteúdo é servido, independentemente de onde o utilizador feitas este pedido a partir do. Em alguns casos, poderá querer restringir o acesso ao conteúdo por país. Este tópico explica como utilizar a funcionalidade de **Filtragem Geo** para configurar o serviço para permitir ou bloquear o acesso por país.

> [AZURE.IMPORTANT] Os produtos Verizon e Akamai fornecem a mesma funcionalidade de filtragem geo, mas a interface de utilizador é diferente. Este documento descreve a interface para **Azure CDN padrão de Verizon** e **Azure CDN Premium a partir do Verizon**. Para uma filtragem geo com o **Azure CDN padrão de Akamai**, consulte o artigo [restringir o acesso ao conteúdo por país - Akamai](cdn-restrict-access-by-country-akamai.md).

Para obter informações sobre a que se aplicam a configurar este tipo de restrição, consulte a secção [Considerações](cdn-restrict-access-by-country.md#considerations) no final do tópico.  

>[AZURE.NOTE] Assim que a configuração está configurada, será aplicado a todos os pontos finais **CDN Azure a partir do Verizon** este perfil Azure CDN.

![Filtragem de país/região](./media/cdn-filtering/cdn-country-filtering.png)

##<a name="step-1-define-the-directory-path"></a>Passo 1: Definir o caminho do diretório

Quando configurar um filtro de país/região, tem de especificar o caminho relativo para a localização à qual os utilizadores vão ser permitidos ou o acesso negados. Pode aplicar filtragem de país/região para todos os seus ficheiros com "/" ou selecionada pastas ao especificar caminhos de diretório.

Filtro de caminho do directório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg

##<a name="step-2-define-the-action-block-or-allow"></a>Passo 2: Definir a ação: bloquear ou permitir

**Bloco:** Os utilizadores de países/regiões especificados não terão acesso aos activos solicitados a partir desse caminho recursiva. Se não existem outras opções de filtragem de país/região tiverem sido configuradas para essa localização, em seguida, todos os outros utilizadores são permitidos acesso.

**Permitir:** Apenas os utilizadores de países/regiões especificados são permitidos acesso aos elementos solicitada a partir desse caminho recursiva.

##<a name="step-3-define-the-countries"></a>Passo 3: Definir os países/regiões

Selecione os países/regiões que pretende bloquear ou permitir para o caminho. Para mais informações, consulte o artigo [Azure CDN de códigos de país/região específicos Verizon](https://msdn.microsoft.com/library/mt761717.aspx).

Por exemplo, a regra de bloqueio /Photos/Estrasburgo/irá filtrar ficheiros, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Códigos de país/região

A funcionalidade de **Filtragem Geo** utiliza os códigos de país para definir os países a partir da qual um pedido de vai ser permitido ou bloqueado para um diretório seguro. Irá encontrar os códigos de país no [Azure CDN de códigos de país/região específicos Verizon](https://msdn.microsoft.com/library/mt761717.aspx). Se especificar "EU" (Europa) ou "Turma" (países da Ásia/Pacífico), um subconjunto dos endereços IP que têm origem a partir de qualquer país/região em que regiões vai ser bloqueado ou permitido.


##<a id="considerations"></a>Considerações sobre

- Poderá demorar até 90 minutos para as alterações à configuração filtragem do país, entre em vigor.
- Esta funcionalidade não suporta a carateres universais (por exemplo, ' *').
- O país/região filtragem configuração associada com o caminho relativo será aplicado modo recursivo para esse caminho.
- Pode ser aplicada apenas uma regra para o mesmo caminho relativo (não pode criar vários filtros de país/região que apontem para o mesmo caminho relativo. No entanto, uma pasta pode ter vários filtros de país/região. Este é devido a natureza recursiva dos filtros de país/região. Por outras palavras, pode ser atribuída uma subpasta de uma pasta previamente configurada um filtro de país/região diferente.
