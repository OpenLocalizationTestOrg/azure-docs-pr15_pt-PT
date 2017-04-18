<properties
   pageTitle="Como registar origens de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce como registar origens de dados com o catálogo de dados do Azure, incluindo os campos de metadados extraídos durante o registo."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-register-data-sources"></a>Como registar origens de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o **Catálogo de dados do Azure** é tudo sobre ajudando às pessoas descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir dos seus dados existentes. E o primeiro passo para efetuar uma origem de dados detetável através do **Catálogo de dados do Azure** consiste em Registe-se de que origem de dados.
## <a name="registering-data-sources"></a>Registar origens de dados
O registo é o processo de extrair metadados da origem de dados e copiar os dados para o serviço de **Catálogo de dados do Azure** . Os dados permanecem onde-atualmente reside e permanece sob o controlo dos administradores e políticas do sistema atual.

Para registar uma origem de dados, simplesmente inicie a ferramenta de registo de origem do **Catálogo de dados do Azure** dados a partir do portal de **Catálogo de dados do Azure** . Registo com o seu trabalho ou conta escolar (as mesmas credenciais de Azure Active Directory que utilizar para iniciar sessão no portal do) e, em seguida, selecione a origem de dados que pretende para registar.
Consulte o tutorial de [Introdução ao Azure catálogo de dados](data-catalog-get-started.md) para obter mais detalhes passo a passo.

Assim que tiver sido registada a origem de dados, o catálogo controla a localização e índices respectivos metadados, para que os utilizadores possam pesquisar, procurar e descobrir a origem de dados e, em seguida, utilizar a localização para ligar à mesma, utilizando a aplicação ou a ferramenta da sua escolha.

## <a name="sources-supported"></a>Origens de suportadas
Consulte [DSR de catálogo de dados](data-catalog-dsr.md) para a lista de origens de dados atualmente suportados.
<br/>


## <a name="structural-metadata"></a>Metadados estruturais
Quando está a registar uma origem de dados, a ferramenta de registo irá extrair informações sobre a estrutura de objetos que selecione – este é referido como estruturais metadados.

Para todos os objetos, este metadados estruturais irão incluir a localização do objecto, para que os utilizadores que descobrir os dados podem utilizar essas informações para ligar para o objeto nas ferramentas de cliente da sua escolha. Outros metadados estruturais incluem o nome do objeto e o tipo e o nome da coluna/atributo e tipo de dados.

## <a name="descriptive-metadata"></a>Metadados descritivo
Para além dos principais estruturais metadados extraídos da origem de dados, a ferramenta de registo da origem de dados também irá extrair também metadados descritivo. SQL Server Analysis Services e o SQL Server Reporting Services, é disponibilizado descrição das propriedades ao exposto por estes serviços. Para o SQL Server, vai ser extraídos valores, fornecidos utilizando ms_description propriedade expandida. Para a base de dados Oracle, a ferramenta de registo da origem de dados irá extrair a coluna de comentários a partir da vista ALL_TAB_COMMENTS.

Para além dos metadados descritivo extraídos da origem de dados, os utilizadores também podem introduzir metadados descritivo utilizando a ferramenta de registo da origem de dados. Os utilizadores podem adicionar etiquetas e podem identificar especialistas para os objetos que está a ser registados. Todos os metadados este descritivo é copiado para o serviço de **Catálogo de dados do Azure** juntamente com os metadados estruturais.

## <a name="including-previews"></a>Incluindo pré-visualizações

Por predefinição, apenas os metadados são extraídos de origens de dados e copiado para o serviço de **Catálogo de dados do Azure** , mas Noções sobre que uma origem de dados com frequência é tornada mais fácil ao ver um exemplo dos dados nela contidos.

A ferramenta de registo de origem de dados do **Catálogo de dados do Azure** permite aos utilizadores incluir uma pré-visualização de instantâneo dos dados em cada tabela e a vista que está registada. Se o utilizador opte-in para incluir pré-visualizações durante o registo, a ferramenta de registo irá incluir até 20 registos de cada tabela e a vista. Este instantâneo, em seguida, é copiado para o catálogo juntamente com os metadados estrutural e descritivo.


> [AZURE.NOTE]  Largura tabelas com um grande número de colunas, poderão ter menos de 20 registos incluídos na sua pré-visualização.


## <a name="including-data-profiles"></a>Incluindo os perfis de dados

Tal como pré-visualizações incluindo podem fornecer contexto útil para utilizadores procurar origens de dados no **Catálogo de dados do Azure**, incluindo um perfil de dados pode também tornam mais fácil de compreender as origens de dados descoberta.

A ferramenta de registo de origem de dados do **Catálogo de dados do Azure** permite aos utilizadores incluir um perfil de dados para cada tabela e a vista que está registada. Se o utilizador escolhe incluir um perfil de dados durante o registo, a ferramenta de registo irá incluir agregação estatísticas sobre os dados em cada tabela e a vista, incluindo:

* O número de linhas e o tamanho dos dados no objeto
* A data para a atualização mais recente dos dados e o esquema de objeto
* O número de registos nulos e valores distintos para colunas
* Os valores mínimo, máximo, média e o desvio-padrão para colunas

Estas estatísticas, em seguida, são copiadas para o catálogo juntamente com os metadados estruturais e descritivo.

> [AZURE.NOTE]  Colunas de texto e data esta não incluirá estatísticas média ou o desvio-padrão no respetivo perfil de dados.

## <a name="updating-registrations"></a>Atualizar os registos

Registar uma origem de dados-tornará detetável no **Catálogo de dados do Azure** utilizando os metadados e pré-visualização do opcional extraídas durante o registo. Se a origem de dados tem de ser atualizados no catálogo de (por exemplo, se tiver alterado o esquema de um objeto, ou tabelas originalmente excluídas devem ser incluídas ou um utilizador que pretende atualizar os dados incluídos na pré-visualizações) a ferramenta de registo da origem de dados pode executar novamente.

Voltar a registar uma origem de dados já registado executa uma operação de impressão em série "upsert": objetos existentes serão atualizados, enquanto novos objetos de serão criados. Quaisquer metadados fornecidos pelos utilizadores através do portal do **Catálogo de dados do Azure** serão mantidos.

## <a name="summary"></a>Resumo
Registar uma origem de dados com o **Catálogo de dados do Azure** facilita que origem de dados detetar e compreender, copiando estrutural e descritivo metadados da origem de dados para o serviço de catálogo. Assim que tiver sido registada uma origem de dados,-pode, em seguida, ser anotado, gerida e descoberta utilizando o portal de **Catálogo de dados do Azure** .

## <a name="see-also"></a>Consulte também
- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como registar origens de dados.
