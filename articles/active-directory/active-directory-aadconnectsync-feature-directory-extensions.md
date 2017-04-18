<properties
   pageTitle="Sincronização do Azure AD Connect: extensões Directory | Microsoft Azure"
   description="Este tópico descreve a funcionalidade de extensões de diretório na ligação do Azure AD."
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
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões de diretório
Extensões Directory permite-lhe expandir o esquema no Azure AD com o seus próprio atributos a partir do Active Directory no local. Esta funcionalidade permite-lhe criar aplicações LOB utilizado por outros atributos que continuar a gerir no local. Estes atributos podem ser consumidos através de [extensões de diretório do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou do [Microsoft Graph](https://graph.microsoft.io/). Pode ver os atributos disponíveis com o [Explorador de Azure AD Graph](https://graphexplorer.cloudapp.net) e [do Microsoft Graph Explorador](https://graphexplorer2.azurewebsites.net/) respetivamente.

Em apresentar sem carga de trabalho do Office 365 consome estes atributos.

Configure quais os atributos adicionais que pretende sincronizar com o caminho de definições personalizadas no Assistente de instalação.
![Esquema extensão assistente](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) a instalação mostra os seguintes atributos, são candidatos válidos:

- Tipos de objeto do utilizador e grupo
- Atributos de valor único: cadeia, booleano, o número inteiro, binários
- Atributos múltiplos: cadeia, binários

A lista de atributos é lido da cache de criado durante a instalação da ligação do Azure AD. Se tiver expandido esquema do Active Directory com os atributos adicionais, o [esquema deve ser atualizada](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) antes destes novos atributos estão visíveis.

Um objeto pode ter até 100 atributos de extensões de diretório. O comprimento máximo é 250 caracteres. Se um valor do atributo for maior, é truncado pelo motor de sincronização.

Durante a instalação da ligação do Azure AD, uma aplicação é registada onde seguintes atributos estão disponíveis. Pode ver esta aplicação no portal do Azure.  
![Aplicação de extensão de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Estes atributos estão agora disponíveis através do gráfico:  
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos são o prefixo com extensão\_{AppClientId}\_. O AppClientId tem o mesmo valor para todos os atributos no seu diretório do Azure AD.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
