
<properties 
    pageTitle="Como RemoteApp do Azure guardar dados de utilizador e definições de? | Microsoft Azure"
    description="Saiba como RemoteApp do Azure guarda os dados de utilizador utilizando o disco de perfil de utilizador."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Como RemoteApp do Azure guardar dados de utilizador e definições de?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp guarda a identidade do utilizador e as personalizações entre dispositivos e sessões. Estes dados de utilizador são armazenados num disco de por coleção por utilizador, conhecido como um disco de perfil de utilizador (ACT). O disco segue o utilizador e garante que o utilizador tem uma experiência consistente, independentemente de onde de iniciar sessão. guarda 

Discos de perfil de utilizador são totalmente transparentes ao utilizador — utilizadores guardar documentos à sua pasta de documentos (no que é apresentado ao ser uma unidade local) e alterar as definições de aplicação como habitualmente. Ao mesmo tempo, todas as definições pessoais persistirem quando se liga ao Azure RemoteApp a partir de qualquer dispositivo. Só o utilizador vê dos respetivos dados no mesmo local.

Cada Act tem 50GB de armazenamento persistente e contém ambas as definições de dados e de aplicações do utilizador. 

Continue a ler para detalhes sobre os dados de perfil de utilizador.

>[AZURE.NOTE] Precisa de desativar a ACT? Pode fazer neste momento, dar saída mensagem no blogue do Pavithra, [Desativar o utilizador perfil discos (UPDs) no Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), para obter detalhes.


## <a name="how-can-an-admin-get-to-the-data"></a>Como um administrador aceder aos dados?

Se precisar de aceder aos dados para um dos seus utilizadores (para recuperação de falhas ou se o utilizador sai da empresa), contacte o suporte do Azure e fornecer as informações da subscrição para a coleção de e a identidade do utilizador. A equipa do Azure RemoteApp fornecer-lhe um URL para o VHD. Transfira esse VHD e obter quaisquer documentos ou ficheiros de que precisa. Note que o VHD é 50GB, irá demorar um bit para o transferir.


## <a name="is-the-data-backed-up"></a>Os dados cópias de segurança?

Sim, vamos guardar uma cópia de segurança dos dados de utilizador por localização geográfica. Os dados são só de leitura e podem ser acedidos da mesma forma, tal como os dados normais seria (contacto RemoteApp Azure obtê-lo), se for o Centro de dados principal para baixo. Os dados são copiados em tempo real para a localização de cópia de segurança e não podemos manter cópias das versões diferentes. Por isso, no danos nos dados, podemos não conseguir restaurá-la para uma versão de boa anteriormente conhecida mas se for o Centro de dados principal para baixo, poderá obter dados de utilizador a partir de outra localização.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Como aos utilizadores ver o Act do lado do servidor?

Cada utilizador terá os seus próprios diretório no servidor que mapas para os respetivos Act: c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>O que é a melhor forma de utilizar o Outlook e Act?

Azure RemoteApp guarda o estado do Outlook (caixas de correio, PSTs) entre sessões. Para permitir que este, precisamos PST a ser armazenado nos dados de perfil de utilizador (c:\users\<nome de utilizador >). Esta é a localização predefinida para os dados, por isso, desde que não altere a localização, os dados vão permanecer entre sessões.

Recomendamos também que utilizar o modo "em cache" no Outlook e utilizar o modo "servidor/online" para procurar.

Consulte [Este artigo](remoteapp-outlook.md) para obter mais informações sobre como utilizar o Outlook e RemoteApp do Azure.

## <a name="what-about-redirection"></a>E em relação redirecionamento?
Pode configurar RemoteApp do Azure para permitir que os utilizadores acedam dispositivos locais ao configurar o [redirecionamento](remoteapp-redirection.md). Dispositivos locais, em seguida, poderão aceder aos dados na Act.

## <a name="can-i-use-my-upd-as-a-network-share"></a>Pode utilizar meu Act como uma partilha de rede?
Não. UPDs não podem ser utilizados como uma partilha de rede. Um Act só está disponível ao utilizador quando o utilizador ativamente está ligado à RemoteApp do Azure.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Se posso eliminar um utilizador a partir de uma coleção, é os respetivos Act eliminado?

Não, quando eliminar um utilizador, podemos não eliminar automaticamente a ACT – em vez disso, podemos armazenar os dados até eliminar a coleção de. cerca de 90 dias depois de eliminar a coleção podemos eliminar todos os UPDs. 

Se precisar de eliminar um Act a partir de uma coleção, contacte o Azure RemoteApp - podemos pode eliminar Act a partir do nosso lado.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>Pode aceder UPDs dos meus utilizadores (utilizadores atuais ou eliminados)?

Sim, se contactar [RemoteApp do Azure](mailto:remoteappforum@microsoft.com), podemos pode configurar lhe com um URL para aceder aos dados. Terá de cerca de 10 horas transferir quaisquer dados ou ficheiros a partir do Act antes de aceder a expira.

## <a name="are-upds-available-offline"></a>Estão disponíveis offline os UPDs?

Botão direito do rato agora não fornecemos acesso offline para UPDs, para além da janela de acesso de hora 10 descrita acima. Isto significa que não atualmente temos uma maneira de fornecer-lhe acesso por muito tempo suficiente para concluir tarefas mais complicadas como executar software antivírus instalado nas UPDs ou aceder a dados para uma auditoria.

