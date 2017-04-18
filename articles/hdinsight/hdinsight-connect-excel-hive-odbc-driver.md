<properties
   pageTitle="Ligar o Excel para Hadoop com o controlador ODBC ramo | Microsoft Azure"
   description="Saiba como configurar e utilizar o controlador de ODBC Hive da Microsoft para o Excel para consultar os dados num HDInsight cluster."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Ligar o Excel para Hadoop com o controlador de ODBC Hive da Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Solução de grande dados da Microsoft integra componentes do Microsoft Business Intelligence (BI) clusters Apache Hadoop que foram implementados por Azure HDInsight. Um exemplo desta integração é a capacidade de ligar o Excel para o armazém de dados do ramo de um cluster de Hadoop HDInsight a utilizar o controlador de Microsoft Hive Open Database Connectivity (ODBC).

Também é possível ligar os dados associados a um cluster de HDInsight e outras origens de dados, incluindo outros clusters Hadoop (que não sejam HDInsight), a partir do Excel utilizando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e utilizar o Power Query, consulte o artigo [Ligar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [AZURE.NOTE] Enquanto os passos neste artigo podem ser utilizados com cluster uma Linux ou HDInsight baseados no Windows, Windows é necessário para a estação de trabalho do cliente.

**Pré-requisitos**:

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Cluster de um HDInsight**. Para criar uma, consulte o artigo [Introdução ao Azure HDInsight][hdinsight-get-started].
- **A estação de trabalho** com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 autónomo ou Office 2010 Professional Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Instalar o controlador ODBC Hive da Microsoft

Transferir e instalar o controlador de ODBC Hive da Microsoft a partir do [Centro de transferências][hive-odbc-driver-download].

Este controlador pode ser instalado em versões de 32 bits ou 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012 e permitirá que a ligação ao Azure HDInsight (versão 1.6 e posterior) e Azure HDInsight emulador (v.1.0.0.0 e posterior). Deve instalar a versão que corresponde à versão da aplicação onde irá utilizar o controlador de ODBC. Para este tutorial, o controlador da será utilizada a partir do Office Excel.

##<a name="create-hive-odbc-data-source"></a>Criar a origem de dados Hive ODBC

Os passos seguintes mostram-lhe como criar uma origem de dados ODBC ramo de registo.

1. A partir do Windows 8 ou Windows 10, prima a tecla Windows para abrir o ecrã de início e, em seguida, escreva **origens de dados**.
2. Clique em **Configurar dados ODBC origens (32 bits)** ou **Configurar a origens de dados ODBC (64 bits)** , consoante a sua versão do Office. Se estiver a utilizar o Windows 7, selecione **Origens de dados ODBC (32 bits)** ou **Origens de dados ODBC (64 bits)** da **Ferramentas administrativas**. Este procedimento inicia a caixa de diálogo do **Administrador da origem de dados ODBC** .

    ![Administrador da origem de dados OBDC][img-hdi-simbahiveodbc-datasource-admin]

3. A partir do DNS do utilizador, clique em **Adicionar** para abrir o Assistente de **Criar uma nova origem de dados** .
4. Selecione o **Controlador de ODBC Hive da Microsoft**e, em seguida, clique em **Concluir**. Este procedimento inicia a caixa de diálogo do **Microsoft Hive controlador DNS configuração de ODBC** .

5. Escreva ou selecione os seguintes valores:

    Propriedade|Descrição
    ---|---
    Nome da origem de dados|Dê um nome para a sua origem de dados
    Anfitrião|Introduza &lt;HDInsightClusterName >. azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net
    Porta|Utilize <strong>443</strong>. (Esta porta ter sido alterada a partir do 563 para 443.)
    Base de dados|Utilize a <strong>predefinição</strong>.
    Tipo de servidor de ramo|Selecione <strong>Hive servidor 2</strong>
    Mecanismo|Selecione o <strong>Serviço do Azure HDInsight</strong>
    Caminho de HTTP|Deixe em branco.
    Nome de utilizador|Introduza o nome de utilizador do HDInsight cluster utilizador. Este é o nome de utilizador criada durante o processo de aprovisionar cluster. Se tiver utilizado a opção criar rápida, o nome de utilizador predefinido é <strong>admin</strong>.
    Palavra-passe|Introduza a palavra-passe de utilizador de cluster HDInsight.
    </table>

    Existem alguns parâmetros importantes ter em consideração quando clica em **Opções avançadas**:

    Parâmetro|Descrição
    ---|---
    Utilizar consultas nativas|Quando está selecionada, o controlador de ODBC não irá tentar converter TSQL HiveQL. Deve utilizá-la apenas se estiver 100%-se de que estão a submeter declarações de HiveQL puras. Quando se liga ao SQL Server ou base de dados do SQL Azure, deve deixar desmarcada.
    Linhas obtidas por bloco|Quando a obter uma grande quantidade de registos, este parâmetro de sintonização poderá ser necessário assegurar ideais espetáculos.
    Comprimento da coluna predefinido cadeia, comprimento da coluna binário, escala da coluna Decimal|O tipo de dados comprimentos e indicação poderá afetar a forma como os dados são devolvidos. Estes irão provocar informações incorretas ser devolvido devido a perda de precisão e/ou truncamento.


    ![Opções avançadas][img-HiveOdbc-DataSource-AdvancedOptions]

6. Clique em **Testar** para testar a origem de dados. Quando a origem de dados está configurada corretamente, mostra *testes foi concluída com êxito!*.
7. Clique em **OK** para fechar a caixa de diálogo de ensaio. Nova origem de dados deverá agora estar listada no **Administrador da origem de dados ODBC**.
8. Clique em **OK** para sair do assistente.

##<a name="import-data-into-excel-from-hdinsight"></a>Importar dados para o Excel do HDInsight

Os passos abaixo descrevem a forma de importar dados de uma tabela hive para um livro do Excel utilizando a origem de dados ODBC que criou nos passos acima.

1. Abra um livro novo ou existente no Excel.
2. No separador **dados** , clique em **De outras origens de dados**e, em seguida, clique em **a partir do Assistente de ligação de dados** para iniciar o **Assistente de ligação de dados**.

    ![Assistente de ligação de dados aberta][img-hdi-simbahiveodbc.excel.dataconnection]

3. Selecione **ODBC DSN** como origem de dados e, em seguida, clique em **seguinte**.
4. A partir de origens de dados ODBC, selecione o nome da origem de dados que criou no passo anterior e, em seguida, clique em **seguinte**.
5. Volte a introduzir a palavra-passe para o cluster no Assistente de e, em seguida, clique em **teste** para verificar a configuração mais uma vez, se necessário.
6. Clique em **OK** para fechar a caixa de diálogo de ensaio.
7. Clique em **OK**. Aguarde que a caixa de diálogo **Seleccionar base de dados e tabela** abrir. Isto pode demorar alguns segundos.
8. Selecione a tabela que pretende importar e, em seguida, clique em **seguinte**. O *hivesampletable* é uma tabela hive de exemplo que vem com HDInsight clusters.  Pode escolher se ainda não criou uma. Para mais informações sobre como executar Hive consultas e criar tabelas de ramo, consulte [Utilizar Hive com HDInsight][hdinsight-use-hive].
8. Clique em **Concluir**.
9. Na caixa de diálogo **Importar dados** , pode alterar ou especificar a consulta. Para fazê-lo, clique em **Propriedades**. Isto pode demorar alguns segundos.
10. Clique no separador **definição** e, em seguida, acrescentar **200 do limite** a instrução select ramo na caixa de **texto do comando** texto. A modificação irá limitar o conjunto de registos devolvidos para 200.

    ![Propriedades de ligação][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Clique em **OK** para fechar a caixa de diálogo Propriedades da ligação.
12. Clique em **OK** para fechar a caixa de diálogo **Importar dados** .  
13. Volte a introduzir a palavra-passe e, em seguida, clique em **OK**. Bastam alguns segundos antes dos dados são importados para o Excel.

##<a name="next-steps"></a>Próximos passos

Neste artigo o que aprendeu como utilizar o controlador de ODBC Hive da Microsoft para obter dados a partir do serviço do HDInsight para o Excel. Da mesma forma, pode obter dados a partir do serviço do HDInsight numa base de dados do SQL. Também é possível carregar dados para um serviço de HDInsight. Para obter mais informações, consulte o artigo:

- [Analisar os dados de atrasos em voos utilizando HDInsight][hdinsight-analyze-flight-data]
- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Utilizar Sqoop com HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
