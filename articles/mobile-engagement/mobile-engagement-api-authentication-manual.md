<properties 
    pageTitle="Autenticar com APIs de resto de Cativação Mobile - configuração manual"
    description="Descreve como configurar manualmente a autenticação para Mobile Cativação REST APIs" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar com APIs de resto de Cativação Mobile - configuração manual

Este é uma documentação do anexo para [autenticar com Mobile Cativação REST APIs](mobile-engagement-api-authentication.md). Certifique-se de que lê-la primeiro para obter o contexto. Este exemplo descreve a forma alternativa para fazer a configuração única para configurar o seu autenticação para as Mobile Cativação REST APIs da interface utilizando o Portal do Azure. 

>[AZURE.NOTE] As instruções abaixo estão com base neste [Guia do Active Directory](../resource-group-create-service-principal-portal.md) e personalizadas para o que é necessário para a autenticação para Mobile Cativação APIs. Por isso, se referem-se que pretende compreender os passos indicados abaixo detalhadamente. 

1. Início de sessão para a sua conta Azure através do [portal clássica](https://manage.windowsazure.com/).

2. Selecione **Do Active Directory** a partir do painel esquerdo.

     ![Selecione do Active Directory][1]

3. Escolha o **Predefinido do Active Directory** no seu portal Azure. 

     ![Escolha directório][2]

    >[AZURE.IMPORTANT] Esta abordagem só funciona quando estiver a trabalhar no predefinido do Active Directory da sua conta e não irá funcionar se estão a fazer isto num Active Directory que criou na sua conta. 

4. Para ver as aplicações no seu diretório, clique em **aplicações**.

     ![Ver aplicações][3]

5. Clique em **Adicionar**. 

     ![Adicionar aplicação][4]

6. Clique em **Adicionar uma aplicação que está a desenvolver a minha organização**

     ![nova aplicação][5]

6. Preencha o nome da aplicação e selecione o tipo de aplicação como **E/ou de aplicações WEB WEB API** e clique no botão seguinte.

     ![aplicação nome][6]

7. Pode fornecer qualquer fictícios URLs para o **Início de sessão no URL** e **URI de ID da aplicação**. Não são utilizadas para o cenário e os URLs próprios não são validados.  

     ![Propriedades da aplicação][7]

8. No final deste, terá de uma aplicação AAD com o nome que forneceu anteriormente semelhante ao seguinte. Este é o **AD\_aplicação\_nome** e tome nota do mesmo.  

     ![nome da aplicação][8]

9. Clique no nome da aplicação e clique em **Configurar**.

     ![configurar a aplicação][9]

10. Anote o ID do cliente que será utilizada como **cliente\_ID** para sua API chamadas. 

     ![configurar a aplicação][10]

11. Desloque para baixo para a secção **chaves** e adicione uma chave com preferência duração de anos a 2 (termo) e clique em **Guardar**. 

     ![configurar a aplicação][11]


12. Copie imediatamente o valor que é apresentado na chave tal como só é mostrada agora e não está armazenado para que não serão apresentados nunca novamente. Se perder a mesma terá de gerar uma nova chave. Este será o **CLIENT_SECRET** das chamadas API. 

     ![configurar a aplicação][12]

    >[AZURE.IMPORTANT] Esta chave expirará no final da duração da que especificou para que tornar-se de que renová-la quando a hora caso contrário, surgir a autenticação de API não funcionar mais. Também pode eliminar e recrie esta tecla se achar que foi comprometida.
 
13. Clique no botão **Pontos finais de vista** agora que irá abrir a caixa de diálogo **Pontos finais de aplicação** . 

    ![][13]

14. A partir da caixa de diálogo pontos finais de aplicação, copie o **Ponto final o TOKEN de 2.0 OAUTH**. 

    ![][14]

15. Este ponto final será no seguinte formato onde o GUID no URL é a sua **TENANT_ID** por isso, tome nota do mesmo: 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Agora Podemos continuar para configurar as permissões nesta aplicação. Para este terá de abrir o [Azure portal](https://portal.azure.com). 

17. Clique em **Grupos de recursos** e localize o grupo de recursos de **Cativação Mobile** .  

    ![][15]

18. Clique no grupo de recursos de **Cativação Mobile** e navegue para a pá **Definições** aqui. 

    ![][16]

19. Clique em **utilizadores** na pá definições e, em seguida, clique em **Adicionar** para adicionar um utilizador. 

    ![][17]

20. Clique em **selecionar uma função**

    ![][18]

21. Clique no **proprietário**

    ![][19]

22. Procurar o nome da sua aplicação **AD\_aplicação\_nome** na caixa de pesquisa. Não verá este por predefinição aqui. Quando encontrá-lo, selecioná-la e clique em **Selecionar** na parte inferior da pá. 

    ![][20]

23. No pá **Adicionar acesso** , irá aparecer como **1 o utilizador, 0 grupos**. Clique em **OK** neste pá para confirmar a alteração. 

    ![][21]

Agora concluiu a configuração de AAD necessária e está definido para ligar as APIs. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



