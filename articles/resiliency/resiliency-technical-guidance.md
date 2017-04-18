<properties
   pageTitle="Índice de orientação técnica RDP | Microsoft Azure"
   description="Índice de artigos técnicos no Noções sobre e estruturar e são, altamente disponível, aplicações tolerância a falhas, bem como o planeamento de continuidade de recuperação e empresas de falhas"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Orientação técnica do Azure RDP

##<a name="introduction"></a>Introdução

Requisitos de recuperação de falhas e elevada disponibilidade da reunião necessita de dois tipos de dados de conhecimento:

- Compreender técnico detalhada das capacidades de uma plataforma na nuvem
- Dados de conhecimento de como corretamente Arquitectar um serviço distribuído

Esta série de artigos abrange antiga: as funcionalidades e limitações da plataforma Azure relativamente às RDP (por vezes denominada continuidade do negócio). Se está interessado nestes últimos, consulte o artigo a série de artigo com o foco nas [recuperação de falhas e elevada disponibilidade para aplicações do Azure](https://aka.ms/drtechguide). Apesar desta série de artigo toca em padrões de arquitectura e estrutura, que não está o foco da série. Para obter orientações de estrutura, pode consultar o material na secção de [recursos adicionais](#additional-resources) .

As informações estão organizadas para os seguintes artigos:

- [Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).
Pode falhar hardware físico (por exemplo, unidades de, servidores e dispositivos de rede). Recursos podem ser esgotados quando carga pontas. Este artigo descreve as funcionalidades que o Azure disponibiliza para manter a disponibilidade de alta sob as seguintes condições.

- [Recuperação a partir de uma interrupção de serviço de toda a região Azure](resiliency-technical-guidance-recovery-loss-azure-region.md).
Falhas executarão são raras, mas são teoricamente possíveis. Regiões todas podem tornar-se isolados devido a falhas de rede ou pode estar danificadas física catástrofes naturais. Este artigo explica como utilizar o Azure para criar aplicações que aparecem em regiões geograficamente diversificados.

- [Recuperação no local para Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Nuvem altera significativamente economics de recuperação de falhas, permitindo-organizações a utilizar o Azure para estabelecer um segundo site para a recuperação. Pode fazê-lo numa fração do custo de construir e manter um centro de dados secundário. Este artigo explica as funcionalidades que fornece Azure para expandir um centro de dados no local para a nuvem.

- [Recuperação de danos nos dados ou acidentais](resiliency-technical-guidance-recovery-data-corruption.md).
Aplicações podem ter erros esses dados danificados. Operadores de forma incorreta podem eliminar dados importantes. Este artigo explica o que fornece Azure para criar cópias de segurança dos dados e restaurar a um ponto anterior tempo.

##<a name="additional-resources"></a>Recursos adicionais

- [Recuperação de falhas e elevada disponibilidade para aplicações criadas no Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
Este artigo é uma descrição geral detalhada de disponibilidade e recuperação de falhas. Cobrir o desafio de replicação manual para referência e dados transaccionais. As secções finais fornecem resumos dos diferentes tipos de topologias de recuperação de falhas que aparecem em regiões Azure para o nível mais alto de disponibilidade.

- [Lista de verificação de disponibilidade como sendo de alta](resiliency-high-availability-checklist.md).
Este artigo é uma lista de funcionalidades, serviços, e estruturas de que podem ajudá-lo aumentem a RDP e disponibilidade da sua aplicação.

- [Orientação do Microsoft Azure service RDP](resiliency-service-guidance-index.md).
Este artigo é um índice remissivo dos serviços Azure e fornece ligações para falhas recuperação de orientação e orientação de design.

- [Descrição geral: na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md).
Este artigo fornece técnicas de base de dados do Azure SQL para disponibilidade. Centra principalmente no estratégias de cópia de segurança e restauro. Se utilizar a base de dados do SQL Azure no seu serviço de nuvem, deverá rever deste artigo e os seus recursos relacionados.

- [Recuperação de falhas para SQL Server em máquinas virtuais do Azure e disponibilidade de alta](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
Este artigo aborda as opções de disponibilidade que pode explorar ao utilizar infraestrutura como um serviço (IaaS) para os seus serviços de base de dados do anfitrião. Fala sobre grupos de Disponibilidade AlwaysOn, espelhando as bases de dados, envio do registo e cópia de segurança/restauro. Tutoriais várias mostram como utilizar estas técnicas.

- [Melhores práticas para criar serviços em grande escala no Azure serviços em nuvem](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
Este artigo foca-se em desenvolver arquitecturas altamente dimensionáveis na nuvem. Muitas das técnicas que utilizam para melhorar a escalabilidade também melhorar a disponibilidade. Além disso, se a aplicação não é possível escala em caso de carga maior, escalabilidade torna-se um problema de disponibilidade.

- [Cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
Este artigo fornece orientações técnicas sobre como criar cópias de segurança e restaurar o Microsoft SQL Server em máquinas virtuais do Azure de executar.

- [Isenta de falhas: seta de quatro pontas arquitecturas de nuvem e são](https://channel9.msdn.com/Series/FailSafe).
Este artigo fornece orientações para a criação de arquitecturas de nuvem e são orientações para esses arquitecturas de aplicação tecnologias Microsoft e receitas de execução destes arquitecturas de cenários específicos.

- [Técnico caso prático da Microsoft: utilizar tecnologias de nuvem para melhorar a recuperação de falhas](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Neste caso prático mostra como o Microsoft IT utilizado Azure para melhorar a recuperação de falhas.

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série com o foco nas orientações técnicas para RDP Azure. Se está interessado em outros artigos nesta série de leitura, pode começar com [a recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).
