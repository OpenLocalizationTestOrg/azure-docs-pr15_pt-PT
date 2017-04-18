<properties
    pageTitle="Instalar o IIS no seu VM Windows primeiro | Microsoft Azure"
    description="Experimentar a sua primeira máquina de virtual do Windows instalando o IIS e abrir a porta 80 através do portal Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Experimentar com a instalação de uma função no seu VM do Windows
    
Assim que tiver a sua primeira máquina virtual (VM) e a execução, pode mover instalar software e serviços. Para este tutorial, vamos utilizar o Gestor de servidor na VM de servidor do Windows para instalar o IIS. Em seguida, podemos irá criar um grupo de segurança de rede (NSG) utilizando o portal Azure para abrir a porta 80 para o tráfego de IIS. 

Se já tiver criado a primeira VM, devem ir novamente para [criar a sua primeira máquina de virtual do Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md) antes de prosseguir com este tutorial.

## <a name="make-sure-the-vm-is-running"></a>Certifique-se a VM está a ser executado

1. Abra o [Azure portal](https://portal.azure.com).
2. No menu do centro, clique em **máquinas virtuais**. Selecione a máquina virtual a partir da lista.
3. Se o estado estiver **parado (Deallocated)**, clique no botão **Iniciar** na pá **Essentials** da VM. Se o estado está **em execução**, pode mover para o passo seguinte.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Ligar à máquina virtual e iniciar sessão

1.  No menu do centro, clique em **máquinas virtuais**. Selecione a máquina virtual a partir da lista.

3. No pá para a máquina virtual, clique em **Ligar**. Este procedimento cria e transfere um ficheiro do protocolo de ambiente de trabalho remoto (ficheiro. rdp) que é como um atalho para ligar para o seu computador. Poderá pretender guardar o ficheiro no seu ambiente de trabalho para facilitar o acesso. **Abrir** este ficheiro para ligar à sua VM.

    ![Captura de ecrã do portal do Azure a mostrar como se ligar à sua VM](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Obtém um aviso de que o RDP é a partir de um fabricante desconhecido. Este é o normal. Na janela do ambiente de trabalho remoto, clique em **Ligar** para continuar.

    ![Captura de ecrã de um aviso sobre um fabricante desconhecido](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Na janela de segurança do Windows, escreva o nome de utilizador e palavra-passe da conta local que criou quando criou a VM. O nome de utilizador é introduzido como *vmname*& #92; *nome de utilizador*, em seguida, clique em **OK**.

    ![Captura de ecrã de introduzir o nome, nome de utilizador e palavra-passe VM](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Obtém um aviso de que o certificado não pode ser verificado. Este é o normal. Clique em **Sim** para verificar a identidade da máquina virtual e de conclusão de início de sessão.

    ![Captura de ecrã que mostra uma mensagem sobre a verificar a identidade da VM](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Se lhe encontrar problemas quando tenta ligar, consulte o artigo [resolver problemas de ambiente de trabalho remoto ligações para uma baseados no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Instalar o IIS no seu VM

Agora que tem sessão iniciada VM, podemos instala uma função de servidor, de modo a que possa experimentar mais.

1. Abra o **Gestor de servidor** , se ainda não esteja aberto. Clique no menu **Iniciar** e, em seguida, clique em **Gestor de servidor**.
2. No **Gestor de servidor**, selecione **Servidor Local** a partir do painel esquerdo. 
3. No menu, selecione **Gerir** > **Adicionar funções e funcionalidades**.
4. No Assistente de funcionalidades, na página **Tipo de instalação** e adicionar funções escolher **baseado em funções ou funcionalidade com base no tipo de instalação**e, em seguida, clique em **seguinte**.

    ![Captura de ecrã a mostrar o separador Assistente de funcionalidades e adicionar funções para tipo de instalação](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Selecione a VM do conjunto de servidor e clique em **seguinte**.
6. Na página de **Funções de servidor** , selecione **Servidor Web (IIS)**.

    ![Captura de ecrã a mostrar o separador Assistente de funcionalidades e adicionar funções para funções de servidor](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. No pop-up sobre a adição de funcionalidades necessárias para IIS, certifique-se de que a opção **incluir ferramentas de gestão de** está selecionada e, em seguida, clique em **Adicionar funcionalidades**. Quando fecha o pop-up, clique em **seguinte** do assistente.

    ![Captura de ecrã a mostrar pop-up para confirmar a adição a função IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Na página funcionalidades, clique em **seguinte**.
9. Na página de **Função de servidor Web (IIS)** , clique em **seguinte**. 
10. Na página de **Serviços de função** , clique em **seguinte**. 
11. Na página de **confirmação** , clique em **instalar**. 
12. Quando a instalação estiver concluída, clique em **Fechar** no assistente.



## <a name="open-port-80"></a>Abra a porta 80 

Para sua VM aceitar o tráfego de entrada através da porta 80, é necessário adicionar uma regra de entrada ao grupo de segurança de rede. 

1. Abra o [Azure portal](https://portal.azure.com).
2. Em **máquinas virtuais do** selecione a VM que criou.
3. Nas definições de máquinas virtuais, selecione **interfaces de rede** e, em seguida, selecione a interface de rede existente.

    ![Captura de ecrã a mostrar a definição de máquina virtual para as interfaces de rede](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Na **Essentials** para a interface de rede, clique no **grupo de segurança de rede**.

    ![Captura de ecrã a mostrar a secção Essentials para a interface de rede](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. Na pá **Essentials** para o NSG, que deve ter uma existente predefinido regra de entrada para **rdp predefinido permitir** permite-lhe iniciar sessão na VM. Irá adicionar outra regra de entrada para permitir o tráfego do IIS. Clique em **regra de segurança de entrada**.

    ![Captura de ecrã a mostrar a secção Essentials para o NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Em **regras de segurança de entrada**, clique em **Adicionar**.

    ![Captura de ecrã que mostra o botão para adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Em **regras de segurança de entrada**, clique em **Adicionar**. Escreva **80** no intervalo de portas e certifique-se de que **Permitir** está selecionada. Quando tiver terminado, clique em **OK**.

    ![Captura de ecrã que mostra o botão para adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Para obter mais informações sobre NSGs, regras de entrada e saídas, consulte o artigo [Permitir o acesso externo ao seu VM através do portal Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Ligar para o Web site predefinido do IIS

1. No portal do Azure, clique em **máquinas virtuais** e, em seguida, selecione a VM.
2. No pá **Essentials** , copie o **endereço IP público**.

    ![Captura de ecrã a mostrar onde encontrar o endereço IP público da sua VM](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Abra um browser e na barra de endereço, escreva o endereço IP público da seguinte forma: http://<publicIPaddress> e clique em **Enter** para ir para esse endereço.
3. O browser deve abrir a página de web do IIS predefinida. Parece algo parecido com:

    ![Captura de ecrã que mostra o aspeto da página IIS predefinida num browser](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Próximos passos

- Também pode experimentar com [um disco de dados a anexar](virtual-machines-windows-attach-disk-portal.md) a sua máquina virtual. Dados discos fornecem mais armazenamento da sua máquina virtual.
