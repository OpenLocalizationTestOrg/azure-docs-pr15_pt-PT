<properties
   pageTitle="Referência de arquitectura Azure - IaaS: Implementar uma DMZ entre Azure e a Internet | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Implementar uma DMZ entre Azure e a Internet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve os procedimentos recomendados para implementar uma rede de híbrido seguro expande a sua rede no local que aceita o tráfego da rede internet para o Azure. Esta arquitetura de referência expande a arquitetura descrita no artigo [Implementar uma DMZ entre o Azure e o seu centro de dados no local][implementing-a-secure-hybrid-network-architecture]. É recomendado que esse documento de ler e compreende essa referência arquitetura antes de ler este documento.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas. 

Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho executar parcialmente no local e parcialmente no Azure.

- Infraestrutura Azure que encaminha o tráfego de entrada no local e na internet.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura:

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama é na página "DMZ – público".

[! [0]][0]

- **Endereço IP público (PIP).** Este é o endereço IP do ponto final público. Os utilizadores externos ligados à Internet podem aceder ao sistema através deste endereço.

- **Aparelho virtual (NVA) da rede.**  NVA é um termo genérico que descreve uma VM desempenhar tarefas como permitir ou negar acesso como uma firewall, optimizar operações WAN (incluindo a compressão de rede), encaminhamento personalizada ou outras funcionalidades de rede.

- **Balanceador de carga Azure.** Todos os pedidos recebidos da internet passar este Balanceador de carga e são distribuídos aos NVAs na DMZ pública sub-rede entrada.

- **DMZ público sub-rede de entrada.** Esta sub-rede aceita pedidos a partir do Balanceador de carga Azure. Pedidos recebidos são transmitidos para um dos NVAs na DMZ.

- **Sub-rede de saída DMZ público.** Pedidos de que sejam aprovados pela NVA passam esta sub-rede ao balanceador de carga interna para a camada web.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="nva-recommendations"></a>Recomendações NVA

Implemente um conjunto de NVAs para o tráfego com origem na internet e outra para o tráfego de origem no local. É um risco de segurança para utilizar apenas um conjunto de NVAs em ambos os casos, uma vez que este design fornece sem perímetro de segurança entre os dois conjuntos de tráfego de rede. É dos benefícios para utilizar este design porque reduz a complexidade da segurança regras da verificação e torna mais claros quais são as regras correspondem a cada pedido de receção de rede. Por exemplo, um conjunto de NVAs implementa regras para o tráfego da internet apenas enquanto outro conjunto de regras de implementar NVAs apenas para o local tráfego.

Incluir uma camada 7 NVA para terminar as ligações de aplicação ao nível do NVA e manter afinidade com as camadas de back-end. Isto garante conectividade simétrica na qual o tráfego de resposta das camadas de back-end devolve através de NVA.  

### <a name="public-load-balancer-recommendations"></a>Recomendações de Balanceador de carga público ###

Para manter escalabilidade e disponibilidade, implementar a DMZ pública NVAs de entrada num [conjunto de disponibilidade] [ availability-set] e utilizar um [internet opostas Balanceador de carga] [ load-balancer] para distribuir pedidos de internet por NVAs no conjunto de disponibilidade.  

Configure o Balanceador de carga para aceitar pedidos apenas nas portas necessárias para o tráfego da internet. Restringir por exemplo, a entrada pedidos para portas 80 e HTTP entrados pedidos HTTPS à porta 443.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Estruture a sua infraestrutura de com uma opostas Balanceador de carga à frente da sub-rede DMZ pública entrada desde o início da internet. Mesmo se o seu initally arquitetura requer um único NVA, será mais fácil Dimensionar para vários NVAs no futuro se já for implementado na internet opostas Balanceador de carga.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Necessita de internet opostas Balanceador de carga de cada NVA na DMZ público sub-rede entrada para implementar uma [sonda de estado de funcionamento][lb-probe]. Uma sonda de estado de funcionamento deixar de responder neste ponto final que é considerada para não estar disponível e o Balanceador de carga irá direccionar pedidos para outros NVAs no mesmo conjunto de disponibilidade. Tenha em atenção que, se todos os NVAs não responderem, a aplicação irá falhar por isso, é importante ter configurado para DevOps alerta quando o número de ocorrências NVA saudáveis desce abaixo de um determinado limiar de monitorização.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Restringir a monitorização e gestão da funcionalidade para a entrada público DMZ NVA responder aos pedidos a partir da caixa de hiperligação na sub-rede gestão apenas. Tal como é abordado de [execução de uma DMZ entre o Azure e o seu centro de dados no local] [ implementing-a-secure-hybrid-network-architecture] do documento, definir uma rota de rede único da rede no local através do gateway para a caixa de hiperligação na sub-rede de gestão de restringir o acesso.

Se for conectividade de gateway da sua rede no local ao Azure para baixo, ainda pode alcançar a caixa de hiperligação ao implementar um PIP, adicioná-lo para a caixa de salto e remoto da internet.

## <a name="security-considerations"></a>Considerações de segurança

Esta arquitetura de referência implementa vários níveis de segurança:

- Internet opostas Balanceador de carga direciona os pedidos para os NVAs na entrada pública DMZ apenas a sub-rede e apenas nas portas necessárias para a aplicação.

- As regras NSG de entrada e saída pública DMZ sub-rede impedir que o NVAs ser comprometida, bloqueando pedidos que estão fora as regras NSG.

- A configuração de encaminhamento NAT para os NVAs direciona os pedidos recebidos no portas 80 e 443 ao balanceador de carga camada web, mas ignora pedidos em todas as outras portas.

Tenha em atenção que deve registar todos os pedidos recebidos em todas as portas. Os registos, com atenção para pedidos de que estão fora esperados parâmetros, tal como estes podem indicar intrusos tentativas de auditoria regularmente.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Utilizar NSGs para bloquear/fase de tráfego entre camadas de aplicação

Cada uma das camadas web, empresas e dados restringir o tráfego entre elas utilizando NSGs. Isto é, a camada de empresas utiliza uma NSG para bloquear todo o tráfego que não têm origem na camada web e a camada de dados utiliza uma NSG para bloquear todo o tráfego que não têm origem na camada empresas. Se tiver um requisito para expandir as regras NSG para permitir o acesso mais amplo para estes camadas, avaliar estes requisitos contra os riscos de segurança. Cada novo caminho entrado representa uma oportunidade de danos acidentais ou purposeful dados de perda ou aplicação.

## <a name="solution-deployment"></a>Implementação da solução

Uma implementação para uma arquitetura de referência que implementa estas recomendações está disponível no Github. Esta arquitetura de referência inclui uma rede virtual (VNet), grupo de segurança de rede (NSG), Balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implementada com Windows ou Linux VMs ao seguir as instruções abaixo: 

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-public-dmz-network-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Selecione o **Tipo de SO** no pendente de caixa, **windows** ou **linux**.
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-public-dmz-wl-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

6. Aguarde para a implementação concluir.

7. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Utilizar existente** e introduza `ra-public-dmz-network-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

9. Aguarde para a implementação concluir.

10. Os ficheiros de parâmetro incluem o nome de utilizador codificada administrador e a palavra-passe para todos os VMs e é vivamente recomendado que imediatamente altere ambos. Para cada VM na implementação, selecione-o no portal do Azure e, em seguida, clique em **Repor palavra-passe** no pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para persistir.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Seguro arquitetura de rede híbrido"
