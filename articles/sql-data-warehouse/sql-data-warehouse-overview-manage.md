<properties
   pageTitle="Gerir bases de dados no armazém de dados do SQL Azure | Microsoft Azure"
   description="Descrição geral das gerir bases de dados do SQL armazém de dados. Inclui ferramentas de gestão, DWUs e desempenho de escala de saída, resolução de problemas de desempenho da consulta, estabelecer políticas de bom nível de segurança e restaurar uma base de dados a partir de danos nos dados ou de uma falha de regional."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gerir bases de dados no armazém de dados do SQL Azure

Armazém de dados SQL automatiza muitos aspetos de gerir as bases de dados. Por exemplo, para dimensionar desempenho só tem de ajustar e pagar o nível de recursos de cluster à direita e, em seguida, informe armazém de dados SQL fazer todo o trabalho de dimensionamento novamente e escalar para fora. 

Pretenderá indubitavelmente monitorizar a sua carga de trabalho para identificar as suas necessidades de desempenho, bem como resolver problemas de consultas de execução longa. Também terá de efetuar algumas tarefas de segurança para gerir permissões para utilizadores e inícios de sessão.

Esta descrição geral abrange estes aspetos da gestão de armazenamento de dados SQL.

- Ferramentas de gestão
- Cluster de escala
- Interromper e retomar
- Práticas recomendadas do desempenho
- Monitorização de consulta
- Segurança
- Cópia de segurança e restauro

## <a name="management-tools"></a>Ferramentas de gestão

Pode utilizar uma variedade de ferramentas para gerir bases de dados no armazém de dados SQL. Como gerir bases de dados, irá desenvolver preferências da ferramenta para cada tipo de tarefa que necessitar de efetuar.

### <a name="azure-portal"></a>Portal do Azure
O [Azure portal][] é um portal baseado na web onde pode criar, atualizar e eliminar bases de dados-las e monitorizar recursos de base de dados. Esta ferramenta é ótima está se estiver a começar com o Azure, gerir um pequeno número de bases de dados de armazém dados, ou precisa de fazer algo rapidamente.

Para começar a utilizar com o portal do Azure, consulte o artigo [criar um armazém de dados SQL (Azure portal)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>Ferramentas de dados do SQL Server no Visual Studio
[Ferramentas de dados do SQL Server][] (SSDT) no Visual Studio permite-lhe ligar-se, gerir e desenvolver as bases de dados. Se for um programador de aplicação familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), tente utilizar SSDT no Visual Studio.

SSDT inclui o Explorador de objeto do SQL Server que lhe permite visualizar, ligar-se e executar scripts de bases de dados do armazém de dados SQL. Para ligar rapidamente a armazém de dados SQL, pode simplesmente clicar no botão **aberto no Visual Studio** na barra de comandos quando ver os detalhes de base de dados no Portal clássica do Azure.  

Para começar com SSDT no Visual Studio, consulte o artigo [Consulta armazém de dados de SQL Azure com o Visual Studio][].

### <a name="command-line-tools"></a>Ferramentas de linha de comandos
Ferramentas de linha de comandos são ideais para automatizar a sua das cargas de trabalho.  PowerShell e sqlcmd são excelentes duas formas para automatizar os processos.  Recomendamos que estas ferramentas para gerir um grande número de servidores lógicos e implementar alterações de recursos num ambiente de produção, tal como as tarefas necessárias podem ser script e, em seguida, automatizadas.

### <a name="dynamic-management-views"></a>Vistas de gestão de dinâmicos 

DMVs são manteiga e pão de gestão de armazenamento de dados SQL. Quase todas as informações que superfícies no portal do depende DMVs. Para ver uma lista de DMVs de armazém de dados SQL, consulte o artigo [Armazém de dados SQL vistas do sistema][].

Para começar, consulte o artigo [Ligar ' e ' consulta com sqlcmd][]e [criar uma base de dados (PowerShell)][].

## <a name="scale-compute"></a>Cluster de escala

