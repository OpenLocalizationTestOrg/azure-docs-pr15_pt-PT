<properties
    pageTitle="Azure Active B2C de diretório: Ferramenta de auxiliar de personalização página IU | Microsoft Azure"
    description="Uma ferramenta de helper utilizada para demonstrar a funcionalidade de personalização de IU de página no Azure Active Directory B2C"
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
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure B2C diretório ativos: Uma helper ferramenta utilizada para demonstrar a funcionalidade de personalização página (IU) de interface de utilizador

Este artigo é um Assistente para o [artigo de personalização da IU principal](active-directory-b2c-reference-ui-customization.md) no B2C Azure Active Directory (Azure AD). Os passos seguintes descrevem como para o exercício a funcionalidade de personalização de IU página utilizando o conteúdo HTML e CSS de exemplo que fornecemos.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um inquilino do Azure AD B2C

Antes de poder personalizar nada, terá de [obter um inquilino do Azure AD B2C](active-directory-b2c-get-started.md) se ainda não tiver uma.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou iniciar sessão

O conteúdo de exemplo fornecemos pode ser utilizados para imagem de duas páginas uma [política de inscrição ou de início de sessão no](active-directory-b2c-reference-policies.md): a [página de início de sessão unificada](active-directory-b2c-reference-ui-customization.md) e [personalizada conclui ainda pode página atributos](active-directory-b2c-reference-ui-customization.md). Quando [criar a política de inscrição ou iniciar sessão](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adicione a conta Local (endereço de e-mail), Facebook, Google e Microsoft como **fornecedores de identidade**. Estes são os IDPs apenas que irão aceitar os nossos conteúdo HTML de exemplo.  Também pode adicionar um subconjunto destas IDPs se assim o pretender.

## <a name="register-an-application"></a>Registe-se uma aplicação

Terá de para [registar uma aplicação](active-directory-b2c-app-registration.md) no seu inquilino B2C que pode ser utilizado para executar a política. Depois de registar a sua aplicação, tem algumas opções que pode utilizar para executar realmente a política de inscrição:

- Criar um dos B2C de AD Azure as aplicações de guia listadas na secção "Introdução ao" do [Iniciar sessão para cima e inicie sessão consumidores nas suas aplicações](active-directory-b2c-overview.md#getting-started).
- Utilize a aplicação do [Azure AD B2C parques](https://aadb2cplayground.azurewebsites.net) pré-concebidos. Se optar por utilizar o domínio, tem de registar uma aplicação no seu inquilino B2C através do **Redirecionar URI** `https://aadb2cplayground.azurewebsites.net/`.
- Utilize o botão **Executar agora** na sua política no [portal do Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalizar a política

Para personalizar o aspeto e funcionalidade da sua política, é necessário criar primeiro HTML e CSS ficheiros utilizando as convenções específicas do Azure AD B2C. Em seguida, pode carregar o conteúdo estático para uma localização disponível ao público para que possa aceder Azure AD B2C-lo. Isto pode ser o seu servidor web dedicado, armazenamento de Blobs do Azure, Azure rede de entrega de conteúdos ou qualquer outro estática recurso fornecedor de alojamento. São os requisitos apenas que o seu conteúdo está disponível através de HTTPS e pode ser acedido através de CORS. Assim que tiver expostos seu em conteúdo estático na web, pode editar a política de apontar para esta localização e apresentar esse conteúdo aos seus clientes. O [artigo de personalização da IU principal](active-directory-b2c-reference-ui-customization.md) descreve detalhadamente como funciona a funcionalidade de personalização do Azure AD B2C.

Para efeitos neste tutorial, podemos já criado algum conteúdo de exemplo e alojado-lo no armazenamento de Blobs do Azure. O conteúdo de exemplo é uma personalização muito básica no tema da nossa fictícia empresa, "Brinquedos Joaninha". Para experimentar na sua própria política, siga estes passos:

1. Inicie sessão no seu inquilino no [portal do Azure](https://portal.azure.com/) e navegue para a pá funcionalidades B2C.
2. Clique em **políticas de inscrição ou iniciar sessão** e, em seguida, clique na política de (por exemplo, "b2c\_1\_início de sessão\_o\_início de sessão\_no").
3. Clique em **personalização da IU de página** e, em seguida, **unificado a página de inscrição ou iniciar sessão**.
4. Activar ou desactivar a transição de **página personalizada utilizar** para **Sim**. No campo de **página personalizada URI** , introduza `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Clique em **OK**.
5. Clique em **página conta Local de inscrição**. Activar ou desactivar a transição de **utilizar o modelo personalizado** para **Sim**. No campo de **página personalizada URI** , introduza `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Repita o passo mesmo para a **página de inscrição de conta de rede Social**.
 Clique em **OK** duas vezes para fechar as lâminas de personalização da IU.
6. Clique em **Guardar**.

Agora, pode tentar a política personalizada. Pode utilizar a sua própria aplicação ou a parques Azure AD B2C se pretende, mas pode também simplesmente clicar no comando **Executar agora** na pá a política. Selecione a aplicação na caixa pendente e escolha o redirecionamento adequado URI. Clique no botão **Executar agora** . Será aberto um novo separador do browser e pode executar através a experiência de utilizador da inscrever-se para a sua aplicação com o novo conteúdo num local!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Carregar o conteúdo de exemplo para o armazenamento de Blobs do Azure

Se pretender utilizar o armazenamento de Blobs do Azure para alojar o seu conteúdo da página, pode criar a sua própria conta de armazenamento e utilize os nossos ferramenta de helper B2C para carregue os seus ficheiros.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **dados + armazenamento** > **conta de armazenamento**. Terá de uma subscrição do Azure para criar uma conta de armazenamento de Blobs do Azure. Pode inscrever-se de uma avaliação gratuita no [Web site Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Fornecer um **nome** para a conta de armazenamento (por exemplo, "contoso") e escolha as seleções adequadas para **os preços camada**, **grupo de recursos** e **subscrição**. Certifique-se de que tem a opção do **Pin para Startboard** selecionada. Clique em **Criar**.
4. Volte para a Startboard e clique na conta de armazenamento que acabou de criar.
5. Na secção de **Resumo** , clique em **contentores**e, em seguida, clique em **+ Adicionar**.
6. Forneça um **nome** para o contentor (por exemplo, "b2c") e selecione **Blob** como o **tipo de acesso**. Clique em **OK**.
7. O contentor que criou irão aparecer na lista de pá **Blobs** . Escreva o URL do contentor; Por exemplo, deve ser semelhante `https://contoso.blob.core.windows.net/b2c`. Feche o pá **Blobs** .
8. No pá de conta de armazenamento, clique **nas teclas** e anote os valores dos campos **Nome de conta de armazenamento** e a **Chave primária do Access** .

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **dados + armazenamento** > **conta de armazenamento**. Terá de uma subscrição do Azure para criar uma conta de armazenamento de Blobs do Azure. Pode inscrever-se de uma avaliação gratuita no [Web site Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Selecione o **Armazenamento de BLOBs** em **Tipo de conta**e deixe os outros valores como predefinição.  Pode editar o grupo de recursos e localização, se pretender.  Clique em **Criar**.
4. Volte para a Startboard e clique na conta de armazenamento que acabou de criar.
5. Na secção de **Resumo** , clique em **+ contentor**.
6. Forneça um **nome** para o contentor (por exemplo, "b2c") e selecione **Blob** como o **tipo de acesso**. Clique em **OK**.
7. Abra as **Propriedades**do contentor e anote o URL do contentor; Por exemplo, deve ser semelhante `https://contoso.blob.core.windows.net/b2c`. Feche o pá contentor.
8. No pá de conta de armazenamento, clique no **Ícone de chave** e anote os valores dos campos **Nome de conta de armazenamento** e a **Chave primária do Access** .

> [AZURE.NOTE]
    **Chave primária do Access** é uma credencial de segurança importantes.

### <a name="download-the-helper-tool-and-sample-files"></a>Transferir os ficheiros de ferramenta e exemplo helper

Pode transferir o [armazenamento de Blobs do Azure helper ferramenta e exemplo ficheiros como um ficheiro. zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloná-lo a partir de GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositório contém um `sample_templates\wingtip` diretório, que contém o exemplo HTML, CSS e imagens. Para obter estes modelos fazer referência a sua própria conta de armazenamento de Blobs do Azure, terá de editar os ficheiros HTML. Abrir `unified.html` e `selfasserted.html` e substituir as ocorrências da `https://localhost` com o URL do seu próprio contentor que escreveu para baixo nos passos anteriores. Tem de utilizar o caminho absoluto dos ficheiros HTML porque neste caso, o código HTML vai ser fornecido pelo Azure AD, sob o domínio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Carregar os ficheiros de exemplo

No mesmo repositório, deszipar `B2CAzureStorageClient.zip` e executar o `B2CAzureStorageClient.exe` dentro de ficheiros. Este programa será simplesmente carregar todos os ficheiros no diretório que especificou à sua conta de armazenamento e ativar o acesso CORS para os ficheiros. Se seguiu os passos acima, os ficheiros HTML e CSS agora a ser apontar para a sua conta de armazenamento. Tenha em atenção que o nome da sua conta de armazenamento é a parte que antecede `blob.core.windows.net`; Por exemplo, `contoso`. Pode verificar que o conteúdo foi carregado corretamente ao tentar aceder ao `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` num browser. Também utilize [http://test-cors.org/](http://test-cors.org/) para se certificar de que o conteúdo está agora CORS ativado. (Procure "Estado XHR: 200" no resultado.)

### <a name="customize-your-policy-again"></a>Personalizar a política, novamente

Agora que o conteúdo de exemplo que carregou a sua conta de armazenamento, tem de editar a política de inscrição para lhe fazer referência. Repita os passos da secção ["Personalizar a política de"](#customize-your-policy) acima, desta vez utilizando os URLs da sua conta de armazenamento. Por exemplo, a localização da sua `unified.html` ficheiro seria `<url-of-your-container>/wingtip/unified.html`.

Agora pode utilizar a sua própria aplicação ou no botão **Executar agora** executar a política de novamente. O resultado deverá ter um aspeto quase exatamente o mesmo – o que foi utilizado o mesmo de exemplo HTML e CSS em ambos os casos. No entanto, as políticas são agora referenciar o seu próprio instância do armazenamento de Blobs do Azure, e consoante esteja gratuito editar e carregar os ficheiros novamente à medida que introduza. Para obter mais informações sobre como personalizar o HTML e CSS, consulte o [artigo de personalização da IU principal](active-directory-b2c-reference-ui-customization.md).
