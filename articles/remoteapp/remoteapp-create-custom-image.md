<properties
    pageTitle="Como criar uma imagem do modelo personalizado para RemoteApp do Azure | Microsoft Azure"
    description="Saiba como criar uma imagem de modelo personalizado para RemoteApp do Azure. Pode utilizar este modelo com uma colecção de um híbrido ou na nuvem."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Como criar uma imagem do modelo personalizado para RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp utiliza uma imagem do modelo de Windows Server 2012 R2 para todos os programas que pretende partilhar com os utilizadores do anfitrião. Para criar uma imagem de modelo RemoteApp personalizada, pode começar com uma imagem existente ou crie um novo. 


> [AZURE.TIP] Sabia que pode criar uma imagem a partir de um VM Azure? História TRUE e corta para baixo na quantidade de tempo-demora para importar a imagem. Consulte os passos [aqui](remoteapp-image-on-azurevm.md).

Os requisitos para a imagem que podem ser carregados para utilização com o Azure RemoteApp são:


- O tamanho da imagem deve ser um múltiplo de MB. Se tentar carregar uma imagem que não é um múltiplo exato, o carregamento irá falhar.
- O tamanho da imagem tem de ser 127 GB ou mais pequeno.
- Tem de ser num ficheiro VHD (VHDX ficheiros [Hyper-V virtuais unidades de disco rígido] não são atualmente suportados).
- O VHD não tem de ser uma máquina de virtual geração 2.
- O VHD pode ser tamanho fixo ou expansão dinâmica. Um VHD expansão dinâmica é recomendado porque demora menos tempo a carregar para o Azure que um ficheiro VHD tamanho fixo.
- O disco tem de ser inicializado utilizando o registo de arranque do modelo global (MBR) estilo de partição. O estilo de partições GUID partição (GPT tabela) não é suportado.
- O VHD tem de conter uma instalação do Windows Server 2012 R2 única. Pode conter vários volumes, mas apenas uma que contém uma instalação do Windows.
- A função de anfitrião de sessão de ambiente de trabalho remoto (RDSH) e a funcionalidade Experiência de ambiente de trabalho tem de estar instalados.
- A função corretor de ligação de ambiente de trabalho remoto tem *não* ser instalado.
- Tem de ser desactivado sistema de encriptação de ficheiro (EFS).
- A imagem tem de ser processada por Sysprep utilizando os parâmetros **/oobe / generalizar /shutdown** (não utilizar o parâmetro **/mode:vm** ).
- Carregar o VHD a partir de uma cadeia de instantâneo não é suportada.


**Antes de começar**

Precisa de fazer o seguinte antes de criar o serviço:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) no RemoteApp.
- Crie uma conta de utilizador no Active Directory para utilizar como a conta de serviço RemoteApp. Restringir as permissões para esta conta para que só pode associar máquinas para o domínio. Para mais informações, consulte [Configurar o Azure Active Directory para RemoteApp](remoteapp-ad.md) .
- Recolher informações sobre a sua rede no local: endereço IP informações e detalhes do dispositivo VPN.
- Instale o módulo [Azure PowerShell](../powershell-install-configure.md) .
- Reunir informações sobre os utilizadores que pretende conceder acesso ao. Isto pode ser informações da conta Microsoft ou informações de conta de trabalho do Active Directory para os utilizadores.



## <a name="create-a-template-image"></a>Criar uma imagem de modelo ##

Estes são os passos de alto níveis para criar uma nova imagem de modelo de raiz:

1.  Localize uma imagem de DVD de atualização do Windows Server 2012 R2 ou ISO.
2.  Crie um ficheiro VHD.
4.  Instale o Windows Server 2012 R2.
5.  Instale a função de anfitrião de sessão de ambiente de trabalho remoto (RDSH) e a funcionalidade Experiência de ambiente de trabalho.
6.  Instale funcionalidades adicionais necessárias pelas suas aplicações.
7.  Instalar e configurar as aplicações. Para facilitar a partilha de aplicações, adicione quaisquer aplicações ou programas que pretende partilhar ao menu **Iniciar** da imagem, mais especificamente no **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.
8.  Execute qualquer configurações Windows adicionais necessárias pelas suas aplicações.
9.  Desative o sistema de ficheiros de encriptação (EFS).
10. **REQUIRED:** Vá para o Windows Update e instale todas as atualizações importantes.
9.  SYSPREP a imagem.

Os passos detalhados para criar uma nova imagem são:

1.  Localize uma imagem de DVD de atualização do Windows Server 2012 R2 ou ISO.
2.  Crie um ficheiro VHD utilizando a gestão de disco.
    1.  Inicie a gestão de discos (Diskmgmt).
    2.  Crie um VHD dinamicamente expansão de 40 GB ou mais de tamanho. (Estimar a quantidade de espaço necessária para Windows, o seu aplicações e as personalizações. Windows Server com a função RDSH e a funcionalidade Experiência de ambiente de trabalho instalada irão necessitar cerca de 10 GB de espaço).
        1.  Clique em **ação > Criar VHD**.
        2.  Especifique a localização, tamanho e o formato VHD. Selecione **dinamicamente expandir**e, em seguida, clique em **OK**.

            Este procedimento será executado durante vários segundos. Quando a criação de VHD estiver concluída, deverá ver um novo disco sem qualquer letra de unidade e no estado "Não inicializado" na consola de gestão de disco.

        - Com o botão direito no disco (não o espaço não atribuído) e, em seguida, clique em **Iniciar disco**. Selecione **MBR** (registo de arranque do modelo global) como o estilo de partição e, em seguida, clique em **OK**.
        - Criar um novo volume: com o botão direito no espaço não atribuído e, em seguida, clique em **Novo Volume simples**. Pode aceitar as predefinições do assistente, mas certifique-se de que atribuir uma letra de unidade para evitar potenciais problemas ao carregar a imagem do modelo.
        - Com o botão direito do disco e, em seguida, clique em **Desanexar VHD**.





