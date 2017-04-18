<properties
   pageTitle="Base de dados do Azure SQL Azure caso prático da Microsoft - Snelstart | Microsoft Azure"
   description="Saiba mais sobre como SnelStart utiliza a base de dados SQL para expandido rapidamente os seus serviços de negócio a uma taxa de 1.000 novas Azure SQL bases de dados por mês"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Com o Azure, SnelStart tem expandido rapidamente os serviços de negócio a uma taxa de 1.000 novas Azure SQL bases de dados por mês

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart torna popular financeiros e empresas de software de gestão de para pequenas e médias empresas (SMB), nos países baixos. Aos seus 55,000 clientes sejam assistidos por um pessoal dos 110 empregados, incluindo uma equipa de TI de 35. Movendo a partir do software de ambiente de trabalho a um software-como-a-service (SaaS) perguntar se criada com base em Azure, SnelStart efetuadas a maioria dos serviços incorporados, automatizar gestão utilizar ambiente familiar no c# e otimizar o desempenho e escalabilidade por nenhuma empresas sobre - ou em-aprovisionamento utilizando agrupamentos de base de dados flexível. Utilizar o Azure fornece SnelStart fluidez de clientes mover no local e na nuvem.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Por que motivo o SnelStart expandido serviços do ambiente de trabalho para a nuvem

> "Trabalhar com o Azure significa que que possamos entregar mais rápida de software, rapidamente reagir a pedidos de cliente e dimensionar soluções quando exigências aumentar."

> — Henry foi, Software Architect

SnelStart executou uma empresa de software bem sucedida de anos, utilizando um modelo de desenvolvimento tradicional: código, compactar, enviar e repita. Ao longo do tempo, o ritmo de alteração crescia mais rápido e mais rapidamente. Regulamentos alterados com frequência e clientes necessário formas mais fácil para processar registos financeiros e colaborar com as respetivas Modify e administração pública para acompanhar essas alterações.

"Software de envio para clientes foi dispendioso e limitação," acordo com Henry foi, arquitecto de software. "Produção, embalagem e custos de envio limitado com que frequência poderia disponibilizamos software. Tivemos de atualizações de pacote de guias de remessa periódicos, mas que efetuou disco rígido satisfazer necessidades alterando os nossos clientes. Vamos poderia nunca ser assegurados que dos nossos clientes atualizados para a versão mais recente do produto. Por conseguinte, tivemos que suportar várias versões, efetuar a tarefa de suporte muito difíceis de bem."

Foi adiciona, "queremos uma forma de atualizações do programa e lançamento numa acelerada pace, pelo que poderia inovar mais rápido e criar novos serviços para os nossos clientes. Também queremos uma forma de automatizar processos de mais para simplificar as suas necessidades de administração de negócio os nossos clientes."

Para SnelStart, a solução era expandir os serviços ao tornar-se um fornecedor de SaaS baseado na nuvem. A plataforma de base de dados do Azure SQL contribuiu SnelStart consegui-lo sem sempre o overhead IT principal que iria ter necessário uma solução de infraestrutura-como-a-service (IaaS).

O modelo de nuvem também lhe permite SnelStart corrigir erros e fornecer novas funcionalidades rapidamente, sem necessitar de transferir e atualizar o software de clientes. Acordo com foi, "utilizar serviços em nuvem Azure permite-nos rapidamente tomar ações relativamente a alterar os requisitos de terceiros. Em vez de enviar uma nova versão para milhares de clientes, podemos adaptação as informações enviadas a partir da nossa aplicação de ambiente de trabalho para novos formatos de obrigatório por terceiros."

##<a name="a-modern-company-with-traditional-roots"></a>Uma empresa moderna com raízes tradicionais

SnelStart é um negócio moderno, ágil, como sendo de alta-tech com raízes humilde os valores para 1964, quando os fundadores iniciado empresa como um fabricante das peças de instrumentos musicais. Coração do negócio SnelStart software ao realmente beating no 1980s, durante a multiplicação de computador pessoal. A empresa necessária uma melhor alternativa para o software de contabilidade disponível no momento, para que demorou questões as suas próprias mãos. Em 1982, a empresa criada a Fundação do que são eventualmente ficaria SnelStart software de gestão de contas. A partir do início, o software foi admired para a sua simplificar e velocidade — muito para que a empresa acaba por ser alteradas foco e reinvented propriamente dito numa empresa de software.

