<properties
   pageTitle="Serviço de orientação RDP | Microsoft Azure"
   description="Ligações para recuperação de falhas e pro-activos RDP e disponibilidade seta de quatro pontas dos serviços do Microsoft Azure."
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

# <a name="microsoft-azure-service-resiliency-guidance"></a>Orientação do Microsoft Azure service RDP
Microsoft Azure foi concebido para fornecer-lhe os recursos que necessita, quando precisar deles. Como parte do boa estrutura e as práticas operacionais avançadas, que deve saber tanto como arquitectar a utilização dos serviços Azure para alcançar elevada disponibilidade, bem como o que fazer se a aplicação é afetada por uma interrupção de serviço. Para ajudar a este processo, este documento contém ligações para orientação de recuperação de falhas, bem como a orientação de projeto para vários serviços Azure.

##<a name="disaster-recovery-guidance"></a>Orientação de recuperação de falhas
As informações de recuperação de falhas ligações em baixo são podem fornecer-lhe as informações necessárias para ajudá-lo rapidamente a sua aplicação novamente online se são afetados por uma interrupção de serviço Azure. Estas ligações foram criadas para o ajudar a responder à pergunta, "Estou a ser afetada por uma interrupção de serviço Azure, o que posso fazer?"

##<a name="design-guidance"></a>Orientação de projeto
As ligações de orientação de estrutura abaixo são estrutura e orientações arquitetura que foi criada para ajudá-lo a compreender melhor como utilizar cada serviço Azure de uma forma que maximize tempo de utilização da sua aplicação. Estas ligações foram criadas para o ajudar a responder à pergunta "Como posso Certifique-se de que um erro, uma falha de hardware, interrupção de serviço ou outra falha não causam impacto na disponibilidade geral da minha aplicação?" Se não existirem sem orientações específicas para o serviço que está atualmente a procurar, o artigo [elevada disponibilidade para as aplicações criadas no Microsoft Azure](./resiliency-high-availability-azure-applications.md) poderá ter informações adicionais que podem ajudá-lo na sua estrutura. 

##<a name="service-guidance"></a>Orientação do serviço
| Serviço  | Orientação de recuperação de falhas | Orientação de projeto |
|:---------|:--------------------------:|:------------------:|
| [Serviços em nuvem] (https://azure.microsoft.com/services/cloud-services/ "Azure serviços em nuvem")       | [ligação] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Orientação de recuperação de falhas de serviços em nuvem Azure")   | Não disponível |
| [Cofre chave] (https://azure.microsoft.com/services/key-vault/ "Azure cofre chave")                      | [ligação] (../key-vault/key-vault-disaster-recovery-guidance.md "Orientação de recuperação de falhas Cofre de chave do Azure")        | Não disponível |
| [Armazenamento] (https://azure.microsoft.com/services/storage/ "Armazenamento Azure")                            | [ligação] (../storage/storage-disaster-recovery-guidance.md "Orientação de recuperação de falhas de armazenamento do Windows Azure")          | Não disponível |
| [Bases de dados SQL] (https://azure.microsoft.com/services/sql-database/ "Bases de dados Azure SQL")           | [ligação] (../sql-database/sql-database-disaster-recovery.md  "Orientação de recuperação de falhas de base de dados do SQL Azure")    | [ligação] (../sql-database/sql-database-business-continuity.md "Descrição geral de continuidade do negócio com base de dados do SQL Azure") |
| [Máquinas virtuais] (https://azure.microsoft.com/services/virtual-machines/ "Azure máquinas virtuais") | [ligação] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Orientação de recuperação de falhas máquinas virtuais do Azure") | Não disponível |
| [Rede virtual] (https://azure.microsoft.com/services/virtual-network/ "Azure rede Virtual")    | [ligação] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Orientação de recuperação de falhas de rede Virtual do Azure")  | Não disponível |

##<a name="next-steps"></a>Próximos passos
Se estiver à procura de orientação foca-se mais amplamente em sistemas e soluções, leia [recuperação de falhas e elevada disponibilidade para aplicações criadas no Microsoft Azure](https://aka.ms/drtechguide).
