<properties
    pageTitle="Gerir Azure chave cofre utilizando a automatização Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir Azure chave cofre."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Gerir Azure chave cofre utilizando a automatização do Azure

Este guia vai introduzi-lo para o serviço de automatização do Azure e como pode ser utilizado para simplificar a gestão dos seus teclas e segredos no Azure chave cofre.

## <a name="what-is-azure-automation"></a>O que é o Azure automatização?

[Azure automatização](../automation/automation-intro.md) é um serviço Azure para simplificar a gestão de nuvem através de automatização dos processos e configuração de estado pretendido. Utilizar a automatização Azure, manual, repetida, tarefas de execução longa e passíveis de erro podem ser automatizadas para aumentar a fiabilidade, eficiência e a hora a valor para a sua organização.

Automatização Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis, altamente disponível que se adapta para corresponder às suas necessidades. No Azure automatização, processos podem ser pontapés manualmente, pelos sistemas de 3 terceiros ou em intervalos agendados para que as tarefas deverá acontecer exatamente quando necessário.

Reduzir o overhead operacional e libertar IT e funcionários DevOps concentrar-se no trabalho que soma o valor de negócio ao mover as tarefas de gestão de na nuvem para ser executada automaticamente por Azure automatização.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Como Azure automatização ajudam a gerir Azure chave Cofre?

Chave cofre pode ser gerido no Azure automatização utilizando o [AzureRM chave cofre os cmdlets do] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e os [cmdlets do Azure clássica chave cofre](https://msdn.microsoft.com/library/azure/dn868052.aspx). O módulo Azure para gerir clássica chave Cofre está disponível automaticamente no Azure automatização, e pode importar o [módulo AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) para Azure automatização, para que pode efetuar muitas das suas tarefas de gestão de chave cofre dentro do serviço. Também pode emparelhar estes cmdlets do Azure automatização com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas através de serviços do Azure e 3º sistemas de terceiros.

Com os cmdlets do Azure chave cofre pode efetuar as seguintes tarefas entre as outras pessoas: 

- Criar e configurar uma chave Cofre
- Criar ou importar uma chave
- Criar ou actualizar uma palavra-passe
- Atualizar os atributos de uma chave
- Obter uma chave ou palavra-passe
- Eliminar uma chave ou palavra-passe

Eis alguns exemplos de utilizar o PowerShell para Gerir Cofre de chave:  

* [Azure cofre chave - passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Como instalar e configurar uma cofre chave Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de automatização do Azure e como pode ser utilizado para gerir Azure chave cofre, siga estas ligações para mais informações sobre o Azure automatização.

* Consulte o artigo a automatização Azure [Introdução Tutorial](../automation/automation-first-runbook-graphical.md).
* Consulte os [scripts de PowerShell do Azure chave cofre](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
