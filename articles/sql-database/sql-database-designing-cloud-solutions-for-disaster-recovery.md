<properties
   pageTitle="Soluções de recuperação de falhas - base de dados ativo Geo-a replicação SQL em nuvem | Microsoft Azure"
   description="Saiba como estruturar soluções de recuperação de falhas de nuvem planeamento de negócio continuidade utilizando a replicação de geo para cópia de segurança de dados de aplicação com base de dados do SQL Azure."
   keywords="nuvem de recuperação de falhas, soluções de recuperação de falhas, cópia de segurança de dados de aplicação, replicação geo, planeamento de continuidade do negócio"
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
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Estruturar uma aplicação para a recuperação de falhas de nuvem Geo-a replicação do Active a utilizar na base de dados SQL


> [AZURE.NOTE] [Replicação do Geo Active](sql-database-geo-replication-overview.md) está agora disponível para todas as bases de dados em todas as camadas.



Saiba como utilizar [Geo-a replicação do Active](sql-database-geo-replication-overview.md) na base de dados SQL a estrutura da base de dados aplicações e são para falhas regionais e de corrente grave. Planeamento de negócio continuidade, considere a implementação topologia da aplicação, o contrato de nível de serviço está a filtrar, latência tráfego e custos. Neste artigo, vamos observar os padrões de aplicação comuns e abordam as vantagens e compromissos de cada opção. Para obter informações sobre como ativa Geo-replicação com agrupamentos de flexível, consulte o artigo [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Padrão de estrutura 1: activo passivo implementação do como recuperação de falhas de nuvem com uma base de dados cocriação localizado

Esta opção é mais adequada para as aplicações com as seguintes características:

+ Ativa instância numa única região Azure
+ Dependência forte de acesso de leitura e escrita (RW) aos dados
+ Conectividade de publicação em região entre lógica da aplicação e a base de dados não for aceitável devido a latência e o tráfego de custo    

Neste caso, a topologia da aplicação de implementação está optimizada para processamento de catástrofes regionais quando todos os componentes de aplicação são afetados e precisa de activação pós-falha como uma unidade. Para redundância geográfica, lógica da aplicação e a base de dados são replicadas para outro região mas não são utilizadas para a carga de trabalho de aplicação nas condições normais. A aplicação na região secundário deve ser configurada para utilizar uma cadeia de ligação de SQL na base de dados secundária. Gestor de tráfego está configurado para utilizar o [método de encaminhamento de activação pós-falha](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Gestor de tráfego Azure](../traffic-manager/traffic-manager-overview.md) é utilizado ao longo deste artigo ilustração apenas para fins de. Pode utilizar qualquer solução balanceamento de carga que suporta o método de encaminhamento de activação pós-falha.    

Além das instâncias da aplicação principal, considere implementar uma [aplicação de função de trabalho](cloud-services-choose-me.md#tellmecs) pequenas que monitoriza a base de dados principal através da emissão periódicos T-só de leitura (RO) comandos SQL. Pode utilizá-lo automaticamente acionado falha na ligação, para gerar um alerta na consola de administrador da sua aplicação ou para ambas as tarefas. Para se certificar de que monitorização não é afetada pelo falhas de toda a região, deve implementar as instâncias da aplicação de monitorização para cada região e têm cada instância ligada à base de dados principal na região principal e a base de dados secundária na região local. 

> [AZURE.NOTE] Ambas as aplicações de monitorização deve ser ativa e sonda principais e secundários bases de dados. O último pode ser utilizado para detetar uma falha na região secundário e alerta quando a aplicação não está protegida.     

O diagrama seguinte mostra esta configuração antes de uma falha.

![Configuração da base de dados de SQL Geo-replicação. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Após uma falha na região principal, a aplicação de monitorização Deteta que a base de dados principal não está acessível e regista um alerta. Dependendo do seu SLA de aplicação, pode decidir quantos sondas monitorização consecutivas deverão falhar antes de declara uma falha de base de dados. Para alcançar coordenado activação pós-falha do ponto final de aplicação e a base de dados, deve possuir a aplicação de monitorização efetuar os seguintes passos:

1. [Atualizar o estado do ponto final principal](https://msdn.microsoft.com/library/hh758250.aspx) para poder acionar activação pós-falha de ponto final.
2. Ligar a base de dados secundária para [Iniciar a base de dados activação pós-falha](sql-database-geo-replication-portal.md).

Depois de falha na ligação, a aplicação irá processar os pedidos de utilizador na região secundário mas permanecerá cocriação localizada com a base de dados, porque a base de dados principal estará agora na região secundário. Este cenário é ilustrado pelo diagrama seguinte. Em todos os diagramas, linhas contínuas indicam ligações activas, linhas ponteadas indicar suspensas ligações e sinais de paragem indicar accionadores de ação.


![Replicação Geo: Activação pós-falha a base de dados secundária. Cópia de segurança de dados aplicação.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se uma falha de acontece na região secundário, a ligação de replicação entre a página principal e a base de dados secundária está suspensa e a aplicação de monitorização regista um alerta que seja exposto a base de dados principal. Desempenho da aplicação não é afectado, mas a aplicação opera exposta e, por conseguinte, superior conta e risco maiúscula ambas as regiões falharem sucessivamente.

> [AZURE.NOTE] Recomendamos que apenas as configurações de implementação com uma única região de DR. Isto acontece porque a maioria das geografias Azure têm duas regiões. Estas configurações não irão proteger a sua aplicação a partir de uma falha grave do ambas as regiões. Numa eventualidade de uma falha na, pode recuperar as bases de dados numa região terceiro utilizando [geo restaurar operação](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Assim que a indisponibilidade é atenuada, a base de dados secundária é automaticamente sincronizada com a página principal. Durante a sincronização, desempenho do computador primário poderia afectado ligeiramente consoante a quantidade de dados que tem de ser sincronizadas. O diagrama seguinte ilustra uma falha na região secundário.

![Base de dados secundária sincronizados com principal. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


As principais **vantagens** deste padrão de estrutura são:

+ A cadeia de ligação de SQL é definida durante a implementação da aplicação em cada região e não altere depois activação pós-falha.
+ Desempenho da aplicação não é afectado pelo activação pós-falha como a aplicação e a base de dados sempre estão localizados cocriação.

A principal **variação** é que a instância da aplicação redundantes na região secundário só é utilizada para recuperação de falhas.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Padrão de estrutura 2: implementação activo activo para balanceamento de carga de aplicação
Esta opção de recuperação de falhas de nuvem é mais adequada para as aplicações com as seguintes características:

+ Lê elevada relação da base de dados para escritas
+ Latência de escrita da base de dados não causam impacto na experiência de utilizador final  
+ Lógica só de leitura pode ser separada de leitura / escrita lógica utilizando uma cadeia de ligação diferente
+ Lógica só de leitura não dependem dados completamente a ser sincronizados com as atualizações mais recentes  

Se as aplicações tem estas características, balanceamento de carga as ligações de utilizador final em várias instâncias da aplicação de diferentes regiões pode melhorar o desempenho e a experiência de utilizador final. Para implementar balanceamento de carga, cada uma das regiões deve ter uma instância da aplicação ativa com a lógica do (RW) de leitura / escrita ligada à base de dados principal na região principal. A lógica (RO) só de leitura deve estar ligada a uma base de dados secundária na mesma região como a instância da aplicação. Gestor de tráfego deve ser configurado para utilizar o [Encaminhamento de round robin](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou [Encaminhamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md) com [ponto final de monitorização](../traffic-manager/traffic-manager-monitoring.md) ativado para cada instância da aplicação.

Tal como padrão #1, deverá tomar em consideração implementar uma aplicação de monitorização semelhante. Mas, ao contrário padrão #1, a aplicação de monitorização não será responsável pela acionar o ponto final activação pós-falha.

> [AZURE.NOTE] Enquanto este padrão utiliza mais do que uma base de dados secundária, apenas um dos secundários seria utilizado para activação pós-falha por razões indicadas anterior. Uma vez que este padrão requer acesso só de leitura para secundária, necessita de replicação do Geo Active.

Gestor de tráfego deverá estar configurado para obter um desempenho encaminhamento para direcionar as ligações de utilizador para a instância da aplicação mais próxima da localização geográfica do utilizador. O diagrama seguinte ilustra esta configuração antes de uma falha.
![Sem indisponibilidade: desempenho encaminhamento mais próximo de aplicação. Replicação de geo.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se uma falha de base de dados é detectada na região principal, iniciar activação pós-falha da base de dados principal para uma das regiões secundárias, alterar a localização da base de dados principal. O Gestor de tráfego exclui o ponto final offline da tabela de encaminhamento automaticamente, mas continua a encaminhar o tráfego do utilizador final para as restantes instâncias onlinehttps. Uma vez que a base de dados principal é agora numa região diferente, todas as instâncias onlinehttps tem de alterar os respetivos cadeia de ligação de SQL de leitura e escrita para ligar para a nova página principal. É importante que faça esta alteração antes de iniciar a base de dados activação pós-falha. Cadeias de ligação de SQL só de leitura devem permaneça inalteradas à medida que sempre apontem para a base de dados na mesma região. Os passos de activação pós-falha são:  

1. Alterar as cadeias de ligação de SQL de leitura e escrita para apontar para a nova página principal.
2. Ligar a base de dados secundária designada ordem para a [base de dados inicie activação pós-falha](sql-database-geo-replication-portal.md).

O diagrama seguinte ilustra a nova configuração após a activação pós-falha.
![Configuração depois activação pós-falha. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

Em caso de uma falha de uma das regiões secundárias, o Gestor de tráfego automaticamente remove o ponto final offline nesse região a tabela de encaminhamento. O canal de replicação à base de dados secundária nesse região será suspensa. Uma vez que as regiões restantes obter o tráfego de utilizador adicionais neste cenário, desempenho a aplicação é afectado durante a indisponibilidade. Assim que a indisponibilidade é atenuada, a base de dados secundária na região afetada imediatamente é sincronizado com a página principal. Durante a sincronização de desempenho do computador primário poderia afectado ligeiramente consoante a quantidade de dados que tem de ser sincronizadas. O diagrama seguinte ilustra uma falha de uma das regiões secundárias.

![Falha na região secundário. Recuperação de falhas - Geo replicação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

A chave **partido** deste padrão de estrutura é que pode dimensionar a carga de trabalho de aplicação através de vários secundários para alcançar o desempenho ideal do utilizador final. São as **responsabilidades** desta opção:

+ Leitura / escrita as ligações entre instâncias da aplicação e base de dados têm latência e custo variados
+ Desempenho da aplicação é afectado durante a indisponibilidade
+ Instâncias da aplicação são necessárias para alterar a cadeia de ligação de SQL dinamicamente após activação pós-falha de base de dados.  

> [AZURE.NOTE] Uma abordagem semelhante pode ser utilizada para descarregar especializadas das cargas de trabalho como o relatório de tarefas, ferramentas de business intelligence ou cópias de segurança. Normalmente, estas cargas de trabalho consumam recursos de base de dados significativa, por isso, é recomendável que designar um das bases de dados secundárias para-los com o nível de desempenho correspondido para a carga de trabalho antecipada.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Padrão de estrutura 3: implementação activo passivo para preservação de dados  
Esta opção é mais adequada para as aplicações com as seguintes características:

+ Quaisquer perdas de dados são empresas alto risco. A base de dados activação pós-falha só pode ser utilizado como um último recurso se a indisponibilidade é permanente.
+ A aplicação pode funcionar no "modo só de leitura" por um período de tempo.

Neste padrão, a aplicação muda para modo só de leitura quando ligado à base de dados secundária. Lógica da aplicação na região primária encontra a cocriação com a base de dados principal e funciona no modo de leitura e escrita (RW). Lógica da aplicação na região secundário se encontra a com a base de dados secundária e está pronta para trabalhar com em modo só de leitura (RO).  Gestor de tráfego deve ser configurado para utilizar o [Encaminhamento de activação pós-falha](../traffic-manager/traffic-manager-configure-failover-routing-method.md) com [ponto final de monitorização](../traffic-manager/traffic-manager-monitoring.md) ativado para ambas as instâncias da aplicação.

O diagrama seguinte ilustra esta configuração antes de uma falha.
![Implementação de activo passivo antes de activação pós-falha. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando o Gestor de tráfego Deteta uma falha de conectividade à região de principal, muda automaticamente tráfego de utilizador para a instância da aplicação na região secundário. Com este padrão, é importante que que **não faça** iniciar activação de base de dados pós-falha após a indisponibilidade é detectada. A aplicação na região secundário está ativada e funciona no modo só de leitura com a base de dados secundária. Isto é ilustrado pelo diagrama seguinte.

![Falha do: A aplicação no modo só de leitura. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Assim que a indisponibilidade na região primária é atenuada, Gestor de tráfego Deteta o restauro de conectividade na região principal e muda o tráfego do utilizador voltar para a instância da aplicação na região principal. Essa instância de aplicação currículos e funciona no modo de leitura / escrita utilizando a base de dados principal.

> [AZURE.NOTE] Uma vez que este padrão requer acesso só de leitura para o secundário, é necessário Geo-a replicação do Active.

Em caso de uma falha na região secundário, o Gestor de tráfego assinala o ponto final aplicação na região primária apresenta o te102825420como degradado e o canal de replicação está suspensa. No entanto, esta indisponibilidade não impacto no desempenho da aplicação durante a falha do. Assim que a indisponibilidade é atenuada, a base de dados secundária imediatamente é sincronizado com a página principal. Durante a sincronização de desempenho do computador primário poderia afectado ligeiramente consoante a quantidade de dados que tem de ser sincronizadas.

![Falha do: Base de dados secundária. Recuperação de falhas de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Este padrão de estrutura tem várias **vantagens**:

+ Perda de dados-evita durante as falhas temporárias.
+ Não necessitam que implementar uma aplicação de monitorização, tal como a recuperação ter é acionada pelo Gestor de tráfego.
+ Tempo de inatividade depende apenas quão rapidamente o Gestor de tráfego Deteta a falha da conectividade, que é configurável.

As **concessões** são:

+ Aplicação tem de ser capaz de funcionar em modo só de leitura.
+ É necessário para dinamicamente mudar para o mesmo quando está ligado a uma base de dados só de leitura.
+ Retomar de operações de leitura / escrita requer recuperação da região principal, o que significa que o acesso a dados completo pode ser desactivado para horas ou até mesmo dias.

> [AZURE.NOTE] Em caso de falha de serviço permanente na região, manualmente tem de ativar activação pós-falha de base de dados e aceitar a perda de dados. A aplicação irá estar funcional na região secundário com acesso de leitura e escrita para a base de dados.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planeamento de continuidade do negócio: escolher uma estrutura de aplicação para recuperação de falhas na nuvem

Sua estratégia de recuperação de falhas de nuvem específico pode combinar ou expandir estes padrões de estrutura para melhor satisfazer as necessidades da sua aplicação.  Como mencionado anteriormente, a estratégia que escolher baseia SLA que pretende para oferecer os seus clientes e a topologia da aplicação de implementação. Para ajudar a orientar a sua decisão, a tabela seguinte compara as escolhas com base na perda de dados estimado ou recuperação aponte objectivo (RPO) e estimada o tempo de recuperação (ERTER).

| Padrão | RPO | ERTER
| :--- |:--- | :---
| Implementação de activo passivo para a recuperação de falhas com o access encontra a base de dados | Acesso de leitura / escrita < 5 seg. | Tempo de deteção de falhas + API activação pós-falha chamar + teste de verificação de aplicação
| Implementação de activo activo para balanceamento de carga de aplicação | Acesso de leitura / escrita < 5 seg. | Tempo de deteção de falhas + activação pós-falha API chamada + cadeia de ligação de SQL alterar + teste de verificação de aplicação
| Implementação de activo passivo para preservação de dados | Acesso só de leitura < 5 seg acesso de leitura / escrita = zero | Acesso só de leitura = tempo de deteção conectividade Falha + teste de verificação de aplicação <br>Acesso de leitura / escrita = tempo para mitigar a indisponibilidade

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
- Para obter informações sobre como ativa Geo-replicação com agrupamentos de flexível, consulte o artigo [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
