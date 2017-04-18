<properties 
   pageTitle="Brocas de recuperação de falhas de base de dados SQL | Microsoft Azure" 
   description="Saiba a orientação e práticas recomendadas para utilizar a base de dados do SQL Azure para efetuar Brocas de recuperação de falhas que o irão ajudar mantêm o seu missão crítico aplicações empresariais e são e falhas de corrente." 
   services="sql-database" 
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Efetuar desagregação de recuperação de falhas

Recomenda-se que a validação de preparação da aplicação de fluxo de trabalho de recuperação é executada periodicamente. Verificar o comportamento da aplicação e as implicações de perda de dados e/ou a interrupção esse activação pós-falha envolve é aconselhável engenharia. Também é um requisito pela maioria dos normas da indústria como parte de certificação de continuidade do negócio.

Efetuar uma pesquisa de recuperação de falhas consiste em:

- Simulação falha do camada de dados
- Recuperar 
- Validar recuperação de mensagem de integridade de aplicações

Cálculo consoante a forma como a [concebido a sua aplicação de continuidade do negócio](sql-database-business-continuity.md), o fluxo de trabalho para executar a desagregação pode variar. Abaixo estamos descrevem as melhores práticas realizar uma desagregação de recuperação de falhas no contexto da base de dados do SQL Azure. 

##<a name="geo-restore"></a>Restaurar geo

Para impedir que a possível perda de dados ao conduzir uma desagregação de recuperação de falhas, recomendamos que efetuar desagregação utilizando um ambiente de teste através da criação de uma cópia do ambiente de produção e utilizá-lo para verificar o fluxo de trabalho da aplicação activação pós-falha.
 
####<a name="outage-simulation"></a>Simulação de indisponibilidade

Para simular a indisponibilidade pode eliminar ou mudar o nome da base de dados de origem. Isto irá causar a falha da conectividade de aplicação. 

####<a name="recovery"></a>Recuperação

- Execute o restauro Geo da base de dados para um servidor diferente como descrito [aqui](sql-database-disaster-recovery.md). 
- Altere a configuração da aplicação para ligar as bases de dados recuperado e siga o guia de [Configurar uma base de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####<a name="validation"></a>Validação

- Conclua a desagregação, verificando a recuperação de mensagem de integridade aplicação (ou seja, as cadeias de ligação, inícios de sessão, funcionalidade básica testes ou outra peça validações dos procedimentos de signoffs aplicação padrão).

##<a name="geo-replication"></a>Geo replicação

Para uma base de dados estiver protegida utilizando a replicação Geo exercício desagregação irá envolver planeada activação pós-falha à base de dados secundária. A activação planeada pós-falha assegura que a página principal e as bases de dados secundárias se mantenham sincronizadas quando as funções são mudadas. Ao contrário de falha na ligação não planeada, esta operação não irá resultar na perda de dados, para que pode ser efetuada a desagregação no ambiente de produção. 

####<a name="outage-simulation"></a>Simulação de indisponibilidade

Para simular a indisponibilidade pode desativar a aplicação web ou máquina virtual ligado à base de dados. Isto resultará em falhas de conectividade para os clientes web.

####<a name="recovery"></a>Recuperação

- Certifique-se a configuração da aplicação na região DR aponta para o antiga secundária que irão tornar-se completamente acessível principal de novo. 
- Executar o [planeado activação pós-falha](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) para tornar a base de dados secundária um novo principal
- Siga o guia de [Configurar uma base de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####<a name="validation"></a>Validação

- Conclua a desagregação, verificando a recuperação de mensagem de integridade aplicação (ou seja, as cadeias de ligação, inícios de sessão, funcionalidade básica testes ou outra peça validações dos procedimentos de signoffs aplicação padrão).


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre cenários de continuidade do negócio, consulte o artigo [cenários continuidade](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
