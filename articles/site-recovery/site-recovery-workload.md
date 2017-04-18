<properties
    pageTitle="O que das cargas de trabalho pode proteger com Azure recuperação de Site?"
    description="Recuperação de Site Azure protege a sua das cargas de trabalho e aplicações por coordenar a replicação, activação pós-falha e recuperação de máquinas virtuais de no local e servidores físicos para Azure ou para um site secundário no local"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>O que das cargas de trabalho pode proteger com Azure recuperação de Site?


Este artigo descreve das cargas de trabalho e aplicações que pode criar uma réplica com o serviço do Azure recuperação de sites.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia de recuperação (BCDR) de empresas continuidade e falhas, para manter das cargas de trabalho e os dados seguros e disponível durante o tempo de inatividade planeado e não e recuperar a normal condições de trabalho mais cedo possível.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR. Utilizar a recuperação de sites, pode implementar replicação deverá ter em consideração aplicação na nuvem, ou para um site secundário. Se as suas aplicações são janelas ou em execução no servidores físicos, VMware ou Hyper-V, baseado em Linux, pode utilizar a recuperação de Site para orquestrar replicação, efetue testes de recuperação de falhas e executar activações pós-falha e reposição de recurso.


Recuperação de sites integra-se com aplicações Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e do Active Directory. Microsoft também funciona estreitamente com fornecedores à esquerda, incluindo Oracle, SAP, IBM e chapéu vermelho. Pode personalizar soluções de replicação numa base de aplicação por aplicação.

## <a name="why-use-site-recovery-for-application-replication"></a>Porquê utilizar recuperação de Site para a replicação de aplicação?

Recuperação de sites contribui para o nível de aplicação proteção e recuperação da seguinte forma:

- Aplicação-desconhecido, fornecendo replicação para qualquer das cargas de trabalho em execução num computador suportado.
- Replicação junto à imagem, com RPOs tão baixos quanto 30 segundos para satisfazer as necessidades de aplicações empresariais mais importantes.
- Aplicação consistentes instantâneos, para aplicações única ou várias camadas.
- Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação de nível de aplicação, a incluindo replicação de AD, a SQL AlwaysOn, grupos de disponibilidade de base de dados do Exchange (DAGs) e atento de dados Oracle.
- Os planos de recuperação flexível, que permitem-lhe recuperar a pilha de uma aplicação completo com um único clique e incluir para incluir scripts externos e ações manuais no plano de.
- Gestão de rede avançadas no Azure e recuperação de sites para simplificar a requisitos da aplicação rede, incluindo a capacidade de reserva endereços IP, configure balanceamento de carga e integração com o Azure tráfego Gestor, para baixos switchovers de rede RTO.
-  Uma biblioteca de automatização avançada que fornece pronto para produção, específicos da aplicação scripts que podem ser transferidos e integrados com os planos de recuperação.



## <a name="workload-summary"></a>Resumo de carga de trabalho

Recuperação de sites pode criar uma réplica qualquer aplicação em execução num computador suportado. Além disso podemos tenha associou equipas de produto para realizar testes de aplicação específicas adicionais.

**Carga de trabalho** | **Criar uma réplica VMs Hyper-V para um site secundário** | **Criar uma réplica VMs Hyper-V para Azure** | **Criar uma réplica VMware VMs para um site secundário** | **Criar uma réplica VMware VMs para Azure**
---|---|---|---|---
DNS no Active Directory, | Y | Y | Y | Y
Aplicações Web (IIS, SQL) | Y | Y | Y | Y
Gestor de operações do Centro de sistema | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Criar uma réplica da SAP site para Azure para que não sejam cluster | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft)
Exchange (que não sejam Comprimentos) | Y | Brevemente | Y | Y
Ambiente de trabalho remoto/VDI | Y | Y | Y | N/D
Linux (sistema operativo e apps) | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Brevemente | Y | Brevemente
Oracle | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft) | Y (testada pela Microsoft)
Servidor de ficheiros do Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Criar uma réplica do Active Directory e DNS

Um Active Directory e infraestrutura DNS são essenciais para a maioria das aplicações empresariais. Durante a recuperação de falhas, terá de proteger e recuperar estes componentes de infraestrutura, antes de recuperar a sua das cargas de trabalho e apps.

Pode utilizar recuperação de sites para criar um plano de recuperação de falhas automatizado completa para do Active Directory e DNS. Por exemplo, se pretender que provocam a falha ao longo do SharePoint e SAP a partir de um principal para um site secundário, pode configurar um plano de recuperação falha ao longo do Active Directory pela primeira vez e, em seguida, um plano de recuperação de aplicação específicas adicionais a falha ao longo de outras aplicações que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteger do SQL Server

SQL Server fornece uma Fundação de serviços de dados para serviços de dados para aplicações empresariais muitos num centro de dados no local.  Recuperação de sites pode ser utilizada juntamente com o SQL Server HA/DR tecnologias, para proteger as aplicações de várias camadas enterprise que utilizam o SQL Server. Fornece a recuperação de sites:

