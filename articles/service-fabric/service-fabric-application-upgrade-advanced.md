<properties
   pageTitle="Atualização da aplicação: avançadas tópicos | Microsoft Azure"
   description="Este artigo aborda alguns tópicos avançados sobre a actualização de uma aplicação de serviço ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização da aplicação de serviço ferro: avançadas tópicos

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Adicionar ou remover serviços durante uma atualização de aplicação

Se um novo serviço é adicionado a uma aplicação que é já implementada e publicada como uma atualização, o novo serviço é adicionado para a aplicação implementada.  Como uma atualização não vai afetar quaisquer serviços que já foram parte da aplicação. No entanto, uma instância do serviço que foi adicionado tem de ser iniciada para o novo serviço esteja ativa (utilizando o `New-ServiceFabricService` cmdlet).

Também podem ser removidos serviços a partir de uma aplicação como parte de uma atualização. No entanto, todas as instâncias actuais do serviço para-be-eliminados tem de ser interrompidas antes de prosseguir com a atualização (utilizando o `Remove-ServiceFabricService` cmdlet). 

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [AZURE.NOTE]  O modo manual não monitorizado deve ser considerado apenas para uma atualização falhou ou suspensa. O modo monitorizado é o modo de atualização recomendado para as aplicações de serviço ferro.

Azure Service ferro fornece várias modos de atualização para suportar clusters de desenvolvimento e de produção. Opções de implementação escolhidas podem ser diferentes para diferentes ambientes.

A atualização de aplicação de ficha técnica em monitorizada é a atualização mais típica para utilizar em produção. Quando a política de atualização é especificada, o serviço ferro assegura que a aplicação está em bom estada antes da atualização avança.

 O administrador da aplicação pode utilizar o manual ficha técnica em atualização modo da aplicação tenham controlo total sobre o progresso de atualização através de vários domínios atualização. Neste modo é útil quando é necessária uma política de avaliação do Estado de funcionamento personalizada ou complexa, ou uma atualização não convencional está a acontecer (por exemplo, a aplicação já está na perda de dados).

Por fim, a atualização de aplicação de ficha técnica em automatizado é útil para desenvolvimento ou ambientes de testes para fornecer um ciclo de iteração fast durante o desenvolvimento de serviço.

## <a name="change-to-manual-upgrade-mode"></a>Alterar para o modo de atualização manual
**Manual**– pare a atualização da aplicação na UD atual e alterar o modo de atualização manual não monitorizada. O administrador tem de ligar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar com a atualização ou acionar uma reversão iniciando uma nova atualização. Assim que a atualização entra no modo de Manual, esta permanece no modo de Manual até uma nova atualização é iniciada. O comando **GetApplicationUpgradeProgressAsync** devolve ferro\_aplicação\_ATUALIZAR\_estado\_gradual\_reencaminhar\_pendente.

## <a name="upgrade-with-a-diff-package"></a>Atualizar com um pacote de detecção de diferenças

Uma aplicação de serviço ferro pode ser actualizada por aprovisionamento com um pacote de aplicação completo, autónomo. Também pode ser actualizada uma aplicação ao utilizar um pacote de detecção de diferenças que contém apenas os ficheiros da aplicação atualizada, manifesto da aplicação atualizada e os ficheiros de manifesto de serviço.

Um pacote de aplicação completo contém todos os ficheiros necessários para iniciar e executar uma aplicação de serviço ferro. Um pacote de detecção de diferenças contém apenas os ficheiros que alterados entre última disposição e a atualização atual, bem como manifesto da aplicação completo e o serviço manifesto ficheiros. Qualquer referência na manifesto aplicação ou manifestos de serviço que não não possível localizar o esquema de compilação deve ser procurada no arquivo de imagem.

Pacotes de aplicação completo são necessários para a primeira instalação de uma aplicação para o cluster. Atualizações posteriores podem ser um pacote de aplicação completo ou um pacote de detecção de diferenças.

Ocasiões quando utilizar um pacote de detecção de diferenças seria uma boa escolha:

* Um pacote de detecção de diferenças é preferido quando tem um pacote de aplicação grandes que referencia vários ficheiros manifesto de serviço e/ou várias pacotes de código, config pacotes ou pacotes de dados.

* Um pacote de detecção de diferenças é preferido quando tem um sistema de implementação que gera o esquema de compilação diretamente a partir do processo de compilação de aplicação. Neste caso, apesar do código não foi alterado, montagens recentemente concebidas obtém uma soma de verificação diferente. Utilizar um pacote de aplicação completo seria necessitam que Atualize a versão no todos os pacotes de código. Utilizar um pacote de detecção de diferenças, apenas fornece os ficheiros que foi alterado e os ficheiros de manifesto onde a versão foi alterado.

Quando uma aplicação é actualizada utilizando o Visual Studio, o pacote de detecção de diferenças é publicado automaticamente. Para criar um pacote de detecção de diferenças manualmente, manifesto da aplicação e manifestos serviço têm de ser atualizadas, mas apenas os pacotes alterados deverão ser incluídos no pacote de aplicação final. 

Por exemplo, vamos começar com a aplicação seguinte (números de versão fornecidos para facilitar a compreensão):

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Agora, vamos suponha que pretende atualizar apenas o código pacote de serviço 1 utilizando um pacote de detecção de diferenças através do PowerShell. Agora, a aplicação atualizada tem a estrutura de pastas seguintes:

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Neste caso, pode atualizar o manifesto da aplicação para 2.0.0 e o manifesto do serviço para o serviço 1 refletir a atualização do pacote de código. A pasta para o seu pacote de aplicação teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Próximos passos

[Atualizar a sua aplicação de utilizar o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta uma atualização de aplicação utilizando o Visual Studio.

[Atualizar a sua aplicação de utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta uma atualização de aplicação através do PowerShell.

Controlar como a aplicação actualiza utilizando [Parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne o seu actualizações de aplicação compatíveis ao aprender a utilizar [Dados serialização](service-fabric-application-upgrade-data-serialization.md).

Corrigir problemas comuns em actualizações de aplicação ao referir os passos em [Actualizações de resolução de problemas de aplicação](service-fabric-application-upgrade-troubleshooting.md).
 
