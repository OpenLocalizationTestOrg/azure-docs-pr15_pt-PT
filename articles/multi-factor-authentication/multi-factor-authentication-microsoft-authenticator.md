<properties
    pageTitle="Aplicação do Microsoft Authenticator para telemóveis | Microsoft Azure"
    description="Saiba como atualizar para a versão mais recente do Azure autenticador."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Microsoft autenticador

A aplicação Microsoft Authenticator fornece um nível de segurança na sua conta Azure adicional (por exemplo, bsimon@contoso.onmicrosoft.com), suas no local trabalhar conta (por exemplo, bsimon@contoso.com), ou a sua conta Microsoft (por exemplo, bsimon@outlook.com).

Funciona a aplicação de uma das duas formas:

- **Notificação**. A aplicação pode ajudar a evitar acesso não autorizado às contas e parar transações fraudulentas por direccionar uma notificação para o seu smartphone ou tablet. Basta veja a notificação e se for legítimo, selecione **Verificar**. Caso contrário, pode selecionar **Negar**. Para obter informações sobre negação de notificações, consulte o artigo como utilizar a funcionalidade de fraude do relatório e negar para autenticação Multifator.

- **Palavra-passe com o código de verificação**. A aplicação pode ser utilizada como um token de software para gerar um código de verificação OAuth. Introduza o código fornecido pela aplicação para o início de sessão no ecrã, juntamente com o nome de utilizador e palavra-passe, quando lhe for pedido. O código de verificação fornece uma segunda forma de autenticação.

Com a versão da aplicação Microsoft Authenticator, a aplicação antiga do Azure autenticador está a ser substituída.  A aplicação do Azure autenticador irão continuar a trabalhar, mas se decidir mover para a nova aplicação Microsoft Authenticator, este artigo pode ajudá-lo.  

## <a name="install-the-app"></a>Instalar a aplicação

A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Adicionar contas à aplicação

Para cada conta que pretende adicionar à aplicação do Microsoft Authenticator, utilize um dos seguintes procedimentos.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Adicionar uma conta à aplicação ao utilizar o scanner de código QR

1. Aceda ao ecrã de definições de verificação de segurança.  Para obter informações sobre como obter a este ecrã, consulte o artigo [alterar as suas definições de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

    ![O botão Configurar no ecrã de definições de verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Isto reúne um ecrã com um código QR no mesmo.

    ![Ecrã que fornece o código QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra a aplicação Microsoft autenticador. No ecrã **contas** , selecione **+**e, em seguida, especifique que pretende adicionar uma conta escolar ou profissional.

    ![No ecrã contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Ecrã para a especificação de uma conta escolar ou profissional](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Utilizar a câmara para analisar o código QR e, em seguida, selecione **concluído** para fechar o ecrã de código QR.

    ![Ecrã para digitalizar um código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Se a sua câmara não está a funcionar corretamente, pode introduzir o código QR e o URL manualmente. Para mais informações, consulte o artigo [adicionar manualmente uma conta para a aplicação](#add-an-account-to-the-app-manually).

5. Aguarde enquanto a conta é activada. Quando termina de ativação, selecione **contacto-me**.  Esta envia uma notificação ou um código de verificação para o seu telefone.  Selecione **Verificar**.

    ![Ecrã onde selecionar verificar para iniciar sessão](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Se a sua empresa necessita de um PIN para aprovar a verificação de início de sessão, introduza-lo.

    ![Caixa para introduzir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Depois de entrada do PIN estiver concluída, selecione **Fechar**. Neste momento, a sua verificação deve ser efetuada com êxito.
8. Recomendamos que introduza o seu número de telemóvel caso perder o acesso à sua aplicação. Especificar o seu país/região a partir da lista pendente e introduza o seu número de telemóvel na caixa junto ao nome do país. Selecione **seguinte**.
9. Neste momento, tem de configurar o método de contacto. Está na altura para configurar a aplicação palavras-passe para as aplicações do não baseadas no browser, por exemplo, o Outlook 2010 ou mais antigo. Se não utiliza estas aplicações, selecione **concluído**. Caso contrário, continue para o passo seguinte.

    ![Ecrã para a criação de uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Se estiver a utilizar aplicações não baseadas no browser, copie a palavra-passe de aplicação fornecidos e cole a palavra-passe para as suas aplicações. Para obter passos sobre aplicações individuais, tais como o Outlook e o Lync, consulte o artigo como alterar a palavra-passe no seu correio eletrónico para a palavra-passe de aplicação e como alterar a palavra-passe na sua aplicação para a palavra-passe de aplicação.
11. Selecione **concluído**.

Agora deverá ver a nova conta no ecrã **contas** .

![Ecrã de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Adicionar manualmente uma conta à aplicação

1. Aceda ao ecrã de definições de verificação de segurança.  Para obter informações sobre como obter a este ecrã, consulte o artigo [alterar as suas definições de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

    ![O botão Configurar no ecrã de definições de verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Isto reúne um ecrã com um código QR no mesmo.  Tenha em atenção o código e o URL.

    ![Ecrã que fornece o código QR e o URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra a aplicação Microsoft autenticador. No ecrã **contas** , selecione **+**e, em seguida, especifique que pretende adicionar uma conta escolar ou profissional.

    ![No ecrã contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Ecrã para a especificação de uma conta escolar ou profissional](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. No scanner, selecione a **introduzir o código manualmente**.

    ![Ecrã para digitalizar um código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Introduza o código e o URL nas caixas apropriadas na aplicação.

    ![Ecrã para a introdução de código e o URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Ecrã para a introdução de código e o URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Aguarde enquanto a conta é activada. Quando termina a ativação, selecione **contacto-me**. Esta envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.

Agora deverá ver a nova conta no ecrã **contas** .

![Ecrã de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Adicionar uma conta para a aplicação com o ID de toque

A aplicação Microsoft Authenticator no iOS suporta ID Tátil.  Autenticação Multifator Azure permite às organizações necessitam de um PIN para dispositivos. Com o ID de toque, iOS utilizadores não é necessário introduzir um código PIN. Em vez disso, pode digitalizar a sua identificação e selecione **Aprovar**.

Configurar o toque ID com Microsoft Authenticator é simple. Concluir um desafio verificação normal com um PIN. Se o seu dispositivo suportar ID de toque, Microsoft Authenticator irá configure-o automaticamente para essa conta.

![Verificação da configuração de ID de toque](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

De que ponto, quando estiver a necessária para verificar o seu iniciar sessão, selecione a notificação de push recebido e digitalizar a sua identificação em vez de introduzir o PIN.

![Notificação de emissão](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Desinstale a aplicação antiga do Azure autenticação

Depois de adicionar todas as contas para a nova aplicação, pode desinstalar a aplicação antiga do seu telefone.

## <a name="delete-an-account"></a>Eliminar uma conta

Para remover uma conta a partir da aplicação Microsoft Authenticator, selecione a conta e, em seguida, selecione **Eliminar**.

![Botão Eliminar](./media/multi-factor-authentication-azure-authenticator/remove.png)
