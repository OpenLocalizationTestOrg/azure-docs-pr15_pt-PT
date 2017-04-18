
1. No pá **ligações híbrido** , clique na ligação híbrido que acabou de criar, em seguida, clique em **Configuração escuta**.
    
    ![Clique em configuração escuta](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. A **Propriedades de ligação híbrido** pá é aberta. Em **Gestor de ligação de híbrido no local**, selecione **Transferir e configurar manualmente**, guarde o pacote de HybridConnectionManager.msi transferido e copie a cadeia de ligação de gateway.
    
    ![Clique aqui para instalar](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. A partir de uma linha de comandos de administrador, escreva o seguinte comando para iniciar o programa de instalação:

        start HybridConnectionManager.msi
 
7. Depois de executa o programa de instalação, clique em **não agora**, em seguida, navegue para a pasta %ProgramFiles%\Microsoft\HybridConnectionManager, executar HCMConfigWizard.exe e clique em **Sim** na caixa de diálogo **Controlo de conta de utilizador** .
        
7. Cole a cadeia de ligação de híbrido que copiou anteriormente e clique em **OK**. 
    
    ![Instalar o](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Quando concluir a instalação, clique em **Fechar**.
    
    ![Clique em Fechar](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    No pá **ligações híbrido** , a coluna **Estado** mostra agora **ligado**. 
    
    ![Estado ligado](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)