A maioria dos erros de autenticação tempo resultam de definições de configuração incorreta ou inconsistentes. Eis algumas sugestões sobre específico verifique.

* Certifique-se de que não perde em qualquer lugar no botão **Guardar** . Muitas vezes é fácil de fazer e o resultado é que lhe vai ser consultar os valores corretos numa página do portal mas realmente ainda não foram guardadas no ambiente Azure ou aplicação do Azure AD.
* Para definições configuradas no pá **Definições da aplicação** do Azure portal, certifique-se de que o corrigir API app ou web app foi seleccionado quando as definições de foram introduzidas.  Também Certifique-se de que as definições de foram introduzidas como **as definições da aplicação** e não **as cadeias de ligação**, tal como o formato das duas secções é semelhante.
* Para a autenticação para um JavaScript front-end, transfira o manifesto novamente para verificar que `oauth2AllowImplicitFlow` foi alterada com êxito para `true`.
* Certifique-se de que utilizou HTTPS sempre que tiver configurado URLs:

    * No código de projeto
    * No CORS
    * Nas definições de aplicação de ambiente Azure para cada aplicação API e a aplicação web
    * Nas definições da aplicação Azure AD.
    
    Note que se copiar o URL de uma aplicação de API a partir do portal, muitas vezes tem `http://` e tiver manualmente alterá-la para `https://`.

* Certifique-se de que as alterações de código foram implementadas com êxito. Por exemplo, numa solução múltiplos projectos é possível alterar código de um projeto e escolha um dos outros acidentalmente quando pretende implementar a alteração.
* Certifique-se de que vai para HTTPS URLs no seu browser, não HTTP URLs. Por predefinição, cria Visual Studio publicar perfis com URLs de HTTP e que é o que é aberto no browser depois de implementar um projeto.
* Para um JavaScript front-end a autenticação, certifique-se de que CORS está corretamente configurada na aplicação de API que liga para o código JavaScript. Em caso de dúvida sobre se o problema é relacionados com CORS, experimente "*" como o URL de origem permitidos. 
* Para um JavaScript front-end, abra o separador de consola de ferramentas de programador do seu browser para obter mais informações de erro e examinar os pedidos HTTP na rede. No entanto, as mensagens de erro da consola podem ser enganadoras. Se obtiver uma mensagem a indicar um erro CORS, o problema real poderá autenticação. Pode verificar se este for o caso ao executar a aplicação com autenticação temporariamente temporariamente desactivada.
* Para uma aplicação do .NET API, certifique-se de que está a obter quanto informações em mensagens de erro possível definindo [customErrors modo para desativado](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para uma aplicação do .NET API, iniciar uma [sessão remota do depuração](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)e examinar os valores das variáveis que são transmitidos para o código que utiliza ADAL para adquirir uma ligação OAuth ou código que verifica créditos sobre o ID do serviço esperado principal. Repare que o seu código pode selecionar valores de configuração de várias origens diferentes, por isso, é possível encontrar surpresas desta forma. Por exemplo, se de que forma `ida:ClientId` como `ida:ClientID` quando configurar definições de ambiente de aplicação de serviço do Azure, poderá obter o código de `ida:ClientId` valor-está à procura do ficheiro Web. config, ignorando a definição do serviço de aplicação do Azure. 
* Se não funcionarem coisas numa janela do Internet Explorer normal, uma existente início de sessão, pode estar a interferir; Tente InPrivate e tente Chrome ou do Firefox.
