<properties 
   pageTitle="PowerShell para gestão de dispositivos StorSimple | Microsoft Azure"
   description="Saiba como utilizar o Windows PowerShell para StorSimple para gerir o seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Descrição geral

Windows PowerShell para StorSimple fornece uma interface de linha de comandos que pode utilizar para gerir o seu dispositivo do Microsoft Azure StorSimple. Como o nome sugerido pelo é uma interface de comandos com base no Windows PowerShell incorporada num espaço de execução restrita. Da perspetiva do utilizador na linha de comandos, um espaço de execução restrita é apresentado como uma versão do Windows PowerShell restrita. Mantendo algumas das funcionalidades básicas de empresa do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que são orientados para gerir o seu dispositivo do Microsoft Azure StorSimple. 

Este artigo descreve o Windows PowerShell para funcionalidades StorSimple, incluindo como se liga a esta interface e contém ligações para procedimentos passo a passo ou fluxos de trabalho que pode efetuar utilizando esta interface. Os fluxos de trabalho incluem como registar o seu dispositivo, configure a interface de rede no seu dispositivo, instalar as atualizações que requerem o dispositivo para estar no modo de manutenção, alterar o estado do dispositivo e resolver quaisquer problemas com o que se possa deparar.

Depois de ler este artigo, será possível:

- Ligar ao seu dispositivo de StorSimple através do Windows PowerShell para StorSimple.

- Administre o seu dispositivo StorSimple através do Windows PowerShell para StorSimple.

