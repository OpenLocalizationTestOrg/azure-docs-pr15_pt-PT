<properties 
   pageTitle="Conceber soluções de nuvem para utilizar a base de dados SQL Geo-replicação de recuperação de falhas | Microsoft Azure"
   description="Saiba como estruturar a solução na nuvem para recuperação de falhas ao selecionar o padrão de activação pós-falha à direita."
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
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Estratégias de recuperação de falhas para aplicações utilizando agrupamento flexível de base de dados SQL 

Ao longo dos anos que podemos aprendeu que os serviços em nuvem não são incidentes infalíveis e grave pode e irá acontecer. Base de dados SQL fornece várias funcionalidades para fornecer para a continuidade do negócio da sua aplicação quando estes incidentes ocorrerem. Bases de dados [flexível agrupamentos](sql-database-elastic-pool.md) e autónomo suportam o mesmo tipo de capacidades de recuperação de falhas. Este artigo descreve várias estratégias de DR para conjuntos de dados de elásticos tirar partido destas funcionalidades de continuidade do negócio base de dados SQL.

Para efeitos deste artigo utilizamos o padrão de aplicação SaaS ISV canónico:

<i>Uma nuvem moderna com base web aplicação disposições uma base de dados SQL para cada utilizador final. O ISV tem um grande número de clientes e, consequentemente, utiliza muitas bases de dados, conhecidas como bases de dados do inquilino. Uma vez que as bases de dados do inquilino normalmente têm padrões de atividade inesperados, o ISV utiliza um conjunto de dados flexível para tornar a base de dados de custo muito previsíveis adicional períodos de tempo. O conjunto de flexível simplifica também a gestão de desempenho quando a atividade de utilizador pontas. Para além das bases de dados do inquilino a aplicação utiliza também várias bases de dados para gerir perfis de utilizador, segurança, recolher padrões de utilização etc. Disponibilidade de inquilinos individuais não causam impacto na disponibilidade da aplicação como todo. No entanto, a disponibilidade e o desempenho de bases de dados de gestão é fundamental para a função a aplicação e, se as bases de dados de gestão offline a aplicação completa está offline.</i>  

Os restantes do papel discutimos estratégias de DR que abrange um intervalo de cenários a partir das aplicações de arranque sensíveis a maiúsculas e custo para aqueles com os requisitos de disponibilidade estritas.  

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Arranque sensíveis a maiúsculas e custo

<i>Sou um negócio de arranque e estou extremamente custo confidencial.  Pretendo simplificar a implementação e gestão da aplicação e estiver disposto a ter um SLA limitado para clientes individuais. Mas quero assegurar a aplicação, tal como um todo nunca está offline.</i>

Para cumprir o requisito de simplificar, deve implementar todas as bases de dados do inquilino para um conjunto de flexível na região da sua preferência Azure e implementar as bases de dados de gestão como bases de dados de replicadas geo autónomo. Para a recuperação de falhas de inquilinos, utilize geo-restaurar, o que vem incluído sem custos adicionais. Para garantir a disponibilidade das bases de dados gestão, devem ser replicadas geo para outra região (passo 1). O custo em curso da configuração de recuperação do falhas neste cenário é igual ao custo total das bases de dados secundária. Esta configuração é ilustrada no diagrama seguinte.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Em caso de uma falha na região principal, os passos de recuperação para ativar a sua aplicação online sejam ilustrados pelo diagrama seguinte.

- Imediatamente activação pós-falha a gestão de bases de dados (2) à região de DR. 
- Alterar a cadeia de ligação a aplicação para apontar para a região DR. Todas as novas contas e bases de dados do inquilino serão criados na região DR. Os clientes existentes irão ver os respetivos dados temporariamente indisponíveis.
- Crie o conjunto de flexível com a mesma configuração que o conjunto de original (3). 
- Utilize restaurar geo para criar cópias do inquilino de bases de dados (4). Pode ter em consideração acionar a restaura individuais pelas ligações de utilizador final ou utilizar algumas outro esquema de prioridade específico de aplicação.

