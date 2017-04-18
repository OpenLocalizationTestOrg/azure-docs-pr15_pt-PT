<properties 
   pageTitle="Gerir servidores DNS utilizados por uma rede virtual (VNet)"
   description="Saiba como adicionar e remover os servidores DNS na rede virtual (vnet)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Gerir servidores DNS utilizados por uma rede virtual (VNet)

Pode gerir a lista de servidores DNS utilizados num VNet no Portal de gestão ou no ficheiro de configuração de rede. Pode adicionar até 12 servidores DNS para cada VNet. Quando especificar os servidores DNS, é importante verificar que lista os servidores DNS na ordem correta para o seu ambiente. Listas de servidor DNS não funcionam round robin. São utilizadas pela ordem que estão especificados. Se conseguir ser contactado o primeiro servidor DNS, na lista, o cliente irá utilizar esse servidor DNS, independentemente de se o servidor DNS está a funcionar corretamente ou não. Para alterar a ordem de servidor DNS para a sua rede virtual, remover os servidores DNS da lista e adicioná-los novamente pela ordem que pretende.

>[AZURE.WARNING] Depois da lista de DNS foi atualizada, tem de reiniciar as máquinas virtuais localizadas na sua rede virtual para que atende as novas definições de servidor DNS. Máquinas virtuais irão continuar a utilizar os respetivos a configuração atual até que estes estão a ser reiniciados.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Editar uma lista de servidor DNS para uma rede virtual utilizando o Portal de gestão

1. Inicie sessão no **Portal de gestão**.

1. No painel de navegação, clique em **redes**e, em seguida, clique no nome da sua rede virtual na coluna **nome** .

1. Clique em **Configurar**.

1. Em **Servidores DNS**, pode configurar o seguinte procedimento:

    - **Para registar (adicionar) num novo DNS server –** Basta escreva o nome e endereço IP nas caixas. Isto adiciona um servidor DNS à sua rede virtual lista os servidores DNS e também regista o servidor de DNS com o Azure.

    - **Para adicionar um servidor DNS que foi registado anteriormente –** Se já registado um servidor de DNS com Azure, pode selecioná-lo a partir da lista preenchida.

    - **Para remover um servidor DNS da sua rede virtual –** Clique no X junto ao servidor que pretende remover. Tenha em atenção que este apenas remove o servidor esta lista de rede virtual. O servidor DNS permanece registado no Azure para outras redes virtuais para utilizar. Para eliminar um servidor DNS da sua subscrição, aceda à página **redes -> servidores DNS** .

    - **Para reorganizar os servidores DNS –** Remover todos os servidores DNS que são listados e, em seguida, adicioná-los novamente pela ordem que pretende. Lembre-se de que não se trata de uma lista de DNS round robin.

    - **Para mudar o nome de um servidor de DNS** Realçar o servidor de DNS, na lista e, em seguida, escreva o novo nome. Isto irá registar um novo servidor DNS no Azure, bem como adicioná-lo para a lista de servidores DNS para a sua rede virtual. Servidor de DNS antigo e o endereço IP irão permanecer registados com Azure. Pode eliminá-lo na página **DNS Servers** se não está a utilizar para quaisquer outras redes virtuais.

1. Clique em **Guardar** na parte inferior da página para guardar a configuração de servidores DNS nova.

1. Reinicie as máquinas virtuais localizadas na rede de virtual para permitir que este adquirir as novas definições de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Editar uma lista de servidor DNS com um ficheiro de configuração de rede

Para editar uma lista do servidor DNS utilizando um ficheiro de configuração de rede, primeiro irá de exportar as definições de configuração a partir do Portal de gestão. Irá, em seguida, edite o ficheiro de configuração de rede e importe-o novamente através do Portal de gestão. Abaixo encontra uma lista de alto nível dos passos para concluir este processo.

1. Exporte as suas definições de rede virtual para um ficheiro de configuração de rede. Para obter mais informações e passos para exportar as suas definições de configuração de rede, consulte o artigo [Exportar definições de rede Virtual para um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Especifica as informações do servidor DNS para a sua rede virtual. Para obter mais informações sobre como especificar um servidor de DNS, consulte [especificar um servidor DNS num ficheiro de configuração de rede Virtual](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Para obter informações adicionais sobre os ficheiros de configuração de rede, consulte o artigo [Esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [configurar um rede Virtual utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Importe o ficheiro de configuração de rede. Para obter mais informações e passos para importar o ficheiro de configuração de rede, consulte o artigo [Importar um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Reinicie as máquinas virtuais localizadas na rede de virtual para permitir que este adquirir as novas definições de DNS.
