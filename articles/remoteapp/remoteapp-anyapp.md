<properties
   pageTitle="Executar qualquer aplicação do Windows em qualquer dispositivo com RemoteApp do Azure | Microsoft Azure"
   description="Saiba como partilhar qualquer aplicação do Windows com os seus utilizadores utilizando RemoteApp do Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Executar qualquer aplicação do Windows em qualquer dispositivo com RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Pode executar uma aplicação do Windows em qualquer lugar em qualquer dispositivo, neste momento, seriamente - utilizando RemoteApp do Azure. Se se trata de uma aplicação personalizada escritos 10 anos atrás ou uma aplicação do Office, os utilizadores já não tem de ser associados a um sistema operativo (como o Windows XP) para essas algumas aplicações.

Com RemoteApp do Azure, os utilizadores também podem utilizar os seus próprios dispositivos Android ou Apple e obter a mesma experiência que tem no Windows (ou nos telemóveis Windows). Isto é feito por alojamento da sua aplicação do Windows numa coleção de máquinas virtuais de Windows no Azure - os utilizadores podem aceder aos mesmos a partir de qualquer lugar que tenham uma ligação à internet. 

Continue a ler para um exemplo de exatamente como o fazer.

Neste artigo, iremos de partilhar o acesso com todos os nossos utilizadores. No entanto, pode utilizar qualquer aplicação. Desde que pode instalar a aplicação num computador Windows Server 2012 R2, pode partilhá-lo utilizando os passos abaixo. Pode rever os [requisitos da aplicação](remoteapp-appreqs.md) para se certificar de que a sua aplicação irá funcionar.

Tenha em atenção que porque o Access é uma base de dados e queremos nessa base de dados para ser útil, podemos vai ser efetuar alguns passos adicionais para permitir que o acesso de utilizadores a partilha de dados do Access. Se a sua aplicação não é uma base de dados ou não precisa dos utilizadores possam aceder a uma partilha de ficheiros, pode ignorar esses passos neste tutorial

> [AZURE.NOTE] <a name="note"></a>Precisa de uma conta Azure para concluir este tutorial:
> - Pode [Abrir uma conta Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): obter o créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que estão habituados até pode manter a conta e utilização livre Azure serviços, como sites. O cartão de crédito nunca será cobrado, exceto se alterar as definições e pedir para ser cobrada explicitamente.
> - Pode [Ativar benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): subscrição da sua MSDN fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.


## <a name="create-a-collection-in-remoteapp"></a>Criar uma coleção de no RemoteApp

Comece por criar uma coleção de. A coleção de funciona como um contentor para as suas aplicações e utilizadores. Cada uma das coleções baseia-se uma imagem - pode criar os seus próprios ou utilize um fornecida com a sua subscrição. Para este tutorial, estamos a utilizar a imagem de avaliação do Office 2013 - contém a aplicação que recomendamos pretende partilhar.

1. No portal do Azure, desloque-se para baixo na árvore de navegação do lado esquerdo até ver RemoteApp. Abra essa página.
2. Clique em **criar uma coleção de RemoteApp**.
3. Clique em **criação rápida** e introduza um nome para a coleção.
4. Selecione a região que pretende utilizar para criar a coleção. Para obter a melhor experiência, selecione a região que é o mais próximo geograficamente para a localização onde os utilizadores irão aceder à aplicação. Por exemplo, neste tutorial, os utilizadores irão estar localizados em Redmond, Washington. A região Azure mais próxima é **US Ocidental**.
5. Selecione o plano de faturação que pretende utilizar. O plano de faturação básico coloca 16 utilizadores numa grande VM Azure, enquanto o plano de faturação padrão tiver 10 utilizadores numa VM Azure grande. Como um exemplo geral, o plano básico funciona Grã para fluxo de trabalho de tipo de entrada de dados. Para uma aplicação de produtividade, como o Office, teria pretende que o plano padrão.
6. Por fim, selecione a imagem do Office 2013 Professional. Esta imagem contém aplicações do Office 2013. Apenas um lembrete - esta imagem só é ideal para POCs e coleções de avaliação. ' Não é possível utilizar esta imagem numa coleção de produção.
7. Agora, clique em **Criar RemoteApp coleções de sites**.

