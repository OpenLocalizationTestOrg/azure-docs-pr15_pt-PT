<properties
   pageTitle="Orientação técnica do RDP para recuperar a partir de danos nos dados ou acidentais | Microsoft Azure"
   description="Artigo relativa Noções básicas sobre como recuperar de danos nos dados de dados ou eliminação de dados acidentais para e conceber aplicações tolerância a falhas de falhas e são, altamente disponível, bem como planear a recuperação de falhas"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Orientação técnica do Azure RDP: recuperação de danos nos dados ou acidentais

Parte de um plano de continuidade do negócio robusta está a ter um plano se os seus dados obtém danificados ou eliminados acidentalmente. Segue-se informações sobre a recuperação depois de dados foi danificados ou eliminados acidentalmente, devido a erros de aplicação ou de erro do operador.

##<a name="virtual-machines"></a>Máquinas virtuais

Para proteger a sua máquinas virtuais do Azure (por vezes denominada VMs infraestrutura-como-a-service) a partir de erros da aplicação ou acidentais, utilize o [Azure cópia de segurança](https://azure.microsoft.com/services/backup/). Cópia de segurança Azure permite a criação de cópias de segurança que sejam consistentes em vários discos VM. Além disso, o Cofre de cópia de segurança podem ser replicado entre as regiões para fornecer a recuperação de perda de região.

##<a name="storage"></a>Armazenamento

Tenha em atenção que enquanto armazenamento do Windows Azure fornece RDP de dados através de réplicas automatizadas, tal não impede o código da aplicação (ou os programadores/utilizadores) a partir do danificar dados através de eliminação indesejada ou acidental, atualização e assim sucessivamente. Manutenção de fidelidade de dados no erro de aplicação ou utilizador requer técnicas mais avançadas, tais como copiar os dados para uma localização de armazenamento secundário com um registo de auditoria. Os programadores podem tirar partido das blob [capacidade instantâneo](https://msdn.microsoft.com/library/azure/ee691971.aspx), que pode criar só de leitura em qualquer altura instantâneos dos conteúdos de Blobs. Isto pode ser utilizado como a base de uma solução de dados fidelidade para blobs de armazenamento do Windows Azure.

###<a name="blob-and-table-storage-backup"></a>Blob e cópia de segurança de armazenamento de tabela

Enquanto blobs e as tabelas são resistentes altamente, sempre que representam o estado atual dos dados. Recuperação de indesejados modificação ou eliminação de dados pode exigir restaurar os dados para um estado anterior. Isto pode ser obtido tirando partido das capacidades de fornecida pela Azure para armazenar e manter cópias em qualquer altura.

Para Blobs do Azure, pode efetuar cópias de segurança no momento utilizando a [funcionalidade de instantâneo blob](https://msdn.microsoft.com/library/ee691971.aspx). Para cada instantâneo, apenas são cobradas para o armazenamento necessário para armazenar as diferenças dentro do blob desde o último instantâneo de estado. Os instantâneos estão dependentes relativo à existência do blob original que quais se baseiam, por isso é aconselhável uma operação de copiar para outro blob ou até mesmo outra conta de armazenamento. Este procedimento assegura que os dados de cópia de segurança está corretamente protegidos contra acidentais. Para tabelas Azure, pode fazer cópias de ponto-in-time para uma tabela diferente ou para Blobs do Azure. Mais detalhadas de orientação e exemplos de efetuar cópias de segurança de nível da aplicação de tabelas e blobs podem ser encontradas aqui:

  * [Proteger as suas tabelas contra erros de aplicação](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Proteger os seus Blobs contra erros de aplicação](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Base de dados

Existem várias opções de [continuidade do negócio](../sql-database/sql-database-business-continuity.md) (cópia de segurança, restaurar) disponíveis para a base de dados do SQL Azure. Podem ser copiados bases de dados utilizando a funcionalidade de [Cópia de base de dados](../sql-database/sql-database-copy.md) ou ao [Exportar](../sql-database/sql-database-export.md) e [Importar](https://msdn.microsoft.com/library/hh710052.aspx) um ficheiro de bacpac do SQL Server. Cópia da base de dados fornece resultados consistentes, enquanto que não um bacpac (através do serviço de importação/exportação). Ambas as opções executar como serviços baseados na fila de espera no Centro de dados do e não fornecem atualmente SLA um hora para conclusão.

>[AZURE.NOTE]As opções de cópia e importar/exportar de base de dados coloque um grau significativo de carga da base de dados de origem. Podem accionar contenção de recursos ou limitação de eventos.

###<a name="sql-database-backup"></a>Cópia de segurança da base de dados SQL

Em qualquer altura cópias de segurança para base de dados do Microsoft Azure SQL são realizadas ao [copiar a base de dados do Azure SQL](../sql-database/sql-database-copy.md). Pode utilizar este comando para criar uma cópia de uma base de dados consistente no mesmo servidor de base de dados lógico ou para um servidor diferente. Em ambos os casos, a cópia da base de dados é completamente funcional e completamente independentes da base de dados de origem. Cada cópia que criar representa uma opção de recuperação em qualquer altura. Pode recuperar o estado de base de dados completamente alterando a nova base de dados com o nome da base de dados de origem. Em alternativa, pode recuperar um subconjunto específico de dados a partir de nova base de dados através de consultas de Transact-SQL. Para detalhes adicionais sobre a base de dados SQL, consulte o artigo [Descrição geral de continuidade do negócio com base de dados do SQL Azure](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server em máquinas virtuais cópia de segurança

Para o SQL Server utilizado com infraestrutura Azure como um máquinas virtuais de serviço (muitas vezes denominado IaaS ou IaaS VMs), existem duas opções: tradicionais cópias de segurança e envio de registo. Utilizar as cópias de segurança tradicionais permite-lhe restaurar a um ponto específico no tempo, mas o processo de recuperação é lento. O restauro cópias de segurança tradicionais requer começar com uma cópia de segurança completa inicial e, em seguida, aplicar qualquer tomadas após que as cópias de segurança. A segunda opção é configurar um registo de envio sessão para atrasar o restauro das cópias de segurança do registo (por exemplo, por duas horas). Este procedimento fornece uma janela para recuperar a partir de erros feitos na página principal.

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Azure

Alguns serviços de plataforma Azure armazenam informações numa conta de armazenamento controlado pelo utilizador ou base de dados do SQL Azure. Se o recurso de conta ou de armazenamento é eliminado ou danificado, este pode causar erros graves com o serviço. Nestes casos, é importante manter cópias de segurança que iria permitem-lhe criar estes recursos se foram eliminados ou danificados.

Para Azure Web Sites e serviços de Mobile Azure, tem de fazer cópia de segurança e manter as bases de dados associadas. Para o serviço de multimédia do Azure e máquinas virtuais, tem de manter a conta de armazenamento do Windows Azure associada e todos os recursos nessa conta. Por exemplo, para máquinas virtuais, deve fazer cópia de segurança e gerir os discos VM no armazenamento de Blobs do Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Listas de verificação para danos nos dados ou acidentais

##<a name="virtual-machines-checklist"></a>Lista de verificação máquinas virtuais

  1. Reveja a secção máquinas virtuais deste documento.
  2. Criar cópias de segurança e manter os discos VM com cópia de segurança do Azure (ou o seu próprio sistema de cópia de segurança com o armazenamento de Blobs do Azure e instantâneos VHD).

##<a name="storage-checklist"></a>Lista de verificação de armazenamento

  1. Reveja a secção de armazenamento deste documento.
  2. Regularmente fazer cópia de segurança recursos de armazenamento crítica.
  3. Considere utilizar a funcionalidade de instantâneo para blobs.

##<a name="database-checklist"></a>Lista de verificação de base de dados

  1. Reveja a secção de base de dados deste documento.
  2. Crie cópias de segurança no momento utilizando o comando Copiar de base de dados.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server na lista de verificação máquinas virtuais cópia de segurança

  1. Reveja o SQL Server em máquinas virtuais cópia de segurança secção deste documento.
  2. Utilizar tradicional cópia de segurança e restaurar técnicas.
  3. Crie um registo adiado envio sessão.

##<a name="web-apps-checklist"></a>Lista de verificação do Web Apps

  1. Criar cópias de segurança e manter a base de dados associado, se existirem.

##<a name="media-services-checklist"></a>Lista de verificação dos serviços de multimédia

  1. Criar cópias de segurança e manter os recursos de armazenamento associado.

##<a name="more-information"></a>Obter mais informações

Para mais informações sobre as funcionalidades de cópia de segurança e restauro no Azure, consulte o artigo [armazenamento, cenários de cópia de segurança e recuperação](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série com o foco nas [orientações técnico RDP Azure](./resiliency-technical-guidance.md). Se está a procurar mais RDP, recuperação de falhas e recursos de elevada disponibilidade, consulte o artigo RDP Azure orientações técnicas [recursos adicionais](./resiliency-technical-guidance.md#additional-resources).