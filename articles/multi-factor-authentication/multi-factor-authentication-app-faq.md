<properties
    pageTitle="Aplicação Microsoft autenticador perguntas mais frequentes"
    description="Fornece uma lista de perguntas mais frequentes e respostas relacionados com a aplicação Microsoft Authentication e Azure a autenticação Multifator."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Perguntas mais frequentes do Microsoft Authenticator aplicação

A aplicação Microsoft Authenticator substituído a aplicação autenticador Azure e a aplicação recomendada quando é utilizada a autenticação Multifator de Azure. Esta aplicação está disponível para Windows Phone, Android e iOS.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

- **O que aconteceu ao Azure autenticador, Multifator Auth e apps de conta Microsoft?**

    A aplicação Microsoft Authenticator substitui os outros estas aplicações. Azure autenticador atualizado para Microsoft Authenticator. Se utilizar a autenticação Multifator ou aplicações de conta Microsoft, instalar Microsoft Authenticator e adicione os seus novamente. Certifique-se terminar de adicionar as suas contas para a nova aplicação antes de eliminar os ficheiros mais antigos.

- **Já estiver a utilizar a aplicação Microsoft Authenticator para códigos de verificação. Como é que mudo notificações push com um clique?**  

    Aprovar uma sessão através de notificações push só está disponível para contas do Microsoft e não para contas de terceiros, como Google ou o Facebook. Para contas Microsoft escolar ou profissional, a sua organização pode escolher desativar esta opção, embora.

    Se utilizar uma conta Microsoft para a sua conta pessoal e pretender mudar para as notificações push, terá de adicionar novamente a sua conta. Registe novamente o dispositivo com a sua conta e configurar notificações push.  

    Se a sua conta não possui verificação de dois passos ativada, consulte o artigo [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para decidir se é ideal para si.  

- **Quando posso poderão utilizar notificações push com um clique no iPhone ou iPad?**  

    Esta funcionalidade está na versão beta até ao fim do Agosto, quando esta se torna disponível para contas do Microsoft. Se quiser participar no nosso programa beta, enviar e-mail msauthenticator@microsoft.com. Inclua o nome próprio, apelido e Apple ID na sua mensagem.  

- **Notificações push com um clique funcionam para contas não Microsoft?**  

    Não, as notificações push apenas funcionam com contas do Microsoft e contas do Azure Active Directory. Se utiliza o seu trabalho ou escola contas do Azure AD, estes podem desativar esta funcionalidade.  

- **Posso restaurado o meu dispositivo a partir de uma cópia de segurança e o meu códigos de conta estão em falta ou não está a funcionar. O que aconteceu?**  

    Por motivos de segurança, podemos não restaurar contas a partir de cópias de segurança de aplicação. Se restaurar a aplicação iOS a partir de uma cópia de segurança, o contas ainda é apresentado mas não funcionam para receber verificações de início de sessão no ou gerar códigos de segurança. Depois de restaurar a aplicação, eliminar as suas contas e adicioná-los novamente.

- **Recebi um novo dispositivo. Como remover a aplicação Microsoft Authenticator do meu dispositivo antigo e deslocar-se para a nova?**

    Adicionar a aplicação Microsoft Authenticator a um novo dispositivo não automaticamente removê-lo a partir de qualquer outros dispositivos. Para gerir os dispositivos que estão configurados para a sua conta, visite o Web site mesmo que utilizar para gerir a verificação de dois passos e optar por remover aplicações antigas.

    Para contas pessoais do Microsoft, este Web site é a sua página de [segurança da conta](https://account.microsoft.com/security) . Para contas Microsoft escolar ou profissional, este Web site pode ser [Asminhasaplicações](https://myapps.microsoft.com) ou um portal de personalizado que a sua organização tenha configurado.

## <a name="contact-us"></a>Contacte-nos

Se a sua pergunta não foi respondida aqui, deixe um comentário na parte inferior da página. Ou, [contacte o suporte](https://support.microsoft.com/contactus) e podemos irá responder para o seu problema, logo podemos.

Se contactar o suporte, inclua quanto as seguintes informações como o pode fazer:

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

- [Perguntas mais frequentes do Azure autenticação Multifator](multi-factor-authentication-faq.md)  
- [Sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para contas Microsoft
- [Tem problemas com a verificação de dois passos?](multi-factor-authentication-end-user-troubleshoot.md)
