<properties 
   pageTitle="Gerir NSGs utilizando o portal de pré-visualização no Gestor de recursos | Microsoft Azure"
   description="Saiba como gerir existente NSGs utilizando o portal de pré-visualização no Gestor de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Gerir NSGs utilizando o portal de pré-visualização

> [AZURE.SELECTOR]
- [Portal](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [Clip Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Obter informações sobre

Pode ver o seu NSGs existentes, recuperar regras para um NSG existente e saber quais os recursos um NSG está associado a.

### <a name="view-existing-nsgs"></a>Ver NSGs existentes
Para ver todos os NSGs existentes numa subscrição, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Procurar >** > **grupos de segurança de rede**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Verifique a lista de NSGs no pá a **grupos de segurança de rede** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Para ver a lista de NSGs no grupo de recursos **RG NSG** , siga os passos abaixo. 

1. Clique em **grupos de recursos >** > **RG NSG** > **…**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Na lista de recursos, procure por itens a apresentar o ícone NSG, conforme apresentado na pá **recursos** abaixo.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Lista todas as regras para um NSG

Para ver as regras de um NSG denominada **NSG FrontEnd**, siga os passos abaixo. 

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique em **regras de entrada de segurança**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. O separador **regras de segurança de entrada** é apresentado como é mostrado abaixo.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. No separador **Definições** , clique em **regras de segurança de saída** para ver as regras de saída.

>[AZURE.NOTE] Para ver as regras predefinidas, clique no ícone de **regras predefinidas** no topo da pá que apresenta as regras.

### <a name="view-nsgs-associations"></a>Vista NSGs associações

Para ver o que associar recursos que é a NSG **NSG FrontEnd** , siga os passos abaixo.

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique em **sub-redes** para ver que sub-redes são associados à NSG.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. No separador **Definições** , clique em **interfaces de rede** para ver o que é associado à NSG NIC.

## <a name="manage-rules"></a>Gerir regras

Pode adicionar regras para um NSG existente, editar regras existentes e remover regras.

### <a name="add-a-rule"></a>Adicionar uma regra

Para adicionar uma regra a permitir o tráfego de **entrada** a porta **443** a partir de qualquer computador para **NSG FrontEnd** NSG, siga os passos abaixo.

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique em **regras de entrada de segurança**.
3. Na pá **segurança regras de entrada** , clique em **Adicionar**. Em seguida, na pá **Adicionar regra de segurança de entrada** , preencher os valores, conforme apresentado abaixo e, em seguida, clique em **OK**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Depois de aguardar alguns segundos, repare que a nova regra no pá **regras de entrada de segurança** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Alterar uma regra

Para alterar a regra que criou acima para permitir o tráfego de entrada a partir da **Internet** apenas, siga os passos abaixo.

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique na regra que criou acima.
3. Na pá **https permitir** , altere a propriedade de **origem** , conforme apresentado abaixo e, em seguida, clique em **Guardar**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar a regra que criou acima, siga os passos abaixo.

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique na regra que criou acima.
3. Na pá **https permitir** , clique em **Eliminar**e, em seguida, clique em **Sim**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gerir associações

Pode associar uma NSG para sub-redes e NIC. Também pode dissociate uma NSG a partir de qualquer recurso que está associado.

### <a name="associate-an-nsg-to-a-nic"></a>Associar um NSG para uma imagem

Para associar **NSG FrontEnd** NSG para **TestNICWeb1** NIC, siga os passos abaixo.

1. O **grupos de segurança de rede** pá ou a pá de **recursos** apresentada acima, clique em **NSG FrontEnd**.
2. No separador **Definições** , clique em **interfaces de rede** > **associar** > **TestNICWeb1**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>DISSOCIATE uma NSG a partir de uma imagem

Para dissociar **NSG FrontEnd** NSG a partir do **TestNICWeb1** NIC, siga os passos abaixo.

1. A partir do portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **…**  >  **TestNICWeb1**.
2. Na pá **TestNICWeb1** , clique em **segurança de alteração...**  > **None**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Também pode utilizar este pá para associar a NIC para qualquer NSG existente.

### <a name="dissociate-an-nsg-from-a-subnet"></a>DISSOCIATE uma NSG a partir de uma sub-rede

Para dissociar **NSG FrontEnd** NSG da sub-rede **front-end** , siga os passos abaixo.

1. A partir do portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **…**  >  **TestVNet**.
2. No pá **Definições** , clique em **sub-redes** > **FrontEnd** > **grupo de segurança de rede** > **nenhum**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Na pá **front-end** , clique em **Guardar**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associar um NSG para uma sub-rede

Para associar **NSG FrontEnd** NSG à sub-rede **FronEnd** novamente, siga os passos abaixo.

1. A partir do portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **…**  >  **TestVNet**.
2. No pá **Definições** , clique em **sub-redes** > **FrontEnd** > **grupo de segurança de rede** > **NSG FrontEnd**.
3. Na pá **front-end** , clique em **Guardar**.

>[AZURE.NOTE] Também pode associar uma NSG para uma sub-rede a partir do pá de **Definições** do thh NSG.

## <a name="delete-an-nsg"></a>Eliminar um NSG

Só pode eliminar um NSG se não associou a qualquer outro recurso. Para eliminar uma NSG, siga os passos abaixo.

1. A partir do portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **…**  >  **NSG FrontEnd**.
2. Na pá **Definições** , clique em **interfaces de rede**.
3. Se existirem quaisquer NIC listado, clique em NIC e siga o passo 2 no [Dissociate uma NSG a partir de uma imagem](#Dissociate-an-NSG-from-a-NIC).
4. Repita o passo 3 para cada NIC.
5. Na pá **Definições** , clique em **sub-redes**.
6. Se existirem quaisquer sub-redes listados, clique na sub-rede e siga os passos 2 e 3 na [Dissociate uma NSG a partir de uma sub-rede](#Dissociate-an-NSG-from-a-subnet).
7. Desloca-se da esquerda para a pá **NSG FrontEnd** , em seguida, clique em **Eliminar** > **Sim**.

[Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Próximos passos

- [Ativar o registo](virtual-network-nsg-manage-log.md) para NSGs.
