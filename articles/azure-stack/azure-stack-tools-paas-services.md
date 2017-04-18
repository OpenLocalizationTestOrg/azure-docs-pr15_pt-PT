<properties
    pageTitle="Serviços de ferramentas e PaaS para Azure pilha | Microsoft Azure"
    description="Saiba como começar a trabalhar com os serviços de PaaS na pilha de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Ferramentas para pilha Azure

Pode transferir as ferramentas de descrito abaixo. Se quiser ser notificado de novos serviços e ferramentas, siga #AzureStack no Twitter.

## <a name="template-tools"></a>Ferramentas de modelo

### <a name="azure-stack-github-templates"></a>Modelos de pilha Github Azure
Explore a coleção de crescimento dos [Modelos do Azure pilha GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates). Tal como [Azure](https://github.com/Azure/azure-quickstart-templates), estes modelos de "Início rápido" Gestor de recursos do Azure destinam-se para começar a utilizar com simples blocos modulares e exemplos, prontos para implementar no Microsoft Azure pilha técnico pré-visualização prova de conceito ambiente. Estão incluídas primeiros exemplos de carga de trabalho de terceiros para [ad-não-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-não-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint-2013-não-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), bem como vários modelos de 101 simples [101-simples-windows-vm](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm)na mesma tabela.


### <a name="marketplace-item-packaging-tool-and-sample"></a>Ferramenta de embalagem do item de Marketplace e exemplo
[Transferir e utilizar a ferramenta de embalagem](http://www.aka.ms/azurestackmarketplaceitem) para criar itens de marketplace para os seus próprios modelos personalizados adicionar a mercado Azure pilha. Podem encontrar instruções sobre como criar um item de marketplace e prepará-lo a sua inquilinos no [item de criar Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Ferramentas de programador


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Utilizar o Visual Studio e Azure pilha TP2 na máquina MAS CON01 virtual
Se pretender utilizar o Visual Studio na consola VM para trabalhar com modelos de pilha de Azure, tem de instalar as versões corretas das ferramentas necessárias. Utilize o seguinte procedimento para instalar as versões suportadas para TP2.

1. Utilize a ligação de ambiente de trabalho remoto para iniciar sessão na máquina virtual MAS CON01 com as credenciais de azurestack\azurestackadmin.
2. Instalar e abra Web plataforma Installer.
3. Localizar e instalar o **Visual Studio de Comunidade 2015 com Microsoft Azure SDK - 2.9.5**.
4. Utilizando **Adicionar/remover programas**, desinstale o **Microsoft Azure PowerShell (Setembro)** que obtém instalado como parte do SDK.
5. Abra o instalador do plataforma Web.
6. Localizar e instalar o **Microsoft Azure PowerShell - 2 de pré-visualização técnica do Azure pilha**. 
7. Reinicie o MAS CON01 virtual machine.
8. Utilize a ligação de ambiente de trabalho remoto para iniciar sessão na máquina virtual MAS CON01 com as credenciais de azurestack\azurestackadmin.
9. Abrir o Visual Studio e validar que pode ligar-se para o ambiente do Azure pilha, obter modelos e assim sucessivamente. 

### <a name="azure-powershell-sdk"></a>Azure PowerShell SDK
Azure PowerShell é um módulo que fornece cmdlets para gerir Azure e na pilha de Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, teste, implementar e gerir soluções e serviços entregues através da plataforma do Azure pilha.
[Transferir o Azure PowerShell SDK](http://aka.ms/azStackPsh) e [instalar o PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure cruzada interfaces de linha de comandos de plataforma
Instale rapidamente a Interface de linha de comandos do Azure (Azure CLI) para utilizar um conjunto de comandos baseados na shell de abrir origem para criar e gerir recursos no Microsoft Azure pilha.

[Transferir o clip do Windows](http://aka.ms/azstack-windows-cli)

[Transferir o clip do Mac](http://aka.ms/azstack-linux-cli)

[Transferir o clip de Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Se estiver num computador Mac ou Linux, também pode obter o clip utilizando o comando`npm install -g azure-cli@0.9.11`</br>
> + Se estiver a obter problemas de validação de certificado, execute o comando`set NODE_TLS_REJECT_UNAUTHORIZED=0`
