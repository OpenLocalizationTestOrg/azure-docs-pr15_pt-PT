<properties 
    pageTitle="Configurar uma rede virtual com um ficheiro de configuração de rede" 
    description="Instruções para exportar e importar um ficheiro de configuração de rede para o Portal de gestão do Azure para poder criar ou modificar redes virtuais. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurar uma rede virtual com um ficheiro de configuração de rede

Pode configurar uma rede virtual (VNet) utilizando o portal de gestão do Azure ou ao utilizar um ficheiro de configuração de rede.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Criar e modificar um ficheiro de configuração de rede 
A forma mais fácil para a criação de um ficheiro de configuração de rede é exportar as definições de rede de uma configuração de rede virtual existente, em seguida, modifique o ficheiro para contêm as definições que pretende configurar redes virtuais.

Para editar o ficheiro de configuração de rede, pode simplesmente abrir o ficheiro, faça as alterações adequadas e guarde o ficheiro. Pode utilizar qualquer editor de *xml* para efetuar alterações ao ficheiro de configuração de rede. 

Deve seguir estreitamente as orientações para [definições de esquema do ficheiro de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera uma sub-rede que tenha algo implementado como **em utilização**. Quando uma sub-rede está a ser utilizado, seja modificado. Antes de modificar, mova tudo o que tenham implementado à sub-rede para sub-rede diferente que não está a ser modificada.   Consulte o artigo [Mover um VM ou função instância para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exportar e importar definições de rede virtual utilizando o Portal de gestão  
Pode importar e exportar definições de configuração de rede contidas no seu ficheiro de configuração de rede utilizando o PowerShell ou o Portal de gestão. As instruções abaixo irão ajudá-lo a exportação e importação utilizando o Portal de gestão. 

### <a name="to-export-your-network-settings"></a>Para exportar as suas definições de rede
Quando exporta, todas as definições para as redes virtuais na sua subscrição serão escritas para um ficheiro. XML. 

1. Iniciar sessão no **Portal de gestão**.
2. No Portal de gestão, na parte inferior da página **redes** , clique em **Exportar**. 
3. Na janela do **Exportar configuração de rede** , certifique-se de que selecionou a subscrição para o qual pretende exportar as suas definições de rede. Em seguida, clique em marca de verificação no canto inferior direito. 
4. Quando lhe for pedido, guarde o ficheiro *NetworkConfig.xml* para a localização da sua escolha.


### <a name="to-import-your-network-settings"></a>Para importar as suas definições de rede

1. No **Portal de gestão**, no painel de navegação no canto inferior esquerdo, clique em **Novo**.
2. Clique em **Serviços de rede** -> **Rede Virtual** -> **Importar a configuração**.
3. Na página **importar o ficheiro de configuração de rede** , procure o ficheiro de configuração de rede e, em seguida, clique na seta **seguinte** .
4. Na página **construir a sua rede** , verá informações no ecrã que mostra quais as secções de configuração de rede vai ser alteradas ou criadas. Se parecerem corretas para as alterações, clique em marca de verificação para avançar para atualizar ou criar a sua rede virtual. 