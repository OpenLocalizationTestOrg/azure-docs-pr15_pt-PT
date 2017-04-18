<properties
    pageTitle="Atualização do DirSync e Azure AD Sync | Microsoft Azure"
    description="Descreve como atualizar a partir do DirSync e Azure AD Sync para ligação do Azure AD."
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


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Atualizar a sincronização do Windows Azure Active Directory ("DirSync") e sincronização do Azure Active Directory ("Azure AD Sync")
Ligação do Azure AD é a melhor forma para ligar o seu diretório no local com o Azure AD e no Office 365. Este é um tempo excelente atualizar a ligação do Azure AD a partir do Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync, tal como estas ferramentas são agora preteridas e irão chegar fim de suporte no 13 de Abril de 2017.

As ferramentas de sincronização de duas identidade são preteridas foram-lhe fornecidas para clientes única floresta (DirSync) e para floresta múltipla e outras avançadas clientes (Azure AD Sync). Estas ferramentas mais antigas foram substituídas numa única solução que está disponível para todos os cenários: ligação do Azure AD. Oferece novas funcionalidades, funcionalidade melhoramentos e suporte para novos cenários. Para poder continuar a sincronizar os dados de identidades no local para o Azure AD e Office 365, é fortemente recomendado que atualizar para a ligação do Azure AD.

A última versão do DirSync foi disponibilizada em Julho de 2014 e a última versão do Azure AD Sync foi lançada em Maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é a ligação do Azure AD
Ligação do Azure AD é a tarefa sucessora DirSync e Azure AD Sync. Combina todos os cenários estes dois suportados. Pode ler mais acerca do mesmo no [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Agenda de descontinuação

Data | Comentário
 --- | ---
13 de Abril de 2016 | Windows Azure Active Directory Sync ("DirSync") e Microsoft Azure Active Directory Sync ("Azure AD Sync") são anunciada como preterida.
13 de Abril de 2017 | Suporte termina. Os clientes já não poderão abrir um incidente de suporte sem atualizar para ligação do Azure AD pela primeira vez.

## <a name="how-to-transition-to-azure-ad-connect"></a>Como a transição para a ligação do Azure AD
Se estiver a executar o DirSync, existem duas formas, pode atualizar: atualização em paralelo implementação no local. Uma atualização no local é recomendada para a maioria dos clientes e se tiver um recentes operativo sistema e menor do que 50.000 objetos. Em outros casos recomenda-se para efetuar uma implementação paralela onde a sua configuração DirSync é movida para um novo servidor a executar a ligação do Azure AD.

Se utiliza o Azure AD Sync é recomendada uma atualização no local. Se pretender, é possível instalar um novo servidor de ligação do Azure AD em paralelo e efetue uma migração de rotação a partir do seu servidor do Azure AD Sync para ligação do Azure AD.

Solução | Cenário
----- | -----
[Actualizar a partir do DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se tiver um servidor do DirSync existente já a ser executado.</li>
[Actualizar a partir do Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md)| <li>Se está a mover a partir do Azure AD Sync.</li>

Se pretender ver como executar uma atualização no local a partir do DirSync a ligação do Azure AD, em seguida, consulte o artigo este vídeo 9 de canal:

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
**P: Posso recebeu uma notificação de e-mail de equipa do Azure e/ou uma mensagem a partir do Centro de mensagens do Office 365, mas estiver a utilizar o ligar.**  
A notificação também foi enviada para os clientes que utilizam a ligação do Azure AD com um número de compilação 1.0. \*.0 (utilizando uma versão 1.1 do tipo de ficheiro). A Microsoft recomenda clientes para se manter atual com ligação do Azure AD lançamentos. Com o 1.1 que a funcionalidade de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) irão tornar realmente fácil ter uma versão recente do ligação do Azure AD sempre instalado.

**P: será DirSync/Azure AD Sync deixarão de funcionar em 13 de Abril de 2017?**  
Não. A data para quando estes já não poderão comunicar com o Azure AD vai ser anunciada numa data posterior. Será capaz de localizar essas informações neste tópico quando se encontra disponível.

**P: quais as versões DirSync pode atualizar a partir do**  
É suportada para atualizar a partir de qualquer versão do DirSync atualmente a ser utilizado.

**P: como o conector de AD Azure para o FIM/MIM?**  
O conector do Azure AD para o FIM/MIM **não** foi anunciada como preterida. É, a **funcionalidade fixar**; sem a funcionalidade nova é adicionada e recebe sem correções de erros. A Microsoft recomenda clientes utilizá-lo para planear mover a partir dos mesmos para ligação do Azure AD. Recomendamos vivamente para não iniciar qualquer novas implementações utilizá-lo. Este conector será anunciado preterido no futuro.

## <a name="additional-resources"></a>Recursos adicionais

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