Em 1995, SnelStart disponibilizada a sua aplicação de contabilidade primeira para Windows. A aplicação, criada com base em bases de dados do Microsoft Visual Basic 1.0 e o Microsoft Access, contribuiu aumentar o cliente base aos utilizadores mais de 5.000.

Hoje em dia, SnelStart é um fornecedor principal de uma aplicação de administração de negócio direcionada para SMB neerlandês e empresários trabalhadores e linha de negócio (LOB). Como Carlo Kuip, arquitecto de TI, diz, "nosso objetivo é proporcionar percentagem de 100 automatização dos serviços de administração de negócio para os nossos clientes."

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>Otimizar o desempenho e custo com agrupamentos de flexível

SnelStart foi uma em grande escala dos primeiros a adoptar agrupamentos de base de dados flexível. Agrupamentos flexível ajudam a empresa limitar os custos e gerir requisitos de desempenho de forma mais eficiente. Acordo com foi, "utilizando agrupamentos de base de dados flexível, podemos pode otimizar o desempenho com base nas necessidades dos nossos clientes, sem indevidamente aprovisionamento. Se tivemos aprovisionar com base na carga de pico, seria bastante dispendioso. Em vez disso, a opção para partilhar recursos entre vários, baixa utilização bases de dados nos para criar uma solução que executa bem e está rentável permitem. "

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Ajudar a bases de dados SQL Azure containerize dados para isolamento e segurança 

Base de dados SQL Azure permite SnelStart para facilmente e transparente mover clientes no local dados de administração de negócio para Azure. A base de dados do SQL Azure é um contentor conveniente que fornece isolamento, um limite para autenticação, autorização e capacidades de cópia de segurança e restauro fácil. Bases de dados fornecem um modelo de conceptual bem adequado para a administração de empresas. De acordo com Carlo Kuip, arquitecto de TI, "itens dentro este limite do contentor contêm dados confidenciais que são cruciais para um negócio e armazenar esses itens numa base de dados são isolado mantém-los bem protegido. Que possamos gerir a autorização ao nível da base de dados e ainda automatizar a gestão e a escala de saída destas bases de dados sem necessidade de administradores de base de dados (DBAs) no docentes."

Armazém de dados SQL Azure também é reproduzido uma função do bloco de segurança e gestão de SnelStart, ajudando a empresa recolher dados de telemetria, como a deteção de intrusos, registo de atividade de utilizador e conectividade.

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure sobrecarga remove para que os programadores podem perder tempo mais fornecer valor 

O modelo de plataforma Azure removida sobrecarga infraestrutura e ativado SnelStart automatizar implementações utilizar bibliotecas de gestão de c#. Como Kuip dos Estados, "Pedimos foram conseguir aumentar os nossos operações atuais com um docentes muito pequena enquanto em simultâneo, aumentando escalabilidade, a velocidade e falhas opções de recuperação para os nossos clientes. A tecla shift para o desenvolvimento de serviços disponibilizado recursos focar-se em novos serviços e funcionalidades, em vez de apenas a atualizar código existente para manter o com novos regulamentos ou códigos de imposto." Ele adiciona, "ao automatizar gestão e utilizar SaaS oferta, estamos a conseguir entregar mais valor para os nossos clientes sem ter de fazer investimentos grandes no docentes operacional." Por exemplo, utilizando agrupamentos de base de dados Azure e flexível, SnelStart conseguiram adicionar uma variedade de funcionalidades novas, incluindo mais robusta integração de dados do cliente com bancos, faturação novos serviços, controlos de fundo de pequenas empresas e serviços de e-mail.

> "Na apenas os primeiros alguns meses de 2016, podemos expandidas nossas implementações de base de dados do Azure SQL de 5,500 cerca a mais do que 12.000 e podemos atualmente está a adicionar cerca de 1.000 bases de dados por mês."

> — Henry foi, Software Architect

