<properties
   pageTitle="Gerir identidades no Azure | Microsoft Azure"
   description="Explica e compara os métodos diferentes disponíveis para gerir a identidade nos sistemas de híbrido que abrangem o limite no-local/nuvem com Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Gerir identidades no Azure

Na maioria dos sistemas de empresa com base no Windows, irá utilizar o Active Directory (AD) para fornecer os serviços de gestão de identidade para as suas aplicações. AD funciona bem num ambiente no local, mas quando expandir a sua infraestrutura de rede para a nuvem tem algumas decisões importantes para tornar relativamente a como gerir a identidade. Pode expandir os domínios do seu no local para incorporar VMs na nuvem? Devo criar novos domínios na nuvem e em caso afirmativo, como? Deverá que implementar o seu próprio floresta na nuvem ou deve efetuar utilizar do Azure Active Directory (AAD)?

Este artigo descreve algumas opções comuns para os desafios colocados por este cenário de reunião e ajuda a que determinar qual a solução melhor corresponder às suas necessidades com base nos seus requisitos.

## <a name="using-azure-active-directory"></a>Utilizar o Azure Active Directory

Pode utilizar AAD para criar um domínio do AD na nuvem e ligá-lo para no local domínio do AD. AAD permite-lhe configurar o serviço single sign-on (SSO) para os utilizadores a executar as aplicações acedidas através da nuvem.

[! [0]][0]

AAD é uma forma simples para implementar um domínio de segurança na nuvem. É utilizada a por muitas aplicações da Microsoft, tal como o Microsoft Office 365. 

Benefícios da utilização AAD:

- Não é necessário para manter uma infraestrutura do Active Directory na nuvem. AAD totalmente é gerida e mantida pela Microsoft.

- AAD fornece as mesmas informações de identidade que está disponível no local.

- Autenticação pode ocorrer no Azure, reduzir a necessidade de aplicações externas e utilizadores contactar o domínio no local.

Pontos a ter em conta ao utilizar AAD:

- Serviços de identidade estão limitados aos utilizadores e grupos. Não existe nenhuma capacidade para autenticar contas de serviço e o computador.

- Tem de configurar conectividade com o seu domínio no local para manter o diretório AAD sincronizado. 

- For o responsável da publicação de aplicações que os utilizadores podem aceder na nuvem através de AAD.

Para obter informações detalhadas, leia o artigo [Execução do Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Utilizar o Active Directory na nuvem associadas a uma floresta no local

Pode alojar serviços AD Directory (AD DS) no local, mas num cenário de híbrido onde os elementos de uma aplicação do estão localizados no Azure poderá ser mais eficaz para criar uma réplica esta funcionalidade e o repositório de AD na nuvem. Esta abordagem pode ajudar a reduzir a latência causada pela enviar autenticação e pedidos de autorização local a partir da nuvem novamente para o AD DS em execução no local. 

[! [1]][1]

Esta abordagem requer que criar o seu próprio domínio na nuvem e associá-lo para a floresta no local. Criar VMs para os serviços de AD DS do anfitrião.

Vantagens de utilizar um domínio separado na nuvem:

- Fornece a capacidade para autenticar o utilizador, o serviço e computador contas no local e na nuvem.

- Fornece acesso à mesma informação de identidade que está disponível no local.

- Não é necessário para gerir floresta AD separada; o domínio na nuvem pode pertencer à floresta no local.

- Pode aplicar a política de grupo definida pelos objetos de GPO no local para o domínio na nuvem.

Considerações para utilizar um domínio separado na nuvem:

- Requer que criar e gerir os seus servidores de AD DS e domínio na nuvem.

- Poderão existir algumas latência de sincronização entre os servidores de domínio na nuvem e os servidores a executar no local.

Para obter informações sobre como configurar esta arquitetura, consulte o artigo [Prolongamento Active Directory diretório serviços (adiciona) para Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Utilizar o Active Directory com floresta separada

Uma organização que executa o Active Directory (AD) no local, poderá ter uma floresta que incluam muitos domínios diferentes. Pode utilizar domínios para fornecer isolamento entre as áreas funcionais que devem ser mantidos em separado, possivelmente por motivos de segurança, mas pode partilhar informações entre domínios estabelecendo relações de fidedignidade.

[! [2]][2]

Uma organização que utiliza domínios separados pode tirar partido do Azure por reposicionar um ou mais destes domínios para floresta separada na nuvem. Em alternativa, uma organização poderá optar por manter todos os recursos de nuvem logicamente distintas desse mantido no local e armazenar informações sobre recursos da nuvem no seu próprio diretório, como parte de uma floresta também contida na nuvem.

Benefícios da utilização floresta separada na nuvem:

- Pode implementar identidades no local e separe identidades só Azure.

- Não precisa de criar uma réplica nos locais floresta do AD para o Azure, reduzindo os efeitos de latência da rede.

Considerações sobre:

- Autenticação para identidades no local na nuvem executa rede extra *saltos* para no local servidores AD.

- Requer que a implementar o seu próprio os servidores de AD DS e floresta na nuvem e estabelecer relações entre florestas fidedignidade adequado.

Documentos de [criar uma floresta de recursos de serviços de diretório do Active Directory (adiciona) no Azure] [ adds-forest-in-azure] descreve como implementar esta abordagem mais detalhadamente.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>Utilizar a Federação do Active Directory serviços (ADFS) com o Azure

ADFS pode executar no local, mas num cenário de híbrido onde aplicações estão localizadas no Azure pode ser mais eficiente para implementar esta funcionalidade na nuvem, conforme apresentado abaixo.

[! [3]][3]

Esta arquitetura é especialmente útil para:

- Soluções que utilizam funcionalidades federada autorização para expor aplicações web para organizações do parceiro.

- Sistemas que suportam o acesso de browsers executar fora da firewall organizacional.

- Sistemas que permitem aos utilizadores para acederem aos aplicações web ligando a partir de dispositivos externos autorizados como computadores remotos, blocos de notas e outros dispositivos móveis. 

Vantagens de utilizar o ADFS com o Azure:

- Pode tirar partido deverá ter em consideração em afirmações aplicações.

- Fornece a capacidade de parceiros externos para a autenticação de fidedignidade.

- Fornece compatibilidade com vasto conjunto de protocolos de autenticação.

Considerações para utilizar o ADFS com Azure:

- Requer que implementar o seus próprio servidores adiciona, ADFS e Proxy de aplicação Web ADFS na nuvem.

- Esta arquitetura pode ser complexa para configurar.

Para obter informações detalhadas, leia o artigo [Implementar a Federação do Active Directory serviços (ADFS) no Azure][adfs-in-azure].

## <a name="next-steps"></a>Próximos passos

Os recursos abaixo explicam como implementar arquitecturas descritas neste artigo.

- [Execução do Azure Active Directory][implementing-aad]
- [Expandir serviços do Active Directory (adiciona) para Azure][extending-adds]
- [Criar uma floresta de recursos de serviços de diretório do Active Directory (adiciona) no Azure][adds-forest-in-azure]
- [Implementar a serviços de Federação do Active Directory (ADFS) no Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Arquitetura de identidade nuvem utilizando o Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Seguro arquitetura de rede híbrida com o Active Directory"
[2]: ./media/guidance-identity/figure3.png "Seguro arquitetura de rede híbridos com separada AD domínios e florestas"
[3]: ./media/guidance-identity/figure4.png "Seguro arquitetura de rede híbrida com o ADFS"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md