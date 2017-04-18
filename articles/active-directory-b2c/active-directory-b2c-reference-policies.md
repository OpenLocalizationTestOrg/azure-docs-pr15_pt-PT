<properties
    pageTitle="Azure Active B2C de diretório: Quadro de política Extensible | Microsoft Azure"
    description="Um tópico no âmbito extensible política do Azure Active Directory B2C e sobre como criar vários tipos de políticas"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active B2C de diretório: Quadro de política Extensible

## <a name="the-basics"></a>Noções básicas

O quadro de política extensible do Azure Active Directory (Azure AD) B2C é a intensidade do principais do serviço. Políticas de totalmente descrevem experiências de identidade do consumidor como inscrição, início de sessão no ou perfil de edição. Por exemplo, uma política de inscrição permite-lhe controlar comportamentos ao configurar as seguintes definições:

- Tipos de conta (contas de rede sociais como o Facebook ou contas locais como endereço de correio eletrónico) que podem utilizar os consumidores inscrever-se para a aplicação.
- Atributos (por exemplo, nome próprio, código postal e tamanho) para ser recolhidos do consumidor durante a inscrição.
- Utilização de autenticação Multifator.
- A aspeto e funcionalidade de todas as páginas de inscrição.
- As informações (manifestos como em afirmações num token) que a aplicação receba quando a política de executar acabamentos.

Pode criar várias políticas de diferentes tipos no seu inquilino e utilizá-los nas suas aplicações, conforme necessário. Políticas de podem ser reutilizadas através de aplicações. Isto permite que os programadores definir e modificar experiências de identidade do consumidor mínimas com ou sem alterações ao seu código.

Políticas estão disponíveis para utilização através de uma interface de programador simples. A aplicação accionadores uma política de utilizando um pedido de autenticação de HTTP padrão (passar um parâmetro de política no pedido) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre os pedidos de invocar uma política de inscrição e aqueles invocar uma política de início de sessão no são o nome da política utilizado em parâmetro de cadeia de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obter mais detalhes sobre o quadro de política, consulte esta [mensagem de blogue](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição

Para ativar a inscrição na sua aplicação, terá de criar uma política de inscrição. Esta política descreve as experiências que consumidores irão pelas durante a inscrição e os conteúdos de tokens que a aplicação irá receber em desportivas efetuada com êxito.

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique em **+ Adicionar** no topo da pá.
4. O **nome** determina o nome da política de inscrição utilizado pela sua aplicação. Por exemplo, introduza "SiUp".
5. Clique em **fornecedores de identidade** e selecione "Enviar por correio eletrónico inscrição no". Opcionalmente, também pode selecionar fornecedores de identidade social, caso já tenha configurado. Clique em **OK**.
6. Clique em **atributos de inscrição**. Aqui escolher atributos que pretende recolher do consumidor durante a inscrição. Por exemplo, selecione "País/região", "Nome a apresentar" e "Código Postal". Clique em **OK**.
7. Clique em **afirmações da aplicação**. Aqui escolher afirmações que pretende que sejam devolvidos em tokens enviados para a sua aplicação após uma experiência de inscrição efetuada com êxito. Por exemplo, selecione "Nome a apresentar", "Fornecedor de identidade", "Código Postal", "É novo de utilizador" e "ID de utilizador, objeto".
8. Clique em **Criar**. Tenha em atenção que acabou de criar a política de aparece como "**B2C_1_SiUp**" (o **B2C\_1\_ ** fragmento é automaticamente adicionado) no pá **políticas de inscrição** .
9. Abra a política ao clicar em "**B2C_1_SiUp**".
10. Selecione "Contoso B2C aplicação" no menu pendente de **aplicações** e `https://localhost:44321/` na **URL de resposta / redirecionar URI** pendente.
11. Clique em **Executar agora**. Abre num novo separador do browser e pode executar através da experiência do consumidor da inscrever-se para a sua aplicação.

    > [AZURE.NOTE]
    Bastam para cima um minuto para a criação de política e atualizações entre em vigor.

## <a name="create-a-sign-in-policy"></a>Criar uma política de início de sessão

Para permitir que iniciar sessão na sua aplicação, terá de criar uma política de início de sessão. Esta política descreve as experiências que consumidores irão pelas durante a sessão e o conteúdo da tokens de que a aplicação irá receber no início de sessão-ins efetuada com êxito.

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Iniciar sessão políticas**.
3. Clique em **+ Adicionar** no topo da pá.
4. O **nome** determina o nome da política de início de sessão no utilizado pela sua aplicação. Por exemplo, introduza "SiIn".
5. Clique em **fornecedores de identidade** e selecione "Local conta de início de sessão". Opcionalmente, também pode selecionar fornecedores de identidade social, caso já tenha configurado. Clique em **OK**.
6. Clique em **afirmações da aplicação**. Aqui escolher afirmações que pretende que sejam devolvidos em tokens enviados para a sua aplicação após uma experiência de início de sessão efetuada com êxito. Por exemplo, selecione "Nome a apresentar", "Fornecedor de identidade", "Código Postal" e "ID de utilizador, objeto". Clique em **OK**.
7. Clique em **Criar**. Tenha em atenção que acabou de criar a política de aparece como "**B2C_1_SiIn**" (o **B2C\_1\_ ** fragmento é automaticamente adicionado) no pá **políticas de início de sessão** .
8. Abra a política ao clicar em "**B2C_1_SiIn**".
9. Selecione "Contoso B2C aplicação" no menu pendente de **aplicações** e `https://localhost:44321/` na **URL de resposta / redirecionar URI** pendente.
10. Clique em **Executar agora**. Abre num novo separador do browser e pode executar através a experiência do consumidor de iniciar sessão na sua aplicação.

    > [AZURE.NOTE]
    Bastam para cima um minuto para a criação de política e atualizações entre em vigor.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou iniciar sessão

Esta política trata ambas as experiências consumidor de inscrição e iniciar sessão com a configuração de um único. Consumidores são por um instrutor para baixo o caminho direita (inscrição ou início de sessão no) consoante o contexto. Também descreve os conteúdos de tokens de que a aplicação irá receber desportivas efetuada com êxito ou inícios de sessão.  Um exemplo de código para a política de inscrição ou iniciar sessão está [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição ou iniciar sessão**.
3. Clique em **+ Adicionar** no topo da pá.
4. O **nome** determina o nome da política de inscrição utilizado pela sua aplicação. Por exemplo, introduza "SiUpIn".
5. Clique em **fornecedores de identidade** e selecione "Enviar por correio eletrónico inscrição no". Opcionalmente, também pode selecionar fornecedores de identidade social, caso já tenha configurado. Clique em **OK**.
6. Clique em **atributos de inscrição**. Aqui escolher atributos que pretende recolher do consumidor durante a inscrição. Por exemplo, selecione "País/região", "Nome a apresentar" e "Código Postal". Clique em **OK**.
7. Clique em **afirmações da aplicação**. Aqui escolher afirmações que pretende que sejam devolvidos em tokens enviados para a sua aplicação após uma experiência de inscrição ou iniciar sessão com êxito. Por exemplo, selecione "Nome a apresentar", "Fornecedor de identidade", "Código Postal", "É novo de utilizador" e "ID de utilizador, objeto".
8. Clique em **Criar**. Tenha em atenção que acabou de criar a política de aparece como "**B2C_1_SiUpIn**" (o **B2C\_1\_ ** fragmento é automaticamente adicionado) no pá **políticas de inscrição ou iniciar sessão** .
9. Abra a política ao clicar em "**B2C_1_SiUpIn**".
10. Selecione "Contoso B2C aplicação" no menu pendente de **aplicações** e `https://localhost:44321/` na **URL de resposta / redirecionar URI** pendente.
11. Clique em **Executar agora**. Abre num novo separador do browser e pode executar através a experiência do consumidor inscrição ou iniciar sessão, tal como está configurado.

    > [AZURE.NOTE]
    Bastam para cima um minuto para a criação de política e atualizações entre em vigor.

## <a name="create-a-profile-editing-policy"></a>Criar um perfil de política de edição

Para ativar a edição no seu pedido de perfil, terá de criar um perfil de Editar política. Esta política descreve as experiências que consumidores irão pelas durante a edição de perfil e os conteúdos de tokens de que a aplicação irá receber concluído com êxito.

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de edição de perfil**.
3. Clique em **+ Adicionar** no topo da pá.
4. O **nome** determina o nome da política utilizado pela sua aplicação de edição de perfil. Por exemplo, introduza "SiPe".
5. Clique em **fornecedores de identidade** e selecione "Endereço de E-Mail". Opcionalmente, também pode selecionar fornecedores de identidade social, caso já tenha configurado. Clique em **OK**.
6. Clique em **atributos de perfil**. Aqui escolher atributos que o consumidor pode ver e editar. Por exemplo, selecione "País/região", "Nome a apresentar" e "Código Postal". Clique em **OK**.
7. Clique em **afirmações da aplicação**. Aqui escolher afirmações que pretende que sejam devolvidos em tokens enviados para a sua aplicação depois de um perfil efetuada com êxito experiência de edição. Por exemplo, selecione "Nome a apresentar" e "Código Postal".
8. Clique em **Criar**. Tenha em atenção que acabou de criar a política de aparece como "**B2C_1_SiPe**" (o **B2C\_1\_ ** fragmento é automaticamente adicionado) na pá **políticas de edição de perfil** .
9. Abra a política ao clicar em "**B2C_1_SiPe**".
10. Selecione "Contoso B2C aplicação" no menu pendente de **aplicações** e `https://localhost:44321/` na **URL de resposta / redirecionar URI** pendente.
11. Clique em **Executar agora**. Abre num novo separador do browser e pode executar através do perfil consumidor experiência na sua aplicação de edição.

    > [AZURE.NOTE]
    Bastam para cima um minuto para a criação de política e atualizações entre em vigor.
    
## <a name="create-a-password-reset-policy"></a>Criar uma política de reposição de palavra-passe

Para ativar a palavra-passe extensivamente repor a sua aplicação, terá de criar uma política de reposição de palavra-passe. Nota a palavra-passe de todo o inquilino repor a opção especificada [aqui](active-directory-b2c-reference-sspr.md) é continuam a ser aplicáveis para as políticas de início de sessão. Esta política descreve as experiências que os consumidores irão pelas durante a reposição de palavra-passe e o conteúdo de tokens de que a aplicação irá receber concluído com êxito.

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Repor a palavra-passe políticas**.
3. Clique em **+ Adicionar** no topo da pá.
4. O **nome** determina o que nome da política utilizado pela sua aplicação de repor a palavra-passe. Por exemplo, introduza "personalizada SSPR".
5. Clique em **fornecedores de identidade** e selecione "Repor a palavra-passe utilizando o endereço de e-mail". Clique em **OK**.
6. Clique em **afirmações da aplicação**. Aqui escolher afirmações que pretende que sejam devolvidos em tokens enviados regressar à aplicação após experiência de repor uma palavra-passe efetuada com êxito. Por exemplo, selecione "ID de utilizador, objeto".
7. Clique em **Criar**. Tenha em atenção que acabou de criar a política de aparece como "**B2C_1_SSPR**" (o **B2C\_1\_ ** fragmento é automaticamente adicionado) na pá **políticas de reposição de palavra-passe** .
8. Abra a política ao clicar em "**B2C_1_SSPR**".
9. Selecione "Contoso B2C aplicação" no menu pendente de **aplicações** e `https://localhost:44321/` na **URL de resposta / redirecionar URI** pendente.
10. Clique em **Executar agora**. Abre num novo separador do browser e pode executar através a experiência do consumidor Repor palavra-passe na sua aplicação.

    > [AZURE.NOTE]
    Bastam para cima um minuto para a criação de política e atualizações entre em vigor.

## <a name="additional-resources"></a>Recursos adicionais

- [Token, a sessão e a configuração de início de sessão único](active-directory-b2c-token-session-sso.md).
