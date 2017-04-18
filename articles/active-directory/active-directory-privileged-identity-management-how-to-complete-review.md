<properties
   pageTitle="Como concluir uma revisão acesso | Microsoft Azure"
   description="Depois de iniciada uma revisão do access na gestão de identidades do Azure AD privilegiados, saiba como executá-la e ver os resultados"
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
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Como concluir uma revisão do access na gestão de identidades do Azure AD privilegiados


Os administradores de função privilegiados podem rever acesso privilegiado uma vez por uma [Revisão de segurança foi iniciado](active-directory-privileged-identity-management-how-to-start-security-review.md). Gestão de identidades do Azure AD privilegiados (PIM) irá enviar automaticamente uma mensagem de e-mail pedir aos utilizadores para rever o seu acesso. Se um utilizador não conseguiu obter uma mensagem de e-mail, pode enviá-los as instruções no [artigo como efetuar uma revisão de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Depois de período de revisão de segurança ou todos os utilizadores tem concluído a sua rever personalizada, siga os passos neste artigo para gerir a revisão e ver os resultados.

## <a name="manage-security-reviews"></a>Gerir segurança revisões

1. Aceda ao [portal do Azure](https://portal.azure.com/) e selecione a aplicação de **Gestão de identidades do Azure AD privilegiados** no seu dashboard.
2. Selecione a secção **revê o acesso** do dashboard.
3. Selecione a revisão de acesso que pretende gerir.

No pá de detalhe a revisão de acesso existem opções de um número para gerir esse rever.

![Botões de revisão do access PIM - captura de ecrã][1]

### <a name="remind"></a>Lembrar

Se rever um acesso estiver definido para cima, para que os utilizadores rever eles próprios, o botão de **Lembrar** envia uma notificação. 

### <a name="stop"></a>Parar

Todas as revisões do access têm uma data de fim, mas pode utilizar o botão **Parar** para conclui-la mais cedo. Se todos os utilizadores ainda não foi revistos por desta vez, não poderá para depois de parar a revisão. Não é possível reiniciar uma revisão depois está parado.

### <a name="apply"></a>Aplicar

Depois de concluída uma revisão do access, quer porque a data de fim de instalações atingido ou parado-lo manualmente, no botão **Aplicar** implementa o resultado da revisão. Se o acesso de um utilizador foi negado na revisão, este é o passo que removerá os respetivos atribuição de funções.  

### <a name="export"></a>Exportar

Se pretender aplicar os resultados de rever a segurança manualmente, pode exportar rever. Botão **Exportar** começa a transferir um ficheiro CSV. Pode gerir os resultados no Excel ou outros programas que abrem ficheiros CSV.

### <a name="delete"></a>Eliminar

Se não estiver interessado em rever a quaisquer outras, eliminá-lo. Botão **Eliminar** remove rever a partir da aplicação PIM.

> [AZURE.IMPORTANT] Não irá aparecer um aviso antes de eliminação ocorre, por isso, certifique-se de que pretende eliminar essa rever.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
