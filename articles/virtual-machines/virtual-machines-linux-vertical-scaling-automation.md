<properties
    pageTitle="Dimensionar verticalmente Azure máquina virtual com a automatização Azure | Microsoft Azure"
    description="Como verticalmente dimensionar uma máquina de Virtual Linux em resposta a monitorização de alertas de automatização do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>Dimensionar verticalmente Azure máquina virtual com a automatização do Azure

Dimensionamento vertical é o processo de crescentes ou decrescentes os recursos de uma máquina em resposta à carga de trabalho. No Azure pode fazê-lo ao alterar o tamanho da Máquina Virtual. Isto pode ajudar a nos cenários seguintes

- Se a Máquina Virtual não está a ser utilizado com frequência, pode redimensioná-lo para baixo para um tamanho mais pequeno para reduzir os custos mensais
- Se a Máquina Virtual está a ver um carregamento de pico, pode ser redimensionada para um tamanho para aumentar a sua capacidade maior

O destaque para obter os passos realizar esta tarefa está como abaixo

1. Azure automatização para aceder à sua máquinas virtuais do programa de configuração
2. Importar runbooks a escala do Azure automatização Vertical para a sua subscrição
3. Adicionar um webhook ao seu livro de execuções
4. Adicionar um alerta para o seu computador Virtual

> [AZURE.NOTE] Devido ao tamanho da primeira Máquina Virtual, os tamanhos podem ser dimensionado, podem ser limitados devido a disponibilidade dos outros tamanhos no cluster que Máquina Virtual atual está implementada no. No runbooks a automatização publicados utilizados neste artigo estamos encarregam-neste caso e apenas Dimensionar dentro de abaixo pares de tamanho VM. Isto significa que uma máquina de Virtual Standard_D1v2 inesperadamente não vão ser dimensionados para cima para Standard_G5 ou dimensionados para baixo para Basic_A0.

>| VM tamanhos par de dimensionamento |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Azure automatização para aceder à sua máquinas virtuais do programa de configuração

A primeira coisa que precisa de fazer é criar uma conta de automatização do Azure que aloja runbooks utilizado para dimensionar as instâncias VM escala definida. Recentemente o serviço de automatização introduzida a funcionalidade de "Executar como conta", que torna a definição para cima o capital de serviço para automaticamente a executar o runbooks em nome do utilizador muito fácil. Pode ler mais sobre isto no artigo abaixo:

* [Autenticar Runbooks com a conta Azure executar como](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar runbooks a escala do Azure automatização Vertical para a sua subscrição

Os que são necessários para verticalmente dimensionamento Máquina Virtual runbooks já estão publicados na Galeria de livro execuções de automatização do Azure. Terá de importá-los na sua subscrição. Pode obter informações sobre como importar runbooks lendo o seguinte artigo.

* [Livro execuções e módulo galerias de automatização do Azure](../automation/automation-runbook-gallery.md)

Os que precisam de ser importado runbooks são apresentadas na imagem abaixo

![Importar runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu livro de execuções

Após importar runbooks que vai precisar de adicionar um webhook ao livro de execuções, de modo que pode ser acionado a um alerta a partir de uma Máquina Virtual. Os detalhes de criação de um webhook para o seu livro de execuções podem ser lidos aqui

* [Azure webhooks de automatização](../automation/automation-webhooks.md)

Certifique-se de que copiar o webhook antes de fechar a caixa de diálogo webhook que irá precisar isto na secção seguinte.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta para o seu computador Virtual

1. Selecione definições de Máquina Virtual
2. Selecione "Alerta de regras"
3. Selecione "Adicionar alerta"
4. Selecione uma métrica seja acionada o alerta sobre
5. Selecione uma condição, que, quando preenchidas vai fazer com que o alerta para fire
6. Selecione um limite para a condição no passo 5. para ser preenchidas
7. Selecione um período de através da qual o serviço de monitorização irá verificar a condição e o limiar nos passos 5 e 6
8. Cole webhook que copiou da secção anterior.

![Adicionar o alerta para uma máquina de Virtual 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar o alerta para uma máquina de Virtual 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)