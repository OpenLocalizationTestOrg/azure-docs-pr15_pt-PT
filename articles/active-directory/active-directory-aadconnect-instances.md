<properties
    pageTitle="Ligação do Azure AD: Sincronizar instâncias do serviço | Microsoft Azure"
    description="Esta página documentos considerações especiais para instâncias do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Ligação do Azure AD: Considerações especiais para instâncias
Ligação do Azure AD com mais frequência é utilizado com a instância de toda a nível mundial do Azure AD e no Office 365. Mas também existem outras instâncias e estes têm requisitos diferentes relativamente a URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
A [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) é uma nuvem soberania operada por um utilizador fidedigno dados alemão.

Este nuvem atualmente está na pré-visualização. Muitos dos cenários que normalmente pode fazer por si, tais como verificar domínios, deve ser executado pelo operador. Contacte o seu representante local da Microsoft para obter mais informações sobre como participar na pré-visualização.

URLs para o abrir no servidor proxy |
--- |
\*. microsoftonline.de |
\*. windows.net |
+ Listas de revogação de certificados |

Quando iniciar sessão no seu diretório do Azure AD tem de utilizar uma conta de no domínio de onmicrosoft.de.

Funcionalidades atualmente não se encontra presentes na Alemanha Microsoft Cloud:

- Azure AD ligar o estado de funcionamento do não está disponível.
- As atualizações automáticas não está disponível.
- Repetição de escrita de palavra-passe não está disponível.

## <a name="microsoft-azure-government-cloud"></a>Nuvem do Microsoft Azure administração pública
Na [nuvem da Microsoft Azure administração pública](https://azure.microsoft.com/features/gov/) é uma nuvem para Governo dos E.U.A.

Este nuvem tem sido suportada por versões anteriores do DirSync. A partir de compilação 1.1.180 de ligação do Azure AD a próxima geração da nuvem é suportada. Este geração está a utilizar só EUA com base os pontos finais e uma lista diferente de URLs para o abrir no servidor proxy.

URLs para o abrir no servidor proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Listas de revogação de certificados |

Ligação do Azure AD não conseguir detetar automaticamente o que está localizado no diretório da sua Azure AD na nuvem administração pública. Em vez disso, tem de efetuar as seguintes ações quando instala a ligação do Azure AD.

1. Inicie a instalação de ligação do Azure AD.
2. Assim que vir a primeira página onde é suposto existirem para aceitar o EULA, não continue mas deixar o Assistente de instalação em execução.
3. Inicie o regedit e alterar a chave de registo `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` ao valor `2`.
4. Aceda novamente ao Assistente de instalação de ligação do Azure AD, aceitar o EULA e continuar. Durante a instalação, certifique-se utilizar o caminho de instalação de **configuração personalizado** (e não Express instalação). Em seguida, continue a instalação como costuma fazer.

Funcionalidades atualmente não se encontra presentes na nuvem Microsoft Azure administração pública:

- Azure AD ligar o estado de funcionamento do não está disponível.
- As atualizações automáticas não está disponível.
- Repetição de escrita de palavra-passe não está disponível.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