## <a name="do-registry-key-settings-persist"></a>Definições de chave de registo persistirem?
Sim, nada escritos HKEY_Current_User faz parte da Act.

## <a name="can-i-disable-upds-for-a-collection"></a>Pode desativar UPDs para uma coleção de?

Sim, pode pedir RemoteApp do Azure para desativar UPDs para uma subscrição, mas não pode fazer essa mesmo. Isto significa que serão desativados em UPDs para todas as colecções na subscrição.

Poderá pretender desativar UPDs em qualquer uma das seguintes situações: 

- Precisa de concluir o access e o controlo dos dados de utilizador (para auditoria e rever efeitos como instituições financeiras).
- Tem de utilizador de 3 terceiros gestão soluções no local do perfil e pretende continuar a utilizá-los na sua implementação do Azure RemoteApp façam parte de um domínio. Isso, é necessário o agente de perfil ser carregado para a imagem de ouro. 
- Não precisa de qualquer armazenamento de dados local ou ter todos os dados na partilha de ficheiro ou na nuvem e gostaria de controlo guardar dos dados utilizando localmente RemoteApp do Azure.

Para mais informações, consulte [Desativar utilizador perfil discos (UPDs) no Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>Pode restringir os utilizadores a partir de guardar os dados para a unidade de sistema?

Sim, mas terá de configurar que na imagem do modelo antes de criar a coleção. Utilize os passos seguintes para bloquear o acesso a unidade de sistema:

1. Execute **gpedit. msc** na imagem do modelo.
2. Navegue para **configuração do utilizador > modelos administrativos > componentes do Windows > Explorer**.
3. Selecione as seguintes opções:
    - **Ocultar estas unidades especificadas no meu computador**
    - **Impedir o acesso a unidades a partir do meu computador**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>Pode propagar UPDs? Pretende colocar alguns dados na Act que está disponível o utilizador inicia sessão pela primeira vez.

Sim, ao criar a imagem do modelo, pode adicionar informações para o perfil predefinido. Que informações, em seguida, são adicionadas à Act.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>Pode alterar o tamanho do Act dependendo da quantidade de dados que pretende armazenar?

Não, todos os UPDs tem 50 GB de armazenamento. Se pretende armazenar diferentes quantidades de dados, experimente o seguinte:

1. Desative UPDs para a coleção.
2. Configure o acesso aos utilizadores uma partilha de ficheiros.
3. Carregue a partilha de ficheiros utilizando um script de arranque. Consulte abaixo para obter detalhes sobre os scripts de arranque no Azure RemoteApp.
4. Utilizadores diretos para guardar todos os dados para a partilha de ficheiros.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Como executar um script de arranque RemoteApp do Azure?

Se pretender executar um script de arranque, comece por criar uma tarefa agendada na imagem do modelo que vai utilizar para a coleção. (Faça este *antes de* executar o sysprep.) 

![Criar uma tarefa de sistema](./media/remoteapp-upd/upd1.png)

![Criar uma tarefa de sistema que é executada quando um utilizador inicia sessão](./media/remoteapp-upd/upd2.png)

No separador **Geral** , certifique-se alterar a **Conta de utilizador** em segurança para "BUILTIN\Utilizadores.."

![Alterar a conta de utilizador para um grupo](./media/remoteapp-upd/upd4.png)

A tarefa agendada será executado o script de arranque, utilizando as credenciais do utilizador. Agendar a tarefa para executar todas as uma vez que um utilizador inicia sessão.

![Definir o accionador para a tarefa como "no início de sessão"](./media/remoteapp-upd/upd3.png)

Também pode utilizar [os scripts de política de grupo com base no arranque](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>E quanto colocar um script de arranque no menu Iniciar? Que funcionarão?

Por outras palavras, posso criar um ficheiro. bat que executa um script de janela config e guardá-lo para a pasta de Menu\Programs\StartUp c:\ProgramData\Microsoft\Windows\Start e, em seguida, tem esse script ser executada sempre que um utilizador inicia uma sessão de RemoteApp?

Não, que não é suportado com RemoteApp do Azure, que utiliza RDSH, que também não suporta scripts de arranque no menu Iniciar.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>Pode utilizar mstsc.exe (o programa de ambiente de trabalho remoto) para configurar scripts de início de sessão?

Não, não suportado RemoteApp do Azure.

## <a name="can-i-store-data-on-the-vm-locally"></a>Pode armazenar dados sobre a VM localmente?

Não, os dados armazenados em qualquer lugar na VM diferente de na Act serão perdidos. Não existe a oportunidade de alta o utilizador não irá obter a mesma VM da próxima vez iniciar sessão no Azure RemoteApp. Vamos não pretende manter persistente VM de utilizador, para que o utilizador não irá iniciar sessão na mesma VM e os dados serão perdidos. Para além disso, quando estamos a atualizar a coleção de, os VMs existentes são substituídos por um novo conjunto de VMs -, que significa que quaisquer dados armazenados na VM propriamente dito são perdidos. A recomendação é armazenar dados Act, armazenamento partilhado como os ficheiros Azure, servidor de ficheiros no interior de um VNET ou na nuvem com um sistema de armazenamento na nuvem como o DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Como posso montagem uma partilha de ficheiros do Azure numa VM, através do PowerShell?

Pode utilizar o cmdlet líquido PSDrive para montagem da unidade, da seguinte forma:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


Também pode guardar as suas credenciais, executando o seguinte:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Que permite-lhe ignorar o credencial parâmetro - no cmdlet PSDrive novo.
