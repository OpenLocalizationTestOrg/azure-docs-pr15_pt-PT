<properties
   pageTitle="Configurar uma autenticação com os serviços Web Amazon | Microsoft Azure"
   description="Este artigo descreve como criar e validar uma credencial AWS para runbooks no Azure automatização gerir AWS recursos."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="autenticação AWS, configurar aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com os serviços Web Amazon
Automatizar tarefas comuns com recursos dos serviços de Web no Amazon (AWS) podem ser feito com automatização runbooks no Azure.  Pode automatizar tarefas muitos no AWS utilizando runbooks de automatização, tal como pode com recursos no Azure.  Tudo o que é necessário são duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais.  Especificamente a tecla de acesso AWS e chave secreta.  Para mais informações, reveja o artigo [Utilizar o AWS credenciais](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de automatização.  Para mais informações sobre como configurar uma conta de automatização do Azure, reveja o artigo [Configurar o Azure como conta executar](../automation/automation-sec-configure-azure-runas-account.md).  

Para autenticar com AWS, tem de especificar um conjunto de credenciais AWS para autenticar o runbooks executar o Azure automatização. Se já tiver uma conta de automatização criada e pretende utilizá-la para autenticar com AWS, pode seguir os passos na secção seguinte.  Se pretender dedicada de uma conta para recursos AWS runbooks direccionado para, deve primeiro, crie uma nova [conta de automatização executar como](../automation/automation-sec-configure-azure-runas-account.md) (ignorar a opção para criar um principal de serviço) e, em seguida, siga os passos abaixo.

## <a name="configure-automation-account"></a>Configurar conta de automatização
Para Azure automatização comunicar com AWS, terá primeiro obter as suas credenciais AWS e armazená-los como recursos no Azure automatização.  Execute os seguintes passos documentados no documento AWS [Gerir teclas de acesso para a sua conta AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma tecla de acesso e copiar o **ID da chave de acesso** e o **Segredo tecla de acesso** (opcionalmente transferir o seu ficheiro chave armazená-la num local seguro).

Depois de ter criado e copiado suas chaves de segurança AWS, terá de criar um activo credenciais com uma conta de automatização do Azure ao guardá-los e de referência-los com o seu runbooks segura.  Siga os passos na secção **para criar uma nova credencial** no artigo [ativos de credenciais na automatização do Azure](../automation/automation-certificates.md/###To create a new credential with the Azure portal) e introduza as seguintes informações:

1. Na caixa **nome** , introduza **AWScred** ou um valor adequado padrões de nomenclatura a seguir.  
2. Na caixa **nome de utilizador** , escreva o seu **ID de acesso** e a sua **Chave de acesso de palavra-passe** na caixa **palavra-passe** e **Confirmar palavra-passe** .   

## <a name="next-steps"></a>Próximos passos

- O artigo solução [automatizar implementação de uma VM no Amazon Web Services](../automation/automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar tarefas no AWS Reivew.
