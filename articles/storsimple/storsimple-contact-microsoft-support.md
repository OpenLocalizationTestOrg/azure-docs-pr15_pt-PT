<properties 
   pageTitle="Contacte o suporte da Microsoft | Microsoft Azure"
   description="Saiba como criar um pedido de suporte e iniciar uma sessão de suporte no seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Contacte o suporte da Microsoft

Se encontrar problemas com a sua solução do Microsoft Azure StorSimple, pode criar um pedido de serviço para obter suporte técnico. Uma sessão com o seu engenharia de suporte online, também poderá ter de iniciar uma sessão de suporte no seu dispositivo StorSimple. Este artigo irá guiar pelo:

- Como criar um pedido de suporte.
- Como iniciar uma sessão de suporte na interface do Windows PowerShell do seu dispositivo StorSimple.

Reveja as [informações e StorSimple 8000 série suporte SLA](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Execute os seguintes passos para criar um pedido de suporte:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de suporte

1. No [portal clássica Azure](https://manage.windowsazure.com/), no canto superior direito, clique no nome da conta e, em seguida, clique em **Contactar o suporte do Microsoft**.

    ![Suporte da MS de contacto através do ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Serão redirecionados para o novo portal Azure (portal.azure.com). Clique no mosaico **novo pedido de suporte** .

    ![Suporte da MS de contacto através do novo portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    No lado direito do ecrã, é apresentado o painel de **novo pedido de suporte** . 

    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Na caixa de diálogo **Noções básicas** , preencha o seguinte procedimento:                                
    1. A partir da lista pendente **tipo de problema** , selecione **técnicos**.
    2. Selecione uma **subscrição** a partir da lista pendente.
    3. A partir da lista pendente de **serviço** , selecione **StorSimple**. 
    4. Selecione um **plano de suporte** a partir da lista pendente. Precisa de um plano de suporte pago para ativar o suporte técnico.

4. Clique em **seguinte**. É apresentada a caixa de diálogo **problema** .

    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Na caixa de diálogo **problema** , preencha o seguinte procedimento:

    1.  Selecione um nível de **gravidade** a partir da lista pendente.
    2.  Selecione um **tipo de problema** a partir da lista pendente.
    3.  Selecione uma **categoria** a partir da lista pendente. 
    4.  Na caixa **Detalhes** , descreva o problema.
    5.  Na caixa **período de tempo** , indique a data, hora e fuso horário que corresponde ao seu problema a ocorrência mais recente.
    6.  Em **carregar ficheiro**, clique no ícone de pasta para navegar para o seu pacote de suporte.
    7.  Selecione a caixa de verificação **partilhar informações de diagnóstico** .

6. Clique em **seguinte**. É apresentada a caixa de diálogo **informação de contacto** .

    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Introduza as informações de contacto e selecione um método de contacto (telemóvel ou correio eletrónico). 

8. Selecione a caixa de verificação **Guardar as alterações feitas contactos para pedidos de suporte futuras** .

9. Clique em **Criar**.

Depois de ter submeter o pedido, um técnico de suporte irá contactá-lo mais cedo possível para continuar com o seu pedido.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte do Windows PowerShell para StorSimple

Para resolver quaisquer problemas que que se poderá deparar com o dispositivo StorSimple, terá de participar com a equipa do Microsoft Support. Microsoft Support poderá ter de utilizar uma sessão de suporte para iniciar sessão no seu dispositivo. 

Execute os seguintes passos para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Aceda ao dispositivo diretamente através da consola série ou através de uma sessão de telnet a partir de um computador remoto. Para fazer isto, siga os passos na [Utilização betumes para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Na sessão que se abre, prima a tecla **Enter** para obter uma linha de comandos.

3. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**.

4. Na linha de comandos, escreva a palavra-passe seguinte: 

    `Password1`

5. Na linha de comandos, escreva o seguinte comando:

    `Enable-HcsSupportAccess`

6. Será apresentada uma cadeia codificada para si. Copie esta cadeia para num editor de texto como o bloco de notas.

7. Guarde esta cadeia e enviá-la numa mensagem de e-mail para Microsoft Support. 

> [AZURE.IMPORTANT] Pode desativar o acesso de suporte ao executar `Disable-HcsSupportAccess`. O dispositivo StorSimple também irá tentar desativar o acesso de suporte 8 horas depois foi iniciada a sessão. É aconselhável alterar as credenciais de dispositivo de StorSimple depois de iniciar uma sessão de suporte.