- Obter ajuda no Windows PowerShell para StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell para StorSimple cmdlets permitem-lhe gerir o seu dispositivo StorSimple a partir de uma série consola ou remotamente através do Windows PowerShell remoto. Para mais informações sobre cada um dos cmdlets individuais que podem ser utilizados nesta interface, aceda a [referência do cmdlet para o Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Os cmdlets do Azure PowerShell StorSimple são um conjunto diferente de cmdlets que permitem-lhe para automatizar StorSimple nível de serviço e tarefas de migração da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para a [referência do cmdlet Azure StorSimple](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Pode aceder ao Windows PowerShell para StorSimple utilizando um dos seguintes métodos:

- [Ligar à StorSimple dispositivo série consola](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Ligar remotamente a StorSimple através do Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo

Pode [Transferir betumes](http://www.putty.org/) ou terminal semelhante software de emulação para ligar ao Windows PowerShell para StorSimple. Tem de configurar betumes especificamente para aceder ao Microsoft Azure StorSimple dispositivo. Os tópicos seguintes contêm passos detalhados sobre como configurar betumes e ligar ao dispositivo. Também são explicadas várias opções de menu na consola do série.

### <a name="putty-settings"></a>Definições para

Certifique-se de que utilize as seguintes definições para ligar para a interface do Windows PowerShell, a partir da consola de série.

#### <a name="to-configure-putty"></a>Para configurar betumes

1. Na caixa de diálogo para **reconfiguração** , no painel de **categoria** , selecione o **teclado**.

2. Certifique-se de que as seguintes opções estão seleccionadas (estes são as predefinições de iniciar uma nova sessão). 

  	|Item de teclado|Selecione|
  	|---|---|
  	|Tecla de retrocesso|Controlo-? (127)|
  	|Teclas de base e de fim|Padrão|
  	|Teclas de função e teclado numérico|ESC [n ~|
  	|Estado inicial de teclas do cursor|Normal|
  	|Estado inicial do teclado numérico|Normal|
  	|Activar funcionalidades adicionais de teclado|Controlo Alt é a diferença entre AltGr|

    ![Definições para suportados](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Clique em **Aplicar**.

4. No painel de **categoria** , selecione **tradução**.

5. Na caixa de lista **do conjunto de caracteres remotos** , selecione **UTF-8**.

6. Em **processamento de carateres de desenho de linha**, selecione **utilizar Unicode pontos de código de desenho de linha**. A ilustração seguinte mostra as seleções para corretas.

    ![UTF betumes definições](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Clique em **Aplicar**.


Agora pode utilizar betumes para ligar à consola de série do dispositivo efetuando os passos seguintes.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Acerca da consola de série

Quando acede a interface do seu dispositivo StorSimple através da consola de série, uma mensagem de faixa é apresentado o Windows PowerShell, seguido de opções do menu. 

A mensagem de faixa contém informações básicas de dispositivo de StorSimple tal como o modelo, nome, versão do software instalado e o estado do controlador de que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa.

![Mensagem de faixa série](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Pode utilizar a mensagem de faixa para identificar se é o controlador de que estiver ligado a ativas ou passiva.

A imagem seguinte mostra as várias opções de espaço de execução que estão disponíveis no menu consola série.

![Registe-se o seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Pode escolher as seguintes definições:

1. **Iniciar sessão com acesso total** Esta opção permite-lhe ligar (com as credenciais adequadas) o espaço de execução **SSAdminConsole** no controlador local. (O controlador de local é o controlador de que está atualmente a aceder ao através da consola de série do seu dispositivo StorSimple.) Esta opção pode também ser utilizada para permitir que Support da Microsoft aceder a ilimitada espaço de execução (uma sessão de suporte) para resolver quaisquer problemas com dispositivos possíveis. Depois de utilizar a opção 1 para iniciar sessão, pode permitir que o engenharia Microsoft Support aceder ao espaço de execução sem restrições ao executar um cmdlet específico. Para obter mais detalhes, consulte [Iniciar uma sessão de suporte](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Iniciar sessão no controlador de ponto a ponto com acesso total** Esta opção é igual a opção 1, exceto que pode ligar (com as credenciais bom) para o espaço de execução **SSAdminConsole** no controlador de ponto a ponto. Uma vez que o dispositivo StorSimple for um dispositivo de alta disponibilidade com dois controladores numa configuração activo passivo, peer refere-se para o outro controlador no dispositivo que está a aceder ao através da consola de série).
Semelhante a opção 1, esta opção pode também ser utilizada para permitir que Support da Microsoft aceder a ilimitada espaço de execução num controlador de ponto a ponto.

3. **Ligar-se com acesso limitado** Esta opção é utilizada para aceder a interface do Windows PowerShell no modo limitado. Não lhe é pedido para introduzir as credenciais do access. Esta opção liga-se para um espaço de execução mais restrito em comparação comparado opções 1 e 2.  Algumas das tarefas que estão disponíveis através da opção 1 que **não é* possível executar este espaço de execução são:

    - Repor as definições de fábrica
    - Alterar a palavra-passe
    - Ativar ou desativar o acesso de suporte
    - Aplicar atualizações
    - Instalar correcções 
                                                

    >[AZURE.NOTE] **Esta é a opção de preferido se tenha esquecido a palavra-passe de administrador do dispositivo e não é possível ligar através da opção 1 ou 2.**

4. **Alterar idioma** Esta opção permite-lhe alterar o idioma de visualização na interface do Windows PowerShell. Os idiomas suportados são inglês, japonês, russo, francês, Sul-coreano, espanhol, italiano, alemão, chinês e português (Brasil).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ligar remotamente a StorSimple através do Windows PowerShell para StorSimple

Pode utilizar o Windows PowerShell remoto para ligar para o seu dispositivo StorSimple. Ao ligar desta forma, não verá um menu. (Verá um menu apenas se a série consola no dispositivo que utilizar para ligar. Ligar remotamente leva-o diretamente para o equivalente da "opção 1 – acesso total" na série consola.) Com o Windows PowerShell remoto, pode ligar para um espaço de execução específico. Também pode especificar o idioma de apresentação. 

O idioma de apresentação seja independente no idioma que definiu utilizando a opção **Alterar idioma** no menu consola série. PowerShell remoto automaticamente vai selecionar a região do dispositivo que estiver a ligar a partir do se não for especificado nenhum.

>[AZURE.NOTE] Se estiver a trabalhar com anfitriões virtuais do Microsoft Azure e dispositivos virtuais StorSimple, pode utilizar o Windows PowerShell remoto e o anfitrião virtual para ligar para o dispositivo virtual. Se configurou uma localização de partilha no anfitrião do no qual pretende guardar informações da sessão do Windows PowerShell, deverá conhecer que inclui principal todos apenas utilizadores autenticados. Por isso, se configurou o partilhar para permitir o acesso por toda a gente e ligar sem especificar as credenciais, será utilizado o capital anónimo não autenticado e verá um erro. Para corrigir este problema, na partilha alojar tem ativar a conta de convidado e, em seguida, dar o convidado acesso total de conta para a partilha ou tem de especificar credenciais válidas juntamente com o cmdlet do Windows PowerShell.

Pode utilizar HTTP ou HTTPS para ligar através do Windows PowerShell remoto. Utilize as instruções seguintes tutoriais:

- [Ligar utilizando remotamente HTTP](storsimple-remote-connect.md#connect-through-http)
- [Ligar remotamente utilizando HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de ligação

Quando estiver a decidir como ligar ao Windows PowerShell para StorSimple, considere o seguinte:

- Ligar-se diretamente ao consola de série do dispositivo é segura, mas ligar a série consola através de parâmetros de rede não está. Tenha cuidado com o risco de segurança quando se liga ao dispositivo série através de parâmetros de rede.

- Ligar-se através de uma sessão de HTTP poderá oferecem mais segurança do que a ligar através da consola de série através de rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas.

- Ligar através de uma sessão de HTTPS é o mais seguro e a opção recomendada.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o seu dispositivo StorSimple através do Windows PowerShell para StorSimple
A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados dentro da interface do seu dispositivo StorSimple para o Windows PowerShell. Para mais informações sobre cada fluxo de trabalho, clique na entrada adequada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para StorSimple fluxos de trabalho

|Se pretender fazer isto...|Utilize este procedimento.|
|---|---|
|Registe-se o seu dispositivo|[Configurar e registar o dispositivo com o Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurar o proxy web</br>Definições de proxy de web de vista|[Configurar o web proxy para o seu dispositivo StorSimple](storsimple-configure-web-proxy.md)|
|Modificar as definições de interface de rede 0 dados no seu dispositivo|[Modificar a interface de 0 rede de dados para o seu dispositivo StorSimple](storsimple-modify-data-0.md)|
|Parar de um controlador </br> Reiniciar ou encerrar um controlador </br> Encerrar um dispositivo</br>Repor o dispositivo para as predefinições de fábrica|[Gerir controladores do dispositivo](storsimple-manage-device-controller.md)|
|Instale as atualizações do modo de manutenção e correcções|[Atualizar o seu dispositivo](storsimple-update-device.md)|
|Entrar no modo de manutenção </br>Sair do modo de manutenção|[Modos de dispositivo StorSimple](storsimple-device-modes.md)|
|Criar um pacote de suporte</br>Desencriptar e editar um pacote de suporte|[Criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md)|
|Iniciar uma sessão de suporte</br>|[Iniciar uma sessão de suporte do Windows PowerShell para StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obter ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, cmdlet ajuda está disponível. Uma versão online, atualizada desta ajuda também está disponível, que pode utilizar para atualizar a ajuda no sistema de.

Obter ajuda na interface deste é semelhante a essa no Windows PowerShell e, a maior parte dos cmdlets relacionados com a ajuda irá funcionar. Pode encontrar ajuda para o Windows PowerShell online na biblioteca do TechNet: [Scripting com o Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Segue-se uma breve descrição dos tipos de ajuda para esta interface do Windows PowerShell, incluindo como atualizar a ajuda.

#### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

- Para obter ajuda para qualquer cmdlet ou função, utilize o seguinte comando:`Get-Help <cmdlet-name>`

- Para obter ajuda online para qualquer cmdlet, utilize o cmdlet anterior com o `-Online` parâmetro:`Get-Help <cmdlet-name> -Online`

- Para obter ajuda completa, pode utilizar o `–Full` parâmetro e para obter exemplos, utilize o `–Examples` parâmetro.

#### <a name="to-update-help"></a>Para atualizar a ajuda

Poderá atualizar facilmente a ajuda na interface do Windows PowerShell. Execute os seguintes passos para atualizar a ajuda no sistema de.

#### <a name="to-update-cmdlet-help"></a>Para atualizar o cmdlet ajuda

1. Inicie o Windows PowerShell com a opção **Executar como administrador** .

1. Na linha de comandos, escreva: `Update-Help`

1. Os ficheiros de ajuda atualizados serão instalados.

1. Depois dos ficheiros de ajuda são instalados, escreva: `Get-Help Get-Command`. Isto vai mostrar uma lista de cmdlets para o qual a ajuda está disponível.


>[AZURE.NOTE] Para obter uma lista de todos os cmdlets disponíveis num espaço de execução, inicie sessão na opção de menu correspondente e executar o `Get-Command` cmdlet.

## <a name="next-steps"></a>Próximos passos
Se tiver quaisquer problemas com o seu dispositivo StorSimple quando efetuar um dos fluxos de trabalho acima, consulte a [Ferramentas para implementações StorSimple de resolução de problemas](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

