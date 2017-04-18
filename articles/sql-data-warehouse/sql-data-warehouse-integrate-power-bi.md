<properties
   pageTitle="Utilizar o Power BI com armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para utilizar o Power BI com armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>Utilizar o Power BI com armazém de dados SQL
Como base de dados SQL Azure, SQL dados armazém direta ligar permite utilizador tirar partido poderosa propagação lógica juntamente com as capacidades analíticas do Power BI.  Com uma ligação direta, as consultas são enviadas para o seu armazém de dados do SQL Azure em tempo real como explorar os dados.  Isto, combinado com a escala do armazém de dados SQL, permite aos utilizadores para criar relatórios dinâmicos em minutos contra terabytes de dados.  Além disso, a introdução do botão Abrir no Power BI permite aos utilizadores diretamente ligar-se do Power BI para o seu armazém de dados SQL sem recolher informações a partir de outras partes do Azure.

Quando utilizar ligar direta fórum Nota:

+ Especifique o nome do servidor completamente qualificado ao ligar (consulte abaixo para obter mais detalhes)
+ Certifique-se de que as regras de firewall para a base de dados estão configuradas para "Permitir o acesso a serviços Azure".
+ Cada ação como selecionar uma coluna ou adicionar um filtro irá diretamente consultar o armazenamento de dados
+ Mosaicos são atualizados em 15 minutos, aproximadamente (atualização não necessitam de ser agendado)
+ As perguntas e respostas não está disponível para ligar direta conjuntos de dados
+ Alterações de esquema não são recolhidas automaticamente
+ Todas as consultas ligar direta irão expirar após 2 minutos

Estes restrições e notas podem ser alteradas como podemos continuar melhorar as experiências. Os passos para ligar são detalhados abaixo.  

## <a name="using-the-open-in-power-bi-button"></a>Utilizar o botão Abrir no Power BI
A forma mais fácil para mover entre o seu armazém de dados SQL e o Power BI é com o botão Abrir no Power BI. Este botão permite-lhe de forma totalmente integrada começar a criar novos dashboards no Power BI.  

1.  Para começar a navegar para a instância do armazém de dados do SQL no Portal clássica do Azure.
2.  Clique no botão Abrir no Power BI.
3.  Se não é possível iniciar sessão diretamente, ou se não tiver uma conta do Power BI, será necessário iniciar sessão.  
4.  Será direcionado para a página de ligação armazém de dados SQL, com as informações do seu armazém de dados SQL povoada previamente.
5.  Depois de introduzir as suas credenciais será completamente ligado para o seu armazém de dados SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Ligar através do portal do Power BI
Além de utilizar o botão Abrir no Power BI, os utilizadores também podem ligar para o seu armazém de dados SQL através do Portal do Power BI.

1.  Clique em 'Obter dados de' na parte inferior do painel de navegação.
2.  Selecione 'Bases de dados'.
3.  Uma vez na página bases de dados, selecione 'Armazém de dados do SQL Azure' e, em seguida, clique em 'Ligar'.
4.  Introduza as informações de ligação necessárias.  Podem encontrar o nome de servidor e o nome da base de dados no Portal do Azure.
5.  Será direcionado para o página principal do Power BI e depois da ligação for feita uma nova entrada em 'Conjuntos de dados' serão apresentados com o nome da sua instância.  
6.   Pode clicar no novo conjunto de dados para explorar todas as tabelas e vistas na base de dados. Selecionar uma coluna irá enviar novamente para a origem, dinamicamente criar o seu visual de uma consulta. Estes efeitos visuais podem ser guardados num novo relatório e afixados novamente para o dashboard.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
