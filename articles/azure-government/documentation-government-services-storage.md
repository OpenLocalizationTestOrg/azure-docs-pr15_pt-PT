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
    ms.date="10/13/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-storage"></a>Armazenamento de administração pública Azure

##  <a name="azure-storage"></a>Armazenamento Azure

Para obter detalhes sobre este serviço e como utilizá-lo, consulte a [documentação de pública de armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Variações

Os URLs para contas de armazenamento no Azure governo são diferentes:

Tipo de serviço|Azure público|Azure administração pública
---|---|---
Armazenamento de BLOBs|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Armazenamento de fila de espera|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Armazenamento de tabela|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Todas as suas scripts e código precisa para ter em conta os pontos finais adequados.  Consulte o artigo [Configurar cadeias de ligação do Azure armazenamento](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Para mais informações sobre APIs consulte o artigo o <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Construtor de conta de armazenamento na nuvem</a>.

O sufixo de ponto final para utilizar estes overloads é core.usgovcloudapi.net 

### <a name="considerations"></a>Considerações sobre

As seguintes informações identificam o limite do Azure Governo para armazenamento do Windows Azure:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Os dados introduzidos, armazenada e processadas dentro de um armazenamento do Windows Azure produto pode conter dados de exportação controlado. Autenticadores estáticos, tais como as palavras-passe e Smart Card PIN de acesso aos componentes de plataforma Azure. Chaves privadas de certificados utilizados para gerir componentes da plataforma Azure. Outros segurança informações/segredos, tal como certificados, chaves de encriptação, chaves principais e chaves de armazenamento armazenadas nos serviços do Azure. | Azure metadados de armazenamento não são permitido que contenha dados exportar controlado. Este metadados incluem todos os dados de configuração introduzidos quando criar e manter o seu produto de armazenamento.  Não introduza dados Regulated/controlados para os seguintes campos: grupos de recursos, nomes de implementação, nomes de recursos, etiquetas de recursos  

##  <a name="premium-storage"></a>Armazenamento de Premium

Para obter detalhes sobre este serviço e como utilizá-lo, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Variações

Armazenamento de Premium é disponibilizado na USGov Virginia. Isto inclui máquinas virtuais de DS série. 

### <a name="considerations"></a>Considerações sobre

Aplicam as mesmas considerações de dados de armazenamento listadas acima para contas de armazenamento premium. 

##  <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações subscrever o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>
