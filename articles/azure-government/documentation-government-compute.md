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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Cluster de administração pública Azure

##  <a name="virtual-machines"></a>Máquinas virtuais

Para obter detalhes sobre este serviço e como utilizá-lo, consulte o artigo [Tamanhos de máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variações

As seguintes SKUs do VM são ficará disponível das versões (DG) no Azure Governo:

VM SKU|VA de administração pública dos e.u.a.|IA de administração pública dos e.u.a.|Notas
---|---|---|---
A|DAS VERSÕES DG|DAS VERSÕES DG|Nenhum
Dv1|DAS VERSÕES DG|-|Nenhum
DSv1|DAS VERSÕES DG|-|Nenhum
Dv2|DAS VERSÕES DG|DAS VERSÕES DG|15 é brevemente
F|DAS VERSÕES DG|DAS VERSÕES DG|Nenhum
G|Planeado|-|Nenhum

###  <a name="data-considerations"></a>Considerações de dados

As seguintes informações identificam o limite do Azure Governo para máquinas virtuais do Azure:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dados introduzidos, armazenados e processadas dentro de uma VM pode conter dados de exportação controlado. Binários em execução no prazo de máquinas virtuais do Azure. Autenticadores estáticos, tais como as palavras-passe e Smart Card PIN de acesso aos componentes de plataforma Azure. Chaves privadas de certificados utilizados para gerir componentes da plataforma Azure. Cadeias de ligação de SQL.  Outros segurança informações/segredos, tal como certificados, chaves de encriptação, chaves principais e chaves de armazenamento armazenadas nos serviços do Azure.  | Não são permitido metadados que contenha dados exportar controlado. Este metadados incluem todos os dados de configuração introduzidos quando criar e manter o Azure Virtual Machine.  Não introduza dados Regulated/controlados para os seguintes campos: nomes de função, grupos de recursos, nomes de implementação, nomes de recursos, etiquetas de recursos de inquilinos  

## <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações, subscreva o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>
