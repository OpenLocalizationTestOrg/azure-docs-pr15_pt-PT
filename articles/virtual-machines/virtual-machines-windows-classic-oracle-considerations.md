<properties
pageTitle="Considerações para utilizar imagens de Oracle VM | Microsoft Azure"
description="Saiba mais sobre configurações suportadas e limitações para uma VM Oracle no Windows Server no Azure antes de implementar."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Considerações de diversas para imagens de máquina virtual Oracle



Este artigo aborda considerações para máquinas virtuais Oracle no Azure, que são baseadas nas imagens de software Oracle fornecidas pela Oracle.  

-  Imagens de máquina virtual de base de dados Oracle
-  Imagens de máquina virtual do WebLogic servidor Oracle
-  Imagens de máquina virtual do JDK Oracle

##<a name="oracle-database-virtual-machine-images"></a>Imagens de máquina virtual de base de dados Oracle
### <a name="clustering-rac-is-not-supported"></a>Não é suportada clusters (RAC)

Azure atualmente não suporta da base de dados Oracle, a Oracle Real aplicação Clusters (RAC). Apenas autónomo instâncias de base de dados Oracle são possíveis. Isto acontece porque Azure atualmente não suporta virtual disco partilha de um modo de leitura/escrita entre várias instâncias de máquina virtual. Multicast UDP também não é suportada.

### <a name="no-static-internal-ip"></a>Sem IP estático interno

Azure atribui a cada máquina virtual um endereço IP interno. A menos que a máquina virtual fizer parte de uma rede virtual, o endereço IP da máquina virtual é dinâmico e pode ser alterada depois de ser reiniciado máquina virtual. Isto pode causar problemas de uma vez que a base de dados Oracle espera o endereço IP a ser estático. Para evitar este problema, recomendamos adicionar máquina virtual a uma rede Virtual Azure. Para mais informações, consulte [criar uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) e de [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) .

### <a name="attached-disk-configuration-options"></a>Opções de configuração do disco ligado

Pode colocar os ficheiros de dados no disco de sistema operativo da máquina virtual ou no discos anexados, também conhecido como discos de dados. Discos ligados poderão oferecem flexibilidade melhor do desempenho e o tamanho que o disco do sistema operativo. O disco do sistema operativo poderá ser preferível apenas para bases de dados em 10 gigabytes (GB).

Discos ligados dependem o serviço de armazenamento de Blobs do Azure. Cada disco é capaz de um teórico máximo de operações de entrada/saída aproximadamente 500 por segundo (IOPS). O desempenho do discos ligados poderão não estar ideal inicialmente e IOPS o desempenho pode melhorar consideravelmente após um período "gravar-in" de cerca de 60 90 minutos de operação contínua. Se um disco subsequentemente permanece inactivo, poderá atenuar desempenho IOPS até outro gravar no período de operação contínua. Resumindo, o active mais de um disco, o mais provável é um desempenho IOPS ideal abordagem.

