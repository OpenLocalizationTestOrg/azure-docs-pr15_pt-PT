<properties
   pageTitle="Activar a encriptação de dados transparente (TDE) para SQL Server esticar base de dados no Azure | Microsoft Azure"
   description="Activar a encriptação de dados transparente (TDE) para SQL Server esticar base de dados no Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Activar a encriptação de dados transparente (TDE) para esticar base de dados no Azure
> [AZURE.SELECTOR]
- [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Encriptação de dados transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa executando encriptação em tempo real e desencriptar de base de dados, associadas cópias de segurança e ficheiros de registo da transação em repouso sem exigir alterações à aplicação.

TDE encripta o armazenamento dos toda uma base de dados utilizando uma chave simétrica denominada a chave de encriptação da base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporados. O certificado de servidor incorporados é exclusivo para cada server Azure. Microsoft roda automaticamente estes certificados pelo menos a cada cerca de 90 dias. Para obter uma descrição geral de TDE, consulte o artigo [Transparente dados encriptação (TDE)].

##<a name="enabling-encryption"></a>Activar a encriptação

Para ativar TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com capacidade de esticar, efetue as seguintes coisas:

1. Abra a base de dados no [portal do Azure](https://portal.azure.com)
2. No pá da base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação de dados transparente**![][1]
4. Selecione a definição **no** e, em seguida, selecione **Guardar**
![][2]


##<a name="disabling-encryption"></a>Desactivar a encriptação

Para desativar o TDE num Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com capacidade de opções esticar, efetue as seguintes coisas:

1. Abra a base de dados no [portal do Azure](https://portal.azure.com)
2. No pá da base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação de dados transparente**
4. Selecione a definição de **desativar** e, em seguida, selecione **Guardar**




<!--Anchors-->
[Encriptação de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