![Criar uma coleção de nuvem no RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Esta ação inicia criar a sua coleção de, mas pode demorar até numa hora.

Agora está pronto para adicionar os seus utilizadores.

## <a name="share-the-app-with-users"></a>Partilhar a aplicação com utilizadores

Assim que a sua coleção de ter sido criada com êxito, é altura de publicar o acesso a utilizadores e adicionar utilizadores aos quais devem ter acesso à mesma.

Se navega afastando-o nó do Azure RemoteApp enquanto a coleção de estava a ser criada, comece por fazer a sua forma voltar à mesma a partir da home page do Azure.

2. Clique em coleção criou anteriores para aceder a opções adicionais e configurar a coleção.
![Uma nova coleção de nuvem RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. No separador **Publicar** , clique em **Publicar** na parte inferior do ecrã e, em seguida, clique em **programas do menu Iniciar publicar**.
![Publicar um programa RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selecione as aplicações que pretende publicar a partir da lista. Para o nosso objetivo, optamos por Access. Clique em **concluído**. Espere que as aplicações concluir a publicação.
![Acesso a publicação no RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Assim que a aplicação terminar publicação, cabeça ao longo para o separador de **Acesso do utilizador** para adicionar todos os utilizadores que precisam de aceder às suas aplicações. Introduza os nomes de utilizador (endereço de e-mail) para os seus utilizadores e, em seguida, clique em **Guardar**.

![Adicionar utilizadores ao RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Agora, é hora para informar os utilizadores sobre estas novas aplicações e sobre como aceder aos mesmos. Para fazer isto, envie os seus utilizadores uma mensagem de e-mail-los a apontar para o URL de transferência do cliente de ambiente de trabalho remoto.
![O cliente de transferir o URL para RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurar o acesso ao Access

Algumas aplicações necessária configuração extra depois de implementá-los através de RemoteApp. Em particular, para aceder à, iremos para criar uma partilha de ficheiros no Azure qualquer utilizador pode aceder. (Se não quiser fazê-lo, pode criar uma [coleção de híbrido](remoteapp-create-hybrid-deployment.md) [em vez dos nossos coleção nuvem] que permite aos utilizadores aceder a ficheiros e as informações na sua rede local.) Em seguida, irá precisamos de indicar ao nossos utilizadores para mapear uma unidade local no seu computador para o sistema de ficheiros Azure.

A primeira parte como administrador do fizer. Em seguida, temos alguns passos para os seus utilizadores.

1. Comece por publicação na interface de linha de comandos (cmd.exe). No separador **Publicar** , selecione **cmd**e, em seguida, clique em **Publicar > publicar programa utilizando caminho**.
2. Introduza o nome da aplicação e o caminho. Para o nosso objetivo, utilize "Explorador de ficheiros" como o nome e "% SYSTEMDRIVE%\windows\explorer.exe" como o caminho.
![Publica o ficheiro cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Agora, precisa de criar uma [conta de armazenamento](../storage/storage-create-storage-account.md)de Azure. Que chamamos om "accessstorage", por isso, escolha um nome que tenha significado para si. (Para misquote Highlander, podem existir apenas uma "accessstorage.") ![Conta de armazenamento do our Azure](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Agora volte para o dashboard para que possa receber o caminho para o seu armazenamento (localização de ponto final). Irá Utilize esta opção num pouco, por isso, certifique-se de que copiá-lo num local.
![O caminho de conta de armazenamento](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. A seguir, assim que a conta de armazenamento ter sido criada, tem a chave primária de acesso. Clique em **Gerir as teclas de acesso**e, em seguida, copie a chave primária de acesso.
6. Agora, defina o contexto da conta de armazenamento e criar uma nova partilha de ficheiros para o Access. Execute os seguintes cmdlets numa janela do Windows PowerShell elevada:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Por isso, para os nossos partilhar, estes são os cmdlets que vamos executar:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Está agora a ativar o utilizador. Em primeiro lugar, tem os seus utilizadores instalar um [cliente RemoteApp](remoteapp-clients.md). Em seguida, os utilizadores tem de mapear uma unidade a partir da sua conta para essa partilha de ficheiros Azure que criou e adicionar os seus ficheiros do Access. Este é como eles fazem-lo:

1. No cliente RemoteApp, o access publicadas aplicações. Inicie o programa cmd.exe.
2. Execute o seguinte comando para mapear uma unidade do seu computador para a partilha de ficheiros:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Se definir o parâmetro **/ persistente** para Sim, a unidade mapeada vai permanecer através de sessões.
1. Agora, inicie a aplicação de Explorador de ficheiros a partir do RemoteApp. Copie os ficheiros de acesso que pretende utilizar na aplicação partilhada para a partilha de ficheiros.
![Colocação aceder a ficheiros de uma partilha Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Por fim, abra o Access e, em seguida, abra a base de dados que acabou de partilhar. Deverá visualizar dados no Access a executar a partir da nuvem.
![Uma base de dados do Access real executar a partir da nuvem](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Agora pode utilizar o Access em qualquer um dos seus dispositivos - apenas Certifique-se de que instala um cliente de RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Agora que já conhece a criação de uma coleção de, experimente criar uma [coleção de que utiliza o Office 365](remoteapp-tutorial-o365anywhere.md). Ou pode criar uma [coleção de híbrido ](remoteapp-create-hybrid-deployment.md)pode aceder à sua rede local.

<!--Image references-->
 
