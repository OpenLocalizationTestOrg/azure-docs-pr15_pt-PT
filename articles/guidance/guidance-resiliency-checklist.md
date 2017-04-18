<properties
   pageTitle="Lista de verificação RDP | Microsoft Azure"
   description="Lista de verificação que dá-lhe preocupações RDP durante a estruturação."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Orientação do Azure RDP: lista de verificação RDP

Estruturar a sua aplicação de RDP requer planear e mitigação uma variedade de modos de falha que podem ocorrer. Reveja os itens nesta lista de verificação contra a estrutura da aplicação para o tornar mais flexível.

## <a name="requirements"></a>Requisitos de

- **Defina os requisitos de disponibilidade do seu cliente.** O cliente terão requisitos de disponibilidade para os componentes na sua aplicação e isto irá afetar a estrutura da sua aplicação. Obter o contrato a partir do seu cliente para as metas de disponibilidade de cada parte da sua aplicação, caso contrário, o design poderão não ser cumpridos expectativas do cliente. Para obter mais informações, consulte a secção [definir os seus requisitos de RDP](guidance-resiliency-overview.md#defining-your-resiliency-requirements) do documento [Estruturar e são aplicações para o Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Análise de modo a falha

- **Efetue uma análise de modo de falha (FMA) para a sua aplicação.** FMA é um processo para criar RDP para uma aplicação mais cedo no palco da estrutura. Aos objetivos de um FMA incluem:  

    - Identifique a que tipos de falhas de uma aplicação poderá deparar. 
    - Capture o impacto de cada tipo de falha na aplicação e efeitos potenciais.
    - Identifique estratégias de recuperação. 

    Para obter mais informações, consulte o artigo [Estruturar e são aplicações para o Azure: análise de modo a falha][fma].  

## <a name="application"></a>Aplicação

- **Implemente várias instâncias de serviços.** Serviços de inevitavelmente irão falhar e, se a sua aplicação depender de uma única ocorrência de um serviço-inevitavelmente falhará também. Para aprovisionar várias instâncias para a [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md), selecione um [Plano de serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) que oferece várias instâncias. Para os serviços em nuvem Azure, configure cada um dos seus funções para utilizar [várias instâncias](../cloud-services/cloud-services-choose-me.md#scaling-and-management). Para [máquinas virtuais do Azure (VMs)](../virtual-machines/virtual-machines-windows-about.md), certifique-se de que a sua arquitetura da VM inclui VM mais do que uma e que cada VM está incluído num [conjunto de disponibilidade][availability-sets].   

- **Utilize um balanceador de carga para distribuir pedidos.** Um balanceador de carga distribuir pedidos da aplicação de instâncias do serviço Saudável removendo danificadas instâncias de rotação. Se utiliza o seu serviço de aplicação de serviço de Azure ou os serviços em nuvem Azure, já é distribuído por si. No entanto, se a sua aplicação utiliza Azure VMs, terá de aprovisionar um balanceador de carga. Consulte a descrição geral do [Balanceador de carga Azure](../load-balancer/load-balancer-overview.md) para obter mais detalhes. 

- **Configure o Azure aplicação Gateways para utilizar várias instâncias.** Dependendo dos requisitos da aplicação, um [Gateway de aplicação do Azure](../application-gateway/application-gateway-introduction.md) poderá ser melhor adequado à distribuição pedidos de serviços da sua aplicação. No entanto, única instâncias do serviço Gateway de aplicação não estão garante por um SLA por isso, é possível que a aplicação poderá falhar se falha a instância de Gateway de aplicação. Aprovisione mais do que uma instância do Gateway aplicação média ou maior para garantir a disponibilidade do serviço nos termos do [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Conjuntos de disponibilidade de utilização para cada camada de aplicação**. Colocar o seu instâncias num [conjunto de disponibilidade] [ availability-sets] garantias de conectividade pelo menos uma instância VM dentro os termos do [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Se o VMs não estiverem num conjunto de disponibilidade que não tiver garante a protegê-los e é possível que poderia todos falhar ou ser atualizados em simultâneo. 

- **Considere implementar a aplicação em várias regiões.** Se a aplicação for implementada a uma única região, o evento raro toda a região torna-se disponível, a aplicação também não estará disponível. Isto pode ser aceitável nos termos da SLA sua aplicação. Se Sim, considere implementar a aplicação e dos seus serviços através de várias regiões. Uma implementação da região com várias pode utilizar um padrão de activo activo (distribuição pedidos entre várias instâncias ativas) ou um padrão de activo passivo (manter uma instância "quente" reserva, no caso de falha da instância primária). Recomendamos que implementar várias instâncias de serviços de sua aplicação em toda a pares regionais. Para obter mais informações, consulte o artigo [empresas continuidade e falhas recuperação (BCDR): Azure emparelhadas regiões](../best-practices-availability-paired-regions.md).

- **Implemente padrões RDP para operações remotas adequado.** Se a sua aplicação depender comunicação entre serviços remoto, o caminho de comunicação inevitavelmente irá falhar. Se existirem várias falhas, as restantes instâncias saudáveis dos serviços a aplicação foi sise com pedidos de. Existem várias padrões útil para lidar com falhas comuns, incluindo o padrão de tempo de espera, [volte a tentar padrão][retry-pattern], o [separador de palavras de circuito] [ circuit-breaker] padrão e outros. Para mais informações, consulte o artigo [Estruturar e são aplicações para o Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Utilize autoscaling para responder a aumentos de carregamento.** Se a sua aplicação não estiver configurada para dimensionar automaticamente à medida que aumenta carga saída, é possível que serviços da sua aplicação falhará se tornam-se saturada de com pedidos de utilizador. Para obter mais detalhes, consulte o seguinte:

    - Geral: [lista de verificação escalabilidade](../best-practices-scalability-checklist.md) 
    - Azure de aplicação de serviço: [contagem de instâncias de dimensionar automaticamente ou manualmente][app-service-autoscale]
    - Serviços em nuvem: [como Dimensionar automaticamente um serviço na nuvem][cloud-service-autoscale]
    - Máquinas virtuais: [conjuntos de escala de dimensionamento e máquina virtual automáticas][vmss-autoscale]


- **Implementar operações assíncronas sempre que possível.** Operações síncronas podem monopolizar os recursos e bloquear outras operações enquanto o autor da chamada aguarda concluir o processo. Estruture cada parte da sua aplicação para permitir a operações assíncronas sempre que possível. Para mais informações sobre como implementar o comportamento assíncrona programação no c#, consulte o artigo [programação assíncrona com assíncrona e aguardar][asynchronous-c-sharp].

- **Utilize o Gestor de tráfego de Azure para encaminhar o tráfego da sua aplicação para regiões diferentes.**  [Gestor de tráfego Azure] [ traffic-manager] executa balanceamento de carga ao nível de DNS e pode encaminhar o tráfego para regiões diferentes, com base em [Encaminhamento de tráfego] [ traffic-manager-routing] método especificado e o estado de funcionamento de pontos finais da aplicação. 

- **Configurar e testar sondas de estado de funcionamento do seu balanceadores de carga e gestores de tráfego.** Garantir que a lógica do Estado de funcionamento verifica as partes críticas do sistema e corretamente responde à sondas de estado de funcionamento.

    - O estado de funcionamento sondas para [O Gestor de tráfego Azure] [ traffic-manager] e [Balanceador de carga Azure] [ load-balancer] servir uma função específica. Para o Gestor de tráfego a sonda de estado de funcionamento determina a falhar sobre para outra região. Para um balanceador de carga-determina a remover uma VM de rotação.      

    - Para uma sonda de tráfego Gestor, o ponto final do Estado de funcionamento deve verificar quaisquer dependências críticas que são implementadas dentro da mesma região e cuja falha deve acionar uma activação pós-falha para outra região.  

    - Para um balanceador de carga, o ponto final do Estado de funcionamento deve reportar o estado de funcionamento da VM. Não inclua outros camadas ou serviços externos. Caso contrário, uma falha de que ocorre fora da VM irão provocar o Balanceador de carga remover a VM de rotação.

    - Para obter orientações sobre como implementar o estado de funcionamento de monitorização na sua aplicação, consulte o artigo [Padrão de monitorização do Estado de funcionamento do ponto final](https://msdn.microsoft.com/library/dn589789.aspx).

- **Monitorizar os serviços de terceiros.** Se a sua aplicação tiver dependências sobre os serviços de terceiros, identificar onde e como podem falhar estes serviços de terceiros e o que essas falhas de efeito terão na sua aplicação. Um serviço de terceiros pode não incluir monitorização e diagnósticos de, por isso, é importante para iniciar a sua exe-las e se ligam-los às estado de funcionamento da sua aplicação e utilizar um identificador exclusivo o registo de diagnóstico. Para mais informações sobre as melhores práticas para a monitorização e diagnósticos, consulte o artigo as [orientações de monitorização e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Certifique-se de que qualquer serviço de terceiros que consumir fornece uma SLA.** Se a aplicação depende de um serviço de terceiros, mas o terceiro fornece não garante disponibilidade sob a forma de um SLA, a aplicação também não pode ser garantir disponibilidade. O SLA só é tão bom como o componente menos disponível da sua aplicação.


## <a name="data-management"></a>Gestão de dados

- **Compreenda os métodos de replicação para origens de dados da sua aplicação.** Os dados da aplicação serão armazenados nas origens de dados diferentes e têm requisitos diferentes disponibilidade. Avaliar os métodos de replicação para cada tipo de armazenamento de dados no Azure, incluindo a [Replicação de armazenamento do Azure](../storage/storage-redundancy.md) e [Base de dados ativo Geo-a replicação SQL](../sql-database/sql-database-geo-replication-overview.md) para se certificar de que são correspondidos requisitos de dados da sua aplicação.

- **Certifique-se de que nenhuma única conta de utilizador tem acesso a dados de produção e cópia de segurança.** As cópias de segurança de dados são comprometidas se uma única conta de utilizador tem permissão para escrever produção e origens de cópia de segurança. Um utilizador malicioso intencionalmente foi possível eliminar todos os seus dados, enquanto que um utilizador habitual acidentalmente pode eliminá-la. Conceba a sua aplicação para limitar as permissões de cada conta de utilizador para que apenas os utilizadores que requerem acesso de escrita têm acesso de escrita e é apenas produção ou cópia de segurança, mas não crie ambos.

- **Documento sua origem de dados falhar ao longo e voltar a falhar processar e testá-la.** No caso de onde a origem de dados falha catastrophically, um operador humano terá que seguir um conjunto de instruções documentados Falha ao longo para uma nova origem de dados. Se os passos documentados tem erros, um operador não poderão com êxito segui-los e falhar ao longo do recurso. Teste regularmente os passos de instruções para verificar se um operador segui-los está conseguir falhar ao longo e falhar novamente a origem de dados com êxito.

- **Valide as cópias de segurança de dados.** Regularmente Certifique-se de que os dados de cópia de segurança são o que esperar ao executar um script, para validar a integridade dos dados, esquema e consultas. Não existe nenhum ponto ter uma cópia de segurança, se não é útil restaurar as suas fontes de dados. Inicie sessão e comunicar eventuais inconsistências verificadas, para que o serviço de cópia de segurança pode ser reparado.

- **Considere utilizar um tipo de conta de armazenamento é geo redundante.** Dados armazenados numa conta de armazenamento do Windows Azure são sempre replicados localmente. No entanto, existem vários estratégias de replicação para selecionar a partir do quando uma conta de armazenamento está aprovisionada. Selecione [Azure acesso de leitura Geo redundantes armazenamento (GRS RT)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para proteger os seus dados de aplicação contra as maiúsculas/minúsculas raros quando toda a região fica indisponível.

    > [AZURE.NOTE] Para VMs, não depende replicação GRS RT para restaurar os discos VM (ficheiros VHD). Em alternativa, utilize a [Cópia de segurança do Azure][azure-backup].   

## <a name="operations"></a>Operações

- **Implemente a monitorização e alertar melhores práticas na sua aplicação.** Sem inicial monitorização, diagnósticos e alertas, não existe nenhuma forma de detetar falhas na sua aplicação e alertar um operador para os corrigir. Para obter mais informações sobre as melhores práticas, consulte o artigo as [orientações de monitorização e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Medir as estatísticas de chamada remoto e disponibilizar as informações para a equipa de aplicação.**  Se não controlar e comunicar as estatísticas de chamada remoto em tempo real e forneça uma forma fácil de Reveja esta informação, a equipa de operações não terá uma vista instantânea para o estado de funcionamento da sua aplicação. E se apenas hora de chamada remoto média de medida, não terá informações suficientes para revelar problemas nos serviços. Resuma métricas de chamada remoto como latência, débito e erros nos percentis 99 e 95. Execute análises estatísticas métricas para descobrir erros que ocorrem dentro de cada percentil.

- **Monitorizar o número de exceções breves e número de tentativas ao longo de um período definido adequado.** Se não registar e monitorizar exceções breves e tentativas ao longo do tempo de repetição, é possível que um problema ou falha pode ser ocultada ao lógica de repetição da sua aplicação. Isto é, se a monitorização e o registo apenas mostra o sucesso ou o fracasso de uma operação, o facto de que a operação teve ser repetida várias vezes devido a exceções irá ser ocultado. Uma tendência de aumentar exceções ao longo do tempo indica que o serviço está a ter um problema e poderá falhar. Para obter mais informações, consulte o artigo [Repetir orientações específicas do serviço][retry-service-guidance].

- **Implementa um sistema de aviso de antecipado um operador de alertas.** Identifica a chave de desempenho indicadores de estado de funcionamento da sua aplicação, tal como exceções breves e remotos latência de chamadas e configurar valores do limiar adequado para cada um deles. Envie um alerta para operações de quando o valor limite é atingido. Defina limiares destes em níveis identificam problemas antes de poderem tornar críticas e requerem uma resposta de recuperação.

- **O processo de lançamento para a sua aplicação do documento.** Sem documentação do processo de lançamento detalhadas, um operador poderá implementar uma atualização incorretas ou indevidamente configurar definições para a sua aplicação. Definir e o processo de lançamento do documento e certifique-se de que está disponível para toda a equipa de operações claramente. Práticas recomendadas para implementação e são da sua aplicação encontram detalhadas mais de [implementação e são] [ guidance-resilient-deployment] secção do documento RDP orientações.

- **Certifique-se de que mais do que uma pessoa da equipa está preparada para monitorizar a aplicação e executar qualquer passos de recuperação manual.** Se tiver apenas um único operador da equipa quem pode monitorizar a aplicação e iniciar os passos de recuperação, essa pessoa torna-se um ponto de falha único. Dar formação múltiplos indivíduos em deteção e recuperação e certifique-se de há sempre pelo menos um active em qualquer altura.

- **Automatize processo de implementação da sua aplicação.** Se a equipa de operações é necessária para implementar manualmente a sua aplicação, o erro humano pode causar a implementação para falhar. Para obter mais informações sobre as melhores práticas para automatizar a implementação da aplicação, consulte o artigo de [implementação e são] [ guidance-resilient-deployment] secção do documento RDP orientações.

- **Estruture o processo de lançamento para maximizar a disponibilidade de aplicação.** Se o processo de lançamento requer a ficar offline durante a implementação serviços, a aplicação vai estar disponível até que estiver novamente online. Utilize a técnica de implementação [azul/verde](http://martinfowler.com/bliki/BlueGreenDeployment.html) ou [Libertar Canárias](http://martinfowler.com/bliki/CanaryRelease.html) para implementar a aplicação para produção. Ambas destas técnicas envolvem implementar o seu código lançamento juntamente com o código de produção para que os utilizadores do código de pré-lançamento podem ser redirecionados para código de produção em caso de falha. Para obter mais informações, consulte o artigo de [implementação e são] [ guidance-resilient-deployment] secção do documento RDP orientações.

- **Iniciar sessão e de auditoria implementações da sua aplicação.** Se utilizar testado técnicas de implementação como azul/verde ou das Canárias lançamentos haverá mais do que uma versão da sua aplicação a ser executada em produção. Caso ocorra um problema, é importante para determinar qual a versão da sua aplicação está a causar um problema. Implemente uma estratégia de registo robustas para capturar quanto informação específica da versão possível. 

- **Certifique-se de que a aplicação não é executado compara [limita a subscrição do Azure](../azure-subscription-service-limits.md).** Subscrições do Azure têm limites determinados tipos de recursos, como número de grupos de recursos, o número de núcleos e o número de contas de armazenamento.  Se aos requisitos da aplicação excedam os limites de subscrição Azure, crie outro Azure subscrição e aprovisionar recursos suficientes aí.

- **Certifique-se de que a aplicação não é executado compara [limites por serviço](../azure-subscription-service-limits.md).** Serviços de Azure individuais têm limites de consumo &mdash; por exemplo, limites de armazenamento, débito, número de ligações, pedidos por segundo e outras métricas. A aplicação falhará se tenta utilizar recursos para além destes limites. Isto resultará serviço optimização e possíveis tempo de inatividade para utilizadores afetados. 

    Dependendo do serviço específico e aos requisitos da aplicação, geralmente pode evitar estes limites ao escalar para cima (por exemplo, escolher outra camada comparar) ou dimensionamento saída (adicionar novas instâncias).  

- **Requisitos de armazenamento da sua aplicação para se situar dentro do destinos de desempenho e escalabilidade do Azure armazenamento de estrutura.** Armazenamento Azure foi concebido para funcionar dentro de alvos de desempenho e escalabilidade predefinidos, por isso, conceber a sua aplicação para utilizar armazenamento dentro esses destinos. Se exceder estes alvos a aplicação irá deparar limitação de armazenamento. Para corrigir isto, Aprovisione contas adicionais de armazenamento. Se executar compara o limite de conta de armazenamento, aprovisionar subscrições adicionais do Azure e, em seguida, aprovisionar contas armazenamento adicionais. Para mais informações, consulte o artigo [escalabilidade de armazenamento do Azure e metas de desempenho](../storage/storage-scalability-targets.md).

- **Selecione o tamanho da memória virtual direita para a sua aplicação.** Medir a real CPU, memória, disco e e/s da sua VMs produção e certifique-se de que o tamanho da memória virtual que selecionou é suficiente. Caso não esteja, a aplicação pode experimentar problemas de capacidade tal como os VMs aproximar os respetivos limites. Tamanhos VM são descritos detalhadamente no documento [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Determine se a carga de trabalho da sua aplicação está flutuantes ou estável ao longo do tempo.** Se a sua carga de trabalho oscilar ao longo do tempo, utilize Azure VM escala define para dimensionar automaticamente o número de ocorrências VM. Caso contrário, terá que aumentar ou diminuir o número de VMs manualmente. Para mais informações, consulte o artigo [Descrição geral de conjuntos de escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Selecione a camada de serviço direita para a base de dados do SQL Azure.** Se a sua aplicação utiliza a base de dados do SQL Azure, certifique-se de que selecionou a camada de serviço adequado. Se selecionar uma camada que não é possível processar requisitos de unidade (DTU) a aplicação da base de dados da transação, irá estar limitada a utilização de dados. Para mais informações sobre como seleccionar o plano de serviço correto, consulte o [Opções de base de dados SQL e o desempenho: compreender o que está disponível em cada camada de serviço](../sql-database/sql-database-service-tiers.md) documento. 

- **Tem um plano de anulação para implementação.** É possível que a sua implementação da aplicação poderia falhar e fazer com que a sua aplicação para ficar indisponíveis. Estruture um processo de reposição da versão anterior para voltar à última versão boa conhecida e minimizar o tempo de inatividade. Consulte o artigo de [implementação e são] [ guidance-resilient-deployment] secção do documento RDP orientações para obter mais informações. 

- **Crie um processo para interagir com suporte Azure.** Se não estiver definido o processo de contactar o [suporte do Azure](https://azure.microsoft.com/support/plans/) antes da necessidade de contactar o suporte surge, tempo de inatividade ser prolongado conforme o processo de suporte compreendê-las pela primeira vez. Inclua o processo de contactar o suporte e crescentes problemas como parte do RDP da sua aplicação desde o início.

- **Certifique-se de que a sua aplicação não utilizar mais do que o número máximo de contas de armazenamento por subscrição.** Azure permite um máximo de 200 contas de armazenamento por subscrição. Se a sua aplicação exigir mais contas de armazenamento do que estão atualmente disponíveis na sua subscrição, terá de criar uma nova subscrição e crie contas de armazenamento adicional aí. Para mais informações, consulte o artigo [subscrição Azure e limites de serviço, quotas e as restrições](../azure-subscription-service-limits.md#storage-limits).

- **Certifique-se de que a aplicação não excede os destinos escalabilidade para discos máquina virtual.** Um VM de IaaS Azure suporta como anexar um número de discos dados dependendo de fatores várias, incluindo o tamanho da memória virtual e o tipo de conta de armazenamento. Se a aplicação exceder os destinos escalabilidade para máquina virtual discos, aprovisionar contas de armazenamento adicional e criar os discos máquina virtual aí. Para obter mais informações, consulte o artigo [escalabilidade de armazenamento do Azure e desempenho destinos] (... / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Teste

- **Execute activação e testar para a sua aplicação de reposição de recurso.** Se totalmente ainda não testado activação e reposição de recurso, não pode ter a certeza de que os serviços dependentes na sua aplicação voltar atrás para cima de uma forma sincronizada durante a recuperação de falhas. Certifique-se de que dependente da sua aplicação dos serviços de activação e falhas novamente na ordem correta.

- **Execute a inserção de testes para a sua aplicação.** A aplicação pode falhar por várias razões, tal como validade do certificado, esgotamento de recursos do sistema numa VM ou falhas de armazenamento. Teste a sua aplicação num ambiente como mais próximo possível produção, por simulação ou acionar falhas reais. Por exemplo, eliminar certificados, artificialmente consumir recursos do sistema ou eliminar uma origem de armazenamento. Verificar capacidade a aplicação para recuperar a partir de todos os tipos de falhas, só por si e em combinação. Verifique se o falhas não são propagação ou em cascata através do seu sistema.

- **Execute testes de produção através de ambos os dados de utilizador síntese e real.** Teste e de produção são raramente idênticas, pelo que é importante utilizar azul/verde ou uma implementação das Canárias e teste a sua aplicação de produção. Esta opção permite-lhe testar a sua aplicação de produção em caso de carga real e certifique-se de que irá funcionar como esperado quando implementado completamente.

## <a name="security"></a>Segurança

- **Implementar a nível da aplicação de proteção contra distribuído ataques de recusa de serviço (DDoS).** Azure serviços estão protegidos contra ataques DDos na camada de rede. No entanto, Azure não é possível proteger contra ataques de camada de aplicação, porque é difícil distinguir entre os pedidos de utilizador VERDADEIRO de pedidos de utilizador malicioso. Para mais informações sobre como proteger contra ataques de DDoS camada de aplicação, consulte a secção "Proteger contra DDoS" de [Segurança do Microsoft Azure rede](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (transferir PDF).

- **Implemente o princípio do menor privilégio para aceder a recursos da aplicação.** A predefinição para acesso aos recursos da aplicação deve ser tão restritiva possível. Conceder permissões ao nível superiores numa base de aprovação. Conceder acesso demasiado permissivo para recursos da sua aplicação por predefinição pode resultar em alguém intencionalmente ou acidentalmente elimina recursos. Azure fornece [o controlo de acesso baseado em funções](../active-directory/role-based-access-built-in-roles.md) para gerir privilégios de utilizador, mas é importante verificar, pelo menos, permissões de privilégio para outros recursos que têm os seus próprios sistemas de permissões como SQL Server. 

## <a name="telemetry"></a>Telemetria

- **Inicie sessão dos dados de telemetria enquanto a aplicação estiver em execução no ambiente de produção.** Capture informações de telemetria robusta, enquanto a aplicação está em execução no ambiente de produção ou não terá informações suficientes para diagnosticar a causa de problemas enquanto ativamente serve utilizadores. Obter mais informações estão disponíveis no início de sessão melhores práticas está disponível nas [orientações de monitorização e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Implemente registo utilizando um padrão assíncrono.** Se forem síncronas operações de registo, pode bloquear o seu código da aplicação. Certifique-se de que o registo de operações são implementadas como operações assíncronas. 

- **Correlacionar os dados do registo ao longo dos limites de serviço.** Numa aplicação típica de camadas, um pedido de utilizador pode percorrer vários limites de serviço. Por exemplo, um pedido de utilizador normalmente com origem na camada web e é transmitido para a camada de negócio e finalmente persiste na camada de dados. Em cenários mais complexos, pode ser distribuído um pedido de utilizador para muitas lojas serviços e dados diferentes. Certifique-se de que o sistema de registo relacionado com chamadas através de limites de serviço para que pode controlar o pedido em toda a sua aplicação.

##  <a name="azure-resources"></a>Recursos Azure 

- **Utilize os modelos de Gestor de recursos do Azure para Aprovisionar recursos.** Modelos de Gestor de recursos tornam mais fácil automatizar implementações através do PowerShell ou o clip do Azure, que produz um processo de implementação mais fiável. Para obter mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure][resource-manager].

- **Atribua recursos significativos nomes.** Que lhe dá uma recursos nomes que fazem sentido torna mais fácil de localizar um recurso específico e compreender o seu papel. Para obter mais informações, consulte o artigo [recomendado convenções para recursos Azure de nomenclatura](guidance-naming-conventions.md) 

- **Utilizar o controlo de acesso baseado em funções (RBCA)**. Utilize RBAC para controlar o acesso aos recursos do Azure implementar. RBAC permite-lhe atribuir funções de autorização a membros da sua equipa de DevOps, para evitar acidentais ou alterações às implementado recursos. Para obter mais informações, consulte o artigo [começar a trabalhar com gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md) 

- **Utilize bloqueios de recursos para recursos críticos, tal como VMs.** Bloqueios recurso impedem que um operador eliminar acidentalmente um recurso. Para obter mais informações, consulte o artigo [recursos de bloqueio com o Gestor de recursos do Azure](../resource-group-lock-resources.md) 

- **Regionais pares.** Quando implementar duas regiões, selecione regiões mesmo par regional. Em caso de falha de abrangente, recuperação de uma região tem prioridade terminar cada par. Alguns serviços como o armazenamento Geo redundante fornecem replicação automática à região de par. Para obter mais informações, consulte o artigo [empresas continuidade e falhas recuperação (BCDR): Azure emparelhadas regiões](../best-practices-availability-paired-regions.md) 

- **Organizar grupos de recursos por função e ciclo de vida**.  Em geral, um grupo de recursos deve conter recursos que partilham o mesmo ciclo de vida. Isto torna mais fácil de gerir implementações, eliminar implementações de teste e atribuir direitos de acesso, reduzir as hipóteses que uma implementação de produção for acidentalmente eliminada ou modificada. Criar grupos de recursos separada para produção, desenvolvimento e teste ambientes. Numa implementação com várias região, coloque recursos para cada região em grupos de recursos separado. Isto torna mais fácil voltar a implementar uma região sem afetar as outras regiões. 

## <a name="azure-services"></a>Serviços do Azure 

Os seguintes itens de lista de verificação aplicam a serviços específicos no Azure.

###  <a name="app-service"></a>Aplicação de serviço 

- **Utilize camadas padrão ou Premium.** Estes camadas suportam faixas de transição e automatizado cópias de segurança. Para obter mais informações, consulte o artigo [Descrição geral de aprofundada de planos do serviço de aplicação do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Evite dimensionamento para cima ou para baixo.** Selecione em vez disso, uma camada e o tamanho da instância que correspondem aos seus requisitos de desempenho em carga normal e, em seguida, [escala de saída](../app-service-web/web-sites-scale.md) as instâncias para processar alterações em volume de tráfego. Dimensionamento cima e para baixo poderá accionar um reinício da aplicação.  

- **Configuração do arquivo de como as definições da aplicação.** Utilize as definições da aplicação para colocar em espera definições de configuração, as definições da aplicação. Defina as definições dos modelos de Gestor de recursos ou através do PowerShell, para que pode aplicá-los como parte de uma implementação automatizada / atualizar processo, o que é mais fiável. Para mais informações, consulte o artigo [Configurar web apps no Azure aplicação de serviço](../app-service-web/web-sites-configure.md). 

- **Crie separados planos de aplicação de serviço de produção e de teste.** Não utilize faixas na sua implementação de produção para testar a ligação.  Todas as aplicações dentro o mesmo plano da aplicação de serviço partilham as instâncias VM mesmo. Se introduz produção e teste implementações o mesmo plano, pode afectar negativa a implementação de produção. Por exemplo, testes de carga poderão prejudicar o site de produção direto. Ao colocar implementações de teste para um plano separada, isolá-los a partir da versão de produção.  

- **Aplicações web em separado da web API**. Se a sua solução tiver um web front-end e um web API, considere permitem-las para as aplicações de serviço de aplicação em separado. Este design torna mais fácil de decomposição a solução pela carga de trabalho. Pode executar a aplicação web e a API nos planos do serviço de aplicação separados, para que podem ser dimensionados forma independente. Caso não precise desse nível de escalabilidade a em primeiro lugar, pode implementar as aplicações para o mesmo plano e mova-os para planos separados mais tarde, se necessário.

- **Evite utilizar a funcionalidade de cópia de segurança da aplicação de serviço para agregar bases de dados Azure SQL.** Em alternativa, utilize a [base de dados SQL automatizado cópias de segurança][sql-backup]. Cópia de segurança da aplicação de serviço exporta a base de dados para um ficheiro de .bacpac SQL, que custos DTUs.  

- **Implemente uma ranhura transição.** Crie uma ranhura de implementação para transição. Implementar actualizações de aplicações para a ranhura transição e verifique a implementação antes de trocá-lo para produção. Isto reduz o risco de uma atualização incorretas na produção. Também assegura que todas as instâncias são aquece antes de a ser convertido em produção. Muitas aplicações tem uma aquecimento significativo e a hora de início de constipação. Para mais informações, consulte o artigo [Configurar ambientes para web apps no Azure aplicação de serviço de teste](../app-service-web/web-sites-staged-publishing.md). 

-  **Crie uma ranhura de implementação para colocar em espera de implementação do último condições conhecida (LKG).** Quando implementar uma atualização produção, deslocar-se a implementação de produção anterior na ranhura LKG. Isto torna mais fácil recuperar uma implementação incorretas. Se detetar um problema mais tarde, pode rapidamente revertê-lo para a versão LKG. Para obter mais informações, consulte o artigo [arquitetura de referência Azure: aplicação web básicas](guidance-web-apps-basic.md). 

- **Ativar o registo de diagnóstico**, incluindo o registo de aplicação e registo do servidor web. Registo é importante para a monitorização e diagnóstico. Consulte o artigo [Ativar diagnósticos do registo para web apps no serviço de aplicação do Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Registo de armazenamento de blob**. Isto torna mais fácil recolher e analisar os dados. 

- **Crie uma conta de armazenamento em separado para registos.** Não utilize a mesma conta de armazenamento de registos e dados da aplicação. Isto ajuda a impedir o registo a partir de reduzir o desempenho da aplicação. 

- **Monitorizar o desempenho.** Utilize um serviço como o [Novo Relic](http://newrelic.com/) ou [Aplicação informações](../application-insights/app-insights-overview.md) para monitorizar aplicações desempenho e comportamento em caso de carga de monitorização de desempenho.  Monitorização de desempenho dá-lhe visão em tempo real a aplicação. Permite-lhe diagnosticar problemas e efetuar cabo uma análise de falhas. 


###  <a name="application-gateway"></a>Gateway de aplicação 

- **Aprovisione, pelo menos, duas instâncias.** Implemente o Gateway aplicação com, pelo menos, duas instâncias. Uma única ocorrência é um ponto de falha único. Utilize duas ou mais ocorrências para redundância e escalabilidade. Para poder qualificar-me para o [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), tem de aprovisionamento duas ou mais ocorrências médias ou grandes. 

### <a name="azure-search"></a>Pesquisa Azure

- **Aprovisione mais do que uma réplica.** Utilize, pelo menos, duas réplicas para alta disponibilidade leitura ou três para leitura e escrita alta disponibilidade.

- **Configure indexadores para implementações da região com várias.** Se tiver uma implementação da região com várias, considere as suas opções para continuidade de indexação.

    - Se a origem de dados replicada geo, aponte cada indexador de cada serviço de pesquisa de Azure regional para respectiva réplica da origem de dados locais.  

    - Se a origem de dados não for replicada geo, aponte indexadores vários na mesma origem de dados, para que os serviços de pesquisa do Azure no várias regiões continuamente e independentemente indexar da origem de dados. Para obter mais informações, consulte o artigo [Considerações de desempenho e Otimização do Azure pesquisa][search-optimization].

###  <a name="azure-storage"></a>Armazenamento Azure 

- **Para dados da aplicação, utilize o acesso de leitura armazenamento geo redundantes (GRS RT).** Armazenamento de RT GRS replica os dados para uma região secundária e fornece acesso só de leitura a partir da região secundário. Se existir uma falha de armazenamento na região principal, a aplicação pode ler os dados da região do secundário. Para mais informações, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md).

- **Para VM discos, utilize o armazenamento de Premium** Para obter mais informações, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

- **Armazenamento de fila de espera, crie uma cópia de segurança fila noutra região.** Armazenamento de fila de espera, uma réplica só de leitura tem limitado utilização, uma vez que não é possível fila ou descarregado itens. Em vez disso, crie uma cópia de segurança fila numa conta de armazenamento no outro região. Se existir uma falha de armazenamento, a aplicação pode utilizar fila de cópia de segurança, até que a região primária fica disponível novamente. Desta forma, a aplicação ainda pode processar novos pedidos de.  

###  <a name="documentdb"></a>DocumentDB 

- **Criar uma réplica da base de dados através de regiões.** Com uma conta com várias região, a base de dados DocumentDB tem de uma escrever região e várias regiões de leitura. Se existir uma falha na região escrita, pode ler a partir de outra réplica. O SDK do cliente trata isto automaticamente. Também pode falhar ao longo da região de escrita para outro região. Para mais informações, consulte o artigo [dados de distribuir globalmente com DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Base de dados SQL 

- **Utilize camadas padrão ou Premium.** Estes camadas fornecem um período de já no momento restaurar (35 dias). Para mais informações, consulte o artigo [Opções de base de dados SQL e o desempenho](../sql-database/sql-database-service-tiers.md).

- **Active a base de dados SQL auditoria.** Auditoria pode ser utilizado para diagnosticar ataques maliciosos ou erro humano. Para mais informações, consulte o artigo [Introdução ao auditoria de base de dados SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Utilização ativa Geo-replicação** Utilize Geo-a replicação do Active para criar um secundário legível numa região diferente.  Se a sua base de dados principal falhar, ou simplesmente necessita de tomar offline, execute um manual activação pós-falha à base de dados secundária.  Até falhar ao longo do, a base de dados secundária permanecerá só de leitura.  Para mais informações, consulte o artigo [Da base de dados ativo Geo-a replicação SQL](../sql-database/sql-database-geo-replication-overview.md). 

- **Sharding de utilização**. Considere utilizar sharding para criar a partições a base de dados na horizontal. Sharding pode fornecer isolamento falhas. Para mais informações, consulte o artigo [dimensionamento saída com a base de dados do SQL Azure](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Utilize em qualquer altura restaurar para recuperar a partir do erro humano.**  Em qualquer altura restaurar devolve a base de dados para um ponto anterior no tempo. Para obter mais informações, consulte o artigo [recuperar uma base de dados do Azure SQL com cópias de segurança da base de dados automatizado][sql-restore].

- **Utilize geo restaurar para recuperar a partir de uma indisponibilidade do serviço.** Restaurar geo restaura uma base de dados a partir de uma cópia de segurança geo redundantes.  Para obter mais informações, consulte o artigo [recuperar uma base de dados do Azure SQL com cópias de segurança da base de dados automatizado][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (a ser executada numa VM)

- **Criar uma réplica da base de dados.** Utilize o SQL Server sempre no grupos de disponibilidade para criar uma réplica da base de dados. Fornece elevada disponibilidade se uma instância do SQL Server falha. Para obter mais informações, consulte o artigo [obter mais informações...](guidance-compute-n-tier-vm.md) 

- **Criar uma cópia de segurança da base de dados**. Se já estiver a utilizar o [Azure cópia de segurança](https://azure.microsoft.com/documentation/services/backup/) para agregar os VMs, considere utilizar [Azure cópia de segurança para utilizar DPM cargas de trabalho do SQL Server](../backup/backup-azure-backup-sql.md). Com esta abordagem, existe uma função de administrador de reserva para a organização e um procedimento de recuperação unificado para VMs e o SQL Server. Caso contrário, utilize o [SQL Server gerido cópia de segurança do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Gestor de tráfego 

- **Execute a reposição de recurso manual.** Depois de uma falha na ligação tráfego Gestor, execute a reposição de recurso manual, em vez de automaticamente a falhar novamente. Antes de voltar a falhar, verifique se todos os subsistemas de aplicação estão saudáveis.  Caso contrário, pode criar uma situação onde a aplicação e para trás inverte entre centros de dados. Para mais informações, consulte o artigo [Executar VMs em várias regiões](guidance-compute-multiple-datacenters.md).

- **Criar um ponto final sonda de estado de funcionamento**. Crie um ponto final de personalizado que relatórios sobre o estado de funcionamento geral da aplicação. Permite que o Gestor de tráfego para falhar sobre se qualquer caminho crítico falhar, não apenas o front-end. O ponto final deve devolver um código de erro HTTP se qualquer dependência crítica está danificado ou inacessível. Não comunicar erros para serviços não críticas, no entanto. Caso contrário, a sonda de estado de funcionamento poderá acionar activação pós-falha quando não é necessária, criar falsos. Para mais informações, consulte [Gestor de tráfego de ponto final de monitorização e activação pós-falha](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Máquinas virtuais 

- **Evite executar uma carga de trabalho de produção numa única VM.** Uma implementação VM única não está e são a manutenção planeada ou não planeada. Em vez disso, coloque VMs vários um conjunto de disponibilidade ou conjunto de escala VM, com um balanceador de carga à frente. Para poder qualificar-me para o SLA, pelo menos mais duas máquinas virtuais tem de ser implementada dentro do mesmo conjunto de disponibilidade. Para mais informações, consulte o artigo [Descrição geral de conjuntos de escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Especifique a disponibilidade definir quando aprovisionar o a VM.** Atualmente, não existe nenhuma forma de adicionar uma VM Gestor de recursos para uma disponibilidade definida após a VM está aprovisionada. Quando adiciona uma nova VM para uma existente disponibilidade definir, certifique-se criar uma imagem para a VM e adicionar NIC ao conjunto de back-end endereço no balanceador de carga. Caso contrário, o Balanceador de carga não encaminha o tráfego de rede para esse VM. 

- **Coloque cada camada de aplicação para um conjunto de disponibilidade em separado.** Numa aplicação de camadas, não coloque VMs a partir de diferentes camadas para o mesmo conjunto de disponibilidade. VMs num conjunto de disponibilidade são colocados em vários domínios falhas (FDs) e atualizar domínios (UD). No entanto, para obter o benefício redundância de FDs e UDs, cada VM no conjunto de disponibilidade têm de lidar com os mesmo pedidos de cliente. 

- **Selecione o tamanho da memória virtual direita com base em requisitos de desempenho.** Quando mover uma carga de trabalho existente para Azure, comece com o tamanho da memória virtual que melhor corresponde aos servidores no local. Em seguida, medir o desempenho da sua carga de trabalho real relativamente às disco IOPS, memória e CPU e ajustar o tamanho, se necessário. Isto ajuda a garantir que a aplicação comporta-se como esperado num ambiente na nuvem. Além disso, se precisar de múltiplas NIC, tenha em atenção o limite da imagem para cada tamanho. 

- **Utilize o armazenamento de premium para VHDs.** Armazenamento de Premium Azure fornece suporte de alto desempenho, baixa latência disco. Para obter mais informações, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md) escolher um tamanho da memória virtual que suporta o armazenamento de premium. 

- **Crie uma conta de armazenamento separada para cada VM.** Coloque os VHDs para um VM para uma conta de armazenamento em separado. Isto ajuda a evitar atingir os limites IOPS para contas de armazenamento. Para mais informações, consulte o artigo [escalabilidade de armazenamento do Azure e metas de desempenho](../storage/storage-scalability-targets.md). No entanto, se estiver a implementar um grande número de VMs, tenha em atenção o limite de por subscrição para contas de armazenamento. Consulte o artigo [limites de armazenamento](../azure-subscription-service-limits.md#storage-limits).

- **Criar uma conta de armazenamento em separado para registos de diagnóstico**. Registos de diagnóstico de escrever a mesma conta de armazenamento como VHDs, para evitar ter ligações que o registo de diagnóstico não vai afetar o IOPS para os discos VM. Uma conta de armazenamento localmente redundante (LRS) padrão é suficiente para registos de diagnóstico. 

- **Instale aplicações num disco dados, não o disco de SO.** Caso contrário, podem atingir o limite de tamanho de disco. 

- **Utilize o Azure cópia de segurança para agregar VMs.** Cópias de segurança protegem contra a perda de dados acidentais. Para obter mais informações, consulte [Proteger VMs Azure com serviços cofre recuperação](../backup/backup-azure-vms-first-look-arm.md).

- **Ativar registos de diagnóstico**, incluindo métricas de estado de funcionamento básicas, registos de infraestrutura e [Diagnósticos de arranque][boot-diagnostics]. Arranque diagnósticos podem ajudar a diagnosticar uma falha de arranque se obtém a sua VM para o estado não são de arranque. Para obter mais informações, consulte o artigo [Descrição geral do Azure registos de diagnóstico][diagnostics-logs].

- **Utilizar a extensão de AzureLogCollector**. (Apenas VMs Windows.) Esta extensão agregados registos de plataforma Azure e carrega-los de armazenamento Azure, sem o operador remotamente registo para a VM. Para mais informações, consulte o artigo [AzureLogCollector extensão](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Rede virtual 

- **A lista branca ou bloco de endereços IP públicos, adicionar um NSG à sub-rede.** Bloquear o acesso de utilizadores maliciosos ou permitir o acesso só de utilizadores que possui privilégios para aceder à aplicação.  

- **Crie uma sonda de estado de funcionamento personalizado.** Pode testar o sondas de estado de funcionamento de Balanceador de carga HTTP ou TCP. Se uma VM for executado num servidor HTTP, a sonda de HTTP é um melhor indicador do Estado de funcionamento que uma sonda TCP. Para uma sonda HTTP, utilize um ponto final de personalizado que reporta o estado de funcionamento geral da aplicação, incluindo todas as dependências críticas. Para mais informações, consulte o artigo [Descrição geral do Balanceador de carga Azure](../load-balancer/load-balancer-overview.md).

- **Não bloquear a sonda de estado de funcionamento.** A sonda de estado de funcionamento de Balanceador de carga é enviada a partir de um endereço IP conhecido, 168.63.129.16. Não bloquear o tráfego para ou a partir deste IP em qualquer políticas de firewall ou as regras de grupo (NSG) de segurança de rede. Bloquear a sonda de estado de funcionamento fará Balanceador de carga remover a VM de rotação. 

- **Ativar o registo de Balanceador de carga.** Os registos de mostram VMs quantos no back-end não está a receber o tráfego de rede devido a respostas sonda falha. Para obter mais informações, consulte [a análise de registo de Balanceador de carga Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