Apesar da abordagem mais simples é anexar um único disco à máquina virtual e colocar ficheiros de base de dados nesse disco, esta abordagem é também mais limitação em termos de desempenho. Em vez disso, muitas vezes pode melhorar o desempenho de IOPS eficaz se utilizar vários discos anexados, distribuir a base de dados por-los e, em seguida, utilize a gestão de armazenamento automáticas da Oracle (ASM). Consulte o artigo [Descrição geral de armazenamento automático Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações. Apesar de poder utilizar repartição de vários discos ao nível do sistema operativo, esse abordagem não é aconselhada porque não é conhecido para melhorar o desempenho IOPS.

Considere duas abordagens diferentes para anexar vários discos com base em se pretende atribuir prioridades o desempenho de operações de leitura ou escrita operações para a sua base de dados:

- É provável que resultam melhor desempenho da operação de escrita, mas pior IOPS para operações de leitura em comparação com a abordagem com matrizes de discos **Oracle ASM na sua própria** . A ilustração seguinte ilustra logicamente este disposição.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Avaliar o compromisso entre o desempenho de escrita e desempenho de leitura caso a caso. Os seus resultados reais podem variar quando utiliza esta.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de recuperação de disponibilidade e falhas alta

Ao utilizar a base de dados Oracle em máquinas virtuais do Azure, é responsável para implementar uma solução de recuperação de disponibilidade e falhas elevada para evitar qualquer tempo de inatividade. Também é responsável por cópias de segurança seus próprios dados e de aplicações.

Alta recuperação de disponibilidade e falhas do Oracle da base de dados Enterprise Edition (sem RAC) no Azure pode ser obtida com [dados atento, ativa dados atento](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)ou [Oracle Golden porta](http://www.oracle.com/technetwork/middleware/goldengate), com duas bases de dados em dois máquinas virtuais em separado. Ambas as máquinas virtuais deverá estar no mesmo [serviço na nuvem](virtual-machines-linux-classic-connect-vms.md) e a mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para se certificar de que podem aceder entre si através o endereço IP persistente privado.  Para além disso, recomendamos que a colocar as máquinas virtuais no mesmo [conjunto de disponibilidade](virtual-machines-windows-manage-availability.md) para permitir que o Azure colocá-los para domínios falhas separadas e actualizar domínios. O mesmo conjunto de disponibilidade podem participar apenas máquinas virtuais no serviço de nuvem do mesmo. Cada máquina virtual tem de ter pelo menos de 2 GB de memória e 5 GB de espaço em disco.

Com atento de dados Oracle, elevada disponibilidade pode ser realizada com uma base de dados principal uma máquina de virtual, uma base de dados (em espera) secundário na outra máquina virtual e a replicação unidirecional configurar entre elas. O resultado é acesso de leitura para a cópia da base de dados. Com Oracle GoldenGate, pode configurar a replicação de bidirecional entre as duas bases de dados. Para saber como configurar uma solução de alta disponibilidade para as bases de dados utilizando estas ferramentas, consulte a documentação do [Atento de dados ativo](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) no Web site da Oracle. Se o precisa de leitura e escrita acesso para a cópia da base de dados, pode utilizar [Oracle ativo dados atento](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquina virtual do WebLogic servidor Oracle

-  **Clusters só são suportado no Enterprise Edition.** Está licenciado para utilizar WebLogic clusters apenas quando utilizar o Enterprise Edition de WebLogic servidor. Não utilize clusters com WebLogic Server Standard Edition.

-  **Tempos limite de ligação:** Se a sua aplicação baseia-se nas ligações para os pontos finais públicos de outro serviço de nuvem Azure (por exemplo, um serviço de camada de base de dados), o Azure poderá Feche estas ligações abrir depois quatro minutos de inatividade. Pode afectar funcionalidades e aplicações depender de conjuntos de ligação, uma vez que as ligações que são inactivas por mais do que esse limite poderão já não permanecem válidas. Se isso afeta a sua aplicação, considere ativar lógica "persistentes" no seu conjuntos de ligação.

    Se um ponto final é *interna* para a implementação de serviço de nuvem Azure (tal como máquina de virtual de base de dados autónomo dentro do serviço na nuvem *mesmo* como máquinas virtuais WebLogic), em seguida, a ligação é direta e não confiar no balanceador de carga Azure e, consequentemente, não está sujeito um tempo limite da ligação.

-  **UDP multicast não é suportada.** Azure suporta a transmissão UDP, mas não multicasting ou difusão. É possível depender de funcionalidades do Azure UDP unicast WebLogic servidor. Para melhores resultados depender de UDP unicast, recomendamos que o tamanho do cluster WebLogic ser mantidos estático, ou ser mantidos com mais do que 10 servidores geridos incluídos no cluster.

-  **Servidor de WebLogic espera portas públicas e privadas para ser o mesmo para acesso T3 (por exemplo, ao utilizar Enterprise JavaBeans).** Considere a hipótese de um cenário de várias camado onde uma aplicação de serviço de camada (EJB) está em execução num cluster de servidor de WebLogic composto por duas ou mais servidores geridos, num serviço de nuvem denominada **SLWLS**. A camada do cliente está num serviço de nuvem diferente, executar um programa de Java simple a tentar ligar EJB na camada de serviço. Porque é necessário para a camada de serviço de balanceamento de carga, um público balanceamento de carga ponto final de tem de ser criada para as máquinas virtuais do cluster de servidor de WebLogic. Se a porta privada que especificou para esse ponto final é diferente da porta pública (por exemplo, 7006:7008), ocorre um erro como as seguintes:

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    Isto acontece porque para qualquer acesso T3 remoto, servidor WebLogic espera a porta do Balanceador de carga e a porta do servidor WebLogic gerido como sendo o mesmo. No caso acima, o cliente está a aceder a porta 7006 (a porta do Balanceador de carga) e o servidor gerido está a escutar 7008 (a porta privada). Esta restrição é aplicável apenas para o access T3, não HTTP.

    Para evitar este problema, utilize um dos seguintes soluções:

    -  Utilize os mesmos números de porta públicas e privadas para os pontos finais de balanceamento de carga dedicados para o T3 access.

    -  Inclua o parâmetro JVM seguinte quando iniciar o servidor de WebLogic:

            -Dweblogic.rjvm.enableprotocolswitch=true

Para obter informações relacionadas, consulte o artigo BDC artigo **860340.1** na <http://support.oracle.com>.

-  **Dinâmicas clusters e limitações de balanceamento de carga.** Imaginemos que pretende utilizar um cluster dinâmico no WebLogic Server e expô-la através de um único e público balanceamento de carga ponto final no Azure. Isto pode ser feito desde que utiliza um número de porta fixo para cada um dos servidores geridos (não dinamicamente atribuídos a partir de um intervalo) e não está a iniciar mais servidores geridos que existem máquinas o administrador está a registar (ou seja, mais do que um geridos por máquina virtual do servidor). Se a sua configuração resulta no mais WebLogic servidores que está a ser iniciados que existem máquinas virtuais (ou seja, onde várias instâncias de servidor de WebLogic partilham a mesma máquina de virtual), em seguida, não é possível para mais do que um às instâncias do servidor de WebLogic servidores para vincular a um determinado número de porta – a outras pessoas em que a máquina virtual irão falhar.

    Por outro lado, se configurar o servidor de administrador para atribuir automaticamente números de porta exclusivo aos seus servidores geridos, em seguida, balanceamento de carga não é possível porque o Azure não suporta mapeamento a partir de uma única porta público para múltiplas portas privados, tal como seria necessário para esta configuração.

-  **Várias instâncias na barra de Weblogic uma máquina virtual.** Dependendo requisitos da sua implementação, recomendamos a opção de executar várias instâncias do servidor de WebLogic no mesmo computador virtual, se for grande o suficiente para a máquina virtual. Por exemplo, num computador virtual tamanho médio, que contém dois núcleos, pode escolher executar duas instâncias do servidor de WebLogic. Note no entanto que ainda, recomendamos que evite introduzirem pontos de falha únicos no seu arquitetura, que seria as maiúsculas/minúsculas se tiver utilizado apenas uma máquina de virtual que esteja a executar várias instâncias do servidor de WebLogic. Utilizar, pelo menos, duas máquinas virtuais pode ser uma abordagem melhor e, em seguida, cada uma das máquinas virtuais poderia executar várias instâncias do servidor de WebLogic. Cada um destas instâncias do servidor de WebLogic ainda pode ser parte do mesmo cluster. Nota, no entanto,-atualmente não é possível utilizar o Azure para os pontos finais de balanceamento de carga que são expostos por essas implementações de servidor de WebLogic dentro da mesma máquina de virtual, uma vez que Balanceador de carga Azure requer os servidores de balanceamento de carga para ser distribuído entre máquinas virtuais exclusivas.

##<a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual do JDK Oracle

-  **JDK 6 e 7 atualizações mais recentes.** Enquanto Recomendamos que utilize a versão mais recente pública suportada do Java (atualmente Java 8), Azure também disponibiliza JDK 6 e 7 imagens. Este passo destina-se a aplicações legadas que ainda não estão prontas para ser atualizada JDK 8. Enquanto as atualizações às imagens JDK anterior poderão já não estar disponíveis ao público em geral, tendo em conta a parceria de Microsoft com Oracle, o JDK 6 e 7 imagens fornecidas pela Azure destinam-se para conter uma atualização não público mais recente, que é normalmente oferecida pelo Oracle a apenas um grupo de clientes suportados da Oracle. Novas versões das imagens JDK serão disponibilizadas ao longo do tempo com versões atualizadas do JDK 6 e 7.

    Só pode ser utilizado o JDK disponível neste JDK 6 e 7 imagens e o máquinas virtuais e imagens derivadas de-las, dentro do Azure.

-  **JDK 64 bits.** As imagens de máquina virtual Oracle WebLogic servidor e as imagens de máquina virtual Oracle JDK fornecidas pela Azure contêm as versões de 64 bits do Windows Server e o JDK.

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de máquina virtual Oracle para Azure](virtual-machines-linux-classic-oracle-images.md)
