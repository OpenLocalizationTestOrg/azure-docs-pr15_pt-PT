<properties
   pageTitle="Actualizar um cluster de Azure Service ferro | Microsoft Azure"
   description="Atualizar o código de serviço ferro e/ou configuração que executa um cluster de ferro de serviço, incluindo a definição de modo de actualização de cluster, atualizar, certificados de adicionar portas de aplicação, efetuar patches SO, e assim sucessivamente. O que esperar quando são executadas as actualizações?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Actualizar um cluster de ferro de serviço do Azure

> [AZURE.SELECTOR]
- [Azure Cluster](service-fabric-cluster-upgrade.md)
- [Cluster autónomo](service-fabric-cluster-upgrade-windows-server.md)

Para qualquer sistema operativo, estruturar para possibilidades é fundamental para alcançar a longo prazo sucesso do seu produto. Um cluster de Azure Service ferro é um recurso que o proprietário, mas parcialmente é gerida pela Microsoft. Este artigo descreve o que é gerido automaticamente e o que pode configurar o seu próprio.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de ferro que é executada no seu Cluster

Pode definir o seu cluster para receber actualizações ferro automática, quando a Microsoft disponibilizar uma nova versão ou selecione para selecionar uma versão suportada ferro que pretende que o seu cluster de estar ligado.

Pode fazê-lo definindo a configuração do cluster "upgradeMode" no portal de ou através do Gestor de recurso no momento da criação ou posterior num cluster vivos 

>[AZURE.NOTE] Certifique-se manter o seu cluster a executar uma versão de ferro suportados sempre. Como e quando anunciar estamos a versão de uma versão nova do ferro de serviço, a versão anterior está marcada para o fim de suporte após um mínimo de 60 dias a partir dessa data. o as versões novas são anunciada [no blogue de equipa do serviço ferro](https://blogs.msdn.microsoft.com/azureservicefabric/ ). A nova versão está disponível para, em seguida, selecione. 

14 dias antes do termo do lançamento do que seu cluster estiver em execução, um evento de estado de funcionamento é gerado que coloca o seu cluster num Estado de funcionamento de aviso. Cluster permanece num Estado Aviso até que Atualize para uma versão suportada ferro.


### <a name="setting-the-upgrade-mode-via-portal"></a>Definir o modo de atualização através do portal 

Pode configurar o cluster para automático ou manual quando estiver a criar o cluster.

![Create_Manualmode][Create_Manualmode]

Pode definir o cluster para automático ou manual quando num cluster direto, utilizando a experiência de gerir. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Atualizar para uma nova versão num cluster de que está definido para modo Manual através do portal.
 
Para atualizar para uma nova versão, tudo o que precisa de fazer é selecione a versão disponível na lista pendente e guardar. A atualização de ferro obtém pontapés automaticamente. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações executadas no cluster) aderiu para durante a atualização.

Se não forem cumpridas as políticas de estado de funcionamento de cluster, a atualização é revertida. Desloque-se para baixo este documento para ler mais sobre como definir essas exigências de estado de funcionamento personalizado. 

Depois de ter corrigido os problemas que resultou na anulação, tem de iniciar a atualização novamente, seguindo os mesmos passos.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Definir o modo de atualização através de um modelo de Gestor de recursos 

Adicionar a configuração de "upgradeMode" para a definição do recurso Microsoft.ServiceFabric/clusters e defina "clusterCodeVersion" para uma das versões ferro suportados conforme apresentado abaixo e, em seguida, implementar o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Atualizar para uma nova versão num cluster de que está definido para modo Manual através de um modelo de Gestor de recursos.
 
Quando o cluster estiver no modo Manual, atualize para uma nova versão, altere "clusterCodeVersion" para uma versão suportada e implementá-lo. A implementação do modelo, kicks da atualização a ferro obtém pontapés automaticamente. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações executadas no cluster) aderiu para durante a atualização.

Se não forem cumpridas as políticas de estado de funcionamento de cluster, a atualização é revertida. Desloque-se para baixo este documento para ler mais sobre como definir essas exigências de estado de funcionamento personalizado. 

Depois de ter corrigido os problemas que resultou na anulação, tem de iniciar a atualização novamente, seguindo os mesmos passos.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obter a lista de todos os versão disponível para todos os ambientes para uma subscrição determinado

Execute o seguinte comando e, deve obter um resultado semelhante ao seguinte.

"supportExpiryUtc" indica ao seu quando um determinado lançamento estiver prestes a expirar ou expirou. A versão mais recente não têm uma data válida - -tem um valor de "9999-12-31T23:59:59.9999999", que significa, muito simplesmente que a data de expiração não está definida ainda.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamento de atualização ferro quando o modo de atualizar cluster é automático