Neste momento a aplicação é voltar a estar online na região DR, mas alguns clientes experiência atraso quando aceder aos seus dados.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a indisponibilidade tiver sido temporária, é possível que a região primária será recuperada por Azure antes de todos os a restaura está concluídas na região DR. Neste caso, deverá orquestrar mover a aplicação novamente à região de principal. O processo irá demorar os passos ilustrados no diagrama seguinte.
 
- Cancele todos os pedidos de pendentes geo-restaurar.   
- Activação pós-falha as bases de dados de gestão à região de primária (5). Nota: Após a recuperação da região de primários da antigos tem torna-se automaticamente secundários. Agora irá mudam funções novamente. 
- Alterar a cadeia de ligação a aplicação para apontarem para o a região principal. Agora todas as novas contas e bases de dados do inquilino serão criados na região principal. Alguns clientes existentes irão ver os respetivos dados temporariamente indisponíveis.   
- Defina todas as bases de dados no conjunto de DR para só de leitura para garantir que não podem ser modificadas na região DR (6). 
- Para cada base de dados no conjunto de DR que foi alterado desde a recuperação, mudar o nome ou eliminar as bases de dados correspondentes no conjunto de primário (7). 
- Copie as bases de dados atualizados do agrupamento de DR ao agrupamento de primário (8). 
- Eliminar o agrupamento de DR (9)

Neste momento a aplicação irá estar online na região primária com todos os inquilino bases de dados disponíveis no conjunto de principal.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A chave **beneficiar** desta estratégia é baixo custo em curso para a camada redundância de dados. Cópias de segurança são tidos automaticamente pelo serviço de base de dados SQL com sem reescrita de aplicação e sem custos adicionais.  O custo é suportado apenas quando as bases de dados flexível são restaurados. O **compromisso** é que a recuperação completa de todas as bases de dados do inquilino irá demorar significativa. Irá dependem do total número de restaura iniciará na região DR e tamanho global as bases de dados do inquilino. Mesmo se atribuir prioridades restaura alguns dos inquilinos sobre outras pessoas, a competir com todos os outros restaura que é iniciadas na mesma região, tal como o serviço serão arbitragem e optimizar para minimizar o impacto geral em bases de dados dos clientes existentes. Além disso, a recuperação ter as bases de dados do inquilino não consegue iniciar até que seja criado o novo conjunto flexível na região DR.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicação maduro com o serviço em camadas 

<i>Estou uma aplicação SaaS maduro com ofertas de serviço em camadas e SLA diferentes para os clientes de avaliação e para o pagamento de clientes. Para os clientes de avaliação, tenho de ter para reduzir o custo quanto possível. Clientes avaliação podem demorar tempo de inatividade mas quiser reduzir respetiva probabilidade. Para os clientes de pagamento, qualquer tempo de inatividade é um risco voo. Pelo que pretendo para se certificar de que pagamentos clientes sempre que conseguem aceder aos respetivos dados.</i> 

Para suportar neste cenário, deverá de separar avaliação inquilinos do pagos inquilinos, colocando-os em agrupamentos de flexível separados. Os clientes de avaliação teria eDTU inferior por inquilino e inferior SLA com um tempo recuperação. Os clientes de pagamento seria num conjunto de dados com mais elevado eDTU por inquilino e um SLA superior. Para garantir a hora de recuperação mais baixa, bases de dados do inquilino os clientes de pagamento devem ser replicadas geo. Esta configuração é ilustrada no diagrama seguinte. 

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Tal como o primeiro cenário, as bases de dados de gestão estará ativa bastante para utilizar um programa autónomo geo-base de dados replicada para a mesma (1). Isto irá garantir o desempenho previsível para as subscrições do cliente novo, atualizações de perfil e outras operações de gestão. O região em que residem os primários de bases de dados a gestão estará a região principal e o região em que residem secundários de bases de dados de gestão serão região de DR.

