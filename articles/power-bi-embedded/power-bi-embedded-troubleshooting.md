<properties
   pageTitle="Resolução de problemas do Microsoft Power BI Preview incorporado"
   description="Resolução de problemas do Microsoft Power BI Preview incorporado"
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

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Resolução de problemas do Microsoft Power BI Preview incorporado
Este artigo fornece respostas para como resolver problemas de **Power BI incorporado**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Definir as cadeias de ligação do SQL Server
Para definir uma cadeia de ligação do SQL Server, tem de seguir um formato específico. Segue-se uma cadeia de ligação de exemplo para o SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para saber mais sobre as cadeias de ligação do SQL Server, consulte os artigos seguintes:

-   [Cadeias de ligação do SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Definir as credenciais
No caso onde tem as credenciais para uma desenvolvimento ou ambiente de teste, como o nome de utilizador e palavra-passe, poderá ter de atualizar as credenciais que correspondem uma solução de produção.

## <a name="see-also"></a>Consulte também
- [Introdução ao exemplo](power-bi-embedded-get-started-sample.md)
- [O que é incorporado do Power BI](power-bi-embedded-what-is-power-bi-embedded.md)
