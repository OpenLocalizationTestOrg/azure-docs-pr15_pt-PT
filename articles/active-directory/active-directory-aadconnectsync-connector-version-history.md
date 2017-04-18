<properties
   pageTitle="Histórico de versões do conector do lançamento | Microsoft Azure"
   description="Este tópico apresenta todas as versões dos conectores para o Gestor de identidade do Forefront (FIM) e o Gestor de identidade da Microsoft (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Histórico de versões do conector do lançamento
As conexões para o Gestor de identidade do Forefront (FIM) e o Gestor de identidade da Microsoft (MIM) são atualizadas com frequência.

>[AZURE.NOTE]
Este tópico é apenas no FIM e MIM. Estes conectores não são suportadas na ligação do Azure AD.

Este tópico listar todas as versões das conexões que foram disponibilizadas.

Ligações relacionadas:

- [Transferir conectores mais recentes](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentação de referência do [Conector de LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md)
- Documentação de referência do [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md)
- Documentação de referência do [Conector de serviços Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- [Conector do PowerShell](active-directory-aadconnectsync-connector-powershell.md) documentação de referência
- [Conector do Lotus Domino](active-directory-aadconnectsync-connector-domino.md) documentação de referência

## <a name="111170"></a>1.1.117.0
Disponibilizado: Março de 2016

**Nova conexão**  
Lançamento inicial do [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Novas funcionalidades:**

- Conector do LDAP genérico:
    - Obter suporte adicionada para importar delta com Isode.
- Conexão de serviços Web:
    - Atualizado a atividade de csEntryChangeResult e setImportErrorCode atividade para permitir que os erros de nível do objeto a ser devolvido novamente para o motor de sincronização.
    - Atualizado os modelos SAP6 e SAP6User para utilizar a nova funcionalidade de erro ao nível do objeto.
- Conector do Lotus Domino:
    - Para exportar, é necessário um certifier por livro de endereços. Agora pode utilizar a mesma palavra-passe para todos os certificadores para facilitar a gestão.

**Problemas fixos:**

- Conector do LDAP genérico:
    - Para IBM Tivoli DS, alguns atributos de referência não foram detetados corretamente.
    - Para abrir LDAP durante a importação uma delta, foram truncados finais no início e no fim de cadeias.
    - Para Novell e NetIQ, cujo nome foi mudado uma exportação que movido de um objeto entre ou/contentores e ao mesmo tempo objecto falhou.
- Conexão de serviços Web:
    - Se o serviço web tivesse vários pontos finais para o mesmo enlace, em seguida, a conexão não corretamente descobriu estes pontos finais.
- Conector do Lotus Domino:
    - Uma exportação do atributo fullName para uma base de dados mail-in não funcionarem.
    - Uma exportação que tanto adicionados e removidos membro de um grupo exportados apenas os membros adicionados.
    - Se um documento de notas é inválido (o atributo isValid definido como falso), em seguida, o falha de conexão.

## <a name="older-releases"></a>Versões mais antigas
Antes de Março de 2016, os conectores foram disponibilizados como tópicos de suporte.

**LDAP genérico**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, Setembro de 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, Março de 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, Janeiro de 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, Setembro de 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, Março de 2014

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, Setembro de 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, Setembro de 2014

**Do Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, Setembro de 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, Março de 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, Agosto de 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, Fevereiro de 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, Outubro de 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, Agosto de 2013

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
