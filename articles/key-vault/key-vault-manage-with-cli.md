<properties
    pageTitle="Gerir Cofre chave utilizando clip | Microsoft Azure"
    description="Utilizar este tutorial para automatizar tarefas comuns no chave cofre utilizando o clip"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Gerir Cofre chave utilizando clip #
Azure chave Cofre está disponível na maioria das regiões. Para mais informações, consulte o artigo [chave cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Utilize este tutorial para ajudá-lo a introdução ao Azure chave cofre para criar um contentor puro (cofre) no Azure, armazenar e gerir chaves de criptografia e segredos no Azure. Orienta-o através do processo de utilizar a Interface de comandos do Azure em diferentes plataformas para criar um cofre que contém uma chave ou palavra-passe que, em seguida, pode utilizar com uma aplicação do Azure. -Em seguida, mostra-lhe como uma aplicação, em seguida, pode utilizar essa tecla ou palavra-passe.

**Estimativa tempo a concluir:** 20 minutos

>[AZURE.NOTE]  Neste tutorial não inclui instruções sobre como escrever a aplicação Azure que um destes passos inclui, que mostra como autorizar uma aplicação para utilizar uma chave ou palavra-passe no cofre chave.
>
>Atualmente, não é possível configurar Azure chave cofre no portal do Azure. Em alternativa, utilize estas instruções de Interface de comandos em diferentes plataformas. Em alternativa, para obter instruções Azure PowerShell, consulte o artigo [Este tutorial equivalente](key-vault-get-started.md).

Para obter informações gerais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, tem de ter o seguinte procedimento:

- Uma subscrição do Microsoft Azure. Se não tiver uma, pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
- Interface de comandos versão 0.9.1 ou posterior. Para instalar a versão mais recente e ligar à sua subscrição do Azure, consulte o artigo [instalar e configurar a Interface de comandos do Azure em diferentes plataformas](../xplat-cli-install.md).
- Uma aplicação que irá estar configurada para utilizar a chave ou palavra-passe que criou neste tutorial. Uma aplicação de exemplo está disponível a partir do [Centro de transferências da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o artigo o ficheiro Leia-me acompanhamento.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obter ajuda com Interface de comandos do Azure em diferentes plataformas

Neste tutorial assume que está familiarizado com a interface de linha de comandos (festa, terminais, linha de comandos)

--Parâmetro ajuda ou -h pode ser utilizado para ver ajuda para comandos específicos. Em alternativa, a azure ajuda [comando], [opções] formato também pode ser utilizado para devolver as mesmas informações. Por exemplo, todos os seguintes comandos devolvem as mesmas informações:

    azure account set --help

    azure account set -h

    azure help account set

Se estiver na dúvida acerca dos parâmetros necessários por um comando, consulte a ajuda do utilizando – ajuda, -h ou azure [comando].

Também pode ler os seguintes tutoriais para se familiarizar com o Gestor de recursos do Azure na Interface de comandos do Azure em diferentes plataformas:

- [Como instalar e configurar a Interface de linha de comandos do Azure em diferentes plataformas](../xplat-cli-install.md)
- [Utilizar Interface de comandos do Azure em diferentes plataformas com o Gestor de recursos Azure](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Ligar a suas subscrições

Para iniciar sessão com uma conta institucional, utilize o seguinte comando:

    azure login -u username -p password

ou, se pretender iniciar sessão ao escrever de forma interativa

    azure login

>[AZURE.NOTE]  O método de início de sessão só funciona com a conta institucional. Uma conta institucional é um utilizador que é gerido pela sua organização e definido no inquilino do Azure Active Directory da sua organização.


Se não possui atualmente uma conta institucional e estiver a utilizar uma conta Microsoft para iniciar sessão na sua subscrição do Azure, pode facilmente criar um utilizando os passos seguintes.

1.  Início de sessão para o início de sessão para o [Portal de gestão do Azure](https://manage.windowsazure.com/)e clique no Active Directory.
2.  Se nenhum directório não existir, selecione criar o seu diretório e forneça as informações pedidas.
3.  Selecione o seu diretório e adicionar um novo utilizador. Este novo utilizador é uma conta institucional. Durante a criação do utilizador, serão fornecidas com ambos os um endereço de e-mail para o utilizador e uma palavra-passe temporária. Guarde estas informações como que é utilizado no outro passo.
4.  A partir do portal de, selecione definições e, em seguida, selecione os administradores. Selecione adicionar e adicionar o novo utilizador como administrador de cocriação. Esta opção permite-a conta institucional gerir a sua subscrição do Azure.
5.  Por fim, inicie sessão no portal do Azure e, em seguida, inicie novamente sessão com a nova conta institucional. Se esta for a primeira vez iniciar sessão com esta conta, ser-lhe-á pedido para alterar a palavra-passe.

Para mais informações sobre como utilizar uma conta institucional com Microsoft Azure, consulte o artigo [Inscrever-se no Microsoft Azure como uma organização](../active-directory/sign-up-organization.md).

Se tem múltiplas subscrições e pretende especificar um específico a utilizar para Azure chave cofre, escreva o seguinte para ver as subscrições para a sua conta:

    azure account list

Em seguida, para especificar a subscrição para utilizar, escreva:

    azure account set <subscription name>

Para mais informações sobre como configurar a Interface de comandos do Azure em diferentes plataformas, consulte o artigo [como instalar e configurar a Interface de comandos do Azure em diferentes plataformas](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Mudar através do Gestor de recursos do Azure

O Cofre chave requer o Gestor de recursos do Azure, por isso, escreva o seguinte para mudar para o Gestor de recursos do Azure modo:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Quando utilizar o Gestor de recursos do Azure, todos os recursos relacionados são criados no interior de um grupo de recursos. Vamos irá criar um novo grupo de recursos 'ContosoResourceGroup' para este tutorial.

    azure group create 'ContosoResourceGroup' 'East Asia'

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é a localização. Para a localização, utilize o comando `azure location list` para identificar como especificar uma localização alternativa neste exemplo. Se precisar de mais informações, escreva:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registar o fornecedor de recursos do cofre chave
Certifique-se de que fornecedor de recursos do cofre chave está registado na sua subscrição:

`azure provider register Microsoft.KeyVault`

Só necessita de ser executadas uma vez por subscrição.


## <a name="create-a-key-vault"></a>Criar uma chave Cofre

Utilizar o `azure keyvault create` comando para criar uma chave cofre. Este script tem três parâmetros obrigatórios: o nome de um grupo de recursos, um nome de chave cofre e a localização geográfica.

Por exemplo, se utilizar o nome do cofre do ContosoKeyVault, o nome do grupo de recursos de ContosoResourceGroup e a localização da Ásia, escreva:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

O resultado deste comando mostra as propriedades do cofre chave que acabou de criar. As duas propriedades mais importantes são:

- **Nome**: no exemplo seguinte é ContosoKeyVault. Irá utilizar este nome para outras cmdlets chave cofre.
- **vaultUri**: no exemplo seguinte é https://contosokeyvault.vault.azure.net. As aplicações que utilizam cofre através do seu REST API tem de utilizar este URI.

A conta Azure agora está autorizada a efetuar qualquer operações deste cofre chave. Ainda mais ninguém é.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou palavra-passe ao Cofre de palavras chave

Se pretender Azure chave cofre para criar uma chave de software protegido por si, utilize o `azure key create` comando e escreva o seguinte:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

No entanto, se tiver uma chave existente num ficheiro .pem guardado como ficheiro local num ficheiro denominado softkey.pem que pretende carregar para o Azure chave cofre, escreva o seguinte para importar a chave a partir de. Ficheiro PEM, protege a chave por software no serviço do Cofre de chave:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Agora pode fazer referência a chave que criado ou carregado para Azure chave cofre, utilizando o respetivo URI. Utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual e utilize **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar uma palavra-passe ao cofre, que é uma palavra-passe com o nome SQLPassword e que tem o valor de Pa$ $w0rd Azure chave cofre, escreva o seguinte:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Agora pode fazer referência a esta palavra-passe que adicionou a Azure chave cofre, utilizando o respetivo URI. Utilizar **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e utilize **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vamos ver a chave ou palavra-passe que acabou de criar:

- Para ver a sua chave, escreva:`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Para ver a sua palavra-passe, escreva:`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Registe-se uma aplicação com o Azure Active Directory

Este passo faria normalmente ser feito por um programador, num computador diferente. Esta não é específica a Azure chave cofre mas é aqui, incluída para integridade.


>[AZURE.IMPORTANT] Para concluir o tutorial, sua conta, o Cofre e a aplicação que irá registar neste passo têm de ser todos no mesmo directório Azure.

As aplicações que utilizam um cofre chave tem de autenticar utilizando um token a partir do Azure Active Directory. Para fazer isto, o proprietário da aplicação primeiro tem de registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:


- Um **ID da aplicação** (também conhecido como um ID de cliente) e a **chave de autenticação** (também conhecido como o segredo partilhado). A aplicação tem de apresentar ambos estes valores ao Azure Active Directory, para obter um token. Como a aplicação está configurada para fazer isto depende da aplicação. Para a aplicação de exemplo do cofre chave, o proprietário da aplicação define estes valores no ficheiro App.



Para registar a aplicação no Azure Active Directory:

1. Inicie sessão no portal do Azure.
2. À esquerda, clique em **Active Directory**e, em seguida, selecione a pasta na qual irá registar a aplicação. <br> <br> Nota: Tem de selecionar o diretório mesmo que contém a subscrição Azure com as quais criou cofre chave. Se não souber qual o directório isto é, clique em **Definições**, identificar a subscrição com a qual criou cofre chave e anote o nome do directório mostrado na última coluna.

3. Clique em **aplicações**. Se não aplicações que foram adicionadas ao seu diretório, esta página irá mostrar apenas a ligação **Adicionar uma aplicação** . Clique na ligação ou em alternativa, pode clicar para **Adicionar** na barra de comandos.
4.  No assistente **Adicionar aplicação** , na **o que pretende fazer?** página, clique em **Adicionar uma aplicação que está a desenvolver a minha organização**.
5.  Na página **diga-nos sobre a aplicação** , especifique um nome para a sua aplicação e selecione **E/ou de aplicações WEB WEB API** (a predefinição). Clique no ícone seguinte.
6.  Na página **Propriedades da aplicação** , especifique o **Início de sessão no URL** e a **Aplicação ID URI** para a aplicação web. Se a aplicação não tiver estes valores, pode torná-las para este passo (por exemplo, pode especificar http://test1.contoso.com para ambas as caixas). Não importa se existirem estes sites; o que é importante é que a aplicação ID URI para cada aplicação é diferente para todas as aplicações no seu diretório. Diretório utiliza esta cadeia para identificar a sua aplicação.
7.  Clique no ícone de concluído para guardar as alterações no assistente.
8.  Na página de início rápido, clique em **Configurar**.
9.  Desloque-se para a secção **chaves** , selecione a duração e, em seguida, clique em **Guardar**. A página atualiza e agora mostra um valor de chave. Tem de configurar a aplicação com este valor de chave e o valor de **ID de cliente** . (As instruções para esta configuração será específicos da aplicação.)
10. Copie o valor de ID de cliente nesta página, que irá utilizar no próximo passo para definir permissões no seu cofre.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorize a aplicação para utilizar a chave ou palavra-passe

Para autorizar a aplicação para aceder à chave ou palavra-passe no cofre, utilize o `azure keyvault set-policy` comando.

Por exemplo, se o seu nome de cofre é ContosoKeyVault e a aplicação que pretende autorizar tem um ID de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e pretende autorizar a aplicação para desencriptar e inicie sessão com as teclas no cofre, em seguida, execute o seguinte:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Se estiver a utilizar na linha de comandos do Windows, deve substitua plicas aspas e também escape as aspas duplas internas. Por exemplo: "[\"desencriptar\",\"início de sessão\"]".

Se pretender autorizar nesse mesmo pedido ler segredos no cofre, execute o seguinte:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se pretender utilizar um módulo de segurança do hardware (HSM) ##

Para assurance adicionada, pode importar ou gerar chaves nos módulos de segurança de hardware (Os HSMs) que nunca deixe o limite da HSM. Os HSMs são 140-2 nível 2 validados FIPS. Se este requisito não se aplica a si, ignorar esta secção e aceda ao [Eliminar o Cofre chave e chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar estas teclas protegida HSM, tem de ter uma subscrição do cofre que suporta as chaves de protegida HSM.

Quando cria a keyvault, adicione o parâmetro 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Pode adicionar protegido por software chaves (como mostrado anteriormente) e em protegida HSM a deste cofre. Para criar uma tecla protegida HSM, defina o parâmetro de destino para 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Pode utilizar o seguinte comando para importar uma chave de um ficheiro de .pem no seu computador. Este comando importa a chave para os HSMs no serviço do Cofre de chave:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

O comando seguinte importa "sua própria chave trazer" pacote (BYOK). Permite-lhe gerar a chave no seu HSM local e transferi-lo para os HSMs no serviço de chave cofre, sem a chave de sair o limite da HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Para obter informações mais detalhadas sobre como gerar este pacote BYOK, veja [como utilizar teclas HSM-Protected com Azure chave cofre](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Elimine a chave cofre e chaves associadas e segredos

Se já não precisar do cofre chave e a chave ou secreta nela contidos, pode eliminar o Cofre chave utilizando o comando Eliminar do azure keyvault:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Ou, pode eliminar um grupo de recursos de Azure inteira, que inclui o Cofre chave e outros recursos que incluídas nesse grupo:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos de Interface de comandos em diferentes plataformas Azure

Outros comandos que também poderá ser útil para gerir Azure chave cofre.

Este comando lista uma visualização tabular de todas as teclas e propriedades selecionadas:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Este comando apresenta uma lista completa das propriedades para a chave especificada:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Este comando lista uma apresentação em tabela de todos os nomes secretos e propriedades seleccionadas:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Eis um exemplo de como remover uma chave específica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Eis um exemplo de como remover uma palavra-passe específica:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Próximos passos

Para programação referências, consulte o artigo [guia o programador Azure chave cofre](key-vault-developers-guide.md).