Inquilino as bases de dados os clientes de pagamento terá bases de dados ativos no conjunto de "pago" aprovisionado na região principal. Deve aprovisionar um agrupamento de secundário com o mesmo nome na região DR. Cada inquilino seria de replicadas geo ao agrupamento de secundário (2). Isto permitirá uma recuperação rápida das bases de dados do inquilino todos os utilizar activação pós-falha. 

Se ocorre uma falha na região principal, os passos de recuperação para ativar a sua aplicação online são ilustrados no diagrama seguinte:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Imediatamente falhe sobre as bases de dados de gestão à região de DR (3).
- Altere a cadeia de ligação a aplicação para apontar para a região DR. Agora todas as novas contas e bases de dados do inquilino serão criados na região DR. Os clientes existentes avaliação irão ver os respetivos dados temporariamente indisponíveis.
- Activação pós-falha bases de dados do inquilino paga ao agrupamento de na região DR para restaurar imediatamente a sua disponibilidade (4). Uma vez que a activação pós-falha é uma alteração ao nível do metadados rápida pode considerar uma optimização onde a activações pós-falha individuais está acionada a pedido às ligações de utilizador final. 
- Se o tamanho do conjunto de dados secundária eDTU era inferior a página principal, porque as bases de dados secundárias necessária apenas a capacidade para alterar os registos de início do processo enquanto terem sido secundários, imediatamente devem aumentar a capacidade de conjunto de dados agora para acomodar a carga de trabalho completa de todos os inquilinos (5). 
- Crie novo conjunto de flexível com o mesmo nome e a mesma configuração na região DR para bases de dados dos clientes avaliação (6). 
- Quando é criado o conjunto dos clientes de avaliação, utilize geo restaurar para restaurar as bases de dados do inquilino de avaliação individual para o novo conjunto de dados (7). Pode ter em consideração acionar a restaura individuais pelas ligações de utilizador final ou utilizar algumas outro esquema de prioridade específico de aplicação.

Neste momento a aplicação é voltar a estar online na região DR. Todos os clientes de pagamento têm acesso aos seus dados enquanto os clientes de avaliação experiência atraso quando aceder aos seus dados.

Quando a região primária é recuperada por Azure *depois de* ter restaurado a aplicação na região DR que pode continuar a executar a aplicação na região ou pode optar por falhar voltar à região de principal. Se a região primária for recuperado *antes do* processo de activação pós-falha está concluído, deverá tomar em consideração na falta anterior imediatamente. A reposição de recurso irá siga os passos ilustrados no diagrama seguinte: 
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Cancele todos os pedidos de pendentes geo-restaurar.   
- Activação pós-falha a gestão de bases de dados (8). Após a recuperação da região a página principal antiga tinha torna-se automaticamente secundária. Agora que se torne a página principal novamente.  
- Activação pós-falha ao inquilino pago bases de dados (9). Da mesma forma, após a recuperação da região de primários da antigos torna-se automaticamente os secundários. Agora que irão tornar-se a primários da novamente. 
- Defina as bases de dados avaliação restaurados que foram alterados na região DR para só de leitura (10).
- Para cada base de dados no conjunto de clientes avaliação DR que foi alterado desde a recuperação, mudar o nome ou eliminar a base de dados correspondente no conjunto principal de clientes avaliação (11). 
- Copie as bases de dados atualizados do agrupamento de DR ao agrupamento de primário (12). 
- Eliminar o agrupamento de DR (13) 

> [AZURE.NOTE] A operação de activação pós-falha é assíncrona. Para minimizar o tempo de recuperação é importante que executar o inquilino comando dos bases de dados activação pós-falha em lotes de bases de dados, pelo menos, 20. 

