<properties
    pageTitle="Ligação do Azure AD na Alemanha Microsoft Cloud"
    description="Ligação do Azure AD integrar o seu directórios no local com o Azure Active Directory. Esta opção permite-lhe fornecer uma identidade comum para aplicações do Office 365, Azure e SaaS integradas com o Azure AD."
    keywords="introdução a ligação do Azure AD, descrição geral da ligação do Azure AD, o que é a ligação do Azure AD, instale o active directory, Alemanha, preto floresta"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Ligação do Azure AD no Microsoft Cloud Alemanha - pré-visualização do público

## <a name="introduction"></a>Introdução
Ligação do Azure AD fornece sincronização entre o Active Directory no local e o Azure Active Directory.
Atualmente, muitos dos cenários na [Alemanha Microsoft Cloud](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) devem ser efetuados pelo operador. Quando utiliza o Microsoft Cloud Alemanha, tem de estar em atenção o seguinte:


- Os URLs seguintes têm de ser abertos num servidor proxy para a sincronização ocorrer com êxito:
    - *. microsoftonline.de
    - *. windows.net
    - + Listas de revogação de certificados

- Quando iniciar sessão no seu diretório do Azure AD, tem de utilizar uma conta de no domínio de onmicrosoft.de.
- As seguintes funcionalidades não estão disponíveis:
    - Estado de funcionamento de ligação do Azure AD
    - Atualizações automáticas
    - Repetição de escrita de palavra-passe

## <a name="download"></a>Transferir
Pode transferir a ligação do Azure AD partir pá a ligação do Azure AD no interior do portal.  Utilize as instruções abaixo para localizar a ligação do Azure AD pá.

### <a name="the-azure-ad-connect-blade"></a>O Azure AD Connect pá

Assim que tiver iniciado sessão no portal do Azure, faça o seguinte:

1. Aceda ao procurar
2.  Selecione o Azure Active Directory
3.  Em seguida, selecione a ligação do Azure AD

Deverá visualizar o seguinte procedimento:

![Ligação do Azure AD pá](media\active-directory-aadconnect-germany\germany1.png)

 
A tabela seguinte descreve as funcionalidades apresentadas na pá.


Título|Descrição|
----- | ----- |
ESTADO DE SINCRONIZAÇÃO|Vamos sabe se a sincronização está ativada ou desactivada.|
ÚLTIMA SINCRONIZAÇÃO|A última vez que uma sincronização bem sucedida concluída.|
DOMÍNIOS FEDERADOS.|Mostra o número de domínios federados atualmente configuradas.|


## <a name="installation"></a>Instalação
Para instalar a ligação do Azure AD, pode utilizar a documentação [aqui](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Funcionalidades avançadas e informações adicionais
Para obter informações adicionais e orientações sobre as definições personalizadas ou configurações avançadas, comece com [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).  Esta página fornece informações e hiperligações para orientações adicionais.
