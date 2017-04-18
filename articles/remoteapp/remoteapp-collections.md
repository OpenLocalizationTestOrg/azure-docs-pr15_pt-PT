<properties 
    pageTitle="Que tipo de coleção de que necessita para RemoteApp do Azure? | Microsoft Azure" 
    description="Saiba mais sobre os tipos de coleções de disponíveis com o Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Que tipo de coleção de que necessita para RemoteApp do Azure?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp permite-lhe partilhar aplicações e recursos com utilizadores em qualquer dispositivo. Vamos fazê-lo através da criação de colecções com mantenha as aplicações e recursos e, em seguida, partilhe essas coleções de sites com utilizadores. Existem 2 Opções de coleções de sites diferentes, com opções de autenticação - o que é ideal para si e de rede diferente?

Vamos abordar as considerações diferentes e escolhas que precisa de efetuar tirar o máximo partido da sua coleção de RemoteApp do Azure. 


## <a name="quick-differences-between-the-collection-types"></a>Rápida diferenças entre os tipos de coleções de sites

|           | Nuvem | Híbrido |
|-----------|-------|--------|
|Utilizar um VNET existente| Sim| Sim|
|Requer que contas ligadas AD (DirSync)| N| Sim|
|Necessita de associação de domínio| N| Sim|
|Requer o controlador de domínio acessível a VNET| N| Sim|

## <a name="cloud-collections"></a>Nuvem coleções de sites
- Rápido criar - a coleção de rapidamente está aprovisionada, o que significa que as suas aplicações obter aos utilizadores mais rápidas.
- Trazer o seus próprio aplicações ou partilhe om. Pode utilizar uma imagem personalizada (criado a partir de um VM Azure) ou das imagens incluídas com a sua subscrição.
- Não tem de configurar uma ligação entre a coleção e o seu domínio no local.
- Mas, opcionalmente, pode utilizar a sua própria VNET Azure para fornecer acesso para o ambiente no local para a partilha de dados ou para utilizar a autenticação não Windows para recursos como SQL Server (utilizando a autenticação de base de dados).


OK, como criar um?

- Nuvem? Criar com a opção de **Criação rápida** no portal.
- Nuvem + VNET? Criar utilizando a opção **criar com VNET** mas não escolher aderir a um domínio.

## <a name="hybrid-collections"></a>Coleções de híbrido
- Fornece acesso total a rede no local + Azure VNET.
- Inclui o acesso de associação de domínio para aplicações e dados. Aplicações remotas podem autenticação contra o Active Directory no local - em seguida, pode aceder a recursos no seu domínio.
- Ativar a monitorização avançada e gestão com soluções existentes do Centro de sistema e políticas de grupo do Windows (através de uma imagem personalizada incorporada no Windows Server 2012 R2)
- Suporta [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para ligar a sua VNET Azure ao seu VNET local.

Criar utilizando a opção **criar com VNET** e selecione para aderir a um domínio.

## <a name="authentication-options"></a>Opções de autenticação
Azure RemoteApp suporta contas do Microsoft e contas do Azure Active Directory, mas não todas as colecções suportam todos os métodos. 

| Tipo de conta                      |                                                             | Nuvem | Nuvem + VNET | Híbrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Conta Microsoft                 |                                                             | Sim   | Sim          | N     |
| Azure Active Directory (Azure AD) |                                                             |       |              |        |
|                                   | Apenas Azure AD                                               | Sim   | Sim          | N     |
|                                   | AD Connect com a sincronização de palavra-passe                               | Sim   | Sim          | Sim    |
|                                   | AD Connect sem sincronização de palavra-passe                            | Sim   | Sim          | N     |
|                                   | AD ligar-se com o AD FS                                       | Sim   | Sim          | Sim    |
|                                   | fornecedores de identidade de suportadas Azure 3 terceiros (como Ping) | Sim   | Sim          | Sim    |
| Autenticação Multifator       |                                                             | Sim   | Sim          | Sim    |



### <a name="cloud-and-cloud--vnet"></a>Nuvem e nuvem + VNET 
Com coleções de nuvem, pode utilizar contas Microsoft, contas do Azure AD ou uma mistura dos dois. Utilize as contas que funcionam melhor para os seus utilizadores.

Não existem requisitos específicos para utilizar contas do Microsoft. 

Se pretender utilizar contas do Azure AD, é necessário para se certificar de que o inquilino do Azure AD corresponde a uma associada à sua subscrição. Quando criou a sua subscrição do Azure RemoteApp, o inquilino do Azure AD que estava a utilizar foi automaticamente associado à sua subscrição. Qualquer utilizador do Azure AD que dar permissão tem de estar ao mesmo inquilino. Se for necessário, pode [alterar o inquilino do Azure AD](remoteapp-changetenant.md) associada à sua subscrição.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Híbrido (ou na nuvem + Azure AD + AD)

Utilizar o Azure AD + no local do Active Directory é um pré-requisito para uma coleção de híbrido. Tem de utilizar o AD Connect para integrar os dois directórios. Mas tiver alguns escolha quando chegar como configurar o AD Connect. 

Existem 2 AD Connect cenários - utilizar a sincronização de palavra-passe ou utilizar a Federação do AD. Consulte as [informações de AD Connect](../active-directory/active-directory-aadconnect.md) descobrir que estes funciona melhor por si.

Também pode utilizar Azure AD + AD uma coleção de nuvem. Certifique-se de que o mesmo configurar passos que segue.

Dar saída [Azure AD + requisitos do Active Directory para RemoteApp do Azure](remoteapp-ad.md) para obter os passos necessários para configurar o Azure AD e o Active Directory.

## <a name="go-create-your-collection"></a>Aceda a criar a sua coleção de!
OK, posso pensar podemos tenha familiarizado-lo agora, pelo que não existe apenas uma coisa da esquerda para a fazer - criar a sua coleção de RemoteApp do Azure primeira.

[Criar uma coleção de nuvem](remoteapp-create-cloud-deployment.md) ou [criar uma coleção de híbrido](remoteapp-create-hybrid-deployment.md) - obter criar apenas.
