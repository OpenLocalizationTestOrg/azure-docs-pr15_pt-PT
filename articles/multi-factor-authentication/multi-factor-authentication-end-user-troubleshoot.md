<properties
    pageTitle="Resolver problemas de verificação de dois passos | Microsoft Azure"
    description="Este documento irá fornecer as utilizadores informações sobre o que fazer se estes ocorrer um problema com a autenticação Multifator do Azure."
    services="multi-factor-authentication"
    keywords = "cliente de autenticação multifactor problema de autenticação, ID de correlação"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>Está a ter problemas com a verificação de dois passos

Este artigo aborda alguns problemas que podem ocorrer com a verificação de dois passos. Se o problema que está a experienciar não for incluído aqui, forneça comentários detalhados na secção comentários para que o podemos melhorar.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Perdi a minha telefone ou ter sido roubado

Existem duas formas para voltar sua conta. A primeira consiste em inicie sessão com o seu número de telefone alternativo autenticação, se configurou um. O segundo é pedir ao seu administrador para limpar as definições.

Se o seu telemóvel foi roubado ou perdido, recomendamos também que tem o seu administrador de repor as palavras-passe de aplicação e desmarque qualquer lembrado dispositivos. Se o seu administrador não-se de que como realizar esta tarefa, reencaminhe-os para este artigo: [Gerir utilizadores e dispositivos](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Utilizar um número de telefone alternativo

Se configurou várias opções de verificação, incluindo um número de telefone secundário ou uma aplicação de autenticador num dispositivo diferente, pode utilizar um dos seguintes procedimentos para iniciar sessão.

Para iniciar sessão utilizando o número de telefone alternativo, siga estes passos:

1. Inicie sessão no como faria normalmente.
2. Quando lhe for pedido para confirmar a sua conta, selecione **utilizar uma hierarquia**.

    ![Inserir função](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Selecione o número de telefone que tem acesso ao.

    ![Telefone alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Depois de está novamente na sua conta, [Gerir as definições](multi-factor-authentication-end-user-manage-settings.md) alterar o seu número de telefone de autenticação.

>[AZURE.IMPORTANT]
>É importante configurar um número de telefone de autenticação secundário. Se o número de telefone principal e para a aplicação móvel no telemóvel mesmo, pode precisa de uma terceira opção se o seu telemóvel é roubado ou.

### <a name="clear-your-settings"></a>Desmarque as definições

Se não tiver configurado um número de telefone de autenticação secundário, terá de contactar o administrador para obter ajuda. Tê-las, desmarque as definições para que da próxima vez que iniciar sessão, será solicitado para [Configurar a sua conta](multi-factor-authentication-end-user-first-time.md) novamente.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Posso não estou a receber um texto ou uma chamada no meu telemóvel

Existem várias razões porque é que pode tentar iniciar sessão, mas não receber o texto ou chamada telefónica. Caso tenha recebido com êxito textos ou chamadas telefónicas com o seu telemóvel no passado, em seguida, este é provavelmente um problema com o fornecedor de telemóvel, não a sua conta. Certifique-se de que possui célula boa sinal e se está a tentar receber uma mensagem de texto certifique-se de que o seu plano de serviço de telefone e suporta mensagens de texto.

Se tiver aguardado alguns minutos para uma chamada ou de texto, a forma mais rápida de obter para a sua conta é experimentar uma opção diferente.

1. Selecione **utilizar uma opção de verificação diferente** na página que está à espera para a sua verificação.

    ![Inserir função](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Selecione o método de número ou de entrega de telefone que pretende utilizar.

    Se tiver recebido vários códigos de verificação, funciona apenas aquela mais recente.

Se não tiver o outro método configurado, contacte o seu administrador e peça-lhes para limpar as definições. Da próxima vez que iniciar sessão, vai ser-lhe configurar a [autenticação multifator](multi-factor-authentication-end-user-first-time.md) novamente.


Se tiver muitas vezes atrasos devido ao sinal de célula incorretas, recomendamos que utilize a [aplicação Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) no seu smartphone. A aplicação pode gerar códigos de segurança aleatório que utiliza para iniciar sessão e estes códigos não exigem início de qualquer ligação de sinal ou internet de célula.


## <a name="app-passwords-are-not-working"></a>As palavras-passe de aplicação não estão a funcionar

Em primeiro lugar, certifique-se de que introduziu a palavra-passe de aplicação corretamente.  Se ainda não está a funcionar, experimente iniciar sessão e [criar uma nova palavra-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).  Se isto não funcionar, contacte o administrador e tivê-los [Eliminar as palavras-passe de aplicação existente](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e, em seguida, pode criar um novo.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontro uma resposta à minha problema.

Se experimentou estes passos de resolução de problemas, mas são continua a ter problemas, contacte o administrador ou a pessoa que configurou a autenticação multifator por si. Poderão ajudá-lo.

Além disso, pode publicar uma pergunta nos [fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [contacte o suporte](https://support.microsoft.com/contactus) e podemos irá responder para o seu problema, logo podemos.

Se contactar o suporte, inclua as seguintes informações:

- **ID de utilizador** – o que é o endereço de e-mail que tentou com sessão?
- **Descrição geral do erro** – que mensagem de erro exata pretendia ver?  Se não tiver nenhuma mensagem de erro, descreva o comportamento inesperado que reparou detalhadamente.
- **Página** – a página que estava a no quando vir o erro (inclua o URL)?
- **Código de erro** - o código de erro específico que está a receber.
- **ID de sessão** - o id da sessão específico que está a receber.
- **ID de correlação** – qual foi o código de id de correlação gerado quando o utilizador viu o erro.
- **Carimbo** – qual foi a uma data e hora viu o erro (incluem o fuso horário)?

Muita desta informação pode ser encontrada na sua página de início de sessão. Quando não verificar o início de sessão no momento, selecione **Ver detalhes**.

![Detalhes do erro de início de sessão](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Incluindo estas informações ajuda-na resolver o problema o mais rapidamente possível.

## <a name="related-topics"></a>Tópicos relacionados
- [Gerir as suas definições para a verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)  
- [Perguntas mais frequentes do Microsoft Authenticator aplicação](multi-factor-authentication-app-faq.md)
