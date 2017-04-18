1. Localize o gateway de rede virtual e clique em **todas as definições** para abrir o pá **Definições** .

2. No pá **Definições** , clique em **ligações**e, em seguida, clique em **Adicionar** no topo da pá para abrir o pá **Adicionar ligação** .

    ![Criar ligação de Site para o Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. No pá **Adicionar ligação** , o **nome da** sua ligação. 

4. Para **tipo de ligação**, selecione **Site-to-site(IPSec)**.

5. Para o **gateway de rede Virtual**, o valor é corrigido porque está a ligar a partir deste gateway.

6. Para o **gateway de rede Local**, clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que pretende utilizar. 

7. **Chave partilhada**, aqui, o valor tem de corresponder o valor que está a utilizar para o seu dispositivo VPN local. Se o seu dispositivo VPN na sua rede local não fornece uma chave partilhada, pode completar um e exportá-lo aqui no seu dispositivo local. O importante é que ambos os corresponderem.

8. Os valores restantes de **subscrição**, **Grupo de recursos**e a **localização** são corrigidos.

9. Clique em **OK** para criar a sua ligação. Verá *Criar ligação* flash no ecrã.

10. Quando a ligação estiver concluída, verá esta apareça no pá **ligações** para o Gateway.

    ![Criar ligação de Site para o Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

