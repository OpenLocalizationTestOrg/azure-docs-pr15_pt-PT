<properties
   pageTitle="Introdução ao SQL da base de dados dinâmicos dados Masking (Azure Portal)"
   description="Como começar com SQL da base de dados dinâmicos dados Masking no Portal do Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Introdução ao SQL da base de dados dinâmicos dados Masking (Azure Portal)

> [AZURE.SELECTOR]
- [Dados dinâmicos máscaras - Azure Portal clássico](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Descrição geral

SQL da base de dados dinâmicos dados Masking limita exposição de dados confidenciais por masking-lo para utilizadores que não sejam privilégios. Máscaras dinâmico de dados são suportada para a versão de V12 da base de dados do SQL Azure.

Máscaras dinâmico de dados ajuda a evitar acesso não autorizado aos dados sensíveis ao ativar clientes designar quantidade dos dados confidenciais para revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que oculte os dados sensíveis a maiúsculas e o conjunto de resultados de uma consulta através de campos da base de dados designada, enquanto os dados na base de dados não forem alterados.

Por exemplo, um representante de suporte no Centro de atendimento telefónico poderá identificar os autores das chamadas por várias dígitos do seu número de segurança social ou número de cartão de crédito, mas esses itens de dados não devem ser totalmente expostas ao representante do serviço. Pode ser definida uma regra de máscaras máscaras de todos os mas os últimos quatro dígitos de qualquer número de segurança social ou número de cartão de crédito no resultado definir de qualquer consulta. Como outro exemplo, pode ser definida uma máscara de dados adequado para proteger dados informação identificativa (PII), para que um programador pode consultar ambientes de produção para fins de resolução de problemas sem violar regulamentos de conformidade.

## <a name="sql-database-dynamic-data-masking-basics"></a>Noções básicas de SQL da base de dados dinâmicos dados Masking

Configurar um dados dinâmicos masking política no Portal do Azure ao selecionar a operação Masking dinâmico de dados no seu pá de configuração da base de dados SQL ou pá definições.


### <a name="dynamic-data-masking-permissions"></a>Permissões de máscaras de dados dinâmicos

Máscaras dinâmico de dados podem ser configuradas pelo administrador da base de dados Azure, administrador do servidor ou funções de uniformizado de segurança.

### <a name="dynamic-data-masking-policy"></a>Política de máscaras de dados dinâmicos

* Os **utilizadores SQL excluídos da máscaras** - um conjunto de utilizadores SQL ou identidades AAD que irão obter dados sem máscara sinalizou nos resultados da consulta SQL. Note que os utilizadores com privilégios de administrador vai ser sempre excluídos da máscaras e irão ver os dados originais sem qualquer máscaras de introdução.

* **Masking regras** - um conjunto de regras que definir os campos designados a ser com máscara e a função de máscaras que será utilizada. Campos designados podem ser definidos utilizando um nome de esquema da base de dados, o nome da tabela e o nome da coluna.

* **Funções de masking** - um conjunto de métodos que controlam exposição de dados para cenários diferentes.

| Função máscaras | Masking lógica |
|----------|---------------|
| **Predefinido**  |**Máscaras completa de acordo com os tipos de dados dos campos designados**<br/><br/>• Utilize XXXX ou menos x se o tamanho do campo for inferior a 4 carateres para tipos de dados de cadeia (nchar, ntext, nvarchar).<br/>• Utilize um valor de zero para tipos de dados numéricos (bigint, bit, decimal, int, dinheiro, numérico, smallint, smallmoney, tinyint, flutuar, real).<br/>• Utilize 01-01-1900 para tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, tempo).<br/>• Para variante SQL, o valor predefinido do tipo de atual é utilizado.<br/>• Para o documento XML <masked/> é utilizado.<br/>• Utilize um valor vazio para tipos de dados especiais (tabela de data/hora, hierarchyid, GUID, binário, imagem, tipos de espacial varbinary).
| **Cartão de crédito** |**Masking método que expõe os últimos quatro dígitos dos campos designados** e adiciona uma constante cadeia como um prefixo a forma de um cartão de crédito.<br/><br/>XXXX-XXXX XXXX 1234|
| **Número de segurança social** |**Masking método que expõe os últimos quatro dígitos dos campos designados** e adiciona uma constante cadeia como um prefixo a forma de um número de segurança social americana.<br/><br/>XXX-XX-1234 |
| **Mensagem de correio electrónico** | **Masking método que expõe a primeira letra e substitui o domínio com XXX.com** utilizando um prefixo constante de cadeia em forma de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** | **Masking método que gera um número aleatório** de acordo com os limites selecionados e tipos de dados real. Se os limites designados são iguais, em seguida, a função máscaras será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** | **Masking método que expõe o primeiro e último caracteres** e adiciona uma cadeia de preenchimento personalizada no meio. Se a cadeia original for menor do que a exposta prefixo e sufixo, será utilizada apenas a cadeia de preenchimento. <br/>prefixo [preenchimento] sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O motor de recomendações DDM assinala determinados campos da base de dados como campos potencialmente confidenciais, que podem ser candidatos ideais para máscaras. No pá Masking dinâmico de dados no portal do, irá ver as colunas recomendadas para a base de dados. Tudo o que precisa de fazer é clique em **Adicionar máscaras de introdução** para uma ou mais colunas e, em seguida, **Guardar** para aplicar uma máscara de para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar o máscaras dinâmico de dados para a base de dados utilizando o Portal do Azure

