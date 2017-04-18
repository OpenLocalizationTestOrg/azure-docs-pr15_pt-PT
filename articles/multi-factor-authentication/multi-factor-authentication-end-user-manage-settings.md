<properties
    pageTitle="Gerir as definições de verificação de dois passos | Microsoft Azure"
    description="Gerir como utilizar o Azure a autenticação Multifator incluindo alterar a sua informação de contacto ou configurar os seus dispositivos."
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

# <a name="manage-your-settings-for-two-step-verification"></a>Gerir as suas definições para a verificação de dois passos

Este artigo responde a questões sobre como atualizar as definições para uma autenticação de verificação ou multifator. Se estiver a ter problemas ao iniciar sessão sua conta, consulte [está a ter problemas com a verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md) para resolver problemas relacionados.


## <a name="where-to-find-the-settings-page"></a>Onde localizar a página de definições
Dependendo de como a sua empresa configurado da autenticação Multifator de Azure, existem alguns locais onde pode alterar as definições como o seu número de telefone.

Se o seu administrador de TI enviada saída de um URL específico ou passos para gerir a verificação de dois passos, siga as instruções. Caso contrário, as instruções seguintes devem funciona para todos os outros participantes. Se siga estes passos, mas não vê as mesmas opções, isso significa que o seu trabalho ou escola personalizados os seus próprios portal. Peça ao seu administrador para a ligação para o seu portal de autenticação Multifator de Azure.


1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Na parte superior, selecione **perfil**.  
3. Selecione a **verificação de segurança adicional**.  

    ![Asminhasaplicações](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Página de verificação de segurança adicional carrega com as suas definições.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Pretendo alterar o meu número de telefone ou adicionar um número secundário

É importante configurar um número de telefone de autenticação secundário.  Uma vez que o número de telefone principal e para a aplicação móvel são provavelmente no mesmo telemóvel, o número de telefone secundário é a única forma que poderão voltar para a sua conta se o seu telemóvel é roubado ou.

> [AZURE.NOTE]
> Se não tem acesso ao seu número de telefone principal e precisar de ajuda sua conta, consulte o nossos tópicos de ajuda na [está a ter problemas com a verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md).

**Para alterar o seu número de telefone principal:**  

1. Na página de verificação adicionais de segurança, selecione a caixa de texto com o seu número de telefone atual e editá-la com o seu número de telefone novo.  
2. Selecione **Guardar**.  
3. Se este for o número que utilizar para a sua opção de verificação preferido, tem de verificar o número da nova antes de que pode guardá-lo.  


**Para adicionar um número de telefone secundário:**  

1. Na página de verificação adicionais de segurança, selecione a caixa junto a **telefone de autenticação alternativo.**  
2. Introduza o seu número de telefone secundário na caixa de texto.  
3. Selecione **Guardar** e as suas alterações concluídas.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Como limpar Authenticator Microsoft a partir do meu dispositivo antigo e deslocar-se para uma nova?
Quando desinstalar a aplicação a partir do seu dispositivo ou repor o dispositivo, não remove a ativação no back-end. Deve utilizar os passos descritos no [Mover para um novo dispositivo](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Próximos passos
- Obter sugestões de resolução de problemas e ajuda sobre [está a ter problemas com a verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md)
- Configure [as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) para qualquer as aplicações que não suportam a verificação de dois passos.