No armazém de dados do SQL, pode rapidamente Dimensionar desempenho saída ou voltar ao crescentes ou decrescentes recursos de cluster de largura de banda e/s, memória e CPU. Para dimensionar o desempenho, tudo o que precisa de fazer é ajustar o número de unidades de armazém de dados (DWUs) que armazém de dados SQL atribui a sua base de dados. Armazém de dados SQL rapidamente faz com que a alteração e alças de todas as alterações subjacentes ao software ou hardware.

Para saber mais sobre dimensionamento DWUs, consulte o artigo [desempenho de escala][].

##  <a name="pause-and-resume"></a>Interromper e retomar

Para guardar os custos, pode interromper e retomar cluster recursos a pedido. Por exemplo, se não estar a utilizar a base de dados durante a noite e no fins de semana, pode colocar em pausa durante essas horas e retomá-la durante o dia. Não de Serei cobrado por DWUs enquanto a base de dados está em pausa.

Para mais informações, consulte o artigo [colocar em pausa calcular][]e [Calcular currículo][].

## <a name="performance-best-practices"></a>Práticas recomendadas do desempenho

Quando começar a trabalhar com uma nova tecnologia, a descobrir as sugestões e truques que funcionam melhor direita desde o início, pode poupar grandes quantidades de tempo.  Irá encontrar melhores práticas ao longo de muitos dos nossos tópicos.

Para ver muitas um resumo das considerações mais importantes quando desenvolver a sua carga de trabalho, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

## <a name="query-monitoring"></a>Monitorização de consulta

Por vezes, uma consulta está a ser executado demasiado longa, mas ainda não tem a certeza de qual é o culpado. Armazém de dados SQL tem gestão dinâmica as vistas (DMVs) que pode utilizar para descobrir a consulta estiver a demorar demasiado tempo. 

Para localizar consultas de execução longa, consulte o artigo [monitorizar a sua carga de trabalho utilizando DMVs][].

## <a name="security"></a>Segurança

Para manter um sistema seguro, tem de estar ligado o alerta e Proteja-se contra qualquer tipo de acesso não autorizado. Necessita de um sistema de segurança para se certificar de que as regras de firewall são num local autorizado, para que apenas podem ligar-se endereços IP. Precisa de autenticação adequada de credenciais de utilizador. Depois de um utilizador tem ligação à base de dados, o utilizador apenas deve ter permissões para executarem um número mínimo de ações. Para proteger dados, pode utilizar a encriptação. Também é importante ter auditoria e controlo para que pode Repita eventos se existe alguma actividade suspeita.

Para saber mais sobre a gestão da segurança, cabeça sobre para um [Descrição geral de segurança][].

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Está com dificuldades backps fiável dos seus dados é uma parte essencial quaisquer bases de dados de produção. Armazém de dados SQL mantém os seus dados seguros por cópias as bases de dados ativos automaticamente a intervalos regulares. Estas cópias de segurança permitem-lhe recuperar a partir de cenários onde tenha danificado os dados ou largadas acidentalmente a sua base de dados ou dados.  Para a agenda de cópia de segurança de dados, política de retenção e como restaurar uma base de dados, consulte o artigo [restaurar a partir de instantâneo][].

## <a name="next-steps"></a>Próximos passos
Utilizar estrutura de base de dados boa princípios irão facilitar gerir as bases de dados no armazém de dados do SQL. Para saber mais, cabeça sobre para um [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Criar um armazém de dados SQL (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Criar uma base de dados (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Consulta armazém de dados Azure SQL com o Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Ligar e de consulta com sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md
[Monitorizar a sua carga de trabalho utilizando DMVs]: sql-data-warehouse-manage-monitor.md
[Cluster de colocar em pausa]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restaurar a partir do instantâneo]: sql-data-warehouse-restore-database-overview.md
[Cluster de currículo]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Desempenho de escala]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Descrição geral de segurança]: sql-data-warehouse-overview-manage-security.md
[Práticas recomendadas do armazém de dados SQL]: sql-data-warehouse-best-practices.md
[Vistas de sistema do armazém de dados SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[Ferramentas de dados do SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal do Azure]: http://portal.azure.com/