A Microsoft mantém o código de ferro e a configuração que é executada num Azure cluster. Vamos executar actualizações monitorizadas automáticas para o software numa base conforme necessário. Estes atualiza os dever código, configuração ou ambas. Para se certificar de que a sua aplicação sofre sem impacto ou um impacto mínimo devido a estes atualiza os, podemos efetue as actualizações as fases seguintes:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é executada ao utilizar todas as políticas de estado de funcionamento de cluster

Nesta fase, as actualizações continuar um domínio de atualização, uma vez e as aplicações que estavam em execução no cluster continuarem a ser executado sem qualquer tempo de inatividade. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações executadas no cluster) aderiu para durante a atualização.

Se não forem cumpridas as políticas de estado de funcionamento de cluster, a atualização é revertida. Em seguida, uma mensagem de e-mail é enviada para o proprietário da subscrição. O e-mail contém as seguintes informações:

- Notificação de tivemos que recuperar uma atualização de cluster.
- Sugerido reparação ações, se existir.
- O número de dias (n) até vamos executar fase 2.

Vamos tentar executar a atualização do mesmo algumas mais vezes caso qualquer atualiza os falhou por razões de infraestrutura. Depois dos dias n a partir da data, que a mensagem foi enviada, podemos continuar a fase 2.

Se as políticas de estado de funcionamento de cluster forem cumpridas, a atualização é considerada efetuada com êxito e marcada como concluída. Isto pode ocorrer durante a atualização inicial ou qualquer um das atualização repetições nesta fase. Não existe nenhuma confirmação de correio eletrónico de uma executar com êxito. Este é para evitar enviar e-mails demasiadas – receber uma mensagem de e-mail que deve ser visualizadas como uma exceção ao normal. Vamos esperar a maior parte das actualizações cluster ser concluída com êxito, sem que afetam a sua disponibilidade de aplicação.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é executada ao utilizar apenas o políticas de estado de funcionamento predefinidas

As políticas de estado de funcionamento nesta fase são definidas de forma a que o número de aplicações que foram Saudável no início da atualização se mantém o mesmo para a duração do processo de atualização. Tal como a fase 1, as actualizações de fase 2 avance um domínio de atualização, uma vez e, as aplicações que estavam em execução no cluster continuarem a ser executado sem qualquer tempo de inatividade. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações executadas no cluster) aderiu para a duração da atualização.

Se as políticas de estado de funcionamento de cluster de facto não forem cumpridas, a atualização é revertida. Em seguida, uma mensagem de e-mail é enviada para o proprietário da subscrição. O e-mail contém as seguintes informações:

- Notificação de tivemos que recuperar uma atualização de cluster.
- Sugerido reparação ações, se existir.
- O número de dias (n) até vamos executar fase 3.

Vamos tentar executar a atualização do mesmo algumas mais vezes caso qualquer atualiza os falhou por razões de infraestrutura. Um e-mail de lembrete é enviado apenas alguns dias antes de n dias encontram-se para cima. Depois dos dias n a partir da data, que a mensagem foi enviada, podemos continuar a fase 3. Os e-mails de enviarmos na fase 2 devem ser tomados seriamente e ações reparação devem ser tomadas.

Se as políticas de estado de funcionamento de cluster forem cumpridas, a atualização é considerada efetuada com êxito e marcada como concluída. Isto pode ocorrer durante a atualização inicial ou qualquer um das atualização repetições nesta fase. Não existe nenhuma confirmação de correio eletrónico de uma executar com êxito.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Uma atualização é executada ao utilizar as políticas de estado de funcionamento agressivas

Estas políticas de estado de funcionamento nesta fase são orientadas para conclusão da atualização em vez do Estado de funcionamento das aplicações. Actualizações do cluster muito poucos conclui nesta fase. Se o seu cluster pode entrar nesta fase, existe a oportunidade de boa que a aplicação ficar danificada e/ou perder disponibilidade.

Semelhantes às outras duas fases, actualizações de fase 3, proceda um domínio para a atualização ao mesmo tempo.

Se não forem cumpridas as políticas de estado de funcionamento de cluster, a atualização é revertida. Vamos tentar executar a atualização do mesmo algumas mais vezes caso qualquer atualiza os falhou por razões de infraestrutura. Após esta ação, cluster é afixado, para que já não irá receber suporte e/ou atualiza os.

Uma mensagem de e-mail com esta informação é enviada para o proprietário da subscrição, juntamente com as ações de reparação. Não podemos esperar qualquer clusters para aceder a um Estado onde fase 3 falhou.

