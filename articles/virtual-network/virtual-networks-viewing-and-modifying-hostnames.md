<properties 
   pageTitle="Ver e modificar os nomes de anfitriões | Microsoft Azure"
   description="Como ver e alterar os nomes de anfitriões para máquinas virtuais Azure, da web e funções de trabalho para a resolução de nomes"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Ver e modificar os nomes de anfitriões

Para permitir que o seu instâncias de função seja referenciado por nome de anfitrião, tem de definir o valor para o nome do anfitrião no ficheiro de configuração de serviço para cada função. Fazê-lo adicionando o nome do anfitrião pretendida para o atributo **vmName** do elemento de **função** . É utilizado o valor do atributo **vmName** como base para o nome do anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e existem três instâncias dessa função, os nomes de anfitrião dos instâncias será *webrole0*, *webrole1*e *webrole2*. Não é necessário especificar um nome de anfitrião para máquinas virtuais no ficheiro de configuração, porque o nome do anfitrião para uma máquina virtual é povoado com base no nome da máquina virtual. Para mais informações sobre como configurar um serviço do Microsoft Azure, consulte o [Esquema de configuração do Azure Service (.cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Ver nomes de anfitriões

Pode ver os nomes de anfitrião de máquinas virtuais e instâncias de função num serviço de nuvem ao utilizar qualquer uma das ferramentas abaixo.

### <a name="azure-portal"></a>Portal do Azure

Pode utilizar o [Azure portal](http://portal.azure.com) para ver os nomes de anfitrião para máquinas virtuais no pá a descrição geral para uma máquina virtual. Tenha em atenção que a pá mostra um valor de **nome** e o **Nome do anfitrião**. Apesar de inicialmente são os mesmos, ao mudar o nome do anfitrião o nome da máquina virtual ou função instância não será alterada.

Instâncias de função também podem ser visualizadas no portal do Azure, mas quando listar as instâncias num serviço de nuvem, o nome do anfitrião não é apresentado. Irá ver um nome para cada instância, mas esse nome não representar o nome do anfitrião.

### <a name="service-configuration-file"></a>Ficheiro de configuração de serviço

Pode transferir o ficheiro de configuração do serviço para um serviço implementado a partir do pá **Configurar** do serviço no portal do Azure. Pode, em seguida, procure o atributo **vmName** para o elemento do **nome da função** ver o nome do anfitrião. Tenha em atenção que este nome de anfitrião é utilizado como base para o nome do anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e existem três instâncias dessa função, os nomes de anfitrião dos instâncias será *webrole0*, *webrole1*e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de trabalho remoto

Depois de activar o ambiente de trabalho remoto (Windows), Windows PowerShell remoto (Windows) ou ligações SSH (Linux e Windows) ao seu máquinas virtuais ou instâncias de papel, pode ver o nome do anfitrião a partir de uma ligação ativa do ambiente de trabalho remoto de várias formas:

- Escreva o nome do anfitrião na linha de comandos ou SSH terminal.

- Escrever ipconfig/todas ao comando pedir (apenas para o Windows).

- Ver o nome do computador nas definições do sistema (apenas para o Windows).

### <a name="azure-service-management-rest-api"></a>Gestão de serviços Azure REST API

A partir de um cliente do resto, siga estas instruções:

1. Certifique-se de que tem um certificado de cliente para se ligar ao portal do Azure. Para obter um certificado de cliente, siga os passos apresentados em [como: informações de transferência e importar definições de publicar e subscrição](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Defina uma entrada de cabeçalho x-ms-versão com um valor de 2013-11-01 com o nome.

1. Enviar um pedido de no seguinte formato: https://management.core.windows.net/\<subscrition id\>/serviços/hostedservices/\<nome do serviço\>?embed detalhe = true

1. Procure o elemento **HostName** para cada elemento **RoleInstance** .

>[AZURE.WARNING] Também pode ver o sufixo de domínio interno do seu serviço de nuvem da resposta de chamada resto ao selecionar o elemento **InternalDnsSuffix** ou ao executar ipconfig/tudo a partir de uma linha de comandos numa sessão de ambiente de trabalho remoto (Windows) ou através da execução gato /etc/resolv.conf a partir de um terminal SSH (Linux).

## <a name="modifying-a-hostname"></a>Modificar um nome de anfitrião

Pode modificar o nome do anfitrião para qualquer máquina virtual ou instância de função ao carregar um ficheiro de configuração do serviço modificados ou ao mudar o nome do computador a partir de uma sessão de ambiente de trabalho remoto.

## <a name="next-steps"></a>Próximos passos

[Resolução do nome de (domínio DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração do serviço Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar as definições de DNS utilizando os ficheiros de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