1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

2. Navegue para a pá definições da base de dados que inclui os dados confidenciais que pretende máscara.

3. Clique no mosaico **Dinâmico de dados Masking** que inicia a pá de configuração **Masking dinâmico de dados** .

    * Em alternativa, pode deslocar-se para baixo para a secção **operações** e clique em **Masking dinâmico de dados**.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. Poderá ver algumas colunas de base de dados que o motor de recomendações tem sinalizados para máscaras no pá de configuração **Masking dinâmico de dados** . Para aceitar as recomendações, basta clicar em **Adicionar máscaras de introdução** para uma ou mais colunas e uma máscara será criada com base no tipo de predefinido para esta coluna. Pode alterar a função máscaras clicando na regra máscaras e edição de máscaras formato do campo para um formato diferente da sua escolha. Certifique-se de que clique em **Guardar** para guardar as definições.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Para adicionar uma máscara de qualquer coluna na base de dados, no topo da pá de configuração **Masking dinâmico de dados** clique em **Adicionar máscaras de introdução** para abrir o pá de configuração de **Adicionar regra de Masking**

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Selecione o **esquema**, **tabelas** e **colunas** para definir o campo designado que irá com máscara.

7. Escolha um **Formato do campo Masking** a partir da lista de dados sensíveis masking categorias.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Clique em **Guardar** os dados masking pá regra para atualizar o conjunto de regras no dados dinâmicos masking política de masking.

9. Escreva os utilizadores SQL ou identidades AAD que devem ser excluídas da máscaras e têm acesso aos dados sensíveis a maiúsculas e sem máscara sinalizou. Isto deve ser uma lista separados por ponto e vírgula de utilizadores. Tenha em atenção que os utilizadores com privilégios de administrador têm sempre acesso aos dados sem máscara sinalizou originais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] Para fazer com que a camada de aplicação, pode apresentar dados sensíveis a maiúsculas e para os utilizadores da aplicação privilégios, adicione o utilizador SQL ou identidade AAD que a aplicação utiliza para a base de dados da consulta. É vivamente recomendado que esta lista contiverem um número mínimo de utilizadores privilegiados para minimizar exposição dos dados confidenciais.

10. Clique em **Guardar** os dados masking pá de configuração para guardar a política de máscaras de novo ou actualizado.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar o dados dinâmicos masking para a base de dados utilizando os cmdlets do Powershell

Consulte [Cmdlets de base de dados do Azure SQL](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar o máscaras dinâmico de dados para a base de dados com a REST API

Consulte o artigo [operações para bases de dados Azure SQL](https://msdn.microsoft.com/library/dn505719.aspx).