- Uma solução de recuperação de falhas simples e rentável para SQL Server. Criar uma réplica da várias versões e edições do SQL Server autónomo servidores de e clusters, para Azure ou para um site secundário.  
- Integração com grupos de Disponibilidade AlwaysOn SQL, para gerir activação e reposição de recurso com os planos de recuperação Azure recuperação de sites.
- Planos de recuperação de fim para fim para todas as camadas numa aplicação, incluindo as bases de dados do SQL Server.
- Dimensionamento do SQL Server para pico carrega com a recuperação de Site, por "de segurança"-los para tamanhos de máquina virtual IaaS maiores no Azure.
- Mais facilmente testes de recuperação de falhas do SQL Server. Pode executar activações pós-falha de teste para analisar dados e executar verificações de conformidade, sem que afetam o ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger do SQL server.

##<a name="protect-sharepoint"></a>Proteger o SharePoint

Recuperação de Site Azure ajuda a proteger implementações do SharePoint, da seguinte forma:

- Elimina a necessidade de e infraestrutura associada custos de um farm frutas pela recuperação de falhas. Utilize a recuperação de Site para criar uma réplica um farm inteiro (camadas Web, a aplicação e a base de dados) para Azure ou para um site secundário.
- Simplifica a implementação de aplicações e gestão. Atualizações implementadas para o site principal são replicadas automaticamente e, por conseguinte, estão disponíveis após activação e recuperação de um farm de um site secundário. Também diminui a complexidade de gestão e os custos associados manter um farm de frutas por atualizado.
- Simplifica desenvolvimento de aplicações do SharePoint e testes através da criação de um ambiente da pedido réplica cópia gosto de produção para testar e depurar.
- Simplifica a transição para a nuvem ao utilizar a recuperação de sites para migrar implementações do SharePoint para o Azure.

[Saiba mais](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sobre como proteger SharePoint.


## <a name="protect-dynamics-ax"></a>Proteger Dynamics AX

Recuperação de Site Azure ajuda a proteger a sua solução Dynamics AX ERP, por:

- Orquestrar replicação de todo o seu Dynamics AX ambiente (camadas Web e AOS, camadas de base de dados, SharePoint) para Azure ou para um site secundário.
- Simplificar a migração de implementações do Dynamics AX na nuvem (Azure).
- Simplificar os desenvolvimento de aplicações do Dynamics AX e testes através da criação de uma cópia de produção semelhante a pedido, para testar e depurar.

[Saiba mais](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sobre como proteger AX dinâmicos.

## <a name="protect-rds"></a>Proteger RDS

Serviços de ambiente de trabalho remoto (RDS) ativa a infraestrutura de ambiente de trabalho virtual (VDI), com base em sessão ambientes de trabalho e aplicações, permitindo que os utilizadores trabalhar em qualquer lugar. Com o Azure recuperação de sites, pode:

- Criar uma réplica geridas ou agrupados virtuais ambientes de trabalho para um site secundário, aplicações remotas sessões e e para um site secundário ou Azure.
- Eis o pode criar uma réplica:

**RDS** | **Criar uma réplica VMs Hyper-V para um site secundário** | **Criar uma réplica VMs Hyper-V para Azure** | **Criar uma réplica VMware VMs para um site secundário** | **Criar uma réplica VMware VMs para Azure** | **Criar uma réplica servidores físicos para um site secundário** | **Criar uma réplica servidores físicos para Azure**
---|---|---|---|---|---|---
**Agrupado ambiente de Trabalho Virtual (não geridos)** | Sim | N | Sim | N | Sim | N
**Agrupado ambiente de trabalho em Virtual (gerida e sem Act)** | Sim | N | Sim | N | Sim | N
**Aplicações remotas e sessões de ambiente de trabalho (sem Act)** | Sim | Sim | Sim | Sim | Sim | Sim


[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger RDS.


## <a name="protect-exchange"></a>Proteger Exchange

Recuperação de site ajuda a proteger o Exchange, da seguinte forma:

- Para pequenas implementações do Exchange, tal como uma única ou autónomo servidores, recuperação de sites pode criar uma réplica e falhar sobre para Azure ou para um site secundário.
- Para implementações maiores, recuperação de sites integra-se com o Exchange DAGS.
- Exchange DAGs são a solução recomendada para recuperação de falhas do Exchange numa empresa.  Planos de recuperação de recuperação do site podem incluir DAGs, para orquestrar Comprimentos activação pós-falha através de sites.


[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger Exchange.

## <a name="protect-sap"></a>Proteger SAP

Utilize a recuperação de Site para proteger a sua implementação SAP, da seguinte forma:

- Ative proteção de implementação de SAP inteira, através da replicação camadas de implementação diferentes para Azure ou para um site secundário.
- Simplificar a migração de nuvem, utilizando recuperação de sites para migrar a sua implementação SAP para Azure.
- Simplificar o SAP desenvolvimento e teste, criando um gosto de produção, copie a pedido para testar e depuração de aplicações.

[Saiba mais](http://aka.ms/asr-sap) sobre como proteger SAP.

## <a name="next-steps"></a>Próximos passos

[Preparar para a implementação de recuperação de sites](site-recovery-best-practices.md) 
