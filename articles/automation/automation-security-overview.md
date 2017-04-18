<properties
   pageTitle="Automatização Azure segurança | Microsoft Azure"
   description="Este artigo fornece uma descrição geral de segurança de automatização e métodos de autenticação diferentes disponíveis para contas de automatização no Azure automatização."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="segurança de automatização, automatização segura" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Azure segurança de automatização
Automatização Azure permite-lhe automatizar tarefas relativamente a recursos no Azure, no local e com outros fornecedores de nuvem como o Amazon Web Services (AWS).  Ordem de um livro de execuções executar ações respetivas necessárias, tem de ter permissões de forma segura acesso aos recursos com os direitos mínimos necessários dentro da subscrição.  
Este artigo irá abranger vários cenários de autenticação suportados pelo automatização do Azure e irá mostrar-lhe como começar com base no ambiente ou ambientes que necessárias para gerir.  

## <a name="automation-account-overview"></a>Descrição geral da conta de automatização
Quando começar a automatização Azure pela primeira vez, terá de criar, pelo menos, uma conta de automatização. Automatização contas lhe permitem identificar os recursos de automatização (runbooks, elementos, as configurações) a partir dos recursos contidos no outras contas de automatização. Pode utilizar contas de automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta de desenvolvimento, outro para produção e outro para o seu ambiente no local.  Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure.

Os recursos de automatização para cada conta automatização estão associados uma única região Azure, mas as contas de automatização podem gerir recursos qualquer região. O motivo principal para criar contas de automatização em diferentes regiões seria se tiver políticas que necessitam de dados e recursos para ser isoladas para uma região específica.

>[AZURE.NOTE]Contas de automatização e os recursos que contêm que são criados no portal do Azure, não podem ser acedidas no portal do Azure clássico. Se pretender gerir nestas contas ou dos seus recursos com o Windows PowerShell, tem de utilizar os Gestor de recursos do Azure módulos.

Todas as tarefas que executam relativamente a recursos utilizando o Gestor de recursos do Azure e os cmdlets Azure no Azure automatização tem autenticar para Azure utilizando a autenticação baseada em credenciais do Azure Active Directory identidade organizacional.  Autenticação baseada em certificado foi o método de autenticação original com o modo de gestão de serviço do Azure, mas tiver sido complicada para a configuração.  Autenticar para Azure com utilizador do Azure AD foi introduzida anterior em 2014 não só para simplificar o processo para configurar uma conta de autenticação, mas também suporta a capacidade de forma não interativa autenticar para Azure com uma única conta de utilizador que trabalhou com o Gestor de recursos do Azure e recursos clássicos.   

Atualmente quando cria uma nova conta de automatização no portal do Azure, cria automaticamente:

-  Executar como conta que cria um novo principal de serviço no Azure Active Directory, um certificado e atribui o controlo de acesso baseado em funções de contribuinte RBCA (), que irão ser utilizado para gerir os recursos de Gestor de recursos utilizando runbooks.
-  Clássica executar como conta ao carregá um certificado de gestão, que será utilizado para gerir o serviço de gestão de Azure ou recursos clássicos utilizando runbooks.  

Controlo de acesso baseado em funções está disponível com o Azure Gestor de recursos para conceder acções permitidas para uma conta de utilizador do Azure AD e executar como conta e esse principal do serviço de autenticação.  Leia o [controlo de acesso baseado em funções no Azure automatização artigo](../automation/automation-role-based-access-control.md) para obter mais informações para ajudar a desenvolver um modelo para gerir permissões de automatização.  

Runbooks em execução no livro execuções híbrido trabalhador no seu centro de dados ou contra computação serviços no AWS não pode utilizar o mesmo método que é normalmente utilizado para autenticar runbooks para recursos Azure.  Isto acontece porque os recursos estiver a executar fora do Azure em por conseguinte, irão necessitar das suas próprias credenciais de segurança definidos em automatização para autenticar a recursos que acederem localmente.  

## <a name="authentication-methods"></a>Métodos de autenticação

A tabela seguinte resume os métodos de autenticação diferente para cada ambiente suportados pelo automatização do Azure e o artigo que descreve como configurar a autenticação para sua runbooks.

Método  |  Ambiente  | Artigo
----------|----------|----------
Conta de utilizador do Azure AD | Gestor de recursos do Azure e gestão de serviços Azure | [Autenticar Runbooks com a conta de utilizador do Azure AD](../automation/automation-sec-configure-aduser-account.md)
Executar Azure como conta | Gestor de recursos do Azure | [Autenticar Runbooks com a conta Azure executar como](../automation/automation-sec-configure-azure-runas-account.md)
Azure executar clássica como conta | Gestão de serviços Azure | [Autenticar Runbooks com a conta Azure executar como](../automation/automation-sec-configure-azure-runas-account.md)
Autenticação do Windows | Centro de dados no local | [Autenticar Runbooks para os trabalhadores de livro execuções híbrido](../automation/automation-hybrid-runbook-worker.md)
AWS credenciais | Serviços de Web da Amazon | [Autenticar Runbooks com os serviços Web Amazon (AWS)](../automation/automation-sec-configure-aws-account.md)



