<properties
    pageTitle="Pilha Azure aplicação de serviço pré-visualização técnica 1 antes de começar a | Microsoft Azure"
    description="Passos para concluir antes de implementar Web Apps no Azure pilha"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Antes de começar com o Azure pilha Web Apps

> [AZURE.NOTE] As seguintes informações só se aplica a implementações do Azure pilha TP1.

Irá precisar de alguns itens para instalar o Azure pilha Web Apps:

- Uma implementação concluída de [1 de pré-visualização técnica do Azure pilha](azure-stack-run-powershell-script.md)
- Espaço suficiente no sistema de pilha de Azure para uma pequena implementação do Azure pilha Web Apps.  O espaço necessário aproximadamente é 20GB de espaço em disco.
- [Um servidor que executa o SQL Server](#SQL-Server).

>[AZURE.NOTE] Os passos seguintes que todos executada na VM de cliente.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Antes de implementar o Azure pilha Web Apps

Para implementar um fornecedor de recursos, tem de executar o PowerShell integrada Scripting Environment(ISE) como administrador. Por este motivo, é necessário permitir os cookies e JavaScript no perfil do Internet Explorer que utiliza para iniciar sessão no Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Desativar a segurança do Internet Explorer avançada

1.  Inicie sessão no computador Azure pilha prova de conceito (conceito) como **AzureStack/administrador**e, em seguida, abra o **Gestor de servidor**.
2.  Desative **A configuração avançada do Internet Explorer** para administradores e utilizadores.
3.  Inicie sessão no máquina virtual ClientVM.AzureStack.local como administrador e, em seguida, abra o **Gestor de servidor**.
4.  Desative **A configuração avançada do Internet Explorer** para administradores e utilizadores.

## <a name="enable-cookies"></a>Ativar cookies

1.  Selecione **Iniciar**, > **todas as aplicações**, > **Acessórios Windows**. Com o botão direito **Do Internet Explorer** > **mais** > **Executar como administrador**.
2.  Se lhe for pedido, selecione **a utilização recomendada, segurança**e, em seguida, clique **em OK**.
3.  No Internet Explorer, selecione **Ferramentas** (o ícone de engrenagem), > **Opções da Internet** > **Privacidade** > **Avançadas**.
4.  Selecione **Avançadas**. Certifique-se de que ambas as caixas de verificação **Aceitar** são selecionadas e, em seguida, selecione **OK** e **OK** novamente.
5.  Feche o Internet Explorer e, reinicie o ISE do PowerShell como administrador.

## <a name="install-the-latest-version-of-azure-powershell"></a>Instalar a versão mais recente do PowerShell do Azure

1.  Inicie sessão no computador Azure pilha conceito como **AzureStack/administrador**.
2.  Utilize a ligação de ambiente de trabalho remoto para iniciar sessão no computador virtual ClientVM.AzureStack.local como administrador.
3.  Abra o **Painel de controlo** e selecione **desinstalar um programa**. 
4.  Com o botão direito no **Microsoft Azure PowerShell - Novembro 2015** e clique em **desinstalar**.
5.  Após desinstalar acabamentos, reinicie o computador ClientVM.AzureStack.local virtual
6.  Transfira e instale o mais recente [Azure PowerShell](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>Do SQL Server

Por predefinição, o instalador do Azure pilha Web Apps está definido para utilizar o SQL Server que está instalado juntamente com o fornecedor de recursos do Azure pilha de SQL. Quando instala o fornecedor de recursos do SQL Server (SQL RP) certifique-se de que tome nota do nome de utilizador de administrador de base de dados e palavra-passe. Precisa de ambas ao instalar o Azure pilha Web Apps.
Nota: Também terá a opção para implementar e utilizar outro servidor para executar o SQL Server. Pode escolher instância do SQL Server para utilizar quando concluir as opções no instalador do Azure pilha Web Apps.
