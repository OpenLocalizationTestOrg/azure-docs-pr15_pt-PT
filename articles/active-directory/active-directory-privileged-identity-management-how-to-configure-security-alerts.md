<properties
   pageTitle="Como configurar alertas de segurança | Microsoft Azure"
   description="Saiba como configurar alertas de segurança para a extensão de gestão de identidades privilegiados Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Como configurar alertas de segurança na gestão de identidades privilegiados do Azure AD

## <a name="security-alerts"></a>Alertas de segurança
Azure privilegiados identidade gestão (PIM) gera alertas quando existe actividade suspeita ou não seguras no seu ambiente. Quando um alerta é acionado,-mostra no dashboard de PIM. Selecione o alerta para ver um relatório que lista os utilizadores ou funções que o acionou o alerta.

![Alertas de segurança de dashboard PIM - captura de ecrã][1]



| Alerta | Accionador | Recomendação |
| ----- | ------- | -------------- |
| **As funções são a ser atribuídas fora PIM** | Um administrador permanentemente tiver sido atribuído a uma função, fora da interface PIM. | Reveja a nova atribuição de funções. Uma vez que outros serviços só podem atribuir administradores permanentes, altere-o para uma atribuição elegível se for necessário. |
| **Funções estão a ser ativadas demasiado frequentemente** | Que ocorreram reactivations demasiadas da mesma função dentro do prazo permitido nas definições. | Contacte o utilizador para ver porque é que eles tem ativado a função por isso, muitas vezes. Talvez o limite de tempo é demasiado pequeno para-los para concluir as tarefas ou talvez que estiver a utilizar o scripts ativar automaticamente uma função. |
| **Funções não exigem autenticação multifator para ativação** | Existem funções sem MFA ativado nas definições. | Vamos exigir MFA para as funções mais altamente privilegiadas, mas vivamente que permitem o MFA para ativação de todas as funções. |
| **Os administradores não estiverem a utilizar as funções privilegiadas** | Existem administradores elegíveis que ainda não o tenha ativado respectivas funções recentemente. | Inicie uma revisão de acesso para determinar os utilizadores que não são precisas acesso. |
| **Existem demasiadas administradores globais** | Existem mais globais administradores de recomendado. | Se tiver um número elevado de administradores globais, é provável que os utilizadores estão a obter mais permissões do que precisam. Mover utilizadores para o menos privilegiadas funções ou certifique algumas páginas elegível para a função em vez de permanentemente atribuídos. |

## <a name="configure-security-alert-settings"></a>Configurar definições de alerta de segurança

Pode personalizar alguns dos alertas de segurança no PIM para trabalhar com o seu ambiente e as metas de segurança. Siga estes passos para alcançar a pá definições:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e selecione o mosaico do **Azure AD privilegiados gestão de identidades** do dashboard.
2. Selecione **gerido funções privilegiadas** > **Definições** > **definições de alertas**.

    ![Navegue até definições de alertas de segurança][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerta de "Funções estão a ser ativadas demasiado frequentemente"

Este alerta accionadores se um utilizador activa a mesma função privilegiada várias vezes dentro de um período especificado. Pode configurar o período de tempo e o número de activações.

- **Período definido de renovação de ativação**: Especifique em dias, horas, minutos e segundo o período de tempo que pretende utilizar para controlar suspeita renovação.

- **Número de renovação de ativação**: Especifique o número de e, de 2 a 100, que considera valioso de alerta, dentro do período de tempo que escolheu. Pode alterar esta definição ao mover o controlo de deslize ou escrever um número na caixa de texto.


### <a name="there-are-too-many-global-administrators-alert"></a>Alerta de "Existem demasiadas administradores globais"

Se forem cumpridos duas diferente e pode configurar ambos-los de PIM accionadores este alerta. Em primeiro lugar, tem de atingir um determinado limiar de administradores globais. Em segundo lugar, uma determinada percentagem as total de atribuições de funções tem de ser administradores globais. Se cumprir apenas um dos seguintes medidas, não é apresentado o alerta.  

- **Número mínimo de administradores globais**: Especifique o número de administradores globais, de 2 a 100, que considere um montante não seguros.

- **Percentagem de administradores globais**: Especifique a percentagem de administradores que sejam administradores globais, a partir de 0% para 100%, que é seguro no seu ambiente.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alerta de "Os administradores não estiverem a utilizar as funções privilegiadas"

Este alerta accionadores se um utilizador acede um determinado período de tempo sem ativar uma função.

- **Número de dias**: Especifique o número de dias, de 0 a 100, o que um utilizador pode ir sem ativar uma função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
