<properties
    pageTitle="Documentação do Azure administração pública | Microsoft Azure"
    description="Este procedimento fornece uma comparision das funcionalidades e orientações sobre como desenvolver aplicações para administração pública do Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Segurança do Governo Azure e de identidade

##  <a name="key-vault"></a>Cofre chave
Para obter detalhes sobre este serviço e como utilizá-lo, consulte o artigo o <a href="https://azure.microsoft.com/documentation/services/key-vault">documentação pública Azure chave cofre.</a>

### <a name="data-considerations"></a>Considerações de dados
As seguintes informações identificam o limite do Azure Governo para Azure Cofre de chave:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados são encriptados com uma chave de Azure chave cofre podem conter dados Regulated/controlados. | Não são permitido Azure chave cofre metadados que contenha dados exportar controlado. Este metadados incluem todos os dados de configuração introduzidos quando criar e manter o seu Cofre chave.  Não introduza dados Regulated/controlados para os seguintes campos: nomes de grupo de recursos, nomes de chave cofre, nome da subscrição |

Chave Cofre é disponibilizado no Azure Governo. Tal como público, não existe nenhuma extensão, para que a chave cofre só está disponível através do PowerShell e clip.

## <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações, subscreva o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>
