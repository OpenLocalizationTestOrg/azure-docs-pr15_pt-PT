<properties
    pageTitle="Relatório de utilização não licenciados | Microsoft Azure"
    description="A utilização não licenciados relatório ajuda a que identificar os utilizadores não licenciados que estiver a utilizar o pago funcionalidades do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Relatório de utilização não licenciados

A utilização não licenciados relatório ajuda a que identificar os utilizadores não licenciados que estiver a utilizar o pago funcionalidades do Azure AD. Esta opção permite-para melhorar o utiliza de licenças compradas e identificar sabe quando poderá precisar de mais licenças. 

O relatório mostra a utilização ativa das funcionalidades pagas nos últimos 30 dias. 

## <a name="report-structure"></a>Estrutura de relatório
 
| Nome da coluna          |    Descrição |
| :--                  | :--         |
| Utilizadores não licenciados      |    Nome do utilizador |
| Funcionalidade              | O nome de funcionalidade. Por exemplo: acesso condicional |
| Aplicação do acedida | O nome da aplicação que está a ser acedido com a funcionalidade. Por exemplo: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Se tiver sido eliminada uma conta de utilizador na coluna 'Não licenciados utilizador' será preenchida com um ID, como 1003000090D8B285


## <a name="conditional-access-feature"></a>Funcionalidade de acesso condicional

Utilizadores não licenciados serão sinalizados quando acede a um serviço que tem uma política de acesso condicional aplicada se não possui uma licença do Azure AD Premium. 

Isto se aplica a MFA / políticas de localização, bem como dispositivo as políticas que utilizam o Intune.
 

## <a name="see-also"></a>Consulte também

- [Utilizar o Access condicional com o Office 365 e outros Azure Active Directory ligado aplicações](active-directory-conditional-access.md)
- [Introdução ao access condicional para Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 


