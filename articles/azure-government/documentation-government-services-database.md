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
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Administração pública Azure bases de dados

##  <a name="sql-database"></a>Base de dados SQL

Consulte o<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centro de segurança da Microsoft para o motor de base de dados do SQL</a> e [Azure SQL da base de dados públicas documentação](https://azure.microsoft.com/documentation/services/sql-database/) para obter orientações adicionais sobre configuração de visibilidade de metadados e as melhores práticas de proteção.

### <a name="variations"></a>Variações

Base de dados do SQL V12 está normalmente disponível no Azure Governo.

O endereço do SQL Azure Servers no Azure Governo é diferente:

Tipo de serviço|Azure público|Azure administração pública
---|---|---
Base de dados SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considerações sobre

As seguintes informações identificam o limite do Azure Governo para Azure SQL:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados armazenados e transformados em SQL do Microsoft Azure podem conter dados regulamentada Governo Azure. Tem de utilizar ferramentas de base de dados para transferência de dados de regulamentada Azure Governo dados. | Não são permitido metadados do Azure SQL que contenha dados exportar controlado. Este metadados incluem todos os dados de configuração introduzidos quando criar e manter o seu produto de armazenamento.  Não introduza dados regulamentada/controlados para os seguintes campos: nome, nome da subscrição, grupos de recursos, nome de servidor, início de sessão do servidor admin, nomes de implementação, nomes de recursos, etiquetas de recursos de base de dados

## <a name="azure-redis-cache"></a>Cache de Azure Redis

Para obter detalhes sobre este serviço e como utilizá-lo, consulte o artigo [documentação pública Azure Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variações

Os URLs para aceder a e gerir a Cache de Redis Azure no Azure governo são diferentes:

Tipo de serviço|Azure público|Azure administração pública
---|---|---
Ponto final de cache|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Portal do Azure|https://portal.Azure.com|https://portal.Azure.us

>[AZURE.NOTE] Todas as suas scripts e código tem de conta para os pontos finais adequados e ambientes. Para mais informações, consulte o artigo [como ligar ao Azure Government Cloud](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Considerações sobre

As seguintes informações identificam o limite do Azure Governo para Azure Redis Cache:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados armazenados e transformados em Cache Redis do Azure podem conter dados regulamentada Governo Azure. | Não são permitido Azure metadados da Redis Cache que contenha dados exportar controlado. Não introduza dados regulamentada/controlados para os seguintes campos: nome, nome VNET, nome da subscrição, grupos de recursos, etiquetas de recursos, Redis propriedades em Cache.  

##  <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações subscrever o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>
