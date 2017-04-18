
<properties
    pageTitle="Resolução de problemas de associação dinâmica para grupos | Microsoft Azure"
    description="Sugestões de resolução de problemas para associação dinâmica para grupos no Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Associações dinâmicas para grupos de resolução de problemas

**Posso configurado uma regra num grupo, mas não associações atualizadas no grupo**<br/>Certifique-se de que a definição **Ativar delegada gestão de grupos** está definida para **Sim** na caixa de diálogo **Configurar** . Verá esta definição apenas se tiver sessão iniciada no como um utilizador a quem está atribuída uma licença do Azure Active Directory Premium. Verifique se os valores para os atributos de utilizador na regra: existem utilizadores que satisfaçam a regra?

**Posso configurado uma regra, mas agora os membros existentes da regra são removidos**<br/>Este é um comportamento esperado. Existentes membros do grupo são removidos quando uma regra é activada ou alterada. Os utilizadores devolvidos da avaliação da regra são adicionados como membros ao grupo.     

**Não vejo associação altera instantly quando adicionar ou alterar uma regra, por que motivo não?**<br/>Avaliação dedicada a associação é efectuada periodicamente um processo assíncrona de fundo. O processo de quanto tempo demora é determinada pelo número de utilizadores no seu diretório e o tamanho do grupo criado como resultado da regra. Normalmente, directórios com pequenas quantidades dos utilizadores verão as alterações de associação de grupo em menos de alguns minutos. Diretórios com um grande número de utilizadores podem demorar 30 minutos ou mais longa para preencher.

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
