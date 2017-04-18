
<properties
    pageTitle="Resolver problemas de coleções de híbrido RemoteApp criar | Microsoft Azure"
    description="Saiba como resolver problemas de falhas de criação de coleções de sites do RemoteApp híbrido"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Resolver problemas de criar coleções de híbrido RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Uma coleção de híbrida está alojada no e armazena os dados na nuvem Azure, mas também dados do access permite que os utilizadores e recursos armazenados na sua rede local. Os utilizadores podem aceder aplicações ao iniciar sessão com as respetivas credenciais da empresa sincronizados ou Federado com o Azure Active Directory. Pode implementar uma coleção de híbrido que utiliza uma rede Virtual do Azure existente ou pode criar uma nova rede virtual. Recomendamos que criar ou utilizar sub-rede de uma rede virtual com um intervalo CIDR grande o suficiente para esperado futuro crescimento para RemoteApp do Azure.

Ainda não criou a sua coleção de ainda? Consulte o artigo [criar uma coleção de híbrido](remoteapp-create-hybrid-deployment.md) para obter os passos.

Se estiver a ter problemas ao criar a coleção ou se a coleção de não está a funcionar a forma como pensa que deverá, consulte o artigo as seguintes informações.

## <a name="your-image-is-invalid"></a>A imagem é inválida ##
Se vir uma mensagem como "GoldImageInvalid" quando estão a aguardar Azure aprovisionar a sua coleção de, significa que a imagem do modelo não cumpre [definidos requisitos de imagem](remoteapp-imagereqs.md). Sim, aceda ler essas [exigências](remoteapp-imagereqs.md), corrigir a sua imagem e tente criar novamente a coleção.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>O seu VNET tem definidos grupos de segurança de rede? ##
Se tiver definidos na sub-rede que estiver a utilizar para a coleção de grupos de segurança de rede, certifique-se destes [URLs e portas](remoteapp-ports.md) acessível a partir da sua sub-rede aceda.

Pode adicionar grupos de segurança de rede adicionais para VMs implementados a sub-rede para um controlo mais apertado por si.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Está a utilizar o seus próprio servidores DNS? E são acessíveis a partir do seu sub-rede VNET? ##
>[AZURE.NOTE] Tem de Certifique-se dos que servidores DNS na sua VNET são sempre para cima e sempre conseguir resolver as máquinas virtuais alojadas na VNET. Não utilize o DNS do Google para esta situação.


Para as coleções de híbrido utilizar os seus servidores DNS. Pode especificá-las no seu esquema de configuração de rede ou através do portal de gestão ao criar a sua rede virtual. Os servidores DNS são utilizados pela ordem que estão especificados de uma forma de activação pós-falha (por oposição a round robin).  
Consulte a [Resolução de nomes para VMs e função instâncias](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para se certificar de que os servidores DNS são configurado correcly.

Certifique-se os servidores DNS para a coleção de estão acessíveis e disponível a partir da sub-rede VNET que especificou para esta coleção.

Por exemplo:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definir o seu DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Está a utilizar um controlador de domínio do Active Directory na sua coleção de? ##
Atualmente apenas um domínio do Active Directory pode ser associado RemoteApp do Azure. A coleção de híbrido suporta apenas as contas do Azure Active Directory que foram sincronizadas utilizando DirSync ferramenta a partir de uma implementação do Active Directory do Windows Server; especificamente, seja sincronizado com a opção de sincronização de palavra-passe ou sincronizado com a Federação de serviços de Federação do Active Directory (AD FS) configurada. Tem de criar um domínio personalizado que corresponde ao sufixo UPN do domínio para o seu domínio no local e configurar integração de diretórios.

Para mais informações, consulte [Configurar RemoteApp do Azure no Active Directory](remoteapp-ad.md) .

Certifique-se os detalhes de domínio fornecidos são válidos e o controlador de domínio está acessível a partir do VM criado no sub-rede utilizada para aplicação Remote Azure. Certifique-se também as credenciais de conta de serviço fornecidas tem permissões para adicionar o domínio fornecido computadores e que o nome de AD desde que pode ser resolvido de DNS fornecido na VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Nome do domínio que especificou quando criou a sua coleção de? ##

O nome do domínio que criou ou adicionado tem de ter um nome de domínio interno (não o nome de domínio do Azure AD) e tem de estar no formato de DNS resolvido (Contoso). Por exemplo, que tem um nome interno do Active Directory (Contoso) e um UPN diretório ativo (contoso.com) - tem de utilizar o nome interno ao criar a coleção.
