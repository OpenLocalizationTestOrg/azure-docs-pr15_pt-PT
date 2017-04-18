
<properties 
    pageTitle="Como utilizar a sua subscrição do Office 365 com RemoteApp do Azure | Microsoft Azure"
    description="Saiba como pode utilizar a sua subscrição do Office 365 no Azure RemoteApp para partilhar as aplicações do Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Como utilizar a sua subscrição do Office 365 com RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Sabia que pode utilizar a subscrição do Office 365 existente no Azure RemoteApp para partilhar as aplicações do Office a partir da nuvem? Continue a ler para obter informações sobre o Office 365 + Azure RemoteApp opções, incluindo ligações para artigos sobre o Office 365 que o ajudam a tornar o máximo partido da sua subscrição.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Como posso utilizar contas do Office 365 para RemoteApp do Azure?
Consulte o artigo de novo Pedro para todas as informações: [como utilizar o Azure RemoteApp com contas de utilizador do Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Pode utilizar subscrição do Office 365 para executar as aplicações do Office no Azure RemoteApp?

Sim! Utilizar a sua subscrição do Office 365 é de facto, a única forma de cumprir as suas aplicações do Office para RemoteApp do Azure.

(Nota: se a sua implementação do Azure RemoteApp é entregue por um parceiro alojamento, poderá fornecer-lhe licenças do Office com base num [Contrato de licença do fornecedor de serviço](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


A vantagem acerca da sua subscrição do Office 365 é que permite-lhe utilizar a mesmo licença de utilizador no muitos diferentes plataformas e ambientes, incluindo a nuvem Azure. Quando utilizar as aplicações do Office no Azure RemoteApp não tem de adquirir licenças adicionais ou configurar as licenças existentes de qualquer forma especial. Tudo o que necessita é uma subscrição do Office 365 que inclui o [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus permite a [ativação de computador partilhado](https://technet.microsoft.com/library/Dn782860.aspx) - esta funcionalidade permite temporária ativação baseadas no utilizador para o Office no virtual e ambientes de nuvem, como o Azure RemoteApp (e serviços de ambiente de trabalho remoto).

Que planos do Office 365 incluem o Office 365 ProPlus? Consulte a tabela de [disponibilidade do serviço dentro de cada um dos planos](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Tenha em atenção que não em todos os planos incluem o Office 365 ProPlus (por exemplo, o plano do Office 365 empresas). Se o seu plano não tiver, considere efetuar a atualização para um plano que faz (por exemplo, Office 365 educação E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>OK, então como são as minhas do Office 365 ProPlus licenças utilizadas com RemoteApp do Azure?

Cada licença de utilizador para o Office 365 ProPlus permite um único utilizador ativar as aplicações do Office em até 5 computadores plus tablets e telemóveis. Cada ativação está registada com o utilizador até que desativar o Office no dispositivo. (Os utilizadores podem gerir os dispositivos no [portal do Office 365](https://portal.office365.com/)).

Com RemoteApp do Azure um único utilizador pode iniciar sessão em vários computadores no mesmo dia sem o saberem. Se ao facto do serviço automaticamente gere e escalas recursos na nuvem, enquanto o utilizador vê apenas as aplicações e programas que tenha partilhado. Para este cenário Office 365 ProPlus oferece um modo de ativação do computador partilhado - Isto significa que o utilizador não precisa de fazer qualquer gestão de licenças para aceder a essas recursos e que os computadores individuais não são contabilizados para o limite de ativação do 5 computador.

Desde que (o administrador) atribuir licenças do Office 365 ProPlus aos seus utilizadores, eles podem utilizar o Office nos seus dispositivos pessoais e através da sua coleção de RemoteApp do Azure.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>As aplicações do Office posso utilizar com o Office 365 e RemoteApp do Azure?

Pode utilizar a sua subscrição do Office 365 para ativar e partilhar o Office 2013 em implementações RemoteApp do Azure. Atualmente não suportamos a utilização de outras versões do Office com o Azure RemoteApp. Isto inclui o Office 2003, o Office 2007, o Office 2010 e o Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Relação do Visio Pro ou o Project Pro?

A imagem do Office 365 ProPlus incluída na sua subscrição RemoteApp inclui o Visio Pro e o Project Pro. Mas não pode utilizar a sua subscrição do Office 365 ProPlus para ativar os programas – cada têm os seus próprios licença. Pode ativá-los no [portal do Office 365](https://portal.office365.com/). 

Não tem destes programas de licença se não pretender utilizá-las. Basta ative os programas que pretende utilizar - e ignorar as outras pessoas. Irá vê-los na imagem, mas não pode utilizá-los. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Como posso começar a com o Office 365 e RemoteApp do Azure?

Agora que já conhece os detalhes de licenciamento do Office 365, vamos começar a utilizá-lo no Azure RemoteApp - é muito fácil:

Quando criar coleção de RemoteApp do Azure, utilize a imagem do **Office 365 ProPlus (subscrição necessário)** .

![Azure RemoteApp imagem com o Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Esta imagem contém a versão mais recente do Windows Server e o Office 365 ProPlus. Depois de configurar a coleção (incluindo publicação aplicações), os seus utilizadores simplesmente inicie sessão no Azure RemoteApp (ao utilizar o seu cliente RemoteApp) e fornecer as respetivas credenciais do Office 365 para as aplicações do Office. Licenças são automaticamente entregues sem quaisquer definidos para cima ou gestão necessários.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Pode criar uma imagem personalizada com o Office 365 ProPlus?

Pode criar uma imagem personalizada para a sua coleção que contém o Office 365 ProPlus. Existem 2 escolhas - utilizar a imagem da Galeria Azure que fornecemos ou pode criar a sua própria imagem personalizada e instalar o Office 365 ProPlus aí.

### <a name="use-the-azure-gallery-image"></a>Utilizar a imagem da Galeria Azure

A forma mais fácil para implementar o Office 365 ProPlus para uma coleção de é [começar com uma das imagens do Azure Galeria](remoteapp-image-on-azurevm.md) incluído na sua subscrição RemoteApp do Azure. Certifique-se de que escolher a imagem do **Windows Server remoto ambiente de trabalho anfitrião da sessão com o Office 365 ProPlus pré-instalado** . Em seguida, instale quaisquer outras aplicações que pretende na imagem e está pronto aceder.

### <a name="use-a-custom-image"></a>Utilizar uma imagem personalizada

Pode sempre criar uma imagem personalizada - pode criar um [Azure VM](remoteapp-image-on-azurevm.md) ou [criar localmente a imagem](remoteapp-create-custom-image.md) e carregue-o para Azure. Em ambos os casos, certifique-se de que instalar o Office 365 ProPlus utilizando o nó de ativação do computador partilhado. Utilizar a [Ferramenta de implementação do Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) e siga as [instruções](https://technet.microsoft.com/library/Dn782858.aspx) de instalação.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Desativar as atualizações automáticas no Office 365 ProPlus na sua imagem personalizada - importantes

A imagem personalizada é utilizada por RemoteApp do Azure como um modelo para adicionar recursos adicionais como o pedido a partir do seu aumentos de utilizadores. Para impedir que atrasos e problemas de ligação, desative as atualizações automáticas para o Office na imagem. Se não o fizer, em seguida, todos os recursos criado com nesse modelo irão atualizar automaticamente quando é iniciado. Em alternativa, utilize o processo de RemoteApp do Azure padrão para atualizar a sua imagem personalizada. Dessa forma atualizar as aplicações do Office uma vez na imagem do modelo e, em seguida, informe RemoteApp do Azure encarregam-se de obter as atualizações aos seus utilizadores.

Para desativar as atualizações automáticas, adicione o seguinte para o ficheiro de configuração da ferramenta de implementação do Office:

        <Updates Enabled="FALSE" />

Agora o ficheiro de configuração deve conter destas linhas:
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Então como pode atualizar uma imagem com o Office 365 ProPlus?

Existem vários motivos para atualizar a imagem na sua coleção. Seguem-se apenas alguns:

- Obter as atualizações mais recentes do Windows 
- Obter as atualizações mais recentes de aplicação Office 365 ProPlus
- Atualizar a sua aplicação personalizada
- Altere outras definições de configuração para a própria imagem

Para os passos de fim para fim para atualizar a sua coleção de utilizar a imagem que atualizou, aceda [aqui](remoteapp-update.md). Mas, para obter informações sobre como atualizar a imagem e o Office 365 ProPlus, consulte as seguintes informações.

Tem duas opções para atualizar a sua imagem - substitui a imagem com um novo completamente manual ou atualizar a sua imagem existente.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Substituir a imagem com a imagem mais recente do Azure Galeria + adicionar personalizações
Com esta opção, informe Microsoft encarregam-as atualizações do Windows Server e o Office 365 ProPlus. Em vez de atualizar a sua imagem existente, crie uma imagem completamente nova com base na imagem da Galeria mais recente. Em seguida, repita os passos faziam anteriormente para personalizar a imagem - instalar aplicações personalizadas, modificar a configuração da imagem, etc.

As imagens da Galeria são atualizadas regularmente, para que pode coloca mais facilmente, sabendo que as suas aplicações do Windows Server e o Office 365 ProPlus estão atualizadas. Obviamente, o compromisso é que terá de aplicar as personalizações sempre que receber uma nova imagem. Pode criar scripts para automatizar a definição as personalizações.

### <a name="manually-update-your-existing-image"></a>Atualizar manualmente a sua imagem existente

Com esta opção, utilize ferramentas padrão do Windows para aplicar atualizações para a imagem. Para o Office 365 ProPlus, utilize a ferramenta de implementação do Office para transferir e instalar as mais recentes atualizações ou versões do Office 365 ProPlus.

> [AZURE.IMPORTANT] Lembre-se - desativar as atualizações automáticas do Office 365 ProPlus.

Precisa de mais informações sobre como utilizar a ferramenta de implementação do Office para atualizações?

- [Implementar o clique-e-Use nos produtos do Office 365 utilizando a ferramenta de implementação do Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Implementar e atualizar o Office 365 ProPlus utilizando a ferramenta de implementação do Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vídeo)
- [Configurar definições de atualização para o Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)
