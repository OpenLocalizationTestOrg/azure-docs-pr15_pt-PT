<properties
    pageTitle="Melhores práticas: Azure AD palavra-passe gestão | Microsoft Azure"
    description="Práticas recomendadas implementação e a utilização, documentação do utilizador final de exemplo e guias de formação para gestão de palavras-passe no Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Implementar Gestão de palavra-passe e utilizadores de formação para utilizá-la

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Depois de repor a palavra-passe activação, é o próximo passo que precisa de tomar para os utilizadores com o serviço na sua organização. Para fazer isto, terá de para se certificar de que os utilizadores estão configurados para utilizar o serviço corretamente e também que os utilizadores têm a formação precisam de ser efetuada com êxito na gerir os seus próprios palavras-passe. Este artigo irá explicar a si os conceitos seguintes:

* [**Como obter os seus utilizadores configurados para a gestão de palavra-passe**](#how-to-get-users-configured-for-password-reset)
  * [O que faz com que uma conta configurada para a reposição de palavra-passe](#what-makes-an-account-configured)
  * [Formas como pode para preencher dados de autenticação si próprio](#ways-to-populate-authentication-data)
* [**As melhores formas de implementar o palavra-passe repor a sua organização**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Implementação baseada em correio eletrónico + exemplo comunicações de correio eletrónico](#email-based-rollout)
  * [Criar o seu próprio portal de gestão de palavra-passe personalizada para os seus utilizadores](#creating-your-own-password-portal)
  * [Como utilizar o registo imposto forçar os utilizadores para se registar no início de sessão](#using-enforced-registration)
  * [Como carregar dados de autenticação para contas de utilizador](#uploading-data-yourself)
* [**Utilizador de exemplo e materiais de formação de suporte (brevemente!)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Como obter utilizadores configurado para o repor palavra-passe
Esta secção descreve vários métodos ao qual pode assegurar que cada utilizador na sua organização pode utilizar personalizada de palavra-repor eficazmente caso esquecer de respetiva palavra-passe para si.

### <a name="what-makes-an-account-configured"></a>O que faz com que uma conta configurada
Antes de um utilizador pode utilizar a reposição de palavra-passe, **todas** as condições seguintes devem ser cumpridas:

1.  Reposição de palavra-passe deve ser ativada no diretório.  Saiba como ativar a palavra-passe repor lendo [Permitir que os utilizadores para repor a palavra-passe AD Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [Permitir que os utilizadores repor ou alterar as suas palavras-passe de AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.  O utilizador deve estar licenciado.
 - Para utilizadores na nuvem, o utilizador tem de ter ** **qualquer paga licença do Office 365**, ou uma AAD básicos** ou **AAD Premium licença** atribuída.
 - No local para utilizadores do (federado ou hash sincronizado), o utilizador **tem de ter uma licença de AAD Premium atribuída**.
3.  O utilizador tem de ter o **mínimo conjunto de dados de autenticação definidos** em conformidade com a política de reposição de palavra-passe atual.
 - Dados de autenticação são considerados definidas se o campo correspondente no diretório contiver dados correcto.
 - Um conjunto de dados de autenticação mínimo é definido como naquele **, pelo menos, uma** das opções de autenticação activados se uma política de uma porta está configurada ou **, pelo menos, duas** das opções de autenticação activados se uma política de duas porta está configurada.
4.  Se o utilizador estiver a utilizar uma conta no local, em seguida, [Palavra-passe de escrita não consolidada](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) deve ser ativado e ativada

### <a name="ways-to-populate-authentication-data"></a>Formas para preencher dados de autenticação
Tem várias opções sobre como especificar os dados para os utilizadores na sua organização para ser utilizado de reposição de palavra-passe.

- Editar utilizadores no [Portal de gestão do Azure](https://manage.windowsazure.com) ou o [Portal de administração do Office 365](https://portal.microsoftonline.com)
- Utilizar o Azure AD Sync para sincronizar propriedades de utilizador para o Azure AD a partir de um domínio do Active Directory no local
- Utilize o Windows PowerShell para editar as propriedades do utilizador ao [seguir os passos aqui](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Permitir que os utilizadores registar os seus próprios dados por orientá-los ao portal do registo em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Exigir que os utilizadores registe-se para a palavra-passe repor quando iniciar sessão para a respetiva conta Azure AD definindo a [**exigir que os utilizadores registe-se ao iniciar sessão?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) opção de configuração para **Sim**.

Os utilizadores não precisam de se registar repor para o sistema trabalhar de palavra-passe.  Por exemplo, se tiver o telemóvel existente ou números de telefone do office no seu diretório local, pode sincronizá-las no Azure AD e vamos utilizá-los para automaticamente a reposição de palavra-passe.

Também pode obter mais informações sobre [como os dados são utilizados por palavra-passe repor](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) e [como pode preencher campos individuais autenticação com o PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>O que é a melhor forma de implementar o repor para utilizadores de palavra-passe?
Seguem-se os passos de implementação geral para a reposição de palavra-passe:

1.  Ative a palavra-passe Repor no seu diretório ao aceder ao separador **Configurar** no [Portal de gestão do Azure](https://manage.windowsazure.com) e selecionar **Sim** para a opção de **Utilizadores com capacidade para repor a palavra-passe** .
2.  Atribuir licenças adequadas para cada utilizador a quem gostaria oferecer palavra-passe repor na ao aceder ao separador **licenças** no [Portal de gestão do Azure](https://manage.windowsazure.com).
3.  Opcionalmente, restringir a palavra-passe repor para um grupo de utilizadores a filmar saída a funcionalidade lentamente ao longo do tempo ao definir o botão de alternar **Restringir o acesso para repor a palavra-passe** para **Sim** e selecionar um grupo de segurança para ativar a reposição de palavra-passe (tenha em atenção estes utilizadores têm de ter todas as licenças atribuídas às mesmas).
4.  Informe os utilizadores para utilizar a palavra-passe para repor quer enviar-lhes uma mensagem de e-mail com a indicação-los para registar, permitindo-impostas registo de texto ou ao carregá-se os dados de autenticação adequado para esses utilizadores através de do [Portal de gestão do Azure](https://manage.windowsazure.com), PowerShell ou DirSync.  Obter mais detalhes sobre este são fornecidos abaixo.
5.  Ao longo do tempo, reveja os utilizadores se ter registado, navegue para o separador relatórios e visualizar o relatório de [**Atividade de registo de repor a palavra-passe**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) .
6.  Depois de ter registado um bom número de utilizadores, veja os mesmos utilizar palavra-passe para repor navegar para o separador relatórios e visualizar o relatório de [**Atividade de reposição de palavra-passe**](active-directory-passwords-get-insights.md#view-password-reset-activity) .

Existem várias formas para informar os utilizadores que podem registe-se no e utilizar a palavra-passe repor na sua organização.  Encontram detalhadas mais abaixo.

### <a name="email-based-rollout"></a>Implementação baseada em correio eletrónico
Talvez a abordagem mais simples para informar os utilizadores sobre para registar ou utilizar a palavra-passe de reposição de seja enviando-lhes uma mensagem de e-mail com a indicação-los para fazê-lo.  Abaixo encontra um modelo, que pode utilizar para o fazer.  Esteja à vontade substituir as cores / logótipos com as suas próprias escolher personalizá-lo para corresponder às suas necessidades.

  ![][001]

Pode [Transferir o modelo de mensagem de correio electrónico aqui](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Criar o seu próprio portal de palavra-passe
Uma estratégia que funciona bem para clientes maiores implementar as capacidades da gestão de palavra-passe é a criação de um único "portal de palavra-passe" que os utilizadores podem utilizar para gerir todas as coisas relacionados com a palavra-passe num único local.  

Muitos dos nossos clientes maior Selecione para criar uma entrada DNS, de raiz como https://passwords.contoso.com com ligações para a palavra-passe do Azure AD repor portal, portal de registo de reposição de palavra-passe e páginas de alterar palavra-passe.  Desta forma, em qualquer comunicações por e-mail ou fliers que enviar saída, pode incluir um único, memorizar, URL que os utilizadores podem ir para quando tiverem uma segunda para começar a utilizar com o serviço.

Para começar aqui, podemos tiver criado uma página simple que utiliza as mais recente da IU estrutura paradigmas e irá funcionar em todos os browsers e dispositivos móveis.

  ![][007]

Pode [Transferir o modelo de site aqui](https://github.com/kenhoff/password-reset-page).  Recomendamos que personalizar o logótipo e cores às necessidades da sua organização.

### <a name="using-enforced-registration"></a>Utilizar o registo imposto
Se pretender que os utilizadores para se registar no repor próprios de palavra-passe, pode também forçá-los para registar quando iniciar sessão para o painel de acesso no [http://myapps.microsoft.com](http://myapps.microsoft.com).  Pode ativar esta opção a partir do separador de **Configurar** o directório ao ativar a opção **Exigir utilizadores para o registo ao iniciar sessão para o painel de acesso** .  

Opcionalmente, também pode definir a permissão ou não serão pedidas para voltar a registar após um período de tempo configurável modificando a opção de **número de dias antes dos utilizadores têm de confirmar os respetivos dados de contactos** para ser um valor diferente de zero. Para mais informações, consulte [Personalizar o comportamento de gestão de palavra-passe de utilizador](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Depois de ativar esta opção, quando os utilizadores iniciar sessão para o painel de acesso, Verão num pop-up informá-los de que seu administrador necessário-los para verificar as informações de contacto. Podem utilizá-lo para repor a palavra-passe se estas nunca perder acesso à sua conta.

  ![][003]

Clicar em **Verificar agora** dá-los ao **portal de registo de reposição de palavra-passe** na [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) e requê-los para registar.  Registo através deste método pode ser fechado ao clicar no botão **Cancelar** ou fechar a janela, mas os utilizadores estão a receber um lembrete sempre que iniciar sessão se não registar.

  ![][004]

### <a name="uploading-data-yourself"></a>Carregar dados si próprio
Se quiser carregar dados de autenticação si próprio, os utilizadores não precisam de registar para repor antes de poder para repor a palavra-passe de palavra-passe.  Desde que os utilizadores têm os dados de autenticação definidos na respetiva conta que satisfaça a palavra-passe repor política que definiu, em seguida, esses utilizadores poderão para repor a palavra-passe.

Para saber que propriedades que pode definir através do AAD ligar ou o Windows PowerShell, consulte o artigo [dados que são utilizados pelo reposição de palavra-passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Pode carregar os dados de autenticação através de do [Portal de gestão do Azure](https://manage.windowsazure.com) ao seguir os passos abaixo:

1.  Navegue até ao seu diretório na **extensão do Active Directory** no [Portal de gestão do Azure](https://manage.windowsazure.com).
2.  Clique no separador de **utilizadores** .
3.  Selecione o utilizador que se estiver interessado na partir da lista.
4.  No separador primeiro, irá encontrar **E-mail alternativo**, que podem ser utilizados como uma propriedade para activar a reposição de palavra-passe.

    ![][005]

5.  Clique no separador **Informações de trabalho** .
6.  Nesta página, irá encontrar **Telefone do escritório**, **número de telemóvel**, **Telefone de autenticação**e **E-Mail de autenticação**.  Estas propriedades também podem ser definidas para permitir que um utilizador para repor a palavra-passe dele.

    ![][006]

Consulte o artigo [dados que são utilizados pelo reposição de palavra-passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) para ver como cada uma das seguintes propriedades pode ser utilizada.

Consulte o artigo [como aceder a palavra-passe de reposição de dados para os seus utilizadores a partir do PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) para ver como pode ler e configurar estes dados com o PowerShell.

## <a name="sample-training-materials"></a>Materiais de formação de exemplo
Estamos a trabalhar no materiais de formação de exemplo que pode utilizar para aceder a organização de TI e os utilizadores atualizado rapidamente em como implementar e utilizar a reposição de palavra-passe.  Esteja atento!


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Ligações para a palavra-passe repor documentação
Abaixo estão ligações para todas as páginas de documentação de reposição de palavra-passe do Azure AD:

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os seis diferentes componentes do seu serviço e o que faz cada
* [**Introdução ao**](active-directory-passwords-getting-started.md) - Saiba como permitir os utilizadores para repor e alterar a palavra-passe na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar o aspeto e funcionamento e comportamento do serviço para necessidades da sua organização
* [**Obter informações**](active-directory-passwords-get-insights.md) - Saiba mais sobre os nossos integradas capacidades de relatórios
* [**Perguntas mais frequentes**](active-directory-passwords-faq.md) : Obtenha respostas às perguntas mais frequentes
* [**Resolução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente resolver problemas relacionados com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aceda abrangente para os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
