<properties
    pageTitle="Ligação do Azure AD: Suportadas topologias | Microsoft Azure"
    description="Este tópico detalhes topologias suportadas e não suportadas para ligação do Azure AD"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Ligar topologias para Azure AD
O objectivo deste tópico é descrever diferente no local e topologias Azure AD com a sincronização de ligação do Azure AD como a solução de integração de chave. Descreve as configurações suportadas e não suportadas.

Legenda de imagens no documento:

Descrição | Ícone
-----|-----
No local floresta do Active Directory| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Do Active Directory com a importação filtrada| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Servidor de sincronização do Azure AD Connect| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect servidor "transição modo de sincronização"| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync com FIM2010 ou MIM2016| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Servidor de sincronização do Azure AD Connect, detalhada| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Diretório do Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Cenário não suportado | ![Não suportadas](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Um floresta, um inquilino do Azure AD
![Inquilino única único floresta](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comuns é uma única floresta no local, com um ou vários domínios e uma única Azure AD inquilino. Para a autenticação do Azure AD, é utilizada a sincronização de palavra-passe. A instalação rápida de ligação do Azure AD suporta apenas esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, vários servidores de sincronização para um inquilino do Azure AD
![Única floresta filtrado não suportadas](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não é suportada para ter várias servidores de sincronização de ligação do Azure AD ligados ao mesmo Azure AD inquilino, exceto um [servidor de transição](#staging-server). É suportado, mesmo se estes estão configurados para sincronizar mutuamente conjunto de objetos. Lhe poderá ter considerado isto se não conseguir contactar todos os domínios na floresta a partir de um único servidor ou para distribuir a carga de trabalho através de vários servidor.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Múltiplas florestas, único inquilino do Azure AD
![Inquilino única multi floresta](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações tenham ambientes com vários florestas do Active Directory no local. Existem várias razões para ter mais do que uma floresta do Active Directory no local. Exemplos típicos são estruturas com florestas conta recurso e como um resultado após uma avaliação de aquisições ou aquisição.

Quando tiver várias florestas, todos os florestas tem de ser acessível por única ligação do Azure AD servidor de sincronização. Não possui aderir o servidor para um domínio. Se for necessário para alcançar floresta todos os, o servidor pode ser colocado numa rede DMZ.

O Assistente de instalação de ligação do Azure AD oferece várias opções para consolidar os utilizadores representados em várias florestas. O objetivo é que um utilizador representado apenas uma vez no Azure AD. Existem algumas topologias comuns que pode configurar no caminho de instalação personalizada no Assistente de instalação. Selecione a opção correspondente que representa a topologia na página **identificar exclusivamente os seus utilizadores**. A consolidação apenas está configurada para os utilizadores. Grupos duplicados não são consolidados com a configuração predefinida.

Topologias comuns são descritas na secção seguinte: [topologias separadas](#multiple-forests-separate-topologies), [malha completa](#multiple-forests-full-mesh-with-optional-galsync)e [Recursos de conta](#multiple-forests-account-resource-forest).

A configuração de predefinida na ligação do Azure AD sync assume:

1. Os utilizadores têm apenas uma conta activada e floresta onde se encontra esta conta é utilizada para autenticar o utilizador. Este partem do princípio é para a sincronização ambas as palavras-passe e para a Federação. UserPrincipalName e sourceAnchor/immutableID vir de nesta floresta.
2. Os utilizadores têm apenas uma caixa de correio.
3. Floresta que aloja a caixa de correio para um utilizador tem a melhor qualidade de dados para atributos visíveis na lista de endereços Global Exchange (GAL). Se existir sem caixa de correio no utilizador, qualquer floresta pode ser utilizada para contribuir estes valores de atributo.
4. Se tiver uma caixa de correio ligada, em seguida, também existe outra conta numa floresta diferente utilizada para iniciar sessão.

Se o seu ambiente não corresponder a estes pressupostos, acontece o seguinte procedimento:

- Se tiver mais de uma conta active ou mais do que uma caixa de correio, o motor de sincronização escolhe um e ignorar o outro.
- Uma caixa de correio ligada com nenhuma outra conta active não é exportada para Azure AD. A conta de utilizador não é representada como um membro em qualquer grupo. Uma caixa de correio ligada DirSync sempre seria ser representada como uma caixa de correio normal. Esta alteração intencionalmente é um comportamento diferente para melhor suportar floresta múltipla cenários.

Obter mais detalhes podem ser encontrados na [Noções sobre a configuração de predefinido](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um inquilino do Azure AD
![Multi floresta Multi sincronização não suportadas](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Não é suportada para ter mais de servidor do Azure AD ligar Sync ligado a uma única inquilino do Azure AD. A exceção é a utilização de um [servidor de transição](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Várias florestas – topologias separadas
**Os utilizadores são representados apenas uma vez em todos os directórios**

![Multi floresta os utilizadores de uma vez](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Multi floresta Seperate topologias](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Neste ambiente, todos os florestas no local é tratado como entidades separadas e nenhum utilizador seria apresentar qualquer outra floresta.
Cada floresta tem a sua própria organização do Exchange e não existe nenhuma GALSync entre as florestas. Esta topologia poderão ser a situação depois de uma avaliação de aquisições/aquisição ou numa organização onde está a funcionar cada unidade de negócio, isolada uns dos outros. Estes florestas são na mesma organização no Azure AD e aparecem com uma GAL unificada.
Nesta imagem, cada objeto em toda a floresta é representado uma vez na metaverse e agregado no inquilino de destino Azure AD.

### <a name="multiple-forests--match-users"></a>Várias florestas – utilizadores de correspondência
**Identidades de utilizador existirem em vários directórios**

Para todos os estes cenários comuns é que distribuição e grupos de segurança podem conter uma mistura de utilizadores, contactos e FSP (principais de segurança externo)

FSP é utilizados em adiciona para representar os membros a partir de outras florestas num grupo de segurança. Todos os FSP está Azure AD resolvido para o objeto real.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Várias florestas – malha completa com GALSync opcional
**Identidades de utilizador existirem em vários directórios. Corresponder utilizando: atributo de correio**

![Correio de utilizadores multi floresta](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Malha completa multi floresta](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite aos utilizadores e recursos estar localizados em qualquer floresta e frequentemente aí seria fidedignidades bidirecional entre as florestas.

Se existir mais do que uma floresta Exchange, pode, opcionalmente, haver uma solução de GALSync no local. Cada utilizador seria representada como um contacto em todas as outras florestas. GALSync frequentemente é implementado a utilizar o Gestor de identidades do Forefront 2010 ou o Gestor de identidades do Microsoft 2016. Ligação do Azure AD não pode ser utilizado para GALSync no local.

Neste cenário, objetos de identidade são associados utilizando o atributo de correio. Um utilizador com uma caixa de correio numa floresta está associado com os contactos de outras florestas.

### <a name="multiple-forests--account-resource-forest"></a>Várias florestas – floresta recursos de conta
**Identidades de utilizador existirem em vários directórios. Corresponder utilizando: atributos ObjectSID e msExchMasterAccountSID**

![Floresta de múltiplos utilizadores ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![AccountResource multi floresta](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Na topologia de floresta recurso de conta, tem uma ou mais florestas conta com contas de utilizador ativo. Também tem um ou mais florestas de recurso com contas desativadas.

Neste cenário um (ou mais) **floresta recurso** fidedignidades de todas as **florestas de conta**. Floresta de recursos normalmente tem um esquema do AD expandido com o Exchange e o Lync. Todos os serviços do Exchange e o Lync, bem como de outros serviços partilhados estão localizados nesta floresta. Os utilizadores têm uma conta de utilizador desativado nesta floresta e a caixa de correio está ligada à floresta de conta.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 tem algumas restrições de topologias suportadas. Se planeia utilizar qualquer um dos seguintes procedimentos, em seguida, leia o tópico de topologias suportadas para a carga de trabalho.

Carga de trabalho |  
--------- | ---------
Exchange Online | Se existir mais do que um Exchange organização no local (ou seja, Exchange ter sido implementado a mais do que uma floresta), em seguida, tem de utilizar o Exchange 2013 SP1 ou posterior. Detalhes podem ser encontrados aqui: [implementações híbridas com vários florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype para empresas | Se utilizar vários florestas no local, apenas a topologia de floresta conta recurso é suportada. Detalhes para topologias suportadas podem ser encontradas aqui: [ambientais requisitos para o Skype para empresas Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Servidor de transição
![Servidor de transição](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Ligação do Azure AD suporta instalar um segundo servidor no **modo de teste**. Um servidor neste modo lê dados a partir de todos os directórios ligados, mas não escreva qualquer coisa a directórios ligados. Que está a utilizar o ciclo de sincronização normal e, por isso, tem uma cópia atualizada os dados de identidade. Numa falhas onde o servidor primário falha podem falhar ao longo para o servidor de teste. Pode fazê-lo no Assistente de ligação do Azure AD. Este segundo servidor de preferência pode ser localizado num centro de dados diferentes uma vez que não infraestrutura é partilhada com o servidor principal. Tem de copiar manualmente qualquer alteração da configuração efetuada no servidor principal para o segundo servidor.

Um servidor de teste pode também ser utilizado para testar uma nova configuração personalizada e o efeito tiver dos seus dados. Pode pré-visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, pode tornar o servidor de teste o servidor ativo e configurar o servidor activo antigo no modo de transição.

Este método também pode ser utilizado para substituir o servidor de sincronização do active. Preparar o novo servidor e defini-lo no modo de transição. Certifique-se-está em bom estado, desativar (tornando ativo), o modo de transição e encerrar o servidor atualmente ativo.

É possível ter mais do que um servidor de teste quando quiser ter várias cópias de segurança no centros de dados diferentes.

## <a name="multiple-azure-ad-tenants"></a>Múltiplos inquilinos do Azure AD
A Microsoft recomenda a ter um inquilino única no Azure AD para uma organização.
Antes de planear utilizar múltiplos inquilinos do Azure AD, estes tópicos abrangem cenários comuns, permitindo-lhe utilizar um único inquilino.

Tópico |  
--------- | ---------
Delegação de utilizando unidades administrativas | [Gestão de unidades administrativas no Azure AD](active-directory-administrative-units-management.md)

![Multi floresta Multi inquilino](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Existe uma relação de 1:1 entre um servidor de sincronização de ligação do Azure AD e um inquilino do Azure AD. Cada inquilino do Azure AD, terá uma instalação do servidor de ligação do Azure AD sync. As instâncias de inquilino do Azure AD são por predefinição, isolada e os utilizadores de uma não é possível ver utilizadores no outro inquilino. Se esta separação destina-se, em seguida, esta é uma configuração suportada, mas caso contrário, deve utilizar único modelo de inquilino do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez num inquilino Azure AD
![Floresta único filtrada](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nesta topologia, um servidor de sincronização de ligação do Azure AD está ligado a cada inquilino do Azure AD. Os servidores de sincronização de ligação do Azure AD tem de ser configurados para filtrar para cada tenha um conjunto de objetos para trabalhar com no mutuamente exclusivos. Por exemplo pode reduzir a cada servidor para um determinado domínio ou OR. Só pode ser registado um domínio DNS numa única inquilino do Azure AD. Os UPNs AD tem de utilizar separados espaços de nomes, assim os utilizadores no no local. Por exemplo, na imagem acima três UPN separada sufixos estão registados na no local AD: contoso.com, fabrikam.com e wingtiptoys.com. Os utilizadores em cada no local domínio do AD utilizar um espaço de nomes diferente.

Não existe nenhuma GALsync entre as instâncias de inquilino do Azure AD. O livro de endereços no Exchange Online e o Skype para empresas apenas mostra os utilizadores ao mesmo inquilino.

Esta topologia tem as seguintes restrições ao contrário cenários suportados:

- Apenas um dos inquilinos do Azure AD pode ativar a implementação híbrida do Exchange com o Active Directory no local.
- Só podem ser associados um inquilino do Azure AD dispositivos Windows 10.

O requisito de mutuamente conjunto de objetos também se aplica de escrita não consolidada. Algumas funcionalidades de repetição de escrita não são suportadas com este topologia de uma vez que estas funcionalidades partem do pressuposto de configuração de um único no local:

-   Grupo escrita não consolidada com configuração predefinida
-   Dispositivo repetição de escrita

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes num inquilino Azure AD
![Única floresta Multi inquilino não suportadas](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Conectores de Multi único floresta não suportadas](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Os não é suportado para sincronizar ao mesmo utilizador múltiplos inquilinos do Azure AD.
- É suportada para efetuar uma alteração para que os utilizadores no Azure um AD aparecem como contactos no outro inquilino do Azure AD de configuração.
- Os não é suportado para modificar a ligação do Azure AD sync para ligar a múltiplos inquilinos do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync utilizando a repetição de escrita
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD inquilinos são por predefinição, isolada.

- Os não é suportado para alterar a configuração de ligação do Azure AD sync para ler dados a partir de outro inquilino do Azure AD.
- Não é suportado para exportar utilizadores como contactos para outro local AD utilizando a ligação do Azure AD sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync com o servidor de sincronização no local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

É suportada para utilizar FIM2010/MIM2016 no local para utilizadores de GALsync entre duas organizações do Exchange. Os utilizadores na uma organização mostra como externos utilizadores por contactos da outra organização. Estes anúncios diferente no local, em seguida, podem ser sincronizados para os seus próprios inquilinos do Azure AD.

## <a name="next-steps"></a>Próximos passos
Para saber como instalar a ligação do Azure AD para estes cenários, consulte o artigo [instalação personalizada de ligação do Azure AD](./connect/active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