A chave **beneficiar** desta estratégia é que fornece o mais alto SLA para os clientes de pagamento. Também garante que a novas tentativas são desbloqueadas assim que o conjunto de DR avaliação é criado. O **compromisso** é que esta configuração irá aumentar o custo total das bases de dados do inquilino pelo custo do conjunto de DR secundário para clientes pagos. Além disso, se o agrupamento secundário tem um tamanho diferente, os clientes de pagamento serão uma experiência de desempenho inferior após activação pós-falha até que a atualização do conjunto de dados na região DR seja concluída. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicação geograficamente distribuída com o serviço em camadas

<i>Tenho de ter uma aplicação de SaaS maduro com ofertas de serviço em camadas. Pretende oferecer um SLA muito agressivo aos meus clientes pagas e minimizar o risco de impacto quando ocorrem falhas de uma vez que o mesmo breve interrupções podem causar insatisfação de cliente. É fundamental que os clientes de pagamento sempre podem aceder aos respetivos dados. As tentativas é gratuitas e um SLA não é disponibilizado durante o período de avaliação.</i> 

Para suportam este cenário, deve ter três separados flexível conjuntos de dados. Devem ser aprovisionados dois conjuntos de tamanho igual com alta eDTUs por bases de dados em dois regiões diferentes para conter inquilino as bases de dados os clientes paga. O conjunto de terceiro que contém os inquilinos avaliação teria um eDTUs inferior por bases de dados e aprovisionado de uma das duas região.

Para garantir a hora de recuperação mais baixa durante falhas de inquilino os clientes de pagamento bases de dados devem ser replicadas geo com 50% das bases de dados principais em cada uma das regiões. Da mesma forma, cada uma das regiões teria de 50% das bases de dados secundárias. Desta forma se uma região está offline os apenas 50% de bases de dados dos clientes paga seria ser afetados e teria activação pós-falha. Outras bases de dados seriam permanecem intactos. Esta configuração é ilustrada no diagrama seguinte:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Tal como os cenários anterior, as bases de dados de gestão serão bastante ativo para que deve configurá-los como autónomo replicadas geo bases de dados (1). Isto irá garantir o desempenho de novas subscrições de cliente, atualizações de perfil e outras operações de gestão previsível. Região A seria a região principal para as bases de dados de gestão e a região B será utilizada para a recuperação das bases de dados de gestão.

Bases de dados do inquilino os clientes de pagamento será também replicadas geo mas primários da e secundários dividido entre A região e região B (2). Desta forma as bases de dados principal do inquilino afetados pela indisponibilidade pode activação pós-falha à região de outros e ficam disponíveis. A outra metade das bases de dados do inquilino não será afectada de todo. 

O diagrama seguinte ilustra os passos de recuperação caso ocorra uma falha na região respostas.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Imediatamente falhe sobre gestão bases de dados para região B (3).
- Altere a cadeia de ligação a aplicação para apontar para as bases de dados de gestão na região B. modificar a gestão de bases de dados para se certificar de que a novas contas e bases de dados do inquilino serão criados na região B e as bases de dados do inquilino existente serão encontrados aí também. Os clientes existentes avaliação irão ver os respetivos dados temporariamente indisponíveis.
- Activação pós-falha bases de dados do inquilino paga ao agrupamento de 2 na região B para restaurar imediatamente a sua disponibilidade (4). Uma vez que a activação pós-falha é uma alteração ao nível do metadados rápida pode considerar uma optimização onde a activações pós-falha individuais está acionada a pedido às ligações de utilizador final. 
- Desde agora o agrupamento de 2 contém apenas primárias bases de dados que a carga de trabalho total no conjunto de aumentará por isso, imediatamente devem aumentar o tamanho de eDTU (5). 
- Crie novo conjunto de flexível com o mesmo nome e a mesma configuração na região B para bases de dados dos clientes avaliação (6). 
- Quando é criado o agrupamento de utilize geo restaurar para restaurar a base de dados do inquilino de avaliação individual para o conjunto (7). Pode ter em consideração acionar a restaura individuais pelas ligações de utilizador final ou utilizar algumas outro esquema de prioridade específico de aplicação.


