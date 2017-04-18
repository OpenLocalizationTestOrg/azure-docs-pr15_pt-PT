<properties
    pageTitle="Criar uma VM Linux utilizando o Portal do Azure | Microsoft Azure"
    description="Crie uma VM Linux utilizando o Portal do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Criar uma VM Linux no Azure através do Portal


Este artigo mostra-lhe como utilizar o [Azure portal](https://portal.azure.com/) para criar uma máquina de Virtual Linux.

Os requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Ficheiros de chaves públicos e privados SSH](virtual-machines-linux-mac-create-ssh-keys.md)


1. Iniciado sessão no portal do Azure com a sua identidade conta Azure, clique em **+ Novo** no canto superior esquerdo:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Clique em **máquinas virtuais** no **Marketplace** , em seguida, lista de imagens de **Ubuntu Server 14.04 LTS** a partir do **Aplicações em destaque** .  Certifique-se na parte inferior que o modelo de implementação é `Resource Manager` e, em seguida, clique em **Criar**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Na página **informações básicas** , introduza:
    - um nome para a VM
    - um nome de utilizador para o utilizador Admin
    - definir o tipo de autenticação para **chave pública SSH**
    - a chave pública SSH como uma cadeia (a partir do seu `~/.ssh/` diretório)
    - um recurso nome do grupo ou selecione um grupo existente

    e clique em **OK** para continuar e escolha o tamanho da memória virtual; -deve ter um aspeto semelhante ao seguinte:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Escolha o tamanho de **DS1** , que é instalada Ubuntu num SSD Premium e clique em **Selecionar** para configurar as definições.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. Nas **Definições**, mantenha as predefinições para valores de armazenamento e de rede e clique em **OK** para ver o resumo.  Aviso do tipo de disco foi definido como Premium SSD ao selecionar DS1, o **S** notates SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confirmar as definições para a nova VM de Ubuntu e clique em **OK**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Abra o Dashboard de Portal e na **interfaces de rede** , selecione a NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Abrir o menu de endereços IP público em que as definições de imagem

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH para o endereço IP público utilizando a sua chave pública SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Próximos passos

Agora que criou uma VM Linux rapidamente para utilizar para fins de testes ou demonstração. Para criar uma VM Linux personalizadas para a sua infraestrutura, pode seguir qualquer um dos seguintes artigos.

- [Criar uma VM Linux no Azure utilizar modelos](virtual-machines-linux-cli-deploy-templates.md)
- [Criar um SSH protegido Linux VM no Azure utilizar modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux utilizando o clip do Azure](virtual-machines-linux-create-cli-complete.md)
