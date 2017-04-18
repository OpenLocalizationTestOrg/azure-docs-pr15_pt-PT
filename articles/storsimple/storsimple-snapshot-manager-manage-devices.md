<properties 
   pageTitle="Gerir dispositivos com o Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o snap-in MMC do Gestor de instantâneo StorSimple para ligar e gerir dispositivos StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utilizar o Gestor de instantâneo StorSimple para ligar e gerir dispositivos StorSimple

## <a name="overview"></a>Descrição geral

Pode utilizar nós no painel de Gestor de instantâneo StorSimple **âmbito** Verifique se os dados importados de dispositivo StorSimple e atualizar os dispositivos de armazenamento ligada. Para além disso, quando clica no nó **dispositivos** , pode ver uma lista de dispositivos ligados e informações sobre o estado correspondente no painel de **resultados** .

![Dispositivos ligados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Ligar o dispositivo de Gestor de StorSimple instantâneo** 

Dependendo das suas seleções de **vista** , o painel de **resultados** mostra as seguintes informações sobre cada dispositivo. (Para obter mais informações sobre como configurar uma vista, aceda ao [menu Ver](storsimple-use-snapshot-manager.md#view-menu).


| Coluna de resultados  |Descrição          |
|:----------------|:--------------------| 
| Nome            | O nome do dispositivo, tal como está configurado no portal do Azure clássico|
| Modelo           | O número do modelo do dispositivo|
| Versão         | A versão do software instalado no dispositivo |
| Estado          | Se o dispositivo está disponível |
| Sincronizado pela última vez     | Data e hora quando o dispositivo foi sincronizados pela última vez |
| N. º série      | O número de série para o dispositivo |
 
Se o botão direito do rato no nó de **dispositivos** no painel de **âmbito** , pode selecionar as seguintes ações:

- Adicionar ou substituir um dispositivo 
- Ligue um dispositivo e verificar importações 
- Atualizar os dispositivos ligados 

Se clicar o nó **dispositivos** e, em seguida, botão direito do rato no painel de **resultados** de um nome de dispositivo, pode selecionar a partir das seguintes ações:

- Autenticar um dispositivo 
- Ver detalhes do dispositivo 
- Atualizar um dispositivo 
- Eliminar uma configuração de dispositivo 
- Alterar uma palavra-passe do dispositivo

>[AZURE.NOTE] Todas estas ações também estão disponíveis no painel de **ações** .
 
Neste tutorial explica como utilizar o Gestor de instantâneo StorSimple para ligar e gerir os dispositivos e efetuar as seguintes tarefas:

- Adicionar ou substituir um dispositivo 
- Ligue um dispositivo e verificar importações 
- Atualizar os dispositivos ligados 
- Autenticar um dispositivo 
- Ver detalhes do dispositivo 
- Atualizar um dispositivo individual 
- Eliminar uma configuração de dispositivo 
- Alterar uma palavra-passe do dispositivo expirados
- Substituir um dispositivo falhado

>[AZURE.NOTE] Para obter informações gerais sobre como utilizar a interface StorSimple instantâneo Gestor, vá para a [interface de utilizador StorSimple instantâneo Gestor](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo

Utilize o seguinte procedimento para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , clique com o botão direito do rato em **dispositivos** e, em seguida, clique em **configurar um dispositivo**. É apresentada a caixa de diálogo **configurar um dispositivo** .

    ![Configurar um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Na caixa pendente **dispositivo** , selecione o endereço IP do dispositivo ou dispositivo virtual. 

4. Na caixa de texto **palavra-passe** , escreva a palavra-passe de StorSimple instantâneo gestor que criou para o dispositivo no portal do Azure clássico. Clique em **OK**. Gestor de instantâneo StorSimple procura para o dispositivo que identificou. 

    - Se o dispositivo está disponível, o Gestor de instantâneo StorSimple adiciona uma ligação. 

    - Se o dispositivo não estiver disponível por qualquer motivo, StorSimple instantâneo Gestor devolve uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **configurar um dispositivo** .

## <a name="connect-a-device-and-verify-imports"></a>Ligue um dispositivo e verificar importações

Utilize o seguinte procedimento para ligar um dispositivo StorSimple e certifique-se de que quaisquer grupos de volume existente que tenham associadas cópias de segurança sejam importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Ligue um dispositivo e verificar importa

1. Para ligar ao StorSimple instantâneo Gestor de um dispositivo, siga as instruções em Adicionar ou substituir um dispositivo. Quando se liga para um dispositivo, StorSimple instantâneo Gestor responde da seguinte forma:

    - Se o dispositivo não estiver disponível por qualquer motivo, StorSimple instantâneo Gestor devolve uma mensagem de erro. 

   - Se o dispositivo está disponível, o Gestor de instantâneo StorSimple adiciona uma ligação. Quando selecionar o dispositivo, esta aparece no painel de **resultados** e o campo Estado indica que o dispositivo está **disponível**. Gestor de instantâneo StorSimple importa quaisquer grupos de volume configurados para o dispositivo, desde que os grupos de volume tem associadas cópias de segurança. Políticas de cópia de segurança não são importadas. Grupos de volume que não têm cópias de segurança associadas não são importados.

2. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

3. Com o botão direito no nó superior no painel de **âmbito** e, em seguida, clique em **Apresentar de importações de botão de alternar**.

    ![Botão de alternar selecione importações visualização](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. É apresentada a caixa de diálogo do **Botão de alternar importações visualização** , que mostra o estado dos grupos de volume importados e cópias de segurança. Clique em **OK**. 

Depois dos grupos de volume e cópias de segurança serem importadas com êxito, pode utilizar StorSimple instantâneo Manager para geri-las, tal como irá gerir grupos de volume e cópias de segurança que criou e configurou com o Gestor de instantâneo StorSimple. 

## <a name="refresh-connected-devices"></a>Atualizar os dispositivos ligados

Utilize o seguinte procedimento para sincronizar os dispositivos de StorSimple ligados com o Gestor de instantâneo StorSimple.

####<a name="to-refresh-connected-devices"></a>Para atualizar os dispositivos ligados

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , com o botão direito **dispositivos**e, em seguida, clique em **Atualizar dispositivos**. Isto sincroniza os dispositivos ligados com o Gestor de instantâneo StorSimple para que possam ver os grupos de volume e cópias de segurança, incluindo quaisquer adições recentes. 

    ![Atualizar os dispositivos de StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
A ação **Atualizar dispositivos** obtém quaisquer novos grupos de volume e quaisquer cópias de segurança associadas a partir de dispositivos ligados. Ao contrário da **voltar a analisar volumes** ação disponível para o nó **Volumes** , **Atualizar dispositivos** não restaurar a cópia de segurança do registo.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo

Utilize o seguinte procedimento para autenticar um dispositivo StorSimple com StorSimple instantâneo Manager.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , clique em **dispositivos**.

3. No painel de **resultados** , com o botão direito no nome do dispositivo e, em seguida, clique em **autenticar**.

4. É apresentada a caixa de diálogo **autenticar** . Escreva a palavra-passe do dispositivo e, em seguida, clique em **OK**.

    ![Caixa de diálogo de autenticar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Ver detalhes do dispositivo

Utilize o seguinte procedimento para ver os detalhes de um dispositivo StorSimple e, se necessário, voltar a sincronizar o dispositivo com o Gestor de instantâneo StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Para ver e voltar a sincronizar detalhes do dispositivo

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , clique em **dispositivos**.

3. No painel de **resultados** , com o botão direito no nome do dispositivo e, em seguida, clique em **Detalhes**. 

4. a caixa de diálogo de **Detalhes do dispositivo** é apresentada. Esta caixa mostra o nome, modelo, versão, número de série, estado, destino iSCSI qualificado nome (IQN), última sincronização de data e hora. 

   - Clique em **voltar a sincronizar** para sincronizar o dispositivo.

   - Clique em **OK** ou em **Cancelar** para fechar a caixa de diálogo.

    ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual

Utilize o seguinte procedimento para voltar a sincronizar um dispositivo de StorSimple com o Gestor de instantâneo StorSimple individual.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique em **dispositivos**. 

3. No painel de **resultados** , com o botão direito no nome do dispositivo e, em seguida, clique em **Atualizar dispositivo**. Sincroniza o dispositivo com o Gestor de instantâneo StorSimple. 

## <a name="delete-a-device-configuration"></a>Eliminar uma configuração de dispositivo

Utilize o seguinte procedimento para eliminar uma configuração do dispositivo StorSimple individual a partir do Gestor de StorSimple instantâneo.

#### <a name="to-delete-a-device-configuration"></a>Para eliminar uma configuração de dispositivo

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique em **dispositivos**. 

3. No painel de **resultados** , com o botão direito no nome do dispositivo e, em seguida, clique em **Eliminar**. 

4. É apresentada a seguinte mensagem. Clique em **Sim** para eliminar a configuração ou clique em **não** para cancelar a eliminação.

    ![Eliminar a configuração do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma palavra-passe do dispositivo expirados

Tem de introduzir uma palavra-passe para autenticar um dispositivo StorSimple com StorSimple instantâneo Manager. Configurar esta palavra-passe ao utilizar a interface do Windows PowerShell para configurar o dispositivo. No entanto, pode expirar a palavra-passe. Se tal acontecer, pode utilizar o portal clássico Azure para alterar a palavra-passe. Em seguida, uma vez que o dispositivo tiver sido configurado no StorSimple instantâneo Manager antes da palavra-passe expirou, tem de autenticar novamente o dispositivo no StorSimple instantâneo Manager. 

#### <a name="to-change-the-expired-password"></a>Para alterar a palavra-passe expirada

1. No portal do Azure clássico, inicie o serviço de Gestor de StorSimple.

2. Clique em **dispositivos** > **Configurar** para o dispositivo.

3. Desloque para baixo para a secção StorSimple instantâneo gestor. Introduza uma palavra-passe é 14-15 carateres. Certifique-se de que a palavra-passe contém uma mistura de carateres em maiúsculas, minúsculas, numéricos e especiais.

4. Volte a introduzir a palavra-passe para a confirmar.

5. Clique em **Guardar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Para autenticar novamente o dispositivo

1. Inicie o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , clique em **dispositivos**. É apresentada uma lista de dispositivos configurados no painel de **resultados** . 

3. Selecione o dispositivo, botão direito do rato e, em seguida, clique em **autenticar**.

4. Na janela de **autenticação** , introduza a palavra-passe nova. 

5. Selecione o dispositivo, botão direito do rato e selecione **o dispositivo de atualização**. Sincroniza o dispositivo com o Gestor de instantâneo StorSimple. 

## <a name="replace-a-failed-device"></a>Substituir um dispositivo falhado

Se um dispositivo StorSimple falhar e é substituído por um dispositivo de espera (activação pós-falha), utilize os seguintes passos para ligar para o novo dispositivo e ver as cópias de segurança associadas.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para ligar a um novo dispositivo depois de activação pós-falha

1. Reconfigure a ligação iSCSI para o novo dispositivo. Para obter instruções, ir para "passo 7: Formatar um volume, inicialização e montagem" no [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Se o novo dispositivo StorSimple tem o mesmo endereço IP que o antigo, poderá conseguir ligar-se a configuração do antiga. 

2. Pare o serviço de gestão do Microsoft StorSimple:

    1. Inicie o Gestor de servidor.

    2. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**. 

    3. Na janela de **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**. 

    4. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **Parar o serviço**. 

3. Remova as informações de configuração relacionada com o dispositivo antigo: 

    1. No Explorador de ficheiros, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Elimine os ficheiros na pasta BACatalog. 

4. Reinicie o serviço de gestão do Microsoft StorSimple: 

    1. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**. 

    2. Na janela de **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**. 

    3. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

5. Inicie o Gestor de instantâneo StorSimple. 

6. Para configurar o novo dispositivo StorSimple, conclua os passos no passo 2: ligar um dispositivo StorSimple no [Gestor de instantâneo StorSimple implementar](storsimple-snapshot-manager-deployment.md). 

7. Com o botão direito no nó de nível superior no painel de **âmbito** (StorSimple instantâneo gestor no exemplo) e, em seguida, clique em **Apresentar de importações de botão de alternar**. 

8. É apresentada uma mensagem quando os grupos de volume importados e cópias de segurança estão visíveis no StorSimple instantâneo Manager. Clique em **OK**. 

## <a name="next-steps"></a>Próximos passos

- Saiba como utilizar o [Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como utilizar o [Gestor de instantâneo StorSimple para ver e gerir volumes](storsimple-snapshot-manager-manage-volumes.md).

