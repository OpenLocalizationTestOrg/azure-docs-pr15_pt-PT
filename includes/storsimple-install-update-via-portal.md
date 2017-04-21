<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Para instalar a atualização 1.2 a partir do Azure portal clássico

1. Na página de serviço StorSimple, selecione o seu dispositivo. Navegue para **dispositivos** > **Manutenção**.

2. Na parte inferior da página, clique em **Pesquisar atualizações**. Será criada uma tarefa para verificar a existência de atualizações disponíveis. Será notificado quando a tarefa foi concluída com êxito.

3. Na secção de **Atualizações de Software** na mesma página, verá que estão disponíveis novas atualizações de software. Recomendamos que reveja as notas de lançamento antes de aplicar 1.2 de atualização no seu dispositivo.

    ![Instalar atualizações de software](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. Na parte inferior da página, clique em **Instalar atualizações**.

5. Será pedido de confirmação. Clique em **OK**.

6. Será apresentada uma caixa de diálogo **Instalar atualizações** . O dispositivo deve satisfazer as verificações listadas nesta caixa de diálogo. Estes passos foram concluídos antes da atualizar. Selecione **posso compreender o requisito acima e estiver pronto para atualizar o meu dispositivo**. Clique no ícone de verificação.

    ![Mensagem de confirmação](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Um conjunto de verificações de pré-lançamento automáticas agora será iniciado. Estes incluem:

    - O **Estado de funcionamento do controlador verifica** para verificar se os controladores de dispositivo são saudável e online.
    
    - **Verificações do Estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware no seu dispositivo StorSimple são saudáveis.
    
    - **Dados 0 verifica** para verificar se os dados 0 são activados no seu dispositivo. Se esta interface não é activada, terá de ativá-lo e, em seguida, tente novamente.
    
    - **Controlos de dados 2 e 3 de dados** para verificar que não estão activadas interfaces de rede de dados 2 e 3 de dados. Se estiverem ativadas estas interfaces, em seguida, terá desativá-las e, em seguida, experimente atualizar o seu dispositivo. Esta verificação é executada apenas se estiver a atualizar a partir de um dispositivo executar software das versões DG. Dispositivos que executem o versões 0,1, 0,2 mais próximo ou 0,3 não serão necessário esta verificação.
    
    - **Verificar do gateway** em qualquer dispositivo a executar uma versão anterior ao atualização 1. Esta verificação é executada no dispositivo executar 1 software anterior à atualização mas falha nos dispositivos que têm um gateway configurado para uma interface de rede que não seja dados 0.
 
    Actualização 1.2 só será aplicada se todas as verificações anterior à atualização acima são concluídas com êxito. Será notificado que anterior à atualização verificações estão em curso.
  
    ![Notificação de pré-verificação](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    Segue-se um exemplo na qual a verificação de preparação para a atualização falhou. Será necessário verificar se os controladores de dispositivo são saudável e online. Também terá de verificar o estado de funcionamento dos componentes de hardware. Neste exemplo, controlador de 0 e 1 controlador componentes necessitam de atenção. Poderá ter de contactar a Microsoft Support se não consegue resolver estes problemas por si.

     ![Pré-verificação falhou](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

    > [AZURE.NOTE] Depois de ter aplicado 1.2 de atualização no seu dispositivo StorSimple, controlos de dados 2 e 3 de dados e a verificação de gateway já não será necessários para futuras atualizações. Os outros pré-controlos de continuará a ser necessários.


8. Depois das verificações anterior à atualização estão concluídas com êxito, será criada uma tarefa de atualização. Será notificado quando a tarefa de atualização é criada com êxito.
 
    ![Atualizar a criação de emprego](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    A atualização, em seguida, será aplicada no seu dispositivo.
 
9. Para monitorizar o progresso da tarefa de actualização de, clique em **Vista de tarefa**. Na página **tarefas** , pode ver o progresso de atualização. 

    ![Atualizar o progresso da tarefa](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. A atualização irá demorar algumas horas para concluir. Pode ver os detalhes da tarefa em qualquer altura.

    ![Atualizar detalhes do projecto](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Depois da tarefa estiver concluída, navegue para a página de **Manutenção** e desloque para baixo para **Atualizações de Software**.

12. Certifique-se de que o dispositivo está em execução **StorSimple 8000 série atualização 1.2 (6.3.9600.17584)**. **Última atualização data** também deve ser alterada.

    ![Página de manutenção](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Agora irá ver as que estão disponíveis atualizações de modo de manutenção. Estas atualizações são actualizações desorganização que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo. Siga as instruções em [instalar atualizações do modo de manutenção](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) de instalar estas atualizações através do Windows PowerShell para StorSimple.

> [AZURE.NOTE] Em certos casos, pode ser apresentada a mensagem a indicar que estão disponíveis atualizações de modo de manutenção para cima para 24 horas depois das atualizações do modo de manutenção são aplicadas com êxito no dispositivo.  


