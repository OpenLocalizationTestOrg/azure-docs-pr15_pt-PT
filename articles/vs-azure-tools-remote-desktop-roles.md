<properties 
   pageTitle="Utilizar o ambiente de trabalho remoto com o Azure funções | Microsoft Azure"
   description="Utilizar o ambiente de trabalho remoto com o Azure funções"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-remote-desktop-with-azure-roles"></a>Utilizar o ambiente de trabalho remoto com o Azure funções

Ao utilizar o SDK do Azure e serviços de ambiente de trabalho remoto, pode aceder a funções Azure e máquinas virtuais que são alojadas por Azure. No Visual Studio, pode configurar os serviços de ambiente de trabalho remoto a partir de um projeto Azure. Para permitir que os serviços de ambiente de trabalho remoto, tem de criar um projeto de trabalho que contém uma ou mais funções e, em seguida, publique-o Azure.

>[AZURE.IMPORTANT] Deverá aceder a uma função para a resolução de problemas ou desenvolvimento apenas Azure. A finalidade de cada máquina virtual é executar uma função específica na aplicação do Azure, não para executar outras aplicações de cliente. Se pretender utilizar o Azure para alojar uma máquina de virtual que pode utilizar para qualquer efeito, consulte o artigo aceder o máquinas virtuais do Azure a partir do Explorador de servidor.

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Para ativar e utilizar o ambiente de trabalho remoto para uma função do Azure

1. No Explorador de solução, abra o menu de atalho para o seu projeto e, em seguida, selecione **Publicar**.

    Aparece o Assistente da **Aplicação de Azure publicar** .

    ![Publicar o comando de um projeto do serviço na nuvem](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Na parte inferior da página de **Definições de publicar do Microsoft Azure** do assistente, selecione o **Ambiente de trabalho remoto ativar** todas as funções caixa de verificação. 

    É apresentada a caixa de diálogo de **Configuração do ambiente de trabalho remoto** .

1. Na parte inferior da caixa de diálogo de **Configuração do ambiente de trabalho remoto** , selecione o botão **Mais opções** . 
 
    Isto apresenta uma caixa de listagem pendente que permite-lhe criar ou escolha um certificado para que pode encriptar informações de credenciais quando ligar através do ambiente de trabalho remoto.

1. Na lista pendente, selecione ** &lt;criar >**, ou escolha uma existente a partir da lista. 

    Se escolher um certificado existente, ignore os passos seguintes.

    >[AZURE.NOTE] Os certificados que precisa para uma ligação de ambiente de trabalho remoto são diferentes dos certificados utilizados para outras operações do Azure. O certificado de acesso remoto tem de ter uma chave privada.

    É apresentada a caixa de diálogo **Criar certificado** .

    1. Fornecer um nome amigável para o novo certificado e, em seguida, selecione o botão **OK** . O novo certificado é apresentada na caixa de lista pendente.

    1. Na caixa de diálogo **Configuração do ambiente de trabalho remoto** , fornece um nome de utilizador e uma palavra-passe.
    
        Não pode utilizar uma conta existente. Não especifique administrador como o nome de utilizador para a nova conta.

        >[AZURE.NOTE] Se a palavra-passe não cumprir os requisitos de complexidade, aparece um ícone vermelho ao lado da caixa de texto palavra-passe. A palavra-passe tem de incluir letras maiúsculas, minúsculas e números ou símbolos.

    1. Escolha uma data no qual a conta irá expirar e depois quais as ligações de ambiente de trabalho remotas serão bloqueadas.

    1. Após que forneceu todas as informações necessárias, selecione o botão **OK** .
    
        Existem várias definições que permitem serviços de acesso remoto são adicionadas aos ficheiros .cscfg e .csdef.

1. No Assistente de **Definições de publicar do Microsoft Azure** , selecione o botão **OK** quando estiver pronto para publicar o seu serviço de nuvem.

    Se não estiver pronto para publicar, selecione o botão **Cancelar** . As definições de configuração são guardadas e pode publicar o seu serviço de nuvem mais tarde.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ligar a uma função de Azure utilizando o ambiente de trabalho remoto

Depois de publicar o seu serviço de nuvem no Azure, pode utilizar o Explorador de servidor que inicie sessão nas máquinas virtuais a que aloja Azure. 

1. No Explorador do servidor, expanda o nó do **Azure** e, em seguida, expanda o nó para um serviço na nuvem e uma das suas funções para apresentar uma lista de instâncias.

1. Abrir o menu de atalho para um nó instância e, em seguida, selecione **Ligar-se utilizar o ambiente de trabalho remoto**.

    ![Ligar através do ambiente de trabalho remoto](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Introduza o nome de utilizador e palavra-passe que criou anteriormente. Agora tem sessão iniciada no seu sessão remota.


