<properties
    pageTitle="Resolução de problemas: Gestão de palavra-passe AD Azure | Microsoft Azure"
    description="Resolução de problemas comuns passos para a gestão de palavra-passe do Azure AD, incluindo repor, alterar, repetição de escrita, registo, e que informações para incluir quando à procura de ajuda."
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
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Como resolver problemas de gestão de palavra-passe

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Se estiver a ter problemas com a gestão de palavra-passe, estamos aqui para ajudar. A maioria dos problemas que pode ocorrer pode ser resolvido com alguns passos de resolução de problemas simples que pode ler sobre abaixo para resolver a sua implementação:

* [**Informações para incluir quando precisar de ajuda**](#information-to-include-when-you-need-help)
* [**Problemas com a configuração da gestão de palavra-passe no Portal de gestão do Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemas com a palavra-passe cmdlet relatórios no Portal de gestão do Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemas com a palavra-passe repor Portal de registo**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemas com a palavra-passe repor Portal**](#troubleshoot-the-password-reset-portal)
* [**Problemas com a palavra-passe de escrita não consolidada**](#troubleshoot-password-writeback)
  - [Códigos de erro de registo de eventos de repetição de escrita de palavra-passe](#password-writeback-event-log-error-codes)
  - [Problemas com a conectividade de palavra-passe de escrita não consolidada](#troubleshoot-password-writeback-connectivity)

Se experimentou os passos de resolução de problemas abaixo e continua a ter problemas, pode publicar uma pergunta nos [fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contacte o suporte e podemos irá veja o problema de assim podemos.

## <a name="information-to-include-when-you-need-help"></a>Informações para incluir quando precisar de ajuda

Se não conseguir resolver o problema com as informações abaixo, pode contactar o nossos engenheiros de suporte. Quando contactá-las, é recomendável para incluir as seguintes informações:

 - **Descrição geral do erro** – que mensagem de erro exata fez o utilizador Consulte?  Se não tiver nenhuma mensagem de erro, descreva o comportamento inesperado notadas detalhadamente.
 - **Página** – a página que estava a no quando vir o erro (inclua o URL)?
 - **De data / hora / fuso horário** – qual foi a uma data e hora viu o erro (incluem o fuso horário)?
 - **Código de suporte** – qual foi o código de suporte gerado quando o utilizador viu o erro (para localizar esta, reproduza o erro, em seguida, clique na ligação de suporte código na parte inferior do ecrã e enviar o suporte técnico o GUID que resulta).
   - Se estiver a uma página sem um código de suporte na parte inferior, prima a tecla F12 e pesquisa de SID e CID e enviar essas dois resultados para o suporte técnico.

    ![][001]

 - **ID de utilizador** – qual foi o ID do utilizador ao qual viu (por exemplo, o errouser@contoso.com)?
 - **Informações sobre o utilizador** – foi o utilizador federado, hash de palavra-passe sincronizou, apenas a nuvem?  O utilizador tem uma licença AAD Premium ou do AAD básicas atribuída?
 - **Registo de eventos aplicação** – se estiver a utilizar a palavra-passe de escrita não consolidada e o erro está na sua infraestrutura no local, consulte zip para cima uma cópia do seu registo de eventos de aplicações a partir do seu servidor de ligação do Azure AD e enviar juntamente com o seu pedido.

Incluindo estas informações ajudarão a resolver o problema o mais rapidamente possível.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Resolver problemas de configuração de reposição de palavra-passe no Portal de gestão do Azure
Se se deparar com um erro quando configurar de palavra-passe, poderá conseguir resolvê-lo ao seguir os passos de resolução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Maiúsculas/minúsculas do erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um utilizador ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não consigo ver a secção de <strong>Política de reposição de palavra-passe de utilizador </strong>no separador <strong>Configurar</strong> no portal de gestão do Azure</p>
            </td>
            <td>
              <p>A secção de <strong>Política de reposição de palavra-passe de utilizador </strong>não estiver visível no separador <strong>Configurar</strong> no Portal de gestão do Azure.</p>
            </td>
            <td>
              <p>Isto pode ocorrer se não tem uma licença AAD Premium ou do AAD básicas atribuída ao administrador efetuar esta operação. </p>
              <p>Para corrigir isto, atribuir uma licença AAD Premium ou AAD básicas da conta de administrador em questão ao navegar para o separador <strong>licenças</strong> e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não consigo ver qualquer uma das opções de configuração na secção de <strong>Política de reposição de palavra-passe de utilizador</strong> que são descritas na documentação.</p>
            </td>
            <td>
              <p>A secção de <strong>Política de reposição de palavra-passe de utilizador </strong>está visível, mas o único sinalizador que é apresentado sob ele é o sinalizador de <strong>Utilizadores com capacidade para repor a palavra-passe</strong> .</p>
            </td>
            <td>
              <p>O resto da IU aparecerá quando muda o sinalizador de <strong>Utilizadores com capacidade para repor a palavra-passe</strong> para <strong>Sim.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo uma opção de configuração específico.</p>
            </td>
            <td>
              <p>Por exemplo, não vejo a opção de <strong>número de dias antes de um utilizador tem de confirmar os respetivos dados de contactos</strong> quando posso percorrer a secção de <strong>Política de reposição de palavra-passe de utilizador</strong> (ou outros exemplos do mesmo problema).</p>
            </td>
            <td>
              <p>Número de elementos da IU está ocultos até forem necessárias. Tente ativar todas as opções na página se pretende ver.</p>
              <p>Para obter mais informações sobre todos os controlos que estão disponíveis para si, consulte o <a href="active-directory-passwords-customize.md#password-management-behavior">comportamento de gestão de palavra-passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não consigo ver a opção de configuração de <strong>Escrever novamente as palavras-passe no local</strong></p>
            </td>
            <td>
              <p>A opção de <strong>Escrever novamente as palavras-passe para o local</strong> não estiver visível no separador <strong>Configurar</strong> no Portal de gestão do Azure</p>
            </td>
            <td>
              <p>Esta opção está visível apenas se tiver transferido a ligação do Azure AD e configurado a palavra-passe de escrita não consolidada. Quando tiver concluído isto, essa opção aparece e permite-lhe ativar ou desativar a partir da nuvem de escrita não consolidada.</p>
              <p>Consulte o artigo <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Ativar a palavra-passe de escrita não consolidada na ligação do Azure AD</a> para obter mais informações sobre como fazer isto.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Resolver problemas de relatórios de gestão de palavra-passe no Portal de gestão do Azure
Se se deparar com um erro ao utilizar os relatórios de gestão de palavra-passe, poderá conseguir resolvê-lo ao seguir os passos de resolução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Maiúsculas/minúsculas do erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um utilizador ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não consigo ver qualquer relatórios de gestão de palavra-passe</p>
            </td>
            <td>
              <p>Os relatórios de <strong>atividade de reposição de palavra-passe</strong> e <strong>Repor a palavra-passe actividade de registo</strong> não estão visíveis nos relatórios de <strong>Registo de atividade</strong> no separador <strong>relatórios</strong> .</p>
            </td>
            <td>
              <p>Isto pode ocorrer se não tem uma licença AAD Premium ou do AAD básicas atribuída ao administrador efetuar esta operação. </p>
              <p>Para corrigir isto, atribuir uma licença AAD Premium ou AAD básicas da conta de administrador em questão ao navegar para o separador <strong>licenças</strong> e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Registos de utilizador mostram várias vezes</p>
            </td>
            <td>
              <p>Quando um utilizador regista e-mail alternativo, número de telemóvel e perguntas de segurança, cada aparecem como linhas separadas em vez de uma única linha.</p>
            </td>
            <td>
              <p>Atualmente, quando um utilizador regista, podemos não é possível partem do princípio de que irá registam tudo presentes na página de registo. Como resultado, podemos atualmente inicie sessão cada porção individual de dados que estão registados como um evento em separado.</p>
              <p>Se pretender agregar estes dados, pode transferir o relatório e abrir os dados como uma tabela dinâmica no excel para ter mais flexibilidade.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Resolução de problemas do portal de registo de reposição de palavra-passe
Se se deparar com um erro quando um utilizador para reposição de palavra-passe a registar, poderá conseguir resolvê-lo ao seguir os passos de resolução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Maiúsculas/minúsculas do erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um utilizador ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório não está ativado para a reposição de palavra-passe</p>
            </td>
            <td>
              <p>O administrador não tenha ativado para utilizar esta funcionalidade.</p>
            </td>
            <td>
              <p>Mude o sinalizador de <strong>Utilizadores com capacidade para repor a palavra-passe</strong> para <strong>Sim</strong> e visitas <strong>Guardar</strong> no separador de configuração do diretório Portal de gestão do Azure. Tem de ter um ou básicas licença atribuída ao administrador efetuar esta operação do Azure AD Premium.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O utilizador não tem uma licença AAD Premium ou do AAD básicas atribuída</p>
            </td>
            <td>
              <p>O administrador não tenha ativado para utilizar esta funcionalidade.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium ou do Azure AD básicas para o utilizador no separador <strong>licenças</strong> no Portal de gestão do Azure. Tem de ter um ou básicas licença atribuída ao administrador efetuar esta operação do Azure AD Premium.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Pedido de processamento de erros</p>
            </td>
            <td>
              <p>Utilizador vê um erro que diz:</p>
              <p>

              </p>
              <p>Pedido de processamento de erros </p>
              <p>Ao tentar repor uma palavra-passe.</p>
            </td>
            <td>
              <p>Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por um de um serviço configuração ou falha do problema que não pode ser resolvido. </p>
              <p>Se vir este erro e o é que afetam a sua empresa, contacte o suporte e podemos irão ajudá-lo OMCP. Ver <a href="#information-to-include-when-you-need-help">informações de incluir quando precisar de ajuda</a> para ver o que deve fornecer para a engenharia de suporte para ajudar a uma solução rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Resolução de problemas do portal de reposição de palavra-passe
Se se deparar com um erro quando repor uma palavra-passe para um utilizador, poderá conseguir resolvê-lo ao seguir os passos de resolução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Maiúsculas/minúsculas do erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um utilizador ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório não está ativado para a reposição de palavra-passe</p>
            </td>
            <td>
              <p>A conta não esteja ativada para a reposição de palavra-passe</p>
              <p>Pedimos desculpa, mas o seu administrador não tiver configurado a sua conta para utilizar com este serviço. </p>
              <p>

              </p>
              <p>Se quiser, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si.</p>
            </td>
            <td>
              <p>Mude o sinalizador de <strong>Utilizadores com capacidade para repor a palavra-passe</strong> para <strong>Sim</strong> e visitas <strong>Guardar</strong> no separador de configuração do diretório Portal de gestão do Azure. Tem de ter um ou básicas licença atribuída ao administrador efetuar esta operação do Azure AD Premium.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O utilizador não tem uma licença AAD Premium ou do AAD básicas atribuída</p>
            </td>
            <td>
              <p>Enquanto não podemos repor palavras-passe de conta que não sejam administradores automaticamente, podemos pode contactar o administrador da sua organização para fazê-lo.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium ou do Azure AD básicas para o utilizador no separador <strong>licenças</strong> no Portal de gestão do Azure. Tem de ter um ou básicas licença atribuída ao administrador efetuar esta operação do Azure AD Premium.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório estiver ativado para a reposição de palavra-passe, mas o utilizador tem informações de autenticação em falta ou incorrecto</p>
            </td>
            <td>
              <p>A conta não esteja ativada para a reposição de palavra-passe</p>
              <p>Pedimos desculpa, mas o seu administrador não tiver configurado a sua conta para utilizar com este serviço. </p>
              <p>

              </p>
              <p>Se quiser, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si.</p>
            </td>
            <td>
              <p>Certifique-se de que o utilizador tem correctamente formadas dados de contacto no ficheiro no diretório antes de continuar. Consulte o artigo <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">dados que são utilizados pelo reposição de palavra-passe</a> para obter informações sobre como configurar informações de autenticação no diretório, para que os utilizadores não vir este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório estiver ativado para a reposição de palavra-passe, mas um utilizador tem apenas uma parte dos dados de contacto no ficheiro quando a política estiver definida para exigir o registo dois passos de verificação</p>
            </td>
            <td>
              <p>A conta não esteja ativada para a reposição de palavra-passe</p>
              <p>Pedimos desculpa, mas o seu administrador não tiver configurado a sua conta para utilizar com este serviço. </p>
              <p>

              </p>
              <p>Se quiser, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si.</p>
            </td>
            <td>
              <p>Certifique-se de que o utilizador tem, pelo menos, dois métodos de contactos adequadamente configurados (por exemplo, o número de telemóvel e o telefone do escritório) antes de continuar. Consulte o artigo <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">dados que são utilizados pelo reposição de palavra-passe</a> para obter informações sobre como configurar informações de autenticação no diretório, para que os utilizadores não vir este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório está ativado para repor a palavra-passe e o utilizador está configurado corretamente, mas não é possível ser contactado utilizador </p>
            </td>
            <td>
              <p>!  Vamos encontrou um erro inesperado ao contactá-lo.</p>
            </td>
            <td>
              <p>Isto pode ser o resultado de um erro de serviço temporário ou mal configurado dados de contactos que recomendamos não foi possível detectar corretamente. Se o utilizador aguarda 10 segundos, um experimentar novamente e é apresentada a ligação "Contacte o administrador". Clicar novamente em experimentar será novamente enviar trabalhos da chamada, Considerando que clicar em "Contacte o administrador" irá enviar um e-mail de formulário para o utilizador, palavra-passe ou administradores globais (por essa ordem de precedência) pedir uma palavra-passe repor a ser executado para essa conta de utilizador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilizador nunca recebe a reposição de palavra-passe SMS ou chamada telefónica</p>
            </td>
            <td>
              <p>Utilizador clica em "texto-me" ou "me ligue" e, em seguida, recebe nunca nada.</p>
            </td>
            <td>
              <p>Isto pode dever o resultado de um número de telefone incorrecto no diretório. Certifique-se o número de telefone está no formato "+ NIB xxxyyyzzzzXeeee". Para saber mais sobre a formatação de telefone números para utilização com reposição de palavra-passe Consulte o artigo <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">dados que são utilizados pelo reposição de palavra-passe</a>.</p>
              <p>Se necessitar de uma extensão a ser encaminhado para o utilizador em questão, tenha em atenção que reposição de palavra-passe não suporta extensões, mesmo se especificar um no diretório (estes são removidos antes da chamada é distribuída). Experimente utilizar um número sem uma extensão ou integrar a extensão o número de telefone PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O utilizador recebe nunca e-mail de reposição de palavra-passe</p>
            </td>
            <td>
              <p>Utilizador clica em "enviar por correio eletrónico mim" e, em seguida, recebe nunca nada.</p>
            </td>
            <td>
              <p>A causa mais comuns para este problema é que a mensagem é rejeitada pelo filtro de spam. Selecione o spam, a pasta correio publicitário não solicitado ou eliminado itens para o e-mail.</p>
              <p>Também Certifique-se de que está a dar o e-mail da direita para a mensagem … muitas pessoas têm endereços de e-mail muito semelhantes e terminar o verificar a caixa de entrada errada para a mensagem. Se nenhuma destas opções funcionar, também é possível que o endereço de e-mail no diretório está mal, verifique Certifique-se que o endereço de e-mail está a correta e tente novamente. Para saber mais sobre a formatação de correio eletrónico endereços para utilização com reposição de palavra-passe Consulte o artigo <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">dados que são utilizados pelo reposição de palavra-passe</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Posso ter definido uma política de reposição de palavra-passe, mas quando utiliza uma conta de administrador a reposição de palavra-passe, essa política não é aplicada</p>
            </td>
            <td>
              <p>Contas de administrador a reposição de palavra-passe, consulte as mesmas opções ativadas para a reposição de palavra-passe, o e-mail e o número de telemóvel, independentemente do que política estiver definida na secção de <strong>Política de reposição de palavra-passe de utilizador</strong> do separador de <strong>Configurar</strong> .</p>
            </td>
            <td>
              <p>As opções configuradas na secção de <strong>Política de reposição de palavra-passe de utilizador</strong> do separador <strong>Configurar</strong> só se aplicam aos utilizadores finais na sua organização.</p>
              <p>A Microsoft gere e controlos a palavra-passe de administrador repor a política para assegurar o mais alto nível de segurança</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Impedido de tentar repor demasiadas vezes num dia de palavra-passe de utilizador</p>
            </td>
            <td>
              <p>Utilizador vê um erro indicando:</p>
              <p>

              </p>
              <p>Utilize outra opção.</p>
              <p>Já Tentei confirmar a sua conta demasiadas vezes na última 1 hora (s). Por motivos de segurança, terá de esperar 24 hora (s) antes de tentar novamente. </p>
              <p>Se quiser, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si.</p>
            </td>
            <td>
              <p>Vamos implementar um dispositivo optimização automático para bloquear utilizadores a partir de tentar repor a palavra-passe demasiadas vezes num breve período de tempo. Isto ocorre quando:</p>
              <ol class="ordered">
                <li>
Utilizador tenta validar um número de telefone 5 vezes numa hora.<br\><br\></li>
                <li>
Utilizador tenta utilizar a porta de perguntas de segurança 5 vezes numa hora.<br\><br\></li>
                <li>
Utilizador tenta repor uma palavra-passe para a mesma conta de utilizador 5 vezes numa hora.<br\><br\></li>
              </ol>
              <p>Para corrigir isto, indique ao utilizador de esperar 24 horas após a última tentativa, e o utilizador, em seguida, poderão para repor a palavra-passe dele.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilizador vê um erro ao validar o número de telefone dele</p>
            </td>
            <td>
              <p>Ao tentar Certifique-se de um telefone para utilizar como um método de autenticação, o utilizador vê um erro indicando:</p>
              <p>

              </p>
              <p>Número de telefone incorretos especificado.</p>
            </td>
            <td>
              <p>Este erro ocorre quando o número de telefone introduzido não corresponder o número de telefone no ficheiro.</p>
              <p>Certifique-se de que o utilizador está a introduzir o número de telefone completo, incluindo o código de área e de país/região, ao tentar utilizar um método baseado em telefone para reposição de palavra-passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Pedido de processamento de erros</p>
            </td>
            <td>
              <p>Utilizador vê um erro que diz:</p>
              <p>

              </p>
              <p>Pedido de processamento de erros </p>
              <p>Ao tentar repor uma palavra-passe.</p>
            </td>
            <td>
              <p>Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por um de um serviço configuração ou falha do problema que não pode ser resolvido. </p>
              <p>Se vir este erro e o é que afetam a sua empresa, contacte o suporte e podemos irão ajudá-lo OMCP. Ver <a href="#information-to-include-when-you-need-help">informações de incluir quando precisar de ajuda</a> para ver o que deve fornecer para a engenharia de suporte para ajudar a uma solução rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Resolver problemas de palavra-passe de escrita não consolidada
Se se deparar com um erro ao ativar, desativar ou utilizar a palavra-passe de escrita não consolidada, poderá conseguir resolvê-lo ao seguir os passos de resolução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Maiúsculas/minúsculas do erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um utilizador ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falhas de ativação gerais e de arranque</p>
            </td>
            <td>
              <p>Serviço de reposição de palavra-passe não é iniciado no local com o erro 6800 com o Microsoft no registo de eventos de aplicações a máquina de ligação do Azure AD.</p>
              <p>

              </p>
              <p>Após a ativação, federado ou hash de palavra-passe utilizadores sincronizados não podem repor a palavra-passe.</p>
            </td>
            <td>
              <p>Quando a palavra-passe de escrita não consolidada é ativada, o motor de sincronização irá efetuar a chamada a biblioteca de escrita não consolidada para executar a configuração (ativação) através de conversa com o serviço de ativação na nuvem. Os erros encontrados durante a ativação ou ao iniciar o ponto final WCF para a palavra-passe de escrita não consolidada irão resultar em erros no registo de eventos no registo de eventos do seu computador de ligação do Azure AD.</p>
              <p>Durante o reinício do serviço de ADSync, se tiver sido configurada repetição de escrita, o ponto final WCF será iniciado para cima. No entanto, se o arranque do ponto final falhar, iremos simplesmente registo de evento 6800 com o Microsoft e permitir que o arranque do serviço de sincronização. Estado de presença deste evento, significa que a palavra-passe a repetição de escrita não foi iniciado ponto final para cima. Geram detalhes de registo de eventos para este evento (6800 com o Microsoft) juntamente com entradas de registo de eventos por PasswordResetService componente irá indicar por que motivo não foi possível iniciar o ponto final para cima. Reveja estes erros de registo de eventos e tente novamente iniciar o ligação do Azure AD Se a palavra-passe de escrita não consolidada ainda não está a funcionar. Se o problema persistir, tente desativar e ativar novamente a palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Quando um utilizador tenta repor uma palavra-passe ou desbloquear uma conta com palavra-passe de escrita não consolidada ativada, a operação falha. Além disso, verá um evento na ligação do Azure AD registo de eventos que contém: "motor de sincronização devolvido um erro hr = 800700CE, mensagem = o nome do ficheiro ou extensão é demasiado longa" depois da operação de desbloquear ocorre.
                            </p>
            </td>
            <td>
              <p>Isto pode ocorrer se tinha actualizado a partir de versões mais antigas do ligação do Azure AD ou DirSync. Atualizar para versões mais antigas do ligação do Azure AD definir uma palavra-passe de 254 caráter para a conta do agente de gestão do Azure AD (versões mais recentes irão definir uma palavra-passe do 127 caráter comprimento). Trabalhar essas longas palavras-passe para operações de conector de AD importação e exportação mas não são suportadas pela operação de desbloquear.
                            </p>
            </td>
            <td>
              <p>[Localizar a conta do Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) para a ligação do Azure AD e repor a palavra-passe para conter mais do que 127 caracteres. Em seguida, abra o **Serviço de sincronização** a partir do menu Iniciar. Navegue para os **conectores** e localize o **Conector do Active Directory**. Selecione-o e clique em **Propriedades**. Navegue para a página **credenciais** e introduza a palavra-passe nova. Selecione **OK** para fechar a página.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Erro ao configurar a repetição de escrita durante a instalação de ligação do Azure AD.</p>
            </td>
            <td>
              <p>No último passo do processo de instalação de ligação do Azure AD, verá um erro que indica que não pôde ser configurada palavra-passe de escrita não consolidada.</p>
              <p>

              </p>
              <p>Registo de eventos do Azure AD ligar aplicação contém erro 32009 com texto "Erro introdução auth token".</p>
            </td>
            <td>
              <p>Este erro ocorre nos dois casos seguintes:</p>
              <ul>
                <li class="unordered">
Que especificou uma palavra-passe incorretas para a conta de administrador global especificada no início do processo de instalação de ligação do Azure AD.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Ter tentado utilizar um utilizador federado para a conta de administrador global especificada no início do processo de instalação de ligação do Azure AD.<br\><br\></li>
              </ul>
              <p>Para corrigir este erro, certifique-se de que não são utilizando uma conta federada de administrador global que especificou no início do processo de instalação de ligação do Azure AD e que a palavra-passe especificada está correta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar a repetição de escrita durante a instalação de ligação do Azure AD.</p>
            </td>
            <td>
              <p>O registo de eventos de máquina de ligação do Azure AD contém erro 32002 iniciadas pelo PasswordResetService.</p>
              <p>

              </p>
              <p>Lê o erro: "erro estabelecer ligação ao ServiceBus, o fornecedor de tokens conseguiu fornecer um token de segurança..."</p>
              <p>

              </p>
            </td>
            <td>
              <p>A causa de raiz deste erro é que o serviço de reposição de palavra-passe em execução no seu ambiente no local não é possível ligar a ponto final bus do serviço na nuvem. Este erro é causado, normalmente normalmente por uma regra de firewall a bloquear uma ligação de saída para um determinado porta ou endereço web.</p>
              <p>

              </p>
              <p>Certifique-se da que Firewall do permite ligações de saída para as seguintes opções:</p>
              <ul>
                <li class="unordered">
Todo o tráfego através de TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Ligações de saída para <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Depois de ter atualizado estas regras, reiniciar o computador de ligação do Azure AD e palavra-passe de escrita não consolidada deverá começar a trabalhar novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Palavra-passe de escrita não consolidada ponto final no local não acessível</p>
            </td>
            <td>
              <p>Após trabalhar para algumas tempo, federado ou hash de palavra-passe utilizadores sincronizados não podem repor a palavra-passe.</p>
            </td>
            <td>
              <p>Em alguns casos raros, o serviço de palavra-passe de escrita não consolidada poderá falhar começar novamente quando voltar ter começado a ligação do Azure AD. Nestes casos, primeiro, verifique se o palavra-passe de escrita não consolidada parece estar prem. activado Isto pode ser feito utilizando o Assistente de ligação do Azure AD ou powershell (consulte o artigo HowTos secção acima). Se a funcionalidade parece estar ativada, tente ativar ou desativar a funcionalidade novamente quer através da IU ou PowerShell. Consulte o artigo "passo 2: Ativar repetição de escrita de palavra-passe no seu computador de sincronização de diretórios &amp; configurar regras de firewall" no artigo <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">como ativar/desativar repetição de escrita de palavra-passe</a> para obter mais informações sobre como fazer isto.</p>
              <p>

              </p>
              <p>Se isso não funcionar, experimente desinstalar e voltar a instalar ligação do Azure AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erros de permissões</p>
            </td>
            <td>
              <p>Federado ou sincronização hash de palavra-passe com os utilizadores que tentarem para repor a sua Consulte palavras-passe um erro depois de submeter a palavra-passe que indica que ocorreu um problema de serviço.</p>
              <p>

              </p>
              <p>Para além disso, durante as operações de reposição de palavra-passe, poderá ver que um erro em relação à agente management acesso negado no seu registos de eventos no local.</p>
            </td>
            <td>
              <p>Se vir estes erros no registo de eventos, confirme se o AD MA (que foi especificada no Assistente de no momento da configuração) possui as permissões necessárias para a palavra-passe de escrita não consolidada.</p>
              <p>

              </p>
              <p>NOTE que assim que esta permissão é dado pode demorar até 1 hora para as permissões para são recebidas lentamente para baixo por tarefa em segundo plano sdprop sobre o Centro de dados. </p>
              <p>Palavra-passe repor para trabalhar a permissão tem de ser marcado no descritor de segurança do objeto do utilizador cuja palavra-passe está a ser repor. Até esta permissão aparecer no objeto do utilizador, reposição de palavra-passe irão continuar a falhar com acesso negado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar a palavra-passe de escrita não consolidada a partir do Assistente de configuração de ligação do Azure AD </p>
            </td>
            <td>
              <p>Ocorreu um erro "Não é possível localizar MA" no assistente ao ativar/desativar a palavra-passe de escrita não consolidada</p>
            </td>
            <td>
              <p>Existe um erro conhecido na versão de lançamento do ligação do Azure AD que manifestos na seguinte situação:</p>
              <ol class="ordered">
                <li>
Configurar ligação do Azure AD para inquilino abc.com (verificado o domínio) utilizando credenciais. Isto resulta numa conexão AAD com o nome "abc.com – AAD" a ser criado.<br\><br\></li>
                <li>
Em seguida, em seguida, alterar as credenciais de AAD para o conector (com a sincronização do antiga IU) para (tenha em atenção é ao mesmo inquilino mas o nome de domínio diferente). <br\><br\></li>
                <li>
Agora tenta Ativar/desativar a palavra-passe de escrita não consolidada. O assistente irá construir o nome de conexão utilizando credenciais, como "abc.onmicrosoft.com – AAD" e passar para o cmdlet de palavra-passe de escrita não consolidada. Isto irá falhar porque não existe nenhum conector criada com este nome.<br\><br\></li>
              </ol>
              <p>Este erro foi corrigido no nossas compilações mais recentes. Se tiver uma compilação mais antiga, é uma solução utilizar o cmdlet do powershell para ativar/desativar a funcionalidade. Consulte o artigo "passo 2: Ativar repetição de escrita de palavra-passe no seu computador de sincronização de diretórios &amp; configurar regras de firewall" no artigo <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">como ativar/desativar repetição de escrita de palavra-passe</a> para obter mais informações sobre como fazer isto.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não é possível para repor a palavra-passe para os utilizadores em grupos especiais como Domain Admins / Enterprise administradores etc.</p>
            </td>
            <td>
              <p>Federado ou sincronização hash de palavra-passe com utilizadores que fazem parte de grupos protegidos e tentarem repor a sua Consulte palavras-passe um erro depois de submeter a palavra-passe que indica que ocorreu um problema de serviço.</p>
            </td>
            <td>
              <p>Utilizadores privilegiados no Active Directory estão protegidos, utilizando AdminSDHolder. Consulte o artigo <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> para obter mais detalhes. </p>
              <p>

              </p>
              <p>Isto significa descritores de segurança nestes objetos são verificadas periodicamente para corresponder a um AdminSDHolder especificado no e são repostos se forem diferentes. As permissões adicionais que são necessários para a palavra-passe de escrita não consolidada, por conseguinte, não são recebidas lentamente para esses utilizadores. Isto pode resultar em palavra-passe de escrita não consolidada não está a funcionar esses utilizadores. Como resultado, não suportamos gerir palavras-passe para os utilizadores estes grupos dentro de uma vez que as quebras de modelo de segurança do AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Repor operações falhar com objeto não foi possível encontrar</p>
            </td>
            <td>
              <p>Federado ou sincronização hash de palavra-passe com os utilizadores que tentarem para repor a sua Consulte palavras-passe um erro depois de submeter a palavra-passe que indica que ocorreu um problema de serviço.</p>
              <p>

              </p>
              <p>Para além disso, durante as operações de reposição de palavra-passe, poderá ver um erro no seu registos dos eventos do serviço de ligação do Azure AD indicar um erro "Não foi possível encontrar objeto".</p>
            </td>
            <td>
              <p>Este erro normalmente indica que o motor de sincronização é não é possível localizar o objeto de utilizador no espaço de conexão AAD ou o MV ligada ou o objeto de espaço do AD conexão. </p>
              <p>

              </p>
              <p>Para resolver este problema, certifique-se de que o utilizador é verdade sincronizado a partir no local para AAD através da instância atual do ligação do Azure AD e verificar se o estado dos objetos na espaços de conexão e MV. Confirme se o objeto de AD CS conexão para o objeto MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Repor operações falhar com várias correspondências encontrados erro</p>
            </td>
            <td>
              <p>Federado ou sincronização hash de palavra-passe com os utilizadores que tentarem para repor a sua Consulte palavras-passe um erro depois de submeter a palavra-passe que indica que ocorreu um problema de serviço.</p>
              <p>

              </p>
              <p>Para além disso, durante as operações de reposição de palavra-passe, poderá ver um erro no seu registos dos eventos do serviço de ligação do Azure AD indicar um erro de "Várias maches encontrados".</p>
            </td>
            <td>
              <p>Isto indica que o motor de sincronização detectado que o objeto MV está ligados à mais do que um AD CS objetos através de "Microsoft.InfromADUserAccountEnabled.xxx". Isto significa que o utilizador tem uma conta activada floresta mais do que uma. </p>
              <p>

              </p>
              <p>Atualmente, este cenário não é suportado para palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Operações de palavra-passe falharem com um erro de configuração.</p>
            </td>
            <td>
              <p>Operações de palavra-passe falharem com um erro de configuração. O registo de eventos de aplicação contém o erro de ligação do Azure AD 6329 com texto: 0x8023061f (a operação falhou porque a sincronização de palavra-passe não está ativada neste agente de gestão.)</p>
            </td>
            <td>
              <p>Isto ocorre se a configuração de ligação do Azure AD é alterada para adicionar&nbsp;numa nova floresta do AD (ou para remover e adicionar novamente floresta existente) <strong>depois da</strong> funcionalidade de palavra-passe de escrita não consolidada já foi ativada. Operações de palavra-passe para os utilizadores dessas florestas recentemente adicionados irão falhar. Para corrigir o problema, desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada depois de tem sido concluídas as alterações à configuração floresta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Voltar a escrever as palavras-passe foi repostas pelo works utilizadores corretamente, mas voltar a escrever palavras-passe alterados por um utilizador ou falhas de repor para um utilizador por um administrador.</p>
            </td>
            <td>
              <p>Ao tentar repor uma palavra-passe em nome de um utilizador a partir do Portal de gestão do Azure, verá uma mensagem a informar: "o serviço de reposição de palavra-passe em execução no seu ambiente no local não suporta os administradores repor palavras-passe de utilizador. Actualize para a versão mais recente da ligação do Azure AD para resolver este problema."</p>
            </td>
            <td>
              <p>Isto ocorre quando a versão do motor de sincronização não suporta a operação de palavra-passe de escrita não consolidada específica que foi utilizada. Versões de ligação do Azure AD mais tarde do que 1.0.0419.0911 suporta todas as operações de gestão de palavra-passe, incluindo a palavra-passe repor repetição de escrita, escrita não consolidada alterar palavra-passe e repetição de escrita de reposição de palavra-passe iniciado pelo administrador a partir do Portal de gestão do Azure.&nbsp; Versões de DirSync mais tarde ao 1.0.6862 suportavam a palavra-passe repor de escrita não consolidada apenas. Para resolver este problema, recomendamos vivamente que instale a versão mais recente de ligação do Azure AD ou ligar Azure Active Directory (para obter mais informações, consulte o artigo <a href="active-directory-aadconnect">Ferramentas de integração de diretórios</a>) para resolver este problema e para tirar o máximo partido da palavra-passe de escrita não consolidada na sua organização.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de registo de eventos de repetição de escrita de palavra-passe
Prática recomendada quando resolução de problemas com palavra-passe de escrita não consolidada é inspecionar esse registo de eventos de aplicações no seu computador de ligação do Azure AD. Este registo de eventos irá conter eventos de duas origens de interesse para a palavra-passe de escrita não consolidada. A origem de PasswordResetService será descrevem problemas relacionados com a operação de palavra-passe de escrita não consolidada e operações. A origem de ADSync irá descrevem operações e problemas relacionados com a definição de palavras-passe no seu ambiente de AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Código</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Atribuir um nome / da mensagem</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Origem</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descrição</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>SISTEMA: MMS(4924) 0x80230619 – "uma restrição impede que a palavra-passe sendo alterada para daquele atual especificado."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento ocorre quando o serviço de palavra-passe de escrita não consolidada tenta definir uma palavra-passe no seu diretório local que não cumprir a idade de palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
Se tiver uma idade mínima palavra-passe e tiver alterado recentemente a palavra-passe dentro essa janela de tempo, não conseguir alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para fins de teste, deve ser definida idade mínima 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver ativados requisitos de histórico da palavra-passe, em seguida, tem de selecionar uma palavra-passe que não tenha sido utilizada nos últimos tempos de N, em que N é a definição de histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido utilizada no últimas horas de N, em seguida, verá uma falha de neste caso. Para fins de teste, histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver requisitos de complexidade de palavra-passe, todos eles serão impostos quando o utilizador tenta alterar ou repor uma palavra-passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver filtros de palavra-passe ativados, e um utilizador seleciona uma palavra-passe que não correspondam a critérios de filtragem, em seguida, repor ou alterar a operação irá falhar.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Motor de sincronização devolvido um erro hr = 80230402, mensagem = uma tentativa de obter um objeto falhou porque existem entradas duplicadas com a mesma âncora</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento ocorre quando o mesmo id de utilizador é activado em múltiplos domínios.  Por exemplo, se estiver a sincronizar florestas conta/recurso e têm o mesmo id de utilizador presente e activado em cada, este erro pode ocorrer.  </p>
              <p>Este erro também pode ocorrer se estiver a utilizar um atributo âncora não exclusivos (como alias ou UPN) e dois utilizadores partilharem esse mesmo atributo âncora.</p>
              <p>Para resolver este problema, certifique-se de que não tem nenhum dos utilizadores duplicado dentro de domínios do seu e que está a utilizar um atributo âncora exclusivo para cada utilizador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço no local detectado pedido para um federados de reposição de uma palavra-passe ou sincronização hash de palavra-passe com o utilizador com origem a partir da nuvem. Este evento é que o primeiro evento em cada palavra-passe repor a operação de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que um utilizador selecionado uma nova palavra-passe durante uma operação de reposição de palavra-passe, podemos determinado que esta palavra-passe cumpre os requisitos de palavra-passe da empresa, e essa palavra-passe foi com êxito escrito novamente para o ambiente de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que um utilizador selecionada uma palavra-passe e que a palavra-passe chegou com êxito para o ambiente no local, mas quando podemos tentou definir a palavra-passe no ambiente de AD local, Ocorreu uma falha. Isto pode acontecer por diversas razões:</p>
              <ul>
                <li class="unordered">
Palavra-passe de utilizador não cumprir a idade, o histórico, a complexidade ou filtrar requisitos para o domínio. Experimente uma completamente nova palavra-passe para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço do Friso do Excel não possui as permissões adequadas para configurar a nova palavra-passe da conta de utilizador em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de utilizador é num grupo protegida, tal como administradores de domínio ou de empresa, que não permite a dos operações de conjunto de palavra-passe.<br\><br\></li>
              </ul>
              <p>Consulte o artigo <a href="#troubleshoot-password-writeback">Resolver problemas de repetição de escrita de palavra-passe</a> para saber mais sobre outras situtions que pode fazer com que este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento ocorre se ativar a palavra-passe de escrita não consolidada com ligação do Azure AD e indica que iniciado podemos ativação de organização para o serviço web de palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica o processo de ativação foi efetuada com êxito e que está pronta para utilizar a funcionalidade de palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço no local detetado um pedido de alteração de palavra-passe para um federados ou sincronização hash de palavra-passe com o utilizador com origem a partir da nuvem. Este evento é o primeiro evento no cada operação de repetição de escrita de alteração de palavra-passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que um utilizador selecionado uma nova palavra-passe durante uma operação de alteração de palavra-passe, podemos determinado que esta palavra-passe cumpre os requisitos de palavra-passe da empresa, e essa palavra-passe foi com êxito escrito novamente para o ambiente de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que um utilizador selecionada uma palavra-passe e que a palavra-passe chegou com êxito para o ambiente no local, mas quando podemos tentou definir a palavra-passe no ambiente de AD local, Ocorreu uma falha. Isto pode acontecer por diversas razões:</p>
              <ul>
                <li class="unordered">
Palavra-passe de utilizador não cumprir a idade, o histórico, a complexidade ou filtrar requisitos para o domínio. Experimente uma completamente nova palavra-passe para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço do Friso do Excel não possui as permissões adequadas para configurar a nova palavra-passe da conta de utilizador em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de utilizador é num grupo protegida, tal como administradores de domínio ou de empresa, que não permite a dos operações de conjunto de palavra-passe.<br\><br\></li>
              </ul>
              <p>Consulte o artigo <a href="#troubleshoot-password-writeback">Resolver problemas de repetição de escrita de palavra-passe</a> para saber mais sobre as outras situações que podem causar este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O serviço no local detetado pedido para um federados de reposição de uma palavra-passe ou sincronização hash de palavra-passe com o utilizador com origem do administrador em nome de um utilizador. Este evento é o primeiro evento no cada operação de repetição de escrita de reposição de palavra-passe iniciado pelo administrador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador selecionada uma nova palavra-passe durante uma operação de reposição de palavra-passe iniciados por admin, podemos determinado que esta palavra-passe cumpre os requisitos de palavra-passe da empresa, e essa palavra-passe foi com êxito escrito novamente para o ambiente de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador seleccionado uma palavra-passe em nome de um utilizador e palavra-passe chegou com êxito para o ambiente no local, mas quando podemos tentou definir a palavra-passe no ambiente de AD local, Ocorreu uma falha. Isto pode acontecer por diversas razões:</p>
              <ul>
                <li class="unordered">
Palavra-passe de utilizador não cumprir a idade, o histórico, a complexidade ou filtrar requisitos para o domínio. Experimente uma completamente nova palavra-passe para resolver este problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço do Friso do Excel não possui as permissões adequadas para configurar a nova palavra-passe da conta de utilizador em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de utilizador é num grupo protegida, tal como administradores de domínio ou de empresa, que não permite a dos operações de conjunto de palavra-passe.<br\><br\></li>
              </ul>
              <p>Consulte o artigo <a href="#troubleshoot-password-writeback">Resolver problemas de repetição de escrita de palavra-passe</a> para saber mais sobre outras situtions que pode fazer com que este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento ocorre se desativar a palavra-passe de escrita não consolidada com ligação do Azure AD e indica que iniciado podemos offboarding de organização para o serviço web de palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica o processo de offboarding foi efetuada com êxito e que capacidade de palavra-passe de escrita não consolidada ter sido desactivada com êxito.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o processo de offboarding não foi bem sucedido. Isto pode ser devido a um erro de permissão na nuvem ou no local conta de administrador especificado durante a configuração, ou porque está a tentar utilizar um administrador global nuvem Federados quando desativar a palavra-passe de escrita não consolidada. Para corrigir, verifique o administrativo permissões e que não está a utilizar qualquer federado conta ao configurar a funcionalidade de palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço de palavra-passe de escrita não consolidada foi iniciado com êxito e está pronto para aceitar pedidos de gestão de palavra-passe a partir da nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço de palavra-passe de escrita não consolidada parou e que quaisquer pedidos de gestão de palavra-passe a partir da nuvem não será bem sucedidos.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica podemos obtidos com êxito um token de autorização para o administrador global especificado durante a configuração de ligação do Azure AD para poder iniciar o processo de offboarding ou a ativação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que criámos com êxito a chave de encriptação de palavra-passe que será utilizada para encriptar as palavras-passe a partir da nuvem sejam enviadas para o seu ambiente no local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica um erro desconhecido durante uma operação de gestão de palavra-passe. Veja o texto de exceção no evento para obter mais detalhes. Se estiver a ter problemas, experimente desativar e ativar novamente a palavra-passe de escrita não consolidada. Se não ajudar, incluir uma cópia do seu registo de eventos juntamente com o id de controlo especificado insider para sua engenharia de suporte.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica Ocorreu um erro ao ligar para a palavra-passe de nuvem repor serviço e geralmente ocorre quando o serviço no local foi não é possível ligar ao serviço web de reposição de palavra-passe. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica Ocorreu um erro ao ligar para a instância do seu inquilino serviço bus. Isto pode acontecer uma vez que estejam a impedir as ligações de saída no seu ambiente no local. Verificar a sua firewall para se certificar de que permite ligações ao longo do TCP 443 e <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e tente novamente. Se ainda estiver a ter problemas, experimente desativar e ativar novamente a palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que a entrada transmitida ao nosso API do serviço web foi inválida. Tente novamente a operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que ocorreu um erro desencriptar a palavra-passe que chegaram a partir da nuvem. Isto pode ser devido a um erro de correspondência chave de desencriptação entre o serviço de nuvem e o seu ambiente no local. Para resolver esta questão, desativar e voltar a ativar repetição de escrita de palavra-passe no seu ambiente no local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a ativação, vamos guardar informações específicas do inquilino num ficheiro de configuração no seu ambiente no local. Este evento indica Ocorreu um erro ao guardar o ficheiro ou que quando o serviço foi iniciado aí foi um erro ao ler o ficheiro. Para corrigir este problema, experimente voltar activar e desactivar palavra-passe de escrita não consolidada para forçar uma novamente escrita deste ficheiro de configuração. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – este evento não está presente na ligação do Azure AD, apenas muito cedo constrói do DirSync que suportado de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a ativação, iremos enviar dados a partir da nuvem para a palavra-passe no local repor serviço. Os dados, em seguida, são escritos para um ficheiro na memória antes de a ser enviada para o serviço de sincronização para armazenar estas informações de forma segura no disco. Este evento indica um problema com a escrever ou atualizar os dados na memória. Para corrigir este problema, experimente voltar activar e desactivar palavra-passe de escrita não consolidada para forçar uma novamente escrita desta configuração.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que podemos recebeu uma resposta inválida a partir do serviço web Repor palavra-passe. Para corrigir este problema, experimente voltar activar e desactivar de palavra-passe de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que recomendamos não foi possível obter uma autorização token para a conta de administrador global especificada durante a configuração de ligação do Azure AD. Este erro pode ser causado por um nome de utilizador incorretas ou palavra-passe especificada para a conta de administrador global ou de uma vez que a conta de administrador global especificado esteja federado. Para corrigir este problema, execute novamente a configuração com o nome de utilizador correta e a palavra-passe e certifique-se de que o administrador é um geridos (apenas na nuvem ou seria de sincronização de palavra-passe) conta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica Ocorreu um erro quando a palavra-passe a gerar chave de encriptação ou desencriptar uma palavra-passe que chega a partir do serviço em nuvem. Este erro provável que indica um problema com o seu ambiente. Veja os detalhes do seu registo de eventos para saber mais e resolver este problema. Também poderá tentar desativar e ativar novamente o serviço de palavra-passe de escrita não consolidada para resolver este problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço no local não conseguiu corretamente comunicar com o serviço web de reposição de palavra-passe para iniciar o processo de ativação. Isto pode ser devido a uma regra de firewall ou obter um token de auth do seu inquilino um problema. Para corrigir este problema, certifique-se de que não são bloquear as ligações de saída ao longo do TCP 443 e TCP 9350 9354 ou para <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e que não é Federado com a conta de administrador AAD estiver a utilizar para incorporada. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – este evento não está presente na ligação do Azure AD, apenas muito cedo constrói do DirSync que suportado de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço no local não conseguiu corretamente comunicar com o serviço web de reposição de palavra-passe para iniciar o processo de offboarding. Isto pode ser devido a uma regra de firewall ou um problema ao obter um token de autorização para o seu inquilino. Para corrigir, certifique-se de que não são bloquear as ligações de saída ao longo do 443 ou para <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e que a conta de administrador AAD está a utilizar para offboard não é Federado com. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica tivemos que repetir para ligar a instância do seu inquilino serviço bus. Em condições normais, isto deve não ser uma questão, mas se vir este evento muitas vezes, considere a verificar a sua ligação de rede para bus de serviço, especialmente se for uma latência alta ou uma ligação de largura de banda baixa.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Para poder monitorizar o estado de funcionamento do seu serviço de palavra-passe de escrita não consolidada, iremos enviar dados heartbeat a nossa palavra-passe de reposição de serviço web em 5 minutos. Este evento indica que ocorreu um erro quando enviar estas informações de estado de funcionamento novamente para o serviço web na nuvem. Estas informações de estado de funcionamento não inclui um OII ou PII de dados e são puramente um heartbeat e estatísticas básicas de serviço para que o podemos fornecer informações de estado do serviço na nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que ocorreu um erro desconhecido devolvido pelo AD, verifique o registo de eventos do servidor de ligação do Azure AD para eventos da origem ADSync para obter mais informações sobre este erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o utilizador que está a tentar repor ou alterar uma palavra-passe não foi encontrado no diretório no local. Isto pode ocorrer quando o utilizador foi eliminado no local, mas não na nuvem, ou se existir um problema com a sincronização. Verifique os registos de sincronização, bem como a última sincronização alguns executar detalhes para obter mais informações.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quando repor uma palavra-passe ou pedido de alteração com origem a partir da nuvem, utilizamos a âncora da nuvem especificada durante o processo de configuração de ligação do Azure AD para determinar como deve criar uma hiperligação que pedido voltar a um utilizador no seu ambiente no local. Este evento indica que encontrámos dois utilizadores no seu diretório no local com o mesmo atributo âncora da nuvem. Verifique os registos de sincronização, bem como a última sincronização alguns executar detalhes para obter mais informações.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que a conta de serviço de gestão agente não tem as permissões apropriadas na conta em questão para definir uma palavra-passe nova. Certifique-se de que a conta do Friso do Excel no floresta o utilizador tem repor e alterar permissões de palavra-passe em todos os objectos na floresta.  Para obter mais informações sobre como fazer para este, consulte o artigo <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">passo 4: configurar as permissões adequadas do Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que recomendamos tentou repor ou alterar uma palavra-passe para uma conta que foi desactivada no local. Ativar a conta e tente novamente a operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Evento indica que recomendamos tentou repor ou alterar uma palavra-passe para uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um utilizador tenha tentado uma alteração ou repor a operação de palavra-passe demasiadas vezes durante um período curto. Desbloquear a conta e tente novamente a operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o utilizador especificado uma palavra-passe atual incorreta quando a operação de alteração de efetuar uma palavra-passe. Especifique a palavra-passe correta atual e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento ocorre quando o serviço de palavra-passe de escrita não consolidada tenta definir uma palavra-passe no seu diretório local que não cumprir a idade de palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
Se tiver uma idade mínima palavra-passe e tiver alterado recentemente a palavra-passe dentro essa janela de tempo, não conseguir alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para fins de teste, deve ser definida idade mínima 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver ativados requisitos de histórico da palavra-passe, em seguida, tem de selecionar uma palavra-passe que não tenha sido utilizada nos últimos tempos de N, em que N é a definição de histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido utilizada no últimas horas de N, em seguida, verá uma falha de neste caso. Para fins de teste, histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver requisitos de complexidade de palavra-passe, todos eles serão impostos quando o utilizador tenta alterar ou repor uma palavra-passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se tiver filtros de palavra-passe ativados, e um utilizador seleciona uma palavra-passe que não correspondam a critérios de filtragem, em seguida, repor ou alterar a operação irá falhar.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica Ocorreu uma gravação de problema uma palavra-passe de volta para o seu diretório no local devido a um problema de configuração com o Active Directory. Verifique o registo de eventos de aplicações a máquina de ligação do Azure AD para mensagens de serviço ADSync para mais informações sobre o erro. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – este evento não está presente na ligação do Azure AD, apenas muito cedo constrói do DirSync que suportado de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – este evento não está presente na ligação do Azure AD, apenas muito cedo constrói do DirSync que suportado de escrita não consolidada.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – este evento não está presente na ligação do Azure AD, apenas muito cedo constrói do DirSync que suportado de escrita não consolidada.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Resolver problemas de conectividade de palavra-passe de escrita não consolidada

Se ocorrerem interrupções serviço com o componente de palavra-passe de escrita não consolidada de ligação do Azure AD, eis alguns passos rápidos que pode tomar para resolver este problema:

 - [Reiniciar o Azure AD ligar o serviço de sincronização](#restart-the-azure-AD-Connect-sync-service)
 - [Desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada](#disable-and-re-enable-the-password-writeback-feature)
 - [Instalar a versão mais recente de ligação do Azure AD](#install-the-latest-azure-ad-connect-release)
 - [Resolver problemas de palavra-passe de escrita não consolidada](#troubleshoot-password-writeback)

Em geral, recomendamos que executar estes passos pela ordem acima para recuperar o seu serviço da forma mais rápidos.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o Azure AD ligar o serviço de sincronização
Reiniciar o serviço de sincronização de ligar-se de AD Azure pode ajudar a resolver problemas de conectividade ou outros problemas temporários com o serviço.

 1. Como administrador, clique em **Iniciar** no servidor a executar a **ligação do Azure AD**.
 2. Escreva **"Services. msc"** na caixa de pesquisa e prima **Enter**.
 3. Procure a entrada **do Microsoft Azure AD Connect** .
 4. Botão direito do rato sobre a entrada de serviço, clique em **reiniciar**e aguarde que a operação concluir.

    ![][002]

Estes passos irão restabelecer a ligação com o serviço de nuvem e resolver quaisquer interrupções que poderá estar com.  Se reiniciar o serviço de sincronização não resolver o problema, recomendamos que tenta desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada como um passo seguinte.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada
Desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada podem ajudar a resolver problemas de conectividade.

 1. Como administrador, abra o **Assistente de configuração de ligação do Azure AD**.
 2. Na caixa de diálogo **ligar-se ao Azure AD** , introduza as suas **credenciais de administrador global do Azure AD**
 3. Na caixa de diálogo **ligar ao AD DS** , introduza as suas **credenciais de administrador de serviços de domínio do AD**.
 4. Na caixa de diálogo **identifica os seus utilizadores** , clique no botão **seguinte** .
 5. Na caixa de diálogo **funcionalidades opcionais** , desmarque a caixa de verificação de **palavra-passe de escrita de retorno** .

    ![][003]

 6. Clique em **seguinte** através das páginas de caixa de diálogo restante sem alterar nada até chegar à página **pronto para configurar** .
 7. Certifique-se de que a página de configurar mostra a **opção de escrita anterior da palavra-passe como desativada** e, em seguida, clique no botão de **Configurar** verde para consolidar as alterações.
 8. Na caixa de diálogo **terminada** , desmarque a opção **Sincronizar agora** e, em seguida, clique em **Concluir** para fechar o assistente.
 9. Voltar a abrir o **Assistente de configuração de ligação do Azure AD**.
 10.    **Repita os passos 2-8**, exceto se certificar de que **Selecione a opção de escrita anterior da palavra-passe** sobre as **funcionalidades opcionais do** ecrã para voltar a ativar o serviço.

    ![][004]

Estes passos irão restabelecer a ligação com os nossos serviço na nuvem e resolver quaisquer interrupções que poderá estar com.

Se desativar e voltar a ativar a funcionalidade de palavra-passe de escrita não consolidada não resolver o problema, recomendamos que tentar voltar a instalar ligação do Azure AD como um passo seguinte.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente de ligação do Azure AD
Voltar a instalar o pacote de ligação do Azure AD resolver quaisquer problemas de configuração que podem ser afetar a sua capacidade para quer ligar para os nossos serviços em nuvem ou para gerir palavras-passe no seu ambiente de AD local.
Recomendamos que, execute este passo apenas depois de tentar os dois primeiros passos descritos acima.

 1. Transferir a versão mais recente da ligação do Azure AD [aqui](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Uma vez que já tiver instalado a ligação do Azure AD, apenas terá de efetuar uma atualização no local para atualizar a sua instalação de ligação do Azure AD para a versão mais recente.
 3. Executar o pacote transferido e siga no ecrã instruções para atualizar o seu computador de ligação do Azure AD.  Sem passos manuais adicionais são necessários, a menos que tiver personalizado o fora do regras de sincronização de caixa, caso em que deve **estes fazer cópia de segurança antes de prosseguir com atualizar e manualmente voltar a implementá-los quando tiver terminado**.

Estes passos irão restabelecer a ligação com os nossos serviço na nuvem e resolver quaisquer interrupções que poderá estar com.

Se instalar a versão mais recente do servidor de ligação do Azure AD não resolver o problema, recomendamos que tente novamente activar e desactivar de palavra-passe de escrita não consolidada como um passo final depois de instalar a sincronizar mais recente QFE.

Se isso não resolver o problema, em seguida, recomendamos que consulte o artigo [Resolver problemas de repetição de escrita de palavra-passe](#troubleshoot-password-writeback) e [palavra-passe de Azure AD gestão FAQ](active-directory-passwords-faq.md) para ver se o problema poderá ser abordado aí.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Ligações para a palavra-passe repor documentação
Abaixo estão ligações para todas as páginas de documentação de reposição de palavra-passe do Azure AD:

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os seis diferentes componentes do seu serviço e o que faz cada
* [**Introdução ao**](active-directory-passwords-getting-started.md) - Saiba como permitir os utilizadores para repor e alterar a palavra-passe na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar o aspeto e funcionamento e comportamento do serviço para necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - Saiba como implementar rapidamente e eficazmente gerir palavras-passe na sua organização
* [**Obter informações**](active-directory-passwords-get-insights.md) - Saiba mais sobre os nossos integradas capacidades de relatórios
* [**Perguntas mais frequentes**](active-directory-passwords-faq.md) : Obtenha respostas às perguntas mais frequentes
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aceda abrangente para os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
