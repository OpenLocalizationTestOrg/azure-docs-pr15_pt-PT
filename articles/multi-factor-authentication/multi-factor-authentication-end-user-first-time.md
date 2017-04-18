<properties
    pageTitle="Configurar a verificação de dois passos para a minha conta escolar ou profissional"
    description="Quando a sua empresa configura Azure a autenticação Multifator, vai ser-lhe para se inscrever para verificação de dois passos. Saiba como configurá-lo. "
    services="multi-factor-authentication"
    keywords="como utilizar o azure diretório, do active directory na nuvem, tutorial do active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurar a minha conta para verificação de dois passos

Verificação de dois passos está num passo de segurança adicional que o ajuda a proteger a sua conta, tornando mais difícil para outras pessoas para inserir uma quebra. Se está a ler este artigo, provavelmente recebeu uma mensagem de e-mail do seu administrador escolar ou profissional sobre a autenticação Multifator. Ou talvez Tentei iniciar sessão e tem uma mensagem a perguntar para configurar a verificação de segurança adicional. Se for esse o caso, **que não conseguir iniciar sessão até concluir o processo de inscrição automática**.

Este artigo ajuda-o a configurar o seu **trabalho ou conta escolar**. Se pretende ativar a verificação de dois passos para o seu próprio, conta Microsoft pessoal, consulte o artigo [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Determinar a forma como irá utilizar a autenticação multifator

Verificação de dois passos () funciona por solicitar para dois blocos de identificação quando iniciar sessão. Em primeiro lugar, solicitamos-lhe para o seu nome de utilizador e palavra-passe como costuma fazer. Em seguida, contacto pertence um telefone que recomendamos saber para si e confirmamos que a tentativa de início de sessão no foi legítima.  

Para começar a trabalhar com o processo de configuração, tente iniciar sessão na sua conta tal como faria normalmente. Se o administrador tiver configurado a sua conta da verificação de dois passos, ser-lhe-á pedido para iniciar o processo de inscrição automática. Iniciar este processo ao clicar em **configurá-lo agora.**

![Programa de configuração](./media/multi-factor-authentication-end-user-first-time/first.png)

A primeira pergunta no processo de inscrição é como pretende que configuremos automaticamente contactá-lo. Veja as opções da tabela e utilize as ligações para aceder aos passos de configuração para cada método.

| Método de contacto | Descrição |
| --- | --- |
[Aplicação móvel](#use-a-mobile-app-as-the-contact-method) | - **Receba notificações para verificação.** Esta opção envia uma notificação à aplicação autenticador no seu smartphone ou tablet. Ver a notificação e, se for legítimo, selecione **autenticar** na aplicação. O trabalho ou na escola pode exigir que introduzir um código PIN antes que autenticar.<br>- **Utilize o código de verificação.** Neste modo, a aplicação autenticador gera um código de verificação que atualiza a cada 30 segundos. Introduza o código de verificação mais recente na interface de iniciar sessão.<br>A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Chamada número de telemóvel ou texto](#use-your-mobile-phone-as-the-contact-method) | - **Chamada telefónica** coloca uma chamada de voz automático para o número de telefone fornecidos. Atender a chamada e prima # no teclado de telefone para autenticar.<br>- **Mensagem de texto** termina a uma mensagem de texto que contém um código de verificação. Após a linha de comandos no texto, responder à mensagem de texto ou introduza o código de verificação fornecido para a interface de início de sessão. |  
[Chamada do Office](#use-your-office-phone-as-the-contact-method) | Coloca uma chamada de voz automático para o número de telefone fornecidos. Atender a chamada e prime # no teclado de telefone para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utilizar uma aplicação móvel como o método de contacto

Utilização deste método requer que instalar uma aplicação de autenticador no seu telemóvel ou tablet. Os passos neste artigo são baseados na aplicação do Microsoft Authenticator, que se encontra disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selecione a **aplicação móvel** a partir da lista pendente.
2. Selecione **receber notificações para verificação** ou **código de verificação Utilizar**e, em seguida, selecione **Configurar**.

    ![Ecrã de verificação de segurança adicional](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. No seu telemóvel ou tablet, abra a aplicação e selecione **+** para adicionar uma conta. (Em dispositivos Android, selecione as reticências, em seguida, **Adicionar conta**.)
4. Especifique que pretende adicionar uma conta escolar ou profissional. Abre o scanner de código QR no seu telemóvel. Se a sua câmara não está a funcionar corretamente, pode selecionar para introduzir manualmente as informações da empresa. Para mais informações, consulte o artigo [Adicionar uma conta manualmente](#add-an-account-manually).  
5. Pesquisa a imagem de código QR que apareciam com o ecrã para configurar a aplicação móvel.  Selecione **concluído** para fechar o ecrã de código QR.  

    ![Ecrã de código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Quando tiver terminado de ativação no telemóvel, selecione **contacto-me**.  Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.  
7. Se a sua empresa necessita de um PIN para aprovar a verificação de início de sessão, introduza-lo.

    ![Caixa para introduzir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Depois de entrada do PIN estiver concluída, selecione **Fechar**. Neste momento, a sua verificação deve ser efetuada com êxito.
9. Recomendamos que introduza o seu número de telemóvel caso perder o acesso à sua aplicação móvel. Especificar o seu país/região a partir da lista pendente e introduza o seu número de telemóvel na caixa junto ao nome do país. Selecione **seguinte**.
10. Neste momento, lhe for pedido para configurar a aplicação as palavras-passe para as aplicações não baseadas no browser, por exemplo, o Outlook 2010 ou mais antigo ou a aplicação de correio eletrónico nativo em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **concluído** e ignorar o resto dos passos.
11. Se estiver a utilizar estas aplicações, copiar a palavra-passe de aplicação fornecidos e colá-la a sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajudar com palavras-passe de aplicação].
12. Clique em **concluído**.


### <a name="add-an-account-manually"></a>Adicionar manualmente uma conta
Se pretender adicionar uma conta para a aplicação móvel manualmente, em vez de utilizar o leitor de QR, siga estes passos.

1. Selecione o botão **introduzir manualmente a conta** .  
2. Introduza o código e o URL que são fornecidos na mesma página que mostra o código de barras. Vai este informações nas caixas **código** e o **URL** da aplicação móvel do.

    ![Programa de configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Quando tiver terminado a ativação, selecione **contacto-me**. Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utilizar o seu número de telemóvel como o método de contacto

1. Selecione o **Telefone de autenticação** a partir da lista pendente.  

    ![Programa de configuração](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Selecione o seu país/região da lista pendente e introduza o seu número de telemóvel.
3. Selecione o método que preferir para utilizar com o seu número de telemóvel - texto ou numa chamada.
4. Selecione **contacto-me** para verificar o seu número de telefone. Dependendo do modo que selecionou, uma forma ou ligue para si. Siga as instruções fornecidas no ecrã e, em seguida, selecione **Confirmar**.
5. Neste momento, lhe for pedido para configurar a aplicação as palavras-passe para as aplicações não baseadas no browser, por exemplo, o Outlook 2010 ou mais antigo ou a aplicação de correio eletrónico nativo em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **concluído** e ignorar o resto dos passos.
6. Se estiver a utilizar estas aplicações, copiar a palavra-passe de aplicação fornecidos e colá-la a sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajudar com palavras-passe de aplicação].
7. Clique em **concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utilizar o seu telefone do office como o método de contacto

1. Selecione o **Telefone do escritório** a partir do menu pendente  

    ![Programa de configuração](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. A caixa número de telefone é preenchida automaticamente com as suas informações de contacto da empresa. Se o número for mal ou em falta, peça ao seu administrador para efetuar alterações.
4. Selecione **contacto-me** para verificar o seu número de telefone e irá chamamos o seu número. Siga as instruções fornecidas no ecrã e, em seguida, selecione **Confirmar**.
5. Neste momento, lhe for pedido para configurar a aplicação as palavras-passe para as aplicações não baseadas no browser, por exemplo, o Outlook 2010 ou mais antigo ou a aplicação de correio eletrónico nativo em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **concluído** e ignorar o resto dos passos.
6. Se estiver a utilizar estas aplicações, copiar a palavra-passe de aplicação fornecidos e colá-la a sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, consulte o artigo [o que são as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).
7. Clique em **concluído**.

## <a name="next-steps"></a>Próximos passos

- Alterar as opções preferidas e [Gerir as suas definições para a verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)
- Configure [as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) para as aplicações do dispositivo nativo que não suportam a verificação de dois passos.
- Dar saída a [aplicação Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) para autenticação rápida, segura mesmo quando não tiver o serviço de célula.