1. Instale o Windows Server 2012 R2:
    1. Crie uma nova máquina virtual. Utilize o Assistente de Máquina Virtual novo no Hyper-V gestor ou cliente Hyper-V.
        1. Na página Especificar geração, selecione **1. ª geração**.
        2. Na página ligar Virtual no disco rígido, selecione **utilizar um disco rígido virtual existente**e navegue para o VHD que criou no passo anterior.
        2. Na página de opções de instalação, selecione **instalar um sistema operativo a partir de um CD/DVD_ROM de arranque**e, em seguida, selecione a localização dos seus ficheiros de multimédia de instalação do Windows Server 2012 R2.
        3. Escolha outras opções no Assistente de necessário para instalar o Windows e as aplicações. Concluir o assistente.
    2.  Depois do assistente terminar, edite as definições da máquina virtual e efetue as alterações necessárias para instalar o Windows e os programas, como o número de processadores virtuais e, em seguida, clique em **OK**.
    4.  Ligar a máquina virtual e instalar o Windows Server 2012 R2.
1. Instale a função de anfitrião de sessão de ambiente de trabalho remoto (RDSH) e a funcionalidade Experiência de ambiente de trabalho:
    1. Inicie o Gestor de servidor.
    2. Clique em **Adicionar funções e funcionalidades** no ecrã de boas-vindas ou a partir do menu **Gerir** .
    3. Clique em **seguinte** na página antes de começar.
    4. Selecione **instalação baseado em funções ou funcionalidade baseada**e, em seguida, clique em **seguinte**.
    5. Selecione o computador local a partir da lista e, em seguida, clique em **seguinte**.
    6. Selecione **Serviços de ambiente de trabalho remoto**e, em seguida, clique em **seguinte**.
    7. Expanda **Interfaces de utilizador e infraestrutura** e selecione a **Experiência de ambiente de trabalho**.
    8. Clique em **Adicionar funcionalidades**e, em seguida, clique em **seguinte**.
    9. Na página de serviços de ambiente de trabalho remoto, clique em **seguinte**.
    10. Clique em **sistema anfitrião da sessão de ambiente de trabalho remoto**.
    11. Clique em **Adicionar funcionalidades**e, em seguida, clique em **seguinte**.
    12. Na página Confirmar seleções de instalação, selecione **reiniciar o servidor de destino automaticamente se necessário**e, em seguida, clique em **Sim** no aviso de reiniciar.
    13. Clique em **instalar**. Irá reiniciar o computador.
1.  Instale funcionalidades adicionais uma ferramenta necessária ao seu aplicações, como o .NET Framework 3.5. Para instalar as funcionalidades, execute o Assistente de funcionalidades e adicionar funções.
7.  Instalar e configurar os programas e as aplicações que pretende publicar através de RemoteApp.

>[AZURE.IMPORTANT]
>
>Instale a função RDSH antes de instalar as aplicações, para garantir que quaisquer problemas relacionados com compatibilidade da aplicação sejam detetados antes da imagem é carregada para RemoteApp.
>
>Certifique-se de que um atalho para a sua aplicação (ficheiro**. lnk** ) será apresentado no menu **Iniciar** para todos os utilizadores (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs). Também Certifique-se de que o ícone que ver no menu **Iniciar** é o que pretende que os utilizadores vejam. Caso contrário, alterá-la. (Não faça *tem* para adicionar a aplicação para o início menu, mas é muito mais fácil publicar a aplicação no RemoteApp. Caso contrário, tem de fornecer o caminho de instalação para a aplicação ao publicar a aplicação.)


8.  Execute qualquer configurações Windows adicionais necessárias pelas suas aplicações.
9.  Desative o sistema de ficheiros de encriptação (EFS). Execute o seguinte comando na linha uma janela de comando elevados:

        Fsutil behavior set disableencryption 1

    Em alternativa, pode definir ou adicionar o seguinte valor DWORD no registo:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Se estiver a criar a imagem dentro de uma máquina virtual Azure, mudar o nome a ** \%windir%\Panther\Unattend.xml** de ficheiros, pois irá bloquear o script de carregamento utilizado mais tarde a partir do trabalho. Altere o nome deste ficheiro para Unattend.old para que ainda terá do ficheiro no caso de precisar reverter a sua implementação.
10. Vá para o Windows Update e instale todas as atualizações importantes. Poderá ter de executar o Windows Update várias vezes para obter todas as atualizações. (Por vezes, ter instalado uma atualização, e essa própria actualização necessita de uma atualização.)
10. SYSPREP a imagem. Na linha de comandos elevada, execute o seguinte comando:

    **C:\Windows\System32\sysprep\sysprep.exe / generalizar /oobe /shutdown**

    **Nota:** Não utilize o parâmetro **/mode:vm** do comando SYSPREP apesar de esta é uma máquina virtual.


## <a name="next-steps"></a>Próximos passos ##
Agora que tem a imagem do modelo personalizado, precisar de carregar imagem à coleção de RemoteApp. Utilize as informações nos seguintes artigos para criar a sua coleção de:


- [Como criar uma coleção de híbrido de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Como criar uma coleção de nuvem de RemoteApp](remoteapp-create-cloud-deployment.md)
 