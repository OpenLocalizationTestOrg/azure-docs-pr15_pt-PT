<properties
    pageTitle="Azure Active B2C de diretório: Repor o personalizada de palavra | Microsoft Azure"
    description="Um tópico demonstrar como configurar o personalizada de palavra-repor para os seus consumidores no Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C diretório ativos: Configurar o personalizada de palavra-repor para os seus consumidores

Com a funcionalidade de reposição personalizada de palavra-passe, os utilizadores poderão (que se inscreveram para contas locais) pode repor a palavra-passe sozinho. Significativamente reduz o encargo para a equipa de suporte, especialmente se a aplicação tiver milhões de consumidores utilizá-lo regularmente. Atualmente, suportamos apenas utilizar um endereço de e-mail verificado como um método de recuperação. Vamos adicionar métodos de recuperação adicionais (número de telefone verificado, perguntas de segurança, etc.) no futuro.

> [AZURE.NOTE]
Este artigo aplica-se a palavra-passe personalizada repor utilizado no contexto de uma política de início de sessão. Se precisar de totalmente personalizáveis de palavra-passe reposição políticas invocadas da sua aplicação, consulte [Este artigo](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Por predefinição, no diretório da sua não terão personalizada de palavra-repor ativada. Utilize os passos seguintes para ativá-la:

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) como o administrador de subscrição. Este é o mesmo trabalho ou conta escolar ou a mesma conta Microsoft que utilizou para criar o seu diretório.
2. Navegue para a extensão do Active Directory na barra de navegação no lado esquerdo.
3. Localizar o seu diretório no separador de **diretório** e clique nele.
4. Clique no separador **Configurar** .
5. Desloque para baixo para a secção de **política de reposição de palavra-passe de utilizador** e ative a opção de **utilizadores com capacidade para a palavra-passe repor** como **Sim**. Repare que a opção de **Endereço de E-mail alternativo** está selecionada; Deixe-o tal como está.

    ![Reposição personalizada de palavra-passe](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Clique em **Guardar** na parte inferior da página. Já está!

Para testar, utilize a funcionalidade "Executar agora" no qualquer política de início de sessão no tem contas locais como um fornecedor de identidades. O início de sessão na conta local página (onde introduzir um endereço de e-mail e palavra-passe, ou um nome de utilizador e palavra-passe), clique em **não é possível aceder à sua conta?** para verificar a experiência do consumidor.

> [AZURE.NOTE]
As páginas de reposição personalizada de palavra-podem ser personalizadas utilizando a [funcionalidade de imagem corporativa da empresa](../active-directory/active-directory-add-company-branding.md).
