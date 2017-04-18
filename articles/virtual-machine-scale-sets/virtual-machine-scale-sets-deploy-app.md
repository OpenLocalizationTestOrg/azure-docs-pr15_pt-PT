<properties
    pageTitle="Implementar uma aplicação nos conjuntos de escala de Máquina Virtual | Microsoft Azure"
    description="Implementar uma aplicação nos conjuntos de escala de Máquina Virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Implementar uma aplicação nos conjuntos de escala de Máquina Virtual

Uma aplicação em execução num conjunto de escala de VM normalmente é implementada de uma das três formas:

- Instalar software novo numa imagem plataforma no momento da implementação. Uma imagem de plataforma neste contexto é uma imagem de sistema operativo do Azure Marketplace, como Ubuntu 16.04, Windows Server 2012 R2, etc.

Pode instalar o software novo numa imagem plataforma utilizando uma [Extensão de VM](../virtual-machines/virtual-machines-windows-extensions-features.md). Uma extensão VM é um software que é executada quando uma VM é implementada. Pode executar qualquer código que goste no momento da implementação utilizando uma extensão de scripts personalizados. [Aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) está um exemplo de modelo de Gestor de recursos do Azure com duas extensões VM: um Linux extensão de Script personalizado para instalar Apache e PHP e uma extensão de diagnóstico para emitir dados de desempenho utilizados pelo Azure Autoscale.

Uma vantagem desta abordagem é que tem um nível de separação entre o seu código da aplicação e o sistema operativo e, pode manter a sua aplicação separadamente. Obviamente o que significa que existem também é mais mover peças e tempo de implementação VM poderão ser mais longa se existir um lote para o script transferir e configurar.

**Se a verificação ser efetuada com informações confidenciais no comando extensão de Script personalizado (tal como uma palavra-passe), certifique-se especificar o `commandToExecute` na `protectedSettings` atributo da extensão de Script personalizado em vez do `settings` atributo.**

- Crie uma imagem VM personalizada que inclui o SO e a aplicação num único VHD. Aqui o conjunto de escala é composta por um conjunto de VMs copiados a partir de uma imagem criada pelo utilizador que tem de manter. Esta abordagem não requer nenhuma configuração extra no momento da implementação VM. No entanto, o `2016-03-30` versão VM escala de conjuntos de (e versões anteriores), os discos SO para VMs no conjunto de escala estão limitados a uma conta de armazenamento única. Assim, pode ter um máximo de 40 VMs num conjunto de escala, por oposição a VM 100 por escala Definir limite com imagens de plataforma. Consulte o artigo [Descrição geral de estrutura definir escala](./virtual-machine-scale-sets-design-overview.md) para obter mais detalhes.

- Implementar uma plataforma ou uma imagem personalizada que que mostra basicamente é um anfitrião do contentor e instale a aplicação como um ou mais membros em contentores gerir com uma ferramenta de gestão orchestrator ou configuração. A coisa boa sobre esta abordagem é que tenham captadas sua infraestrutura da nuvem a partir da camada de aplicação e pode mantê-las em separado.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>O que acontece quando um conjunto de escala de VM escalas saída?

Quando adiciona um ou mais VMs para uma escala de definir ao aumentar a capacidade – se manualmente ou através de autoscale – a aplicação é instalada automaticamente. Por exemplo se definir a escala tem extensões definidas, são executadas numa nova VM sempre que é criado. Se o conjunto de escala baseia-se uma imagem personalizada, qualquer novo VM é uma cópia da imagem personalizada origem. Se definir a escala VMs são anfitriões de contentor, em seguida, poderá ter o código de arranque para carregar os contentores de uma extensão de Script personalizado ou uma extensão poderá instalar um agente que regista com um orchestrator cluster (como o serviço de contentor Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Como gerir as atualizações de aplicação no VM conjuntos de escala

Atualizações de aplicação em conjuntos de escala VM, três abordagens principais siga três métodos de implementação de aplicação anterior:

* Atualizar com as extensões de VM. Quaisquer extensões VM que são definidos para um conjunto de escala de VM são executados sempre que é implementada uma nova VM, um VM existente é reimaged ou uma extensão VM é atualizada. Se precisar de atualizar a aplicação, diretamente atualizar uma aplicação através de extensões é uma abordagem viável – basta atualizar a definição da extensão. É uma forma simple para fazê-lo ao alterar fileUris para apontarem para o novo software.

* Abordagem imutáveis imagem personalizada. Quando bolos aplicação (ou componentes de aplicação) para uma imagem VM pode concentrar-se sobre a criação de um pipeline para automatizar a compilação, teste e implementação das imagens. Pode estruturar sua arquitetura para facilitar a trocar rápida de um conjunto de escala faseada para produção. Um bom exemplo desta abordagem é o [Azure Spinnaker controlador funciona](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Embalador e Terraform também suportam o Gestor de recursos do Azure, para que possa também definir o seu imagens "como código" e criá-los no Azure, em seguida, utilize o VHD no seu conjunto de escala. No entanto, ao fazê-lo por isso, teria tornam-se problemático para imagens Marketplace, onde extensões/personalizada scripts tornam-se mais importantes uma vez que não manipular diretamente bits do Marketplace.

* Atualize contentores. Abstract a gestão de ciclo de vida de aplicação a um nível acima a infraestrutura de nuvem, por exemplo por aplicações encapsulating e componentes de aplicação para contentores e gerir nestes contentores através de orchestrators contentor e gestores de configuração como chefe/Puppet.

A escala definir VMs, em seguida, tornam-se um substrato estável para os contentores e introduzir apenas segurança ocasional e atualizações relacionadas com o sistema operativo. Tal como mencionado, o serviço de contentor Azure é um bom exemplo de efetuar esta abordagem e de construção de um serviço à volta do mesmo.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Como é filmar saída de uma atualização SO através de actualização de domínios?

Imaginemos que pretende atualizar a sua imagem do sistema operativo enquanto mantém a execução VM escala definida. Uma forma de fazê-lo é para atualizar a VM imagens uma VM de cada vez. Pode fazê-lo com o PowerShell ou clip de Azure. Existem comandos separados para atualizar o modelo VM escala definida (como a sua configuração é definida) e emitir chamadas de "atualização manual" em VMs individuais.

[Aqui](https://github.com/gbowerman/vmsstools) está um exemplo de script de Python que automatiza o processo de atualização de um domínio de uma atualização de VM escala definida uma vez. (Truque: é mais de uma prova de conceito que uma solução de produção pronto para puro – poderá pretender adicionar alguns etc verificação de erro.).