Se as políticas de estado de funcionamento de cluster forem cumpridas, a atualização é considerada efetuada com êxito e marcada como concluída. Isto pode ocorrer durante a atualização inicial ou qualquer um das atualização repetições nesta fase. Não existe nenhuma confirmação de correio eletrónico de uma executar com êxito.

## <a name="cluster-configurations-that-you-control"></a>Configurações de cluster que pode controlar o

Para além da capacidade de configurar o cluster atualizar modo, aqui estão as configurações que pode alterar num cluster direto.

### <a name="certificates"></a>Certificados

Pode adicionar novos ou eliminar certificados para o cliente através do portal e cluster facilmente. Consulte [Este documento](service-fabric-cluster-security-update-certs-azure.md) para obter instruções detalhadas

![Captura de ecrã que mostra as miniaturas de certificado no portal do Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Portas de aplicações

Pode alterar portas de aplicação ao alterar as propriedades do recurso Balanceador de carga que estão associadas com o tipo de nó. Pode utilizar o portal ou pode utilizar o Gestor de recursos do PowerShell diretamente.

Para abrir uma nova porta no VMs todos os um tipo de nó, faça o seguinte:

1. Adicione uma nova sonda ao balanceador de carga adequado.

    Se implementado seu cluster utilizando o portal, balanceadores de carga são com o nome "LB nome do grupo-NodeTypename recurso", um para cada tipo de nó. Uma vez que os nomes de Balanceador de carga são exclusivos apenas a partir de um grupo de recursos, é melhor se é procurá-los num grupo de recursos específico.

    ![Captura de ecrã que mostra a adição de uma sonda para um balanceador de carga no portal.][AddingProbes]

2. Adicione uma nova regra ao balanceador de carga.

    Adicione uma nova regra para o mesmo Balanceador de carga, utilizando a sonda que criou no passo anterior.

    ![Adicionar uma nova regra para um balanceador de carga no portal.][AddingLBRules]


### <a name="placement-properties"></a>Propriedades de posicionamento

Para cada um dos tipos de nó, pode adicionar propriedades de posicionamento personalizado que pretende utilizar nas suas aplicações. Tipo de nó é uma propriedade predefinida que pode utilizar sem a adicionar explicitamente.

>[AZURE.NOTE] Para obter detalhes sobre a utilização de posicionamento restrições, propriedades de nós e como defini-las, consulte a secção "Posicionamento restrições e propriedades de nó" no documento Gestor de recursos do serviço ferro Cluster no [Que descreve o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Métricas capacidade

Para cada um dos tipos de nó, pode adicionar métricas de capacidade personalizado que pretende utilizar nas suas aplicações a carga de relatório. Para obter detalhes sobre a utilização de métricas de capacidade ao relatório de carregar, referir-se aos documentos Gestor de recursos do serviço ferro Cluster no [Que descreve o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carregar](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>As definições de atualização ferro - as políticas de estado de funcionamento

Pode especificar as políticas de estado de funcionamento personalizado para a atualização de ferro. Se tiver de definir o seu cluster a actualizações ferro automática, em seguida, estas políticas obtenham aplicadas à fase 1 das actualizações ferro automática.
Se tiver de definir o cluster para ferro Manual atualiza os, estas políticas obtenham aplicadas sempre que selecionar uma nova versão acionar o sistema para iniciar a atualização de ferro no seu cluster. Se não substituem as políticas, são utilizadas as predefinições.

Pode especificar as políticas de estado de funcionamento personalizado ou rever as definições atuais em pá "atualização ferro", ao selecionar as definições avançadas de atualização. Reveja a seguinte imagem sobre como. 

![Gerir políticas de estado de funcionamento personalizado][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar definições de ferro para o seu cluster

Consulte [as definições do serviço ferro cluster ferro](service-fabric-cluster-fabric-settings.md) o que e como os pode personalizar.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Patches SO nas VMs que compõem o cluster

Esta funcionalidade é planeada para o futuro como uma funcionalidade de automatizada. Mas atualmente, é responsável correcção seu VMs. Tem de efetuar esta uma VM cada vez, para que não tirar para baixo mais do que uma de cada vez.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Atualiza os do SO nas VMs que compõem o cluster

Se tem de atualizar a imagem SO nas máquinas virtuais do cluster, deve fazê-lo um VM cada vez. É responsável para esta atualização – momento não existe nenhuma automatização para esta situação.

## <a name="next-steps"></a>Próximos passos
- Saiba como personalizar algumas das [definições do serviço de ferro cluster ferro](service-fabric-cluster-fabric-settings.md)
- Saiba como [Dimensionar e reduzir o seu cluster](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [actualizações de aplicação](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG