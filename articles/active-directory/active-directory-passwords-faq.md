<properties
    pageTitle="FAQ: Azure AD palavra-passe gestão | Microsoft Azure"
    description="Perguntas mais frequentes (FAQ) sobre a gestão de palavra-passe no Azure AD, incluindo palavra-passe reposta, registo, relatórios e escrita não consolidada do Active Directory no local."
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

# <a name="password-management-frequently-asked-questions"></a>Gestão de palavras-passe perguntas mais frequentes

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Seguem-se algumas perguntas mais frequentes para todos os aspetos relacionados com a gestão de palavra-passe.

Se encontrar si próprio com uma pergunta que não souber a resposta à ou estiver à procura de ajuda com um problema específico que é opostas, pode ler no abaixo para ver se podemos tenha abrangidas-lo já.  Se o podemos não o fez, não se preocupe! Esteja à vontade fazer qualquer pergunta tiver que não é abrangido aqui nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) e iremos novamente para o utilizador assim que podemos.

Estas FAQs são divididos em secções que se seguem:

- [**Perguntas sobre o registo de reposição de palavra-passe**](#password-reset-registration)
- [**Perguntas sobre a reposição de palavra-passe**](#password-reset)
- [**Perguntas sobre relatórios de gestão de palavra-passe**](#password-management-reports)
- [**Perguntas sobre a palavra-passe de escrita não consolidada**](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe
 - **P: os meus utilizadores registar os seus próprios dados de reposição de palavra-passe?**

 > **A:** Sim, desde que a reposição de palavra-passe está ativada e estão licenciados, pode aceder ao portal do registo de reposição de palavra-passe em http://aka.ms/ssprsetup para registar as respetivas informações de autenticação para ser utilizado com reposição de palavra-passe. Os utilizadores também podem registe-se ao aceder ao painel access na http://myapps.microsoft.com, clicando no separador perfil e clicar em Register para repor a palavra-passe opção. Saiba mais sobre como obter os seus utilizadores configurados para o repor ao ler o artigo como obter os utilizadores configuradas para reposição de palavra-passe de palavra-passe.

 - **P: Definir dados de reposição de palavra-passe em nome dos meus utilizadores?**

 > **A:** Sim, pode fazê-lo com DirSync ou PowerShell, ou através do portal do [Portal de gestão do Azure](https://manage.windowsazure.com) ou administrador do Office. Repor a obter mais informações sobre esta funcionalidade na mensagem no blogue privacidade melhorada para Azure AD MFA e números de telefone de reposição de palavra-passe e por leitura Saiba como os dados são utilizados por palavra-passe.

 - **P: sincronizar dados para as questões de segurança a partir no local?**

 > **A:** Não, não for possível hoje, mas recomendamos são considerá-lo.

 - **P: os meus utilizadores registar dados de forma a que outros utilizadores não é possível ver estes dados?**

 > **A:** Sim, quando os utilizadores registar dados utilizando o Portal de registo de reposição de palavra-passe-é guardado para os campos de autenticação privado só estão visíveis por administradores globais e o utilizador próprio. Repor a obter mais informações sobre esta funcionalidade na mensagem no blogue privacidade melhorada para Azure AD MFA e números de telefone de reposição de palavra-passe e por leitura Saiba como os dados são utilizados por palavra-passe.

 - **P: os meus utilizadores possuo sejam registados antes de poderem utilizar reposição de palavra-passe?**

 > **A:** Não, se definir o suficiente informações de autenticação em nome dos utilizadores, os utilizadores não terão que registar. Reposição de palavra-passe irá funcionar perfeitamente desde que se tiver formatado corretamente dados armazenados nos campos adequados no diretório. Saiba que mais sobre por leitura Saiba como os dados são utilizados pelo reposição de palavra-passe.

 - **P: Posso sincronizar ou definir os campos de E-Mail de autenticação ou telefone de autenticação alternativo, telefone de autenticação em nome dos meus utilizadores?**

 > **A:** Não está a ser, mas recomendamos são considerar ativar esta funcionalidade.

 - **P: como é que o portal do registo saber quais as opções para mostrar os meus utilizadores?**

 > **A:** O portal de registo de reposição de palavra-passe apenas mostra as opções que activou para os utilizadores na secção de política de reposição de palavra-passe de utilizador do separador de configurar o directório. Isto significa que se não ativar, diga perguntas de segurança, em seguida, os utilizadores não conseguirão para se registar nessa opção.

 - **P: quando um utilizador considera registado?**

 > **A:** Um utilizador é considerado registado quando tem, pelo menos, partes de N de informações de autenticação definido, onde N é o número de métodos necessária autenticação definidos por si no [Portal de gestão do Azure](https://manage.windowsazure.com). Para saber mais, consulte o artigo personalizar palavra-passe repor a política de utilizador.


## <a name="password-reset"></a>Reposição de palavra-passe

 - **P: quanto tempo devo esperar para receber um e-mail, SMS ou chamada telefónica de reposição de palavra-passe?**

 > **A:** E-mail, as mensagens SMS e chamadas telefónicas deve chegam são enviadas em 1 minuto, com as maiúsculas/minúsculas normal a ser 5-20 segundos. Se não receber a notificação neste período definido, verifique a sua pasta lixo, que o número / e-mail a ser contactado é aquele que espera e que os dados de autenticação no diretório estão formatados corretamente. Para saber mais sobre a formatação de números de telefone e e-mail endereços para utilização com reposição de palavra-passe Consulte a secção saber como os dados são utilizados pelo reposição de palavra-passe.

 - **P: que idiomas são suportados pelo reposição de palavra-passe?**

 > **A:** Repor a palavra-passe a IU, as mensagens SMS e chamadas de voz estão localizadas no mesmo 40 idiomas suportados no Office 365. São apresentados: Árabe, búlgaro, chinês simplificado, chinês tradicional, croata, checo, dinamarquês, neerlandês, inglês, estónio, finlandês, francês, alemão, grego, hebraico, Hindi, húngaro, indonésio, italiano, japonês, cazaque, coreano, letão, lituano, Malaio (Malásia), norueguês (Bokmål), polaco, português (Brasil), português (Portugal), romeno, russo, Sérvio (Latim), eslovaco, esloveno, espanhol, sueco, tailandês, turco, ucraniano e vietnamita.

 - **P: quais as partes da experiência de reposição de palavra-passe obtenham com a imagem corporativa quando defino organizacional no meu diretório de imagem corporativa do configure separador?**

 > **A:** O portal de reposição de palavra-passe irá mostrar o logótipo da sua organização e será também permitem-lhe para configurar o contacto a ligação do seu administrador apontar para um e-mail personalizado ou um URL. As mensagens de e-mail enviadas por reposição de palavra-passe irão incluir o logótipo da sua organização, cores (neste caso vermelhos), o nome no corpo da mensagem de e-mail e personalizada a partir do nome. Ver um exemplo com todos os elementos com marca corporativa abaixo. Para obter mais informações, leia reposição de palavra-passe de personalizar o aspeto e funcionalidade.

  ![][001]

 - **P: como posso informe os meus utilizadores sobre onde ir para repor a palavra-passe?**

 > **A:** Pode enviar os seus utilizadores para https://passwordreset.microsoftonline.com diretamente ou pode indicá-los para clicar no não é possível aceder à sua conta que se encontram no qualquer escola ou ID de trabalho no ecrã Iniciar sessão. Pode vontade publicar estas ligações (ou criar URL redirecionamentos às mesmas) em qualquer local que está acessível facilmente aos seus utilizadores.

 - **P: Posso utilizar a esta página num dispositivo móvel?**

 > **A:** Sim, esta página funciona em dispositivos móveis.

 - **P: que suporta o desbloquear contas do local active directory quando os utilizadores repor a palavra-passe?**

 > **A:** Sim, quando um utilizador repõe da palavra-passe e a palavra-passe de escrita não consolidada foi implementado com todas as versões de ligação do Azure AD ou versões do Azure AD Sync 1.0.0485.0222 ou posterior, em seguida, essa conta de utilizador serão desbloqueada automaticamente quando esse utilizador repõe dele palavra-passe.

 - **P: como integrar o palavra-passe repor diretamente no meu ambiente de trabalho de entrada experiência de utilizador?**

 > **A:** Isto não é possível hoje. No entanto, se realmente precisa esta capacidade e for um cliente do Azure AD Premium, pode instalar o Microsoft identidade Manager sem custos adicionais e implementar a solução de reposição de palavra-passe no local que se encontram para resolver este requisito.

 - **P: Posso definir perguntas de segurança diferente para regiões diferentes?**

 > **A:** Não, não for possível hoje, mas recomendamos são considerá-lo.

 - **P: como muitas questões podemos configurar para a opção de autenticação de perguntas de segurança?**

 > **A:** Pode configurar até 20 perguntas de segurança personalizado no [Portal de gestão do Azure](https://manage.windowsazure.com).

 - **P: quanto tempo poderão perguntas de segurança estar?**

 > **A:** Perguntas de segurança podem ser entre 3 e 200 caracteres.

 - **P: quanto tempo poderão respostas a perguntas de segurança estar?**

 > **A:** Respostas poderá 3 para 40 carateres de comprimento.

 - **P: são duplicadas respostas a perguntas de segurança rejeitadas?**

 > **A:** Sim, podemos rejeitar duplicadas respostas a perguntas de segurança.

 - **P: Maio um utilizador registe-se mais do que um da mesma pergunta de segurança?**

 > **A:** Não, assim que um utilizador regista uma pergunta específica, poderá não registe-se para essa pergunta uma segunda vez.

 - **P: é possível definir um limite mínimo de perguntas de segurança para o registo e repor?**

 > **A:** Sim, um limite pode ser definido para registo e outro para repor. 3-5 perguntas de segurança podem ser necessárias para o registo e 3-5 podem ser necessários para o repor.

 - **P: se um utilizador tem registado mais do que o número máximo de perguntas exigido para repor, como são perguntas de segurança selecionadas durante a repor?**

 > **A:** Perguntas de segurança N são selecionadas aleatoriamente terminar o número total de perguntas que um utilizador foi registado para, onde N é o número mínimo de perguntas necessários para o repor palavra-passe. Por exemplo, se um utilizador tem 5 perguntas de segurança registadas, mas apenas 3 são necessários para repor, 3 desses 5 serão aleatoriamente selecionado e apresentado ao utilizador no momento da repor. Se o utilizador obtém respostas às perguntas mal, o processo de selecção ocorre novamente para impedir que martelar pergunta.

 - **P: pode impedir que utilizadores tentar repor número de vezes durante um período de hora abreviada de palavra-passe?**

 > **A:** Sim, existem várias funcionalidades de segurança incorporadas no reposição de palavra-passe. Os utilizadores podem apenas a experimentar 5 tentativas de reposição de palavra-passe dentro de uma hora antes de a ser bloqueada 24 horas. Os utilizadores só podem experimentar validar um número de telefone 5 horas dentro de uma hora antes de a ser bloqueada 24 horas. Os utilizadores podem apenas a experimentar um método de autenticação única 5 vezes dentro de uma hora antes de a ser bloqueada 24 horas.

 - **P: por quanto tempo são o e-mail e o código de acesso Monouso SMS válidos?**

 > **A:** A duração da sessão de reposição de palavra-passe é 105 minutos. Isto significa que, desde o início da palavra-passe, repor operação, o utilizador tem 105 minutos para repor a palavra-passe dele. O e-mail e o código de acesso Monouso SMS são inválidos depois de expira este período de tempo.


## <a name="password-management-reports"></a>Relatórios de gestão de palavra-passe

 - **P: quanto tempo demora para os dados sejam apresentadas nos relatórios de gestão de palavra-passe?**

 > **A:** Dados deverão aparecer em relatórios de gestão de palavra-passe dentro de 5 a 10 minutos. -Alguns casos, poderá demorar até numa hora seja apresentado.

 - **P: como pode filtrar os relatórios de gestão de palavra-passe?**

 > **A:** Pode filtrar os relatórios de gestão de palavra-passe ao clicar em pequenas na Lupa para a direita representarem das etiquetas de coluna, no topo do relatório (consulte captura de ecrã). Se pretender fazer mais rica filtragem, pode transferir o relatório para criar uma tabela dinâmica e do excel.

  ![][002]

 - **P: qual é o número máximo de eventos estão armazenados nos relatórios de gestão de palavra-passe?**

 > **A:** Até a palavra-passe 1.000 eventos de registo de repor palavra-passe ou repor são armazenados nos relatórios de gestão de palavra-passe.  Estamos a trabalhar para expandir este número para incluir mais eventos.

 - **P: como extremidade novamente os relatórios de gestão de palavra-passe aceda?**

 > **A:** Os relatórios de gestão de palavra-passe mostram operações ocorridas nos últimos 30 dias. Estamos atualmente a investigar como fazer isto um período de tempo mais longo. Por agora, se precisar de arquivar estes dados, pode transferir periodicamente os relatórios e guardá-los numa localização diferente.

 - **P: existe um número máximo de linhas que pode ser apresentada em relatórios de gestão de palavra-passe?**

 > **A:** Sim, um máximo de 1.000 linhas pode aparecer num dos relatórios de gestão de palavra-passe, se estiver a ser apresentadas na IU do ou a serem transferidos. Estamos atualmente a investigar como aumentar este limite.

 - **P: existe uma API para aceder a reposição de palavra-passe ou o registo de dados do relatório?**

 > **A:** Sim, consulte o artigo a seguinte documentação para saber como pode aceder a reposição de palavra-passe elaboração de relatórios de fluxo de dados.  [Saiba como aceder a palavra-passe de reposição de eventos de elaboração de relatórios através de programação](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Repetição de escrita de palavra-passe
 - **P: como é que funciona a palavra-passe de escrita não consolidada nos bastidores?**

 > **A:** Consulte o artigo [que como palavra-passe de escrita não consolidada funciona](active-directory-passwords-learn-more.md#how-password-writeback-works) para obter uma explicação detalhada do que acontece quando ativar a palavra-passe de escrita não consolidada, bem como a forma como os fluxos de dados através do sistema para o seu ambiente no local. Ver [o modelo de segurança de palavra-passe de escrita não consolidada](active-directory-passwords-learn-more.md#password-writeback-security-model) no como palavra-passe de escrita não consolidada funciona para saber como garantimos a palavra-passe de escrita não consolidada é um serviço altamente seguro.

 - **P: como tempo palavra-passe de escrita não consolidada demorar a funcionar?  Existe um atraso de sincronização como com a sincronização de hash de palavra-passe?**

 > **A:** Repetição de escrita de palavra-passe é instantânea. É uma tubagem síncrona que funciona bastante diferente da sincronização de hash de palavra-passe. Palavra-passe escrita não consolidada permite aos utilizadores obter tempo real comentários sobre o sucesso das sua reposição de palavra-passe ou alterar a operação. A hora média de uma bem sucedida de escrita não consolidada de uma palavra-passe está em 500 ms.

 - **P: que tipos de contas funciona palavra-passe de escrita não consolidada para?**

 > **A:** Palavra-passe escrita não consolidada funciona para federados e palavra-passe Hash sincronização com os utilizadores.

 - **P: o palavra-passe escrita não consolidada impor políticas de palavra-passe do meu domínio?**

 > **A:** Sim, palavra-passe de escrita não consolidada impõe idade de palavra-passe, histórico, complexidade, filtros e quaisquer outras restrições que poderá colocar no local em palavras-passe no seu domínio local.

 - **P: é a repetição de escrita de palavra-passe segura?  Como posso ter a certeza de que não irá obter atacado?**

 > **A:** Sim, palavra-passe de escrita não consolidada é extremamente segura. Para obter mais informações acerca de 4 camadas de segurança implementada pelo serviço palavra-passe de escrita não consolidada, consulte o artigo o [modelo de segurança de palavra-passe de escrita não consolidada](active-directory-passwords-learn-more.md#password-writeback-security-model) funciona como repetição de escrita da palavra-passe.




## <a name="links-to-password-reset-documentation"></a>Ligações para a palavra-passe repor documentação
Abaixo estão ligações para todas as páginas de documentação de reposição de palavra-passe do Azure AD:

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os seis diferentes componentes do seu serviço e o que faz cada
* [**Introdução ao**](active-directory-passwords-getting-started.md) - Saiba como permitir os utilizadores para repor e alterar a palavra-passe na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar o aspeto e funcionamento e comportamento do serviço para necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - Saiba como implementar rapidamente e eficazmente gerir palavras-passe na sua organização
* [**Obter informações**](active-directory-passwords-get-insights.md) - Saiba mais sobre os nossos integradas capacidades de relatórios
* [**Resolução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente resolver problemas relacionados com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aceda abrangente para os detalhes técnicos de como funciona o serviço


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
