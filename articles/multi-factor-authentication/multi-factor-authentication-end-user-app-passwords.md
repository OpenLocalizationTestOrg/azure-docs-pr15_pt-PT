<properties
    pageTitle="O que são palavras-passe de aplicação no Azure MFA?"
    description="Esta página irá ajudar os utilizadores a compreender o que são as palavras-passe de aplicação e o que são utilizadas para com em conta Azure MFA."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>O que são palavras-passe de aplicação no Azure a autenticação Multifator?

Algumas aplicações de não baseadas no browser, tal como o cliente de correio eletrónico nativas Apple que utiliza o Exchange Active Sync, atualmente não suportam autenticação multifator. Autenticação multifator está ativada por utilizador. Isto significa que se um utilizador tenha sido ativado para a autenticação multifator e estiver a tentar utilizar aplicações não baseadas no browser, estarão não é possível fazê-lo. Uma palavra-passe de aplicação permite esta opção para ocorrer.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Clientes do Office 2013 (incluindo o Outlook) agora suportam novo protocolos de autenticação e podem ser ativados para suportar uma autenticação Multifator.  Isto significa que uma vez activada, palavras-passe de aplicação não são necessárias para utilizar com clientes do Office 2013.  Para obter mais informações consulte o artigo [Office autenticação moderna público pré-visualização 2013 anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>Como utilizar palavras-passe de aplicação

Seguem-se algumas coisas que se deve lembrar sobre como utilizar palavras-passe de aplicação.

- A palavra-passe real é gerada automaticamente e não for fornecida pelo utilizador. Esta é uma vez que a palavra-passe gerada automaticamente, é mais difícil intruso adivinhar e é mais segura.
- Neste momento não existe um limite de 40 palavras-passe por utilizador. Se tentar criar um depois de ter atingiu o limite, ser-lhe-á pedido para eliminar uma das suas palavras-passe de aplicação existente para criar um novo.
- Recomenda-se que as palavras-passe de aplicação ser criado por dispositivo e não por aplicação. Por exemplo, pode criar uma palavra-passe de aplicação para o seu portátil e utilizar essa palavra-passe de aplicação para todas as aplicações nesse portátil.
- É-lhe dada uma palavra-passe de aplicação a primeira vez que iniciar sessão.  Se precisar daqueles adicionais, pode criá-los.

![Programa de configuração](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Assim que tiver uma palavra-passe de aplicação, utilize esta em vez da sua palavra-passe original com estas aplicações não baseadas no browser.  Por isso, por exemplo, se estiver a utilizar autenticação multifator e o cliente de e-mail nativo Apple no seu telemóvel.  Utilize a palavra-passe de aplicação para que pode ignorar a autenticação multifator e para continuar a trabalhar.

## <a name="creating-and-deleting-app-passwords"></a>Criar e eliminar palavras-passe de aplicação
Durante a sua sessão inicial recebem uma palavra-passe de aplicação que pode utilizar.  Para além disso também pode criar e eliminar palavras-passe de aplicação mais tarde.  Como fazer isto depende de como utilizar autenticação multifator.  Escolha aquele que a maior parte se aplica a si.

Como utilizar authentiation multifator|Descrição
:------------- | :------------- |
[Utilizá-lo com o Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Isto significa que pretende de criar palavras-passe de aplicação através do portal do Office 365.
[Não sei](#creating-and-deleting-app-passwords-with-myapps-portal)|Isto significa que pretende que irá criar palavras-passe de aplicação através de [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Utilizá-lo com o Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Isto significa que irá pretende que criar palavras-passe de aplicação através do portal do Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Criar e eliminar palavras-passe de aplicação com o Office 365

Se utilizar a autenticação multifator com o Office 365 será pretende criar e eliminar palavras-passe de aplicação através do portal do Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para criar palavras-passe de aplicação no portal do Office 365
--------------------------------------------------------------------------------

1. Inicie sessão no [portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito selecione o widget e selecione definições do Office 365.
3. Clique em verificação de segurança adicional.
4. No lado direito, clique na ligação que diz **atualizar os meus números de telefone utilizados para segurança da conta.** 
 ![Configuração](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isto vai levá-lo para a página que permitirá alterar as definições.
![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, junto a verificação de segurança adicionais, clique no **palavras-passe de aplicação.**
7. Clique em **Criar**.
![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**.
![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.
![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para eliminar palavras-passe de aplicação utilizando o portal do Office 365
--------------------------------------------------------------------------------


1. Inicie sessão no [portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito selecione o widget e selecione definições do Office 365.
3. Clique em verificação de segurança adicional.
4. No lado direito, clique na ligação que diz **atualizar os meus números de telefone utilizados para segurança da conta.** 
 ![Configuração](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isto vai levá-lo para a página que permitirá alterar as definições.
![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, junto a verificação de segurança adicionais, clique no **palavras-passe de aplicação.**
7. Junto a palavra-passe de aplicação que pretende eliminar, clique em **Eliminar**.
![Eliminar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirme a eliminação ao clicar em **Sim**.
![Confirmar a eliminação](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Assim que a palavra-passe de aplicação é eliminada pode clicar em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Criar e eliminar palavras-passe de aplicação com o portal de Asminhasaplicações.
Se não tiver a certeza como utilizar autenticação multifator, em seguida, pode sempre criar e eliminar palavras-passe de aplicação através do portal do asminhasaplicações.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma palavra-passe de aplicação utilizando o portal de Asminhasaplicações

1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione perfil.
3. Selecione a verificação de segurança adicional.
![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isto vai levá-lo para a página que permitirá alterar as definições.
![Programa de configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, junto a verificação de segurança adicionais, clique no **palavras-passe de aplicação.**
6. Clique em **Criar**.
![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**.
![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.
![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para eliminar uma palavra-passe de aplicação utilizando o portal de Asminhasaplicações

1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione perfil.
3. Selecione a verificação de segurança adicional.
![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isto vai levá-lo para a página que permitirá alterar as definições.
![Programa de configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, junto a verificação de segurança adicionais, clique no **palavras-passe de aplicação.**
6. Junto a palavra-passe de aplicação que pretende eliminar, clique em **Eliminar**.
![Eliminar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirme a eliminação ao clicar em **Sim**.
![Confirmar a eliminação](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Assim que a palavra-passe de aplicação é eliminada pode clicar em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Criar palavras-passe de aplicação no portal do Azure

Se utiliza autenticação multifator com o Azure que pretende criar palavras-passe de aplicação através do portal do Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar palavras-passe de aplicação no portal do Azure

1. Inicie sessão no portal de gestão do Azure.
2. Na parte superior, com o botão direito no seu nome de utilizador e selecione a verificação de segurança adicional.
3. Na página proofup, na parte superior, selecione as palavras-passe de aplicação
4. Clique em **Criar**.
5. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**
6. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.
![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para eliminar palavras-passe de aplicação no portal do Azure

1. Inicie sessão no portal de gestão do Azure.
2. Na parte superior, com o botão direito no seu nome de utilizador e selecione a verificação de segurança adicional.
3. Na parte superior, junto a verificação de segurança adicionais, clique no **palavras-passe de aplicação.**
4. Junto a palavra-passe de aplicação que pretende eliminar, clique em **Eliminar**.
5. Confirme a eliminação ao clicar em **Sim**.
6. Assim que a palavra-passe de aplicação é eliminada pode clicar em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
