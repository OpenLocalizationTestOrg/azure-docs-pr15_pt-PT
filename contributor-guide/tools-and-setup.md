<properties
pageTitle="Instalar e configurar o ferramentas para a cocriação na GitHub"
description="Ferramentas e os passos para começar para criação de conteúdo Azure GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Instalar e configurar o ferramentas para a cocriação na GitHub

Siga os passos neste artigo para configurar o ferramentas para contribuir para a documentação técnica Azure. Os contribuintes informais e ocasionais provavelmente podem utilizar o GitHub IU descrita no passo 2.

Se não estiver familiarizado com Git, poderá pretender rever alguma terminologia Git: [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). Além disso, este tópico StackOverflow contém um glossário de termos Git que vai encontrar este conjunto de passos: [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Conteúdo

- [Criar uma conta de GitHub e configurar o seu perfil](#create-a-github-account-and-set-up-your-profile)
- [Inscrever-se no Disqus](#sign-up-for-disqus)
- [Determinar se necessita realmente a seguir os passos restantes](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Permissões no GitHub](#permissions-in-github)
- [Instalar o Git para Windows](#install-git-for-windows)
- [Ativar a autenticação de dois fatores](#enable-two-factor-authentication)
- [Instalar um editor de markdown](#install-a-markdown-editor)
- [Configurar Atom](#configure-atom)
- [Bifurcam do repositório e copiá-la para o seu computador](#fork-the-repository-and-copy-it-to-your-computer)
- [Configurar o seu nome de utilizador e e-mail localmente](#configure-your-user-name-and-email-locally)
- [Próximos passos](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Criar uma conta de GitHub e configurar o seu perfil

Para contribuir para o conteúdo técnico Azure, terá uma conta de [GitHub](http://www.github.com) .

Se for um contribuinte da Microsoft, tem de configurar a conta de GitHub, de modo que claramente identificadas como um empregado da Microsoft. Configure o seu perfil da seguinte forma:

- **Imagem de perfil**: uma imagem do que (obrigatório)
- **Nome**: o primeiro e último nome (obrigatório)
- **E-mail**: endereço de e-mail Microsoft (opcional)
- **Empresa**: Microsoft Corporation (obrigatório)
- **Localização**: listar a sua localização (opcional)

Seu perfil deve ser semelhante este perfil:

<p align="center">
 ![Exemplo de perfil GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Inscrever-se no Disqus

Cada artigo técnico Azure publicado tem uma sequência de comentário fornecida pelo serviço Disqus.

 ![Logótipo de discus](./media/tools-and-setup/discus.png)

Se for um funcionário da Microsoft e, se for o autor do ou um contribuinte para um artigo, tem de se inscrever para Disqus para poder participar na sequência do comentário para o artigo.

1. Inscrever-se para uma conta na [http://www.disqus.com/](http://www.disqus.com/)
2. Preencha o seu perfil da seguinte forma:

 - **Nome completo**: o seu nome completo, tal como é apresentada na sua lista de livro de endereços do Microsoft endereço, assim como as informações entre parênteses, que é o alias plus @MSFT. Formato: *primeiro último [alias@MSFT] *
 - **Localização**: A localização
 - **Breve intermédio**: O título

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Determinar se necessita realmente a seguir os passos restantes

Não poderá ter de seguir todos os passos neste artigo. Depende a ordenação de contribuição conteúda que pretende ou precisa de efetuar.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Submeter uma alteração só de texto para um artigo existente

Se só precisa de ou pretende fazer atualizações textuais para um artigo existente, provavelmente não terá de seguir os passos restantes. Pode utilizar o editor de baseada na web markdown do GitHub para submeter as suas alterações. Basta clicar na hiperligação de GitHub no artigo que pretende modificar:

 ![Exemplo de perfil GitHub](./media/tools-and-setup/contributetogit.png)

 Em seguida, clique no ícone de edição na versão GitHub do artigo

 ![Exemplo de perfil GitHub](./media/tools-and-setup/editicon.PNG)

 Que abre o editor de web de fácil utilização que torna mais fácil submeter as alterações. Não é necessário seguir os passos neste artigo.

###<a name="all-other-changes"></a>Todas as outras alterações
IU de GitHub suporta a criação de novos ficheiros e arrastar e largar imagens. No entanto, quando se trabalha na IU, gerir ramificações pode ser confuso por isso, normalmente, recomendamos que instale as ferramentas e saiba os comandos para criar e gerir artigos. Se pretender utilizar a IU, consulte o artigo:

- [Criar ficheiros no Github](https://github.com/blog/1327-creating-files-on-github)
- [Carregar ficheiros para o seu repositórios](https://github.com/blog/2105-upload-files-to-your-repositories)

A seguinte ordena de trabalho, recomendamos para instalar e saiba como utilizar as ferramentas:

 - Efectuar alterações importantes para um artigo
 - Criar e publicar um novo artigo
 - Adicionar novas imagens ou actualizar imagens
 - Atualizar um artigo durante um período de dias sem publicação alterações desses dias
 - Criação de conteúdo para um lançamento que tem para aceder a num determinado dia numa determinada altura

##<a name="permissions-in-github"></a>Permissões no GitHub

Qualquer pessoa com uma conta de GitHub pode contribuir para conteúdo técnico Azure através do nosso repositório público na [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Não existem permissões especial são necessário.

Se for um PM Microsoft ou sénior quem está a trabalhar no Azure o conteúdo, terá de trabalhar no nosso privado repositório de conteúdo, azure-conteúdo-pr. Visite [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) para pedir as permissões de leitura que lhe permitirão contribuições através de repo privada - iniciar sessão no GitHub utilizando o botão > clique Azure > clique **Join uma equipa** ou **participar outra equipa**e, em seguida, procure e aderir ao grupo **azure conteúdo leitura** .

## <a name="install-git-for-windows"></a>Instalar o Git para Windows

Instale Git para Windows a partir de [http://git-scm.com/download/win](http://git-scm.com/download/win). Esta transferência instala o sistema de controlo de versão Git e instala o Git festa, a aplicação da linha de comandos que irá utilizar para interagir com o seu repositório Git local.

Pode aceitar as predefinições; Se pretender que os comandos esteja disponível dentro da linha de comandos do Windows, selecione a opção que permite-lo.

<p align="center">
 ![Exemplo de perfil GitHub](./media/tools-and-setup/gitbashinstall.png)

(Nota: esta não é igual a "Github para Windows". "Github para Windows" é uma ferramenta com base no interface gráfica diferentes que também funcionam se pretender ler sobre si próprio. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Ativar a autenticação de dois fatores

Tem de ativar a autenticação de dois fatores (2FA) na sua conta do GitHub se estiver a trabalhar no repositório de conteúdo privado. É necessária a no repositório de privada.

Para ativar esta, siga as instruções em ambos os seguintes GitHub tópicos de ajuda:

- [Cerca autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/)
- [Criar um token de acesso para a utilização da linha de comandos](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Quando cria o token de, selecione todos os âmbitos disponíveis na criação de tokens IU ([para obter detalhes sobre cada âmbito](https://developer.github.com/v3/oauth/#scopes))

Depois de activar 2FA, tem de introduzir o token de acesso em vez da sua palavra-passe de GitHub na linha de comandos quando tentar aceder a um repositório de GitHub a partir da linha de comandos. O token de acesso não é o código de autenticação que obtém numa mensagem de texto quando configura 2FA. É uma cadeia longa que tenha este aspeto: fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Algumas notas sobre isto:

- Quando cria token de acesso, guardá-lo num ficheiro de texto para torná-lo facilmente acessível quando precisar dele.

- Mais tarde, quando precisar de colar o token, saberá existem duas formas para colar na linha de comandos:

 - Clique no ícone no canto superior esquerdo da janela de linha de comandos > Editar > colar.
 - Botão direito do rato no ícone no canto superior esquerdo da janela e clique em Propriedades > Opções > modo de edição rápida. Este procedimento configura da linha de comandos para que possa colar clicando na janela da linha de comandos.

## <a name="install-a-markdown-editor"></a>Instalar um editor de markdown

Vamos criar conteúdo utilizando a notação de simples "markdown" na ficheiros, em vez disso mais complexo "a marcação" (HTML, XML, etc.). Por isso, terá de instalar um editor de markdown.

- **Atom**: a maioria dos casos utiliza o editor de markdown do Atom do GitHub: [http://atom.io](http://atom.io). Não requer uma licença para utilização empresarial. -Possui verificação ortográfica.

- **Bloco de notas**: pode utilizar o bloco de notas para uma opção muito lightweight.

- **Prose**: Este é um lightweight, elegante, online e abrir markdown editor de código que oferece uma pré-visualização. Visite [http://prose.io](http://prose.io) e autorizar Prose no seu repositório.

- **[Código do visual Studio](https://www.visualstudio.com/products/code-vs.aspx)** - entrada da Microsoft neste espaço.

## <a name="configure-atom"></a>Configurar Atom

Se utilizar Atom, terá de configurar algumas coisas.

- Atom assume a predefinição de utilizando 2 espaços para os separadores, mas Markdown espera 4 espaços. Se deixá-la com o valor predefinido de dois, um artigo será o aspeto fantástico na pré-visualização do local, mas não quando-é importada para Azure. Por isso, configurar Atom para utilizar 4 espaços - pode encontrar esta definição em ficheiro > Definições > definições do Editor > separador comprimento.
- Provavelmente será também pretende ativar moldar contornos nesta secção demasiado, que é que o mesmo que "moldagem de texto" no bloco de notas.
- Para ativar a pré-visualização de markdown, clique em pacotes > pré-visualização do Markdown > pré-visualização do botão de alternar. Pode utilizar Ctrl-Shift-M para ativar/desativar a vista de HTML de pré-visualização.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Bifurcam do repositório e copiá-la para o seu computador

1. Criar uma bifurcação do repositório no GitHub - Ir para o canto superior direito da página e clique no botão bifurcação. Se lhe for pedido, selecione a sua conta como a localização onde a bifurcação deve ser criada. Esta ação cria uma cópia do repositório dentro da sua conta Git concentrador. Regra geral, escritores técnicos e gestores de programa necessitam bifurcação azure-conteúdo-pr, o repo privada. Precisa de bifurcação azure conteúdo, o público repo contribuintes da Comunidade. Só precisa de bifurcam uma vez; após a primeira configuração, se pretender copiar os seus bifurcação para outro computador, só tem de executar os comandos que se seguem nesta secção para copiar o repo para o seu computador.  Se optar por criar bifurcações de ambos os repositórios, terá de criar uma bifurcação para cada repositório.

2. Copie o pessoal acesso Token que recebeu do [https://github.com/settings/tokens](https://github.com/settings/tokens). Pode aceitar as permissões predefinidas para o token.  Guarde o Token de acesso pessoais num ficheiro de texto para reutilizar posteriormente.

3. Em seguida, copie o repositório de para o seu computador com as suas credenciais incorporadas a cadeia de comandos.  Para fazer isto, abra Git festa e executá-lo como um administrador. Na linha de comandos, introduza o seguinte comando.  Este comando cria um diretório de azure-content(-pr) no seu computador.  Se estiver a utilizar a localização predefinida, este será no c:\users<your Windows user name>\azure-content(-pr).

Repo público:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Privado repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Por exemplo, este comando clonar poderia ter um aspeto da seguinte forma:

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Ligação do repositório remoto e configurar credenciais

Crie uma referência para o repositório de raiz introduzindo estes comandos. Isto configura ligações para o repositório no GitHub para que possa obter as alterações mais recentes no seu computador local e transmitir as suas alterações novamente para GitHub. Este comando também configura o token localmente para que não tenha que introduzir o seu nome e palavra-passe cada vez que tenta aceder a repo montante e o seu bifurcação no GitHub.

Repo público:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Privado repo:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Este botão leva normalmente tempo. Após fazer isto, que não tem de bifurcam novamente ou introduza novamente as suas credenciais. Apenas teria que copiar as bifurcações num computador local novamente se configurado as ferramentas de noutro computador.


## <a name="configure-your-user-name-and-email-locally"></a>Configurar o seu nome de utilizador e e-mail localmente

Para assegurar que está listados corretamente como um contribuinte, tem de configurar o seu nome de utilizador e e-mail localmente no Git.

1. Inicie o Git festa e mudar para o conteúdo do azure ou azure-conteúdo-pr:

   ````
   cd azure-content
   ````

 ou

   ````
   cd azure-content-pr
   ````

2. Configure o seu nome de utilizador para que corresponda ao nome do seu à medida que configurou-o no seu perfil GitHub:

    ````
    git config --global user.name "John Doe"
    ````
3. Configurar o seu e-mail para que corresponda ao e-mail principal designado no seu perfil GitHub; Se for um empregado MSFT, deverá ser o endereço de e-mail MSFT:

    ````
    git config --global user.email "alias@example.com"
    ````
4. Tipo de `git config -l` e reveja as definições do locais para garantir que o nome de utilizador e correio eletrónico na configuração estão corretas.

##<a name="next-steps"></a>Próximos passos

- Compreender o tipo de conteúdo que pertence a repo conteúdo técnico e saber o que não pertence. Ver as [orientações canal conteúdo](./content-channel-guidance.md)!
- Siga [estes passos para criar ou modificar um artigo e, em seguida, submeter a mesma para publicação](./git-commands-for-master.md).
- Copie [o modelo de markdown](../markdown templates/markdown-template-for-new-articles.md) como base para um artigo de novo.
- Utilize [Esta lista de verificação para verificar o seu pedido de solicitação cumprem os critérios de qualidade](./contributor-guide-pr-criteria.md) para intercalação.


###<a name="contributors-guide-navigation"></a>Navegação de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
