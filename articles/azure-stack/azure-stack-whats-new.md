<properties
    pageTitle="O que há de novo na pilha de Azure | Microsoft Azure"
    description="O que há de novo na pilha de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Quais são as novidades no Azure pilha de 2 de pré-visualização técnica
Esta versão fornece novas funcionalidades para inquilinos e administradores.

## <a name="network"></a>Rede   
   - [iDNS](azure-stack-understanding-dns-in-tp2.md) fornece o registo de nome de rede interna e resolução de sistema de nomes de domínio (DNS) sem a infraestrutura adicional do DNS.
   - [Os gateways de rede de virtuais](azure-stack-create-vpn-connection-one-node-tp2.md) fornecem opções de conectividade VPN para recursos Azure ou no local.
   - Percursos do definidos pelo utilizador pode encaminhar o tráfego de rede através de firewalls, segurança, outros eletrodomésticos e outros serviços.
   - Pode criar recursos de rede de mercado.   

## <a name="storage"></a>Armazenamento
 - [Azure filas](https://msdn.microsoft.com/library/dd179353.aspx) ativar serviço persistente e fiável envio de mensagens.
 - Dados de desempenho de armazenamento de captura [a análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx) . Pode utilizar estes dados para pedidos de rastreio, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.
 - Armazenamento de BLOBs suporta [Acrescentar bloco operação](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Suporte de conta de armazenamento API Premium.
 - Suporte do serviço de armazenamento para ferramentas e SDK, tal como Azure clip, PowerShell, .NET, Python e Java SDK comuns do inquilino. 
 - [Assinatura de acesso partilhadas do armazenamento conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) ativar granular delegação de acesso aos seus serviços de armazenamento sem ter de partilhar a sua chave da conta completo.  
 - Serviços de armazenamento agora utilizam [Contas de serviço geridas grupo](https://technet.microsoft.com/library/hh831477.aspx) para segurança forte com sobrecarga de gestão de baixa.
 - Pode recuperar inquilino não utilizadas recursos a pedido.
 - Comprimento de retenção da conta de armazenamento eliminados é configurável.
 - Pode recuperar contas de armazenamento de inquilino eliminadas.

## <a name="compute"></a>Cluster
- Pode retirar máquinas virtuais.
- Pode implementar extensões de máquina virtual para fins de gestão de resolução de problemas e de configuração.
- Pode redimensionar discos máquina virtual.
- Máquinas virtuais pode ter várias interfaces de rede.

### <a name="portal-experience"></a>Experiência de portal
 - Regiões de pilha Azure são uma unidade de escala e gestão do Azure pilha lógica. Nesta pré-visualização, pode ver informações sobre os serviços como cluster, rede e armazenamento por região.
 - Agora pode pré-visualizar a interface de [updates.md da pilha azure] (actualizações).

## <a name="key-vault"></a>Chave Cofre
- [Chave cofre Azure empilhados](azure-stack-kv-intro.md) fornece seguro gestão das suas chaves e palavras-passe para aplicações na nuvem.
- Pode auditoria e monitorizar a utilização de chave por aplicações e VMs.

## <a name="billing-and-usage"></a>Faturação e a utilização
 - [Faturação e consumo APIs](azure-stack-billing-and-chargeback.md) expor os dados no modo como os seus serviços são consumidos.  
 - Fornecedores delegadas ativar revendedores oferecer os seus serviços de pilha de Azure aos seus clientes.

## <a name="monitoring-and-health"></a>Monitorização e estado de funcionamento
 - Novas capacidades de monitorização disponíveis no portal e APIs permitem-lhe para importante ver e gerir alertas no seu ambiente.  

## <a name="next-steps"></a>Próximos passos
- [Compreender a pilha Azure conceito arquitetura](azure-stack-architecture.md)      
- [Compreender a pré-requisitos de implementação](azure-stack-deploy.md)
- [Implementar pilha Azure](azure-stack-run-powershell-script.md)

  
