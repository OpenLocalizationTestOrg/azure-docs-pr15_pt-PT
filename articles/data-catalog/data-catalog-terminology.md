<properties
   pageTitle="Azure terminologia de catálogo de dados | Microsoft Azure"
   description="Este artigo fornece uma introdução à conceitos e termos utilizados na documentação do catálogo de dados do Azure."
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
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-terminology"></a>Azure terminologia de catálogo de dados

## <a name="catalog"></a>Catálogo

O catálogo de dados do Azure é um repositório de metadados baseado na nuvem na qual os dados podem ser registados origens e dados de activos. O catálogo serve como uma localização de armazenamento central para metadados estruturais extraídas de origens de dados e para metadados descritivo adicionadas por utilizadores.

## <a name="data-source"></a>Origem de dados

Uma origem de dados é um sistema ou o contentor que faz a gestão dos elementos de dados. Alguns exemplos incluem bases de dados do SQL Server, bases de dados Oracle, bases de dados do SQL Server Analysis Services (multidimensionais ou tabulares) e os servidores do SQL Server Reporting Services.

## <a name="data-asset"></a>Elementos de dados

Elementos de dados são objectos contidos origens de dados que podem ser registadas com o catálogo. Alguns exemplos incluem tabelas do SQL Server e vistas, Oracle tabelas e vistas, SQL Server Analysis Services medidas, dimensões e KPIs e relatórios do SQL Server Reporting Services.

## <a name="data-asset-location"></a>Localização de elementos de dados

O catálogo armazena a localização de uma origem de dados ou elementos de dados, que podem ser utilizados para ligar à origem de utilizar uma aplicação de cliente. O formato e detalhes da localização variam consoante o tipo de origem de dados. Por exemplo, uma tabela do SQL Server pode ser identificada pelo seu nome de quatro peça – nome do servidor, o nome da base de dados, o nome do esquema, o nome do objeto – enquanto um SQL Server relatório do Reporting Services podem ser identificado pelo seu URL.

## <a name="structural-metadata"></a>Metadados estruturais

Metadados estruturais são os metadados extraídos de uma origem de dados que descreva a estrutura de um ativo de dados. Isto inclui a localização de activos, nome do objeto e tipo e características específicas do tipo de adicionais. Por exemplo, os metadados estrutural para tabelas e vistas incluem os nomes e os tipos de dados para as colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivo

Metadados descritivo são metadados que descreve o objetivo ou a intenção de um ativo de dados. Normalmente descritivo metadados são adicionado por utilizadores catálogo utilizando o portal de catálogo de dados do Azure, mas -lo também pode ser extraída da origem de dados durante o registo. Por exemplo, a ferramenta de registo do catálogo de dados do Azure irá extrair descrições de propriedade descrição no SQL Server Analysis Services e o SQL Server Reporting Services e do [ms_description propriedade expandida](https://technet.microsoft.com/library/ms190243.aspx) nas bases de dados do SQL Server, se estas propriedades foram preenchidas com valores.

## <a name="request-access"></a>Pedir acesso

Metadados descritivo um elementos de dados podem incluir informações sobre como pedir acesso a elementos de dados ou origem de dados. Esta informação é apresentada com a localização de elementos de dados e pode incluir um ou mais das seguintes opções:

- O endereço de e-mail do utilizador ou equipa responsável por conceder acesso à origem de dados.
- O URL do processo documentado que os utilizadores têm de seguir para aceder à origem de dados.
- O URL de uma identidade e acesso ferramenta de gestão (tal como o Microsoft identidade Manager) que pode ser utilizada para aceder à origem de dados.
- Uma entrada de texto livre que descreve como os utilizadores podem aceder à origem de dados.

## <a name="preview"></a>Pré-visualização

Uma pré-visualização no catálogo de dados do Azure é um instantâneo de 20 até registos que podem ser extraídas da origem de dados durante o registo e armazenadas no catálogo com os metadados de elementos de dados. A pré-visualização pode ajudar os utilizadores que descobrir um activo dados compreendem melhor sua função e o objetivo. Por outras palavras, ver dados de exemplo pode ser mais útil que ver apenas os nomes de coluna e os tipos de dados.
Pré-visualizações só são suportadas para tabelas e vistas e tem de estar explicitamente selecionados pelo utilizador durante o registo.

## <a name="data-profile"></a>Perfil de dados

Um perfil de dados no catálogo de dados do Azure é um instantâneo de tabela e nível de coluna metadados sobre um elementos de dados registadas que podem ser extraídos da origem de dados durante o registo e armazenados no catálogo com os metadados de elementos de dados. O perfil de dados pode ajudar os utilizadores que descobrir um activo dados compreendem melhor sua função e o objetivo. Semelhante a pré-visualizações, os perfis de dados tem de estar explicitamente selecionados pelo utilizador durante o registo.

> [AZURE.NOTE] Extrair um perfil de dados pode ser uma operação de dispendiosa para tabelas grandes e vistas e podem aumentar significativamente o tempo necessário para registar uma origem de dados.

## <a name="user-perspective"></a>Perspetiva de utilizador

No catálogo de dados do Azure, qualquer utilizador pode fornecem metadados descritivo para um item de dados registadas. Cada utilizador tem uma perspetiva distinta sobre os dados e a utilização da mesma. Por exemplo, o administrador responsável por um servidor poderá fornecer os detalhes dos seus contrato de nível de serviço (SLA) ou uma cópia de segurança windows; pelos dados podem fornecer ligações a documentação para o negócio processa suporta dados; e um analista poderá fornecer uma descrição nos termos que são mais relevantes para as outras analistas e que pode ser mais importantes aos utilizadores que precisam de detetar e compreender os dados.

Cada um destes perspetivas está implicitamente útil e com o catálogo de dados do Azure cada utilizador pode fornecer as informações que faça sentido aos mesmos, apesar de todos os utilizadores podem utilizar essas informações para compreender os dados e a sua finalidade.

## <a name="expert"></a>Mais complexa

Um especialista é um utilizador que identificou como tendo uma perspetiva informada "especialista" para um item de dados. Qualquer utilizador pode adicionar si ou por outro utilizador como um especialista de um ativo. A ser listado como um especialista não transmitir quaisquer privilégios adicionais no catálogo de dados do Azure; permite aos utilizadores localizar facilmente esses perspetivas que estão mais predispostas ser úteis durante a revisão metadados descritivo de um ativo.

## <a name="owner"></a>Proprietário

Um proprietário é um utilizador com privilégios adicionais para gerir um elementos de dados no catálogo de dados do Azure. Os utilizadores podem obter a propriedade de elementos de dados registadas e os proprietários podem adicionar outros utilizadores como os proprietários de cocriação. Para obter mais informações, consulte [como gerir elementos de dados](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Gestão e de propriedade estão disponíveis apenas no Standard Edition do Azure catálogo de dados.

## <a name="registration"></a>Registo

O registo é a legislação sobre de extrair metadados de elementos de dados a partir de uma origem de dados e copiá-lo para o serviço de catálogo de dados do Azure. Elementos de dados que foram registados, em seguida, podem ser anotados e descobertos.

## <a name="see-also"></a>Consulte também

- [O que é o catálogo de dados do Azure?](data-catalog-what-is-data-catalog.md) -Este artigo fornece uma descrição geral do serviço do catálogo de dados do Azure, o valor-fornece e os cenários suporta.

- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) - este artigo fornece um tutorial de ponto a ponto mostra-lhe como utilizar o catálogo de dados do Azure para deteção de origem de dados.  
