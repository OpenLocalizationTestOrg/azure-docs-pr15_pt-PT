<properties
   pageTitle="Origens de dados do Azure catálogo de dados suportadas | Microsoft Azure"
   description="Especificação de origens de dados atualmente suportados."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
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

# <a name="azure-data-catalog-supported-data-sources"></a>Origens de dados do Azure catálogo de dados suportadas

Os utilizadores do catálogo de dados do Azure podem publicar metadados utilizando um API pública, um clique-uma vez registo ferramenta ou ao introduzir manualmente informações diretamente para o catálogo de dados web portal. A grelha seguinte resume todas as origens de suportados do catálogo de hoje e as funcionalidades de publicação para cada um.  Também listados são as ferramentas de dados externos que pode iniciar cada origem a partir da nossa experiência portal "abrir-in". Grelha da segunda no artigo tem especificação mais técnica de cada propriedades de ligação de origens de dados.


## <a name="list-of-supported-data-sources"></a>Lista de origens de dados suportadas

<table>

    <tr>
       <td><b>Objeto de origem de dados</b></td>
       <td><b>API</b></td>
       <td><b>Introdução manual</b></td>
       <td><b>Ferramenta de registo</b></td>
       <td><b>Ferramentas de abrir</b></td>
       <td><b>Notas</b></td>
    </tr>

    <tr>
      <td>Diretório do arquivo de Lake de dados do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ficheiro de arquivo de Lake de dados do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure armazenamento Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório de armazenamento Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de armazenamento Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Diretório HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ficheiro HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de ramo</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista do MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de base de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de base de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Outros (activo genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de armazém de dados SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, obter, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de armazém de dados SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, obter, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services dimensão</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services medida</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, obter</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório de Reporting Services do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Apenas os servidores de modo nativo. Modo do SharePoint não é suportado.</font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, obter, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, obter, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Hana do SAP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Obter</font></td>
      <td><font size=2>Vistas de cálculo e vistas analíticas apenas; Vistas do atributo não são suportadas.</font></td>
    </tr>

    <tr>
      <td>Tabela de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ficheiro do sistema</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directório de FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ficheiro de FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório de HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ponto final de HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ficheiro de HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidade OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Função OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do Postgresql</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista do Postgresql</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Hana do SAP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto de Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista do SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Se precisar de suporte para outras fontes, submeta um pedido de funcionalidade utilizando o [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Especificação de referência da origem de dados
> [AZURE.NOTE] Coluna de "DSL estrutura" nas seguintes tabela apenas listas de propriedades de ligação para saco de propriedade "endereço" que são utilizados pelo catálogo de dados do Azure (ou seja, se saco de propriedade "endereço" pode conter outras propriedades de ligação da origem de dados que catálogo de dados do Azure persistir, mas que não utilize.)
<table>
    <tr>
       <td><b>Tipo de origem</b></td>
       <td><b>Tipo de elementos</b></td>
       <td><b>Tipo de objeto (s)</b></td>
       <td><b>Estrutura DSL<b></td>
    </tr>
    <tr>
      <td>Arquivo de Lake de dados do Azure</td>
      <td>Contentor</td>
      <td>Dados Lake</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Arquivo de Lake de dados do Azure</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento Azure</td>
      <td>Contentor</td>
      <td>Contentor</td>
      <td>
        <font size=2>protocolo: blobs do azure <br>autenticação: {tecla de acesso de azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contentor</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento Azure</td>
      <td>Tabela</td>
      <td>BLOBs, diretório</td>
      <td>
        <font size=2>protocolo: blobs do azure <br>autenticação: {tecla de acesso de azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contentor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento Azure</td>
      <td>Contentor</td>
      <td>Contentor</td>
      <td>
        <font size=2>protocolo: azure tabelas <br>autenticação: {tecla de acesso de azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>protocolo: azure tabelas <br>autenticação: {tecla de acesso de azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contentor</td>
      <td>Virtual Cluster</td>
      <td>
        <font size=2>protocolo: cosmos <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Vista de sequência, conjunto de sequência,</td>
      <td>
        <font size=2>protocolo: cosmos <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contentor</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Relatório</td>
      <td>Relatório de Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: db2 <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: db2 <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros</td>
      <td>Tabela</td>
      <td>Ficheiro</td>
      <td>
        <font size=2>protocolo: ficheiro <br>autenticação: {nenhuma, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>protocolo: ftp <br>autenticação: {nenhuma, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros distribuído Hadoop</td>
      <td>Contentor</td>
      <td>Cluster</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Ramo</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: ramo <br>autenticação: {hdinsight, nome de utilizador básica, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Ramo</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: ramo <br>autenticação: {hdinsight, nome de utilizador básica, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Contentor</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Relatório</td>
      <td>Relatório de Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Tabela</td>
      <td>Ponto final, ficheiro</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contentor</td>
      <td>Contentor de entidade</td>
      <td>
        <font size=2>protocolo: odata <br>autenticação: {nenhuma, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabela</td>
      <td>Conjunto de entidade, função</td>
      <td>
        <font size=2>protocolo: odata <br>autenticação: {nenhuma, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;recurso</font>
      </td>
    </tr>
    <tr>
      <td>Base de dados Oracle</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Base de dados Oracle</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: postgresql <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: postgresql <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contentor</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Relatório</td>
      <td>Relatório de Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhuma, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabela</td>
      <td>Aplicação híbrida de dados</td>
      <td>
        <font size=2>protocolo: power query <br>autenticação: {oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Equipa de vendas</td>
      <td>Tabela</td>
      <td>Objecto</td>
      <td>
        <font size=2>protocolo: salesforce-com <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>Hana do SAP</td>
      <td>Contentor</td>
      <td>Servidor</td>
      <td>
        <font size=2>protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor</font>
      </td>
    </tr>
    <tr>
      <td>Hana do SAP</td>
      <td>Tabela</td>
      <td>Vista</td>
      <td>
        <font size=2>protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Lista</td>
      <td>
        <font size=2>protocolo: lista do sharepoint <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Armazém de dados SQL</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Armazém de dados SQL</td>
      <td>TableValuedFunction</td>
      <td>Função valor de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Armazém de dados SQL</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Armazém de dados SQL</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Do SQL Server</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Do SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Função valor de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Do SQL Server</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Do SQL Server</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensão}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>protocolo: analysis services <br>autenticação: {windows, basic, anónimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {tabela}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contentor</td>
      <td>Servidor</td>
      <td>
        <font size=2>protocolo: do reporting services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>
        <font size=2>protocolo: do reporting services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contentor</td>
      <td>Base de dados</td>
      <td>
        <font size=2>protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Vista de tabela</td>
      <td>
        <font size=2>protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objecto</font>
      </td>
    </tr>
    <tr>
      <td>Serviços de dados do SQL Server mestre</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size="2">protocolo: mssql mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão</font>
      </td>
    </tr>
    <tr>
      <td>Serviços de dados do SQL Server mestre</td>
      <td>Tabela</td>
      <td>Entidade</td>
      <td>
        <font size="2">protocolo: mssql mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entidade</font>
      </td>
    </tr>
    <tr>
      <td>Outro (não uma a acima)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>protocolo: genérico activo <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iddoactivo</font>
      </td>
    </tr>
</table>
