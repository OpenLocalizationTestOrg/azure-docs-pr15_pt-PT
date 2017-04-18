<properties
   pageTitle="O que é incorporado do Microsoft Power BI?"
   description="Power BI incorporado permite-lhe integrar relatórios do Power BI para as suas web ou aplicações móveis, pelo que não necessita para criar soluções personalizadas para visualizar os dados para os seus utilizadores"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="what-is-microsoft-power-bi-embedded"></a>O que é incorporado do Microsoft Power BI?

Com o **Power BI incorporado**, pode integrar o relatórios do Power BI para a direita para as suas web ou aplicações móveis.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI incorporado é um **serviço Azure** que permite fabricantes independentes de software e os programadores de aplicação experiências de dados do Power BI superfície dentro das suas aplicações. Como um programador já tiver criado aplicações e nessas aplicações tem os seus próprios utilizadores e distinto conjunto de funcionalidades. Também poderão ocorrer nessas aplicações ter alguns elementos de dados incorporados, como gráficos e relatórios que podem agora ser tecnologia do Microsoft Power BI incorporado. Os utilizadores não precisa de uma conta do Power BI para utilizar a aplicação. Estes podem continuar a iniciar sessão na sua aplicação tal como faria com antes e ver e interagir com o Power BI a experiência de elaboração de relatórios sem necessidade de licenciamento adicionais.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licenciamento para o Microsoft Power BI incorporado

No modelo de utilização **Do Microsoft Power BI incorporado** , de licenciamento para o Power BI não está a responsabilidade do utilizador final.  Em vez disso, **compõe** sejam comprados pelo programador da aplicação que está a consumir o piscam e são cobrados para a subscrição que é o proprietário esses recursos.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI incorporado modelo Conceptual

![](media\powerbi-embedded-whats-is\model.png)

Como qualquer outro serviço no Azure, através de do [Azure processador APIs](https://msdn.microsoft.com/library/mt712306.aspx)configurações recursos para o Power BI incorporado. Neste caso, o recurso que aprovisionar o é uma **Coleção de área de trabalho do Power BI**.

## <a name="workspace-collection"></a>Coleções de sites de área de trabalho

Uma **Coleção de área de trabalho** é o Azure contentor de nível superior para recursos que contém 0 ou mais **áreas de trabalho**.  Uma **área de trabalho** **recolha** tem todas as propriedades do Azure padrão, bem como o seguinte procedimento:

-   **Teclas de acesso** – chaves utilizadas quando segura chamar APIs de Power BI para simplificar (descrito na secção posterior).
-   **Os utilizadores** – utilizadores do Azure Active Directory (AAD) que tiver direitos de administrador para gerir a coleção de área de trabalho do Power BI através do portal do Azure ou processador API.
-   **Região** – como parte do aprovisionamento de uma **Coleção de área de trabalho**, pode selecionar uma região para ser aprovisionada. Para mais informações, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabalho

Uma **área de trabalho** é um contentor de conteúdo do Power BI, que pode incluir conjuntos de dados, relatórios e dashboards. Uma **área de trabalho** estiver em branco quando criada pela primeira vez. Durante a pré-visualizar, irá autor todo o conteúdo utilizando o Power BI Desktop e o utilizador irá carregá-lo para uma das suas áreas de trabalho utilizando o [Power BI REST APIs](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Utilizar a área de trabalho coleções de sites e áreas de trabalho
**Área de trabalho coleções de sites** e **áreas de trabalho** são contentores de conteúdo que são utilizados e organizados em independentemente forma melhor se adequa a estrutura da aplicação que está a criar. Será muitas maneiras diferentes que poderia dispor conteúdo nelas. Pode optar por colocar todo o conteúdo dentro de uma área de trabalho e, em seguida, utilizar mais tarde tokens de aplicação para subdividir ainda mais o conteúdo se os seus clientes. Também poderá optar por colocar os seus clientes nas áreas de trabalho em separado para que existe algum separação entre elas. Em alternativa, pode optar por organizar os utilizadores por região em vez de cliente. Este flexível estrutura permite-lhe escolher a melhor forma de organizar o conteúdo.

## <a name="cached-datasets"></a>Conjuntos de dados em cache

Conjuntos de dados em cache podem ser utilizados na pré-visualização.  No entanto, não consigo atualizar dados em cache, assim que tiver sido carregado para o **Microsoft Power BI incorporado**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticação e autorização com tokens de aplicação

**Microsoft Power BI incorporado** difere para a sua aplicação para efetuar a autenticação de utilizador necessárias e autorização. Não existe nenhuma requisito explícito que os seus utilizadores finais ser clientes do Azure Active Directory (Azure AD).  Em vez disso, a sua aplicação expresse ao **Microsoft Power BI incorporado** autorização para compor um relatório do Power BI ao utilizar a **Aplicação de Tokens de autenticação (aplicação Tokens)**.  Estes **Tokens de aplicação** são criados conforme necessário, quando pretende que a aplicação para compor um relatório.  Consulte o artigo [Tokens de aplicação](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Tokens de autenticação de aplicação (aplicação Tokens)** são utilizados para autenticar contra a **Microsoft Power BI incorporado**.  Existem três tipos de **Tokens de aplicação**:

1.  Tokens de aprovisionamento - utilizados quando Aprovisiona o uma nova **área de trabalho** para uma **Coleção de área de trabalho**
2.  Tokens de desenvolvimento - utilizados quando efetuar chamadas diretamente para o **Power BI REST APIs**
3.  Incorporação Tokens - utilizados quando efetuar chamadas para compor um relatório na iframe incorporado

Estes tokens são utilizadas para as várias fases nas suas interações com o **Microsoft Power BI incorporado**.  Os tokens foram concebidos para que pode delegar permissões da sua aplicação do Power BI. Para mais informações, consulte o artigo [Fluxo Token de aplicação](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Consulte também
- [Cenários comuns de incorporado do Microsoft Power BI](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI incorporado](power-bi-embedded-get-started.md)