> [AZURE.NOTE] A operação de activação pós-falha é assíncrona. Para minimizar o tempo de recuperação é importante que executar o inquilino comando dos bases de dados activação pós-falha em lotes de bases de dados, pelo menos, 20. 

Neste momento a aplicação estiver novamente online na região B. Todos os clientes de pagamento têm acesso aos seus dados enquanto os clientes de avaliação experiência atraso quando aceder aos seus dados.

Quando é recuperado região A tem de decidir se pretende utilizar região B para clientes avaliação ou reposição de recurso para utilizar o conjunto de clientes avaliação na região respostas. Um critério pode ser % das bases de dados do inquilino de avaliação modificados desde a recuperação. Independentemente desta decisão terá de voltar equilibrar inquilinos pagos entre dois conjuntos de dados. o diagrama seguinte ilustra o processo de quando as bases de dados do inquilino de avaliação falham voltar a região respostas.  
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Cancele todos os pedidos de pendentes geo-restauro ao agrupamento de DR avaliação.   
- Activação pós-falha a gestão da base de dados (8). Após a recuperação a região, a página principal antiga automaticamente tinha ficou secundária. Agora que se torne a página principal novamente.  
- Selecione a que inquilino pago irão falhar bases de dados para o conjunto de dados 1 e inicie activação pós-falha para os respetivos secundários (9). Após a recuperação da região todas as bases de dados no conjunto de 1 ficaram automaticamente secundários. Agora 50% dos mesmos irão tornar-se primários da novamente. 
- Reduza o tamanho de agrupamento de 2 para o original eDTU (10).
- Conjunto de todos os restaurados avaliação bases de dados na região B para só de leitura (11).
- Para cada base de dados no conjunto de DR avaliação que foi alterado desde a recuperação ter mudar o nome ou eliminar a base de dados correspondente no conjunto de primário avaliação (12). 
- Copie as bases de dados atualizados do agrupamento de DR ao agrupamento de primário (13). 
- Eliminar o agrupamento de DR (14) 

Os principais **benefícios** desta estratégia são:

- Suporta a SLA mais agressiva dos clientes pagamento uma vez que garante que uma falha de não pode ter um impacto na mais de 50% das bases de dados do inquilino. 
- -Garante que a novas tentativas são desbloqueadas assim que a pista de agrupamento de DR é criada durante a recuperação. 
- Permite a utilização mais eficaz da capacidade de conjunto de dados tal como os 50% das bases de dados secundárias no conjunto de dados 1 e agrupamento de 2 são garante para ser menos ativo, em seguida, as bases de dados principais.

A principal **compromissos** são:

- As operações CRUD contra as bases de dados de gestão terá latência inferior para os utilizadores finais ligados à região A mais dos utilizadores finais ligados à região B à medida que vai ser executadas em relação a página principal de bases de dados de gestão.
- Necessita de mais complexa estrutura da base de dados gestão. Por exemplo, cada registo de inquilino seria tem de ter uma etiqueta de localização que precisa de ser alterado durante activação e reposição de recurso.  
- Os clientes de pagamento poderão ter desempenho inferior que o habitual até que a atualização do conjunto de dados na região B seja concluída. 

## <a name="summary"></a>Resumo

Este artigo foca-se em estratégias de recuperação de falhas para a camada de base de dados utilizado por uma aplicação do inquilino com várias SaaS ISV. Deverá basear a estratégia de que escolher as necessidades da aplicação, tal como o modelo de negócio, SLA que pretende oferecer aos seus clientes, orçamento de constrangimento, etc... Cada estratégia descrita destaca os benefícios e compromisso para que pode tomar uma decisão. Além disso, a sua aplicação específica provavelmente irá incluir outros componentes Azure. Por isso, deverá consultar os respetivos indicações de continuidade do negócio e orquestrar a recuperação da camada da base de dados com os mesmos. Para saber mais sobre a gestão de recuperação de aplicações de base de dados no Azure, consulte a [estruturar soluções de nuvem para recuperação de falhas](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
