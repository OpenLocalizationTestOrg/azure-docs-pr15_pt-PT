<properties
   pageTitle="Como ligar a origens de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce como ligar a origens de dados descobertas com o catálogo de dados do Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>


# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o **Catálogo de dados do Azure** é tudo sobre ajudando às pessoas descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir dos seus dados existentes. Um aspecto chave deste cenário está a utilizar os dados – quando um utilizador detetar uma origem de dados e compreende pode utilizar a sua finalidade, o passo seguinte é ligar à origem de dados para colocar os seus dados para utilizar.

## <a name="data-source-locations"></a>Localizações de origem de dados
Durante o registo de origem de dados, o **Catálogo de dados do Azure** recebe metadados sobre a origem de dados. Este metadados incluem os detalhes da localização de origem de dados. Os detalhes da localização variam de origem de dados a origem de dados, mas conterá sempre as informações necessárias para ligar. Por exemplo, a localização para uma tabela do SQL Server inclui o nome do servidor, base de dados nome, nome do esquema e nome da tabela, enquanto que a localização de um relatório do SQL Server Reporting Services inclui o nome do servidor e o caminho para o relatório. Outros tipos de origens de dados terá localizações que reflitam a estrutura e as funcionalidades do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas de cliente integrada
A forma mais simples para ligar a uma origem de dados é utilizar a "aberto no...." menu no portal do **Catálogo de dados do Azure** . Este menu apresenta uma lista de opções para ligar ao activo dados selecionados.
Ao utilizar a vista de mosaico predefinida, este menu está disponível no cada mosaico.

 ![Abrir uma tabela do SQL Server no Excel a partir do mosaico elementos de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Ao utilizar a vista de lista, no menu está disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abrir um relatório do SQL Server Reporting Services no Gestor de relatórios a partir da barra de pesquisa](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicações de cliente suportadas
Ao utilizar o "aberto no...." menu para origens de dados no portal do catálogo de dados do Azure, a aplicação de cliente correto tem de estar instalado no computador cliente.

| Abrir na aplicação | Extensão de ficheiro / protocolo | Versões de aplicação suportada |
| --- | --- | --- |
| Excel | . odc | Excel 2010 ou posterior |
| Excel (parte superior 1000) | . odc | Excel 2010 ou posterior |
| Power Query | . xlsx | Excel 2016 ou Excel 2010 ou o Excel 2013 com o Power Query para Excel suplemento instalado
| Ambiente de trabalho do Power BI | .pbix | Power BI Desktop Julho 2016 ou posterior |
| Ferramentas de dados do SQL Server | vsweb: / / | Visual Studio 2013 atualização 4 ou posterior com ferramentas do SQL Server instalada |
| Gestor de relatórios | http:// | Consulte o artigo [requisitos do browser do SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Os dados, o seu ferramentas
As opções disponíveis no menu de lista irão variam consoante o tipo de dados activo atualmente seleccionado. Obviamente, não todas as ferramentas de possíveis são incluídas na "aberto no...." menu, mas é ainda fácil ligar à origem de dados utilizando qualquer ferramenta de cliente. Quando um elementos de dados está seleccionado no portal do **Catálogo de dados do Azure** , a localização completa é apresentada no painel de propriedades.

 ![Informações de ligação para uma tabela do SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes de informações de ligação serão diferentes a partir do tipo de origem de dados ao tipo de origem de dados, mas as informações incluídas no portal do irão dar-lhe tudo o que precisa para se ligar à origem de dados em qualquer ferramenta de cliente. Os utilizadores podem copiar os detalhes de ligação para as origens de dados que descobriu de utilização de **Catálogo de dados do Azure**, permitindo-lhes trabalhar com os dados nas sua ferramenta à escolha.

## <a name="connecting-and-data-source-permissions"></a>Permissões de origem para ligar e dados
Apesar de **Catálogo de dados do Azure** faz com que as origens de dados detetável, acesso aos dados propriamente dito permanece sob o controlo do proprietário da origem de dados ou o administrador. Descobrir a origem de dados no **Catálogo de dados do Azure** não dar um utilizador quaisquer permissões para aceder à origem de dados própria.

Para facilitar para utilizadores que descobrir uma origem de dados, mas não tem permissão para aceder aos seus dados, os utilizadores podem fornecer informações na propriedade pedir acesso quando anotar uma origem de dados. Informação fornecida aqui – incluindo ligações para o ponto de contacto para ganhar acesso à origem de dados ou processo – é apresentada juntamente com as informações de localização de origem de dados no portal.

 ![Informações de ligação com instruções de acesso do pedido fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##<a name="summary"></a>Resumo
Registar uma origem de dados com o **Catálogo de dados do Azure** assegura que os dados detetável copiando estrutural e descritivo metadados da origem de dados para o serviço de catálogo. Assim que uma origem de dados foi registada e descoberta, os utilizadores podem ligar à origem de dados a partir do portal de **Catálogo de dados do Azure** "aberto no...." " menu ou utilizar as ferramentas de dados à escolha.

## <a name="see-also"></a>Consulte também
- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como ligar a origens de dados.
