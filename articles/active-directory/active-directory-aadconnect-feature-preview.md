<properties
   pageTitle="Ligação do Azure AD: Funcionalidades na pré-visualização | Microsoft Azure"
   description="Este tópico descreve no mais funcionalidades de detalhe que se encontram na pré-visualizar na ligação do Azure AD."
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

# <a name="more-details-about-features-in-preview"></a>Obter mais detalhes sobre as funcionalidades na pré-visualização
Este tópico descreve como utilizar funcionalidades atualmente na pré-visualização.

## <a name="group-writeback"></a>Grupo repetição de escrita
A opção para o grupo de escrita não consolidada no funcionalidades opcionais permite-lhe para escrita não consolidada **Grupos do Office 365** para uma floresta com Exchange instalado. Este é um grupo de que está sempre conhece na nuvem. Se tiver o Exchange no local, em seguida, pode escrever novamente estes grupos para o local por isso, os utilizadores com uma caixa de correio do Exchange no local podem enviar e receber e-mails destes grupos.

Mais informações sobre grupos do Office 365 e como utilizá-las podem ser encontradas [aqui](http://aka.ms/O365g).

Este grupo vai ser representado como um grupo de distribuição no local AD DS. O servidor do Exchange no local tem de ser em atualização cumulativa do Exchange 2013 8 (disponibilizado de Março de 2015) ou Exchange 2016 reconheçam este novo tipo de grupo.

**Notas durante a pré-visualização**

- O atributo do livro de endereços não está atualmente preenchido na pré-visualização. Sem este atributo, grupo só estarão visível na lista de endereços global. A forma mais fácil para preencher deste atributo é utilizar o cmdlet do Exchange PowerShell `update-recipient`.
- Apenas florestas com o esquema do Exchange estão destinos válidos para grupos. Não se foi detetado nenhum Exchange, em seguida, grupo repetição de escrita não será possível ativar.
- Atualmente são suportadas apenas único floresta organização híbridas do Exchange. Se tiver mais do que um Exchange organização no local, em seguida, terá uma solução de GALSync no local para estes grupos que seja apresentado na sua outras florestas.
- A funcionalidade de escrita não consolidada grupo não processa actualmente grupos de segurança ou grupos de distribuição.

>[AZURE.NOTE] Uma subscrição do Azure AD Premium é necessária para o grupo de escrita não consolidada.

## <a name="user-writeback"></a>Repetição de escrita de utilizador
> [AZURE.IMPORTANT] A funcionalidade de pré-visualização de escrita não consolidada do utilizador foi removida na ligação do Azure AD a actualização de Agosto de 2015. Caso a tenha ativado, deve desativar esta funcionalidade.

## <a name="next-steps"></a>Próximos passos
Continue a sua [instalação personalizada de ligação do Azure AD](./connect/active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
