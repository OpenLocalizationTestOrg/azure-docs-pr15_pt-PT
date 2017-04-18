<properties
   pageTitle="Configurar a atualização de uma aplicação de serviço ferro | Microsoft Azure"
   description="Saiba como configurar as definições para atualizar uma aplicação de serviço ferro utilizando o Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de uma aplicação de serviço ferro no Visual Studio

Visual Studio tools para Azure Service ferro fornecer suporte para a atualização de publicação para clusters locais ou remotos. Existem duas vantagens para atualizar a sua aplicação para uma versão mais recente em vez de substituir a aplicação durante o teste e depuração:

- Dados da aplicação não será perdidos durante a atualização.
- Disponibilidade permanece alta para que não haverá qualquer interrupção de serviço durante a atualização, se existirem que suficiente instâncias do serviço distribuir por domínios atualização.

Testes podem ser executados contra uma aplicação enquanto está a ser actualizado-lo.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários atualizar

Pode escolher entre dois tipos de implementação: regular ou para a atualização. Uma implementação normal apaga os dados num cluster e informações de implementação anterior enquanto uma implementação atualização preserva-lo. Quando atualizar uma aplicação de serviço ferro no Visual Studio, tem de fornecer as políticas de verificação de parâmetros de atualização da aplicação e estado de funcionamento do. Parâmetros de atualização da aplicação ajudam a controlar a atualização, enquanto as políticas de verificação do Estado de funcionamento determinam se a atualização foi efetuada com êxito. Consulte o artigo [atualização da aplicação de serviço ferro: atualizar parâmetros](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Existem três modos de atualização: *monitorizadas*, *UnmonitoredAuto*e *UnmonitoredManual*.

  - Uma atualização monitorizadas automatiza a atualização e a verificação de integridade de aplicação.

  - Uma atualização UnmonitoredAuto automatiza a atualização, mas ignora a verificação de integridade de aplicação.

  - Após fazer uma atualização de UnmonitoredManual, tem de atualizar manualmente a cada atualização domínio.

Cada modo atualização requer diferentes conjuntos de parâmetros. Consulte o artigo [atualizar a aplicação de parâmetros](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções disponíveis para a atualização.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar uma aplicação de serviço ferro no Visual Studio

Se estiver a utilizar as ferramentas de ferro de serviço do Visual Studio para atualizar uma aplicação de serviço ferro, pode especificar um processo de publicar para ser uma atualização em vez de uma implementação normal ao selecionar a caixa de verificação **atualizar a aplicação** .

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização

1. Clique no botão **Definições** ao lado da caixa de verificação. É apresentada a caixa de diálogo **Editar parâmetros de atualização** . Caixa de diálogo **Editar parâmetros atualizar** suporta os modos de atualização monitorizadas, UnmonitoredAuto e UnmonitoredManual.

2. Selecione o modo de atualização que pretende utilizar e, em seguida, preencha a grelha de parâmetro.

    Cada parâmetro tem valores predefinidos. O parâmetro opcional *DefaultServiceTypeHealthPolicy* leva-o até uma entrada de tabela hash. Eis um exemplo de formato de entrada da tabela hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outra parâmetro opcional que leva-o até uma entrada de tabela hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Eis um exemplo real vida:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Se selecionar o modo de atualização de UnmonitoredManual, tem de iniciar manualmente uma consola do PowerShell para continuar e concluir o processo de atualização. Referir-se a [atualização da aplicação de serviço ferro: avançadas tópicos](service-fabric-application-upgrade-advanced.md) para obter informações sobre a atualização manual como funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar uma aplicação ao utilizar o PowerShell

Pode utilizar os cmdlets do PowerShell para atualizar uma aplicação de serviço ferro. Consulte o artigo [aplicação de serviço ferro atualizar tutorial](service-fabric-application-upgrade-tutorial.md) e [Iniciar ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar uma política de verificação do Estado de funcionamento no ficheiro manifesto da aplicação

Cada serviço numa aplicação de serviço ferro pode ter os suas próprias parâmetros de política de estado de funcionamento que substituam os valores predefinidos. Pode disponibilizar estas valores de parâmetros no ficheiro manifesto da aplicação.

O exemplo seguinte mostra como aplicar uma política de verificação do Estado de funcionamento exclusivo para cada serviço no manifesto da aplicação.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Próximos passos
Para mais informações sobre como implementar uma aplicação, consulte o artigo [Implementar uma aplicação existente no Azure Service ferro](service-fabric-deploy-existing-app.md).
