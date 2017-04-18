<properties
    pageTitle="Azure Active Directory serviços de domínio: Sincronização no domínios geridos | Microsoft Azure"
    description="Compreender a sincronização de um domínio gerido do Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização de um domínio gerido de serviços de domínio do Azure AD
O diagrama seguinte ilustra como funciona a sincronização nos serviços de domínio do Azure AD geridos domínios.

![Topologia de sincronização nos serviços de domínio do Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização a partir do seu diretório no local ao seu inquilino do Azure AD
Sincronização do Azure AD Connect é utilizada para sincronizar contas de utilizador, grupo associações e credencial efectuar o hash ao seu inquilino do Azure AD. Os atributos de utilizador contas tal como o UPN e no local são sincronizados SID (identificador de segurança). Se utilizar serviços de domínio do Azure AD, hashes credencial legado necessários para autenticação NTLM e Kerberos também são sincronizados para o inquilino do Azure AD.

Se configurar escrita anterior, as alterações que ocorram no seu diretório do Azure AD são sincronizadas regressar ao seu Active Directory no local. Por exemplo, se alterar a palavra-passe utilizar funcionalidades de alteração do Azure AD reposição personalizada de palavra-passe, a palavra-passe alterada é atualizada no seu no local domínio do AD.

> [AZURE.NOTE] Utilize sempre a versão mais recente da ligação do Azure AD para se certificar de que tem correcções para todos os erros conhecidos.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização a partir do seu inquilino do Azure AD para o seu domínio gerido
Contas de utilizador, os membros do grupo e hashes credenciais são sincronizados a partir do seu inquilino do Azure AD para o seu domínio gerido de serviços de domínio do Azure AD. Este processo de sincronização é automático. Não tem de configurar, monitorizar ou gerir deste processo de sincronização. O processo de sincronização também é um-way/unidireccional natureza. É o domínio gerido em grande medida só de leitura, à exceção qualquer ou personalizados que cria. Por conseguinte, não pode efetuar alterações a atributos de utilizador, palavras-passe de utilizador ou os membros do grupo dentro do domínio gerido. Como resultado, não existe nenhuma sincronização inversa de alterações a partir do seu domínio gerido regressar ao seu inquilino do Azure AD.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização a partir de um ambiente floresta múltipla no local
Muitas organizações têm uma infraestrutura de identidade bastante complexas no local que consiste várias florestas de conta. Ligação do Azure AD suporta a sincronização de utilizadores, grupos e hashes de credenciais de floresta múltipla ambientes ao seu inquilino do Azure AD.

Em contrapartida, o seu inquilino do Azure AD é um muito mais simples e plano espaço de nomes. Para permitir que utilizadores sujeito para aceder às aplicações protegidas por Azure AD, resolva conflitos UPN em contas de utilizador em diferentes florestas. Feche o seu ursos de domínio gerido de serviços de domínio do Azure AD semelhança com ao seu inquilino do Azure AD. Por conseguinte, verá uma estrutura de or simples no seu domínio gerido. Todos os utilizadores e grupos são armazenados dentro do contentor de 'AADDC utilizadores', independentemente de domínio no local ou na floresta a partir do qual foram sincronizados no. Poderá ter configurado um or hierárquico estruturar no local. No entanto, o domínio gerido continua a ter uma estrutura simples de or simples.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões - o que não está sincronizado para o seu domínio gerido
Os objetos ou atributos seguintes não são sincronizados para o inquilino do Azure AD ou para o seu domínio gerido:

- **Excluídos atributos:** Pode optar por excluir determinados atributos da sincronizar com o seu inquilino do Azure AD a partir do seu domínio no local com a ligação do Azure AD. Estes excluídos atributos não estão disponíveis no seu domínio gerido.

- **Políticas de grupo:** As políticas de grupo configuradas no seu domínio no local não são sincronizadas para o seu domínio gerido.

- **Partilha SYSVOL:** Do mesmo modo, o conteúdo da partilha SYSVOL no seu domínio no local não é sincronizado para o seu domínio gerido.

- **Objectos de computador:** Objetos de computador para computadores no seu domínio no local não são sincronizados para o seu domínio gerido. Estes computadores não tiverem uma relação de confiança com o seu domínio gerido e pertencem ao seu domínio no local apenas. No seu domínio gerido, localizar objetos de computador apenas para computadores que explicitamente domínio-aderiu ao domínio gerido.

- **SidHistory atributos para utilizadores e grupos:** O utilizador principal e o grupo principal SID a partir do seu domínio no local são sincronizados para o seu domínio gerido. No entanto, atributos SidHistory existentes para utilizadores e grupos não são sincronizados a partir do seu domínio no local para o seu domínio gerido.

- **Estruturas de unidades organização (OU):** Unidades organizacionais definidas no seu domínio no local não são sincronizados para o seu domínio gerido. Existem duas ou incorporada no seu domínio gerido. Por predefinição, o seu domínio gerido tem uma estrutura de or simples. No entanto poderá optar por [criar uma or personalizada no seu domínio gerido](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como atributos específicos são sincronizados para o seu domínio gerido
A tabela seguinte lista algumas atributos comuns e descreve como são sincronizados para o seu domínio gerido.

| No seu domínio gerido de atributos | Origem | Notas |
|:---|:---|:---|
|UPN|Atributo de UPN do utilizador no inquilino do Azure AD|O atributo UPN a partir do seu inquilino do Azure AD é sincronizado como está para o seu domínio gerido. Por conseguinte, a forma mais fiável para iniciar sessão no seu domínio gerido está a utilizar o UPN.|
|SAMAccountName|MailNickname do utilizador do atributo do seu inquilino do Azure AD ou gerados automaticamente|O atributo SAMAccountName é origem a partir do atributo mailNickname no inquilino do Azure AD. Se várias contas de utilizador tem o mesmo atributo mailNickname, o SAMAccountName é gerados automaticamente. Se o utilizador mailNickname ou prefixo UPN for mais de 20 caracteres, a SAMAccountName é para satisfazer o limite de 20 carateres em atributos SAMAccountName gerados automaticamente.|
|Palavras-passe|Palavra-passe de utilizador a partir do seu inquilino do Azure AD|Hashes credenciais necessárias para autenticação NTLM ou Kerberos (também designada credenciais suplementares) são sincronizados a partir do seu inquilino do Azure AD. Se o inquilino do Azure AD for um inquilino sincronizado, estas credenciais são origem a partir do seu domínio no local.|
|Utilizador/grupo principal SID|Gerados automaticamente|O SID principal para contas de utilizador/grupo é gerados automaticamente no seu domínio gerido. Este atributo não corresponder a primário utilizador/grupo SID do objeto no seu no local domínio do AD. Este erro de correspondência é porque o domínio gerido tem um espaço de nomes de SID diferente que o seu domínio no local.|
|Histórico do SID para utilizadores e grupos|Utilizador principal no local e SID de grupo|O atributo SidHistory para utilizadores e grupos no seu domínio gerido está definido para corresponder a correspondente utilizador ou grupo principal SID no seu domínio no local. Esta funcionalidade ajuda facilitar elevação e shift de aplicações no local para o domínio gerido, uma vez que não necessita de para recursos de re ACL.|

> [AZURE.NOTE] **Iniciar sessão no domínio gerido utilizando o formato UPN:** O atributo SAMAccountName poderá para algumas contas de utilizador no seu domínio gerido gerados automaticamente. Se vários utilizadores têm o mesmo atributo mailNickname ou os utilizadores têm demasiados longos prefixos UPN, SAMAccountName para estes utilizadores pode ser gerados automaticamente. Por conseguinte, o formato de SAMAccountName (por exemplo, ' CONTOSO100\joeuser') nem sempre é uma forma fiável de início de sessão para o domínio. Os atributos SAMAccountName gerados automaticamente dos utilizadores pode ser diferentes a partir do seu prefixo UPN. Utilizar o formato UPN (por exemplo, 'joeuser@contoso100.com') para iniciar sessão no domínio gerido sujeito.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não são sincronizados para o inquilino do Azure AD a partir do seu domínio gerido
Tal como descrito numa secção anterior deste artigo, não existe nenhuma sincronização a partir do seu domínio gerido regressar ao seu inquilino do Azure AD. Pode escolher para [criar uma unidade organizacional personalizado (OU)](./active-directory-ds-admin-guide-create-ou.md) no seu domínio gerido. Além disso, pode criar outros ou, os utilizadores, grupos ou contas de serviço nestes ou personalizada. Nenhum dos objetos criados dentro ou personalizados são sincronizados regressar ao seu inquilino do Azure AD. Estes objectos estão disponíveis para utilização apenas dentro do seu domínio gerido. Por conseguinte, estes objetos não estão visíveis utilizar os cmdlets do PowerShell do Azure AD, API do Azure AD Graph ou utilizar a gestão do Azure AD IU.


## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio do Azure AD funcionalidades-](active-directory-ds-features.md)

- [Cenários de implementação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)

- [Considerações de funcionamento em rede para serviços de domínio do Azure AD](active-directory-ds-networking.md)

- [Começar a trabalhar com serviços de domínio do Azure AD](active-directory-ds-getting-started.md)