Gestão de base de dados está ainda mais automatizada utilizando a funcionalidade flexível tarefas. Como Kuip diz, "altamente Agradecemos-lhe a deteção automática de bases de dados numa instância [server] DB do SQL." Esta opção permite-SnelStart executar operações de gestão nos seu cliente dinamicamente crescente base sem sobrecarga adicional.

SnelStart também está a desenvolver uma API que age como um corretor entre dados do cliente e apps criadas pelo parceiros de software de terceiros. Como Kuip Unidos, "Esta API permitirá a outros fornecedores adicionar funcionalidades a nosso software, tal como a eliminação de introdução de dados para faturas e outros documentos." Clientes já não terão de faturas de escrever manualmente para o seu software de gestão de contas pequenas empresas; o software SnelStart irão trocar diretamente as informações necessárias. Isto permite que clientes aderir a suas tarefas de administração de negócio com ecossistema de informações que são sai do transformação digital na indústria.  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Como serviços Azure ativar SaaS para SnelStart

Ao utilizar o Azure, SnelStart pode servir aos seus clientes e os respetivos Modify mais totalmente na nuvem. Por exemplo, clientes e Modify pode partilhar informações ao aceder diretamente a API do cliente do SnelStart, alojado no Azure. Kuip diz, "estes serviços reutilizáveis estão disponíveis ao nossos cliente destinado web apps e fornecem infraestrutura e funções para permitir o acesso a administração de negócio para clientes e ao software de terceiros utilizado dos nossos clientes comuns. Exemplos que fornece capacidades de configuração do produto, gerir regras de firewall e gerir processos de execução longa como cópias de segurança."

> Nosso objetivo é proporcionar percentagem de 100 automatização dos serviços de administração de negócio para os nossos clientes." 

> — Carlo Kuip, arquitecto de TI

Além disso, os serviços web SnelStart permitem clientes e Modify facilmente aceder aos dados no agrupamentos flexível de base de dados do Azure SQL. Este modelo de SaaS associado elasticidade de base de dados e o Gestor de recursos do Azure, fornece SnelStart com funcionalidades de escalabilidade complementam cada implementação Azure. A aplicação está totalmente automatizada utilizar bibliotecas de gestão de c#.

![Arquitetura de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. Partir de 2016 de Junho, SnelStart tem mais do que 11,000 bases de dados e mais de 50 agrupamentos de base de dados flexível
 
##<a name="simplicity-from-the-cloud"></a>Simplificar a partir da nuvem

Desde a ser movida para uma solução baseada na nuvem Azure, SnelStart foi possível crescimento do cliente rápida enquanto oferece funcionalidades de forma inovadoras e serviços de suporte. Acordo com foi, "com Azure, podemos pode entregar perto contínua atualizações para os nossos clientes, sem expandir a nossa equipa de operações. E podemos obter todas as outras excelentes Azure funcionalidades — como o recuperação escalabilidade e falhas — agrupado na. "

> "Quando podemos estavam realmente sobre em Redmond... Recebi uma chamada a partir de um programador novamente na Países Baixos, telefonar-me sobre um problema específico. Foi possível obter Microsoft para fornecer uma alteração no seu ambiente de trabalho de produção 48 horas para resolver o problema de nossos."

> — Henry foi, Software Architect

SnelStart appreciates também a parceria forte que tenha desenvolvido com a equipa do Microsoft Azure SQL DB. Como Kuip Unidos, "temos debates sobre as funcionalidades e como utilizar tecnologia, que é algo apreciadas em ambos os lados".
O objetivo imediato para SnelStart é manter em crescimento seu cliente satisfeito base. Tal como foi dos Estados "Sem limitações técnicos e de recursos que tivemos como um ISV, não existe nenhum limite extremidade como o podemos crescimento."


## <a name="more-information"></a>Obter mais informações

- Para saber mais acerca de agrupamentos de base de dados flexível Azure, consulte o artigo [agrupamentos de base de dados flexível](sql-database-elastic-pool.md).

- Para saber mais sobre as funções de Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre armazém de dados do SQL Azure, consulte o artigo [Armazém de dados SQL](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Para saber mais sobre SnelStart, consulte o artigo [SnelStart](http://www.snelstart.nl).


