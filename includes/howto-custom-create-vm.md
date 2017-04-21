#<a name="how-to-create-a-custom-virtual-machine"></a>Como criar uma Máquina Virtual personalizado

Uma máquina virtual *personalizado* que se refere a uma máquina de virtual que cria utilizando o método de **Galeria a partir de** uma vez que permite-lhe configurar mais opções de que o método de **Criação rápida** . Estas opções incluem:

- Mais opções para a imagem utilizar para criar a máquina virtual (VM)
- Ligar a VM a uma rede virtual
- Adicionar a VM a um serviço de nuvem existente
- Adicionar a VM a um conjunto de disponibilidade

> [AZURE.IMPORTANT] Se pretender que o seu máquina virtual para utilizar uma rede virtual para que pode ligar-se para a mesma diretamente pelo nome do anfitrião ou configurar o ligações de publicação em local, certifique-se de que especificar a rede virtual quando cria a máquina virtual. Uma máquina virtual pode ser configurada para aderir a uma rede virtual apenas quando criar a máquina virtual. Para mais informações sobre redes virtuais, consulte o artigo [Descrição geral de rede Virtual Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Inicie sessão no [portal do Azure](http://manage.windowsazure.com).

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Calcular**, clique em **Máquina Virtual**e, em seguida, clique na **Partir da Galeria**.

4. Selecione a imagem que pretende utilizar e, em seguida, clique na seta para continuar.

5. Se estão disponíveis, na **Data de lançamento versão**várias versões da imagem, selecione a versão que pretende utilizar.

6. Em **Nome da Máquina Virtual**, escreva o nome que pretende utilizar para a máquina virtual.

7. Utilize **camadas** e o **tamanho** para selecionar o tamanho adequado para a máquina virtual. O tamanho que seleccionar afeta a configuração máxima de máquina virtual, bem como os preços. Para obter detalhes de configuração, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. No **Novo nome de utilizador**, escreva um nome para a conta administrativo que pretende utilizar para gerir o servidor.

9. Na **Nova palavra-passe**, escreva uma palavra-passe segura da conta de administrador. Em **Confirmar palavra-passe**, escreva novamente a mesma palavra-passe.

10. Clique na seta para continuar.

11. No **Serviço na nuvem**, efetue um dos seguintes procedimentos:

    - Se esta for a primeira ou apenas máquina virtual o serviço de nuvem, selecione **criar um novo serviço na nuvem**. Em seguida, em **Nome de DNS do serviço de nuvem**, escreva um nome que utiliza entre 3 e 24 letras em minúsculas e números. Este nome torna-se a parte do URI que é utilizado para contactar a máquina virtual através do serviço de nuvem.
    - Se está a ser adicionada esta máquina virtual num serviço na nuvem, selecione-o na lista.

    > [AZURE.NOTE] Para mais informações sobre como colocar máquinas virtuais no serviço de nuvem do mesmo, consulte o artigo [como ligar máquinas virtuais num serviço de nuvem](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Na **Região/afinidade grupo/Virtual rede**, selecione a região, grupo afinidade ou rede virtual que pretende utilizar para a máquina virtual. Para mais informações sobre os grupos de afinidade, consulte o artigo [sobre grupos de afinidade para rede Virtual](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

13. Na **Conta de armazenamento**, selecione uma conta de armazenamento existente para o ficheiro VHD ou utilizar uma conta de armazenamento gerado automaticamente. Apenas a uma conta de armazenamento por região é criada automaticamente. Todas as outras máquinas virtuais que criou com esta definição estiver localizadas nesta conta de armazenamento. Está limitado a 20 contas de armazenamento.

14. Se pretender que a máquina virtual para pertence a um conjunto de disponibilidade, no **Conjunto de disponibilidade**, selecione **Criar disponibilidade definir** ou adicioná-lo a um conjunto de disponibilidade existente.

    **Nota**: máquinas virtuais num conjunto de disponibilidade são implementado domínios falha diferentes. Colocar várias máquinas virtuais na disponibilidade de uma conjunto de ajuda a garantir que a aplicação está disponível durante falhas de rede, falhas de hardware de disco local e qualquer tempo de inatividade planeado.

15.  Em **pontos finais**, reveja os pontos finais novos que serão criados para permitir ligações para a máquina virtual, tais através de ambiente de trabalho remoto ou um cliente de Shell seguro (SSH). Também pode adicionar os pontos finais agora ou criá-las mais tarde. Para obter instruções sobre como criá-las mais tarde, consulte o artigo [como definir o pontos finais para uma Máquina Virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

16.  Em **VM agente**, decida se pretende instalar o agente VM. Este agente fornece o ambiente para instalar as extensões de que podem ajudá-lo interagir com a máquina virtual. Para obter detalhes, consulte o artigo [Gerir as extensões](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Clique na seta para criar a máquina virtual.

    ![Criação de máquina virtual personalizado com êxito](./media/howto-custom-create-vm/VMSuccessWindows.png)

##<a name="next-steps"></a>Próximos passos##
Quando a máquina virtual estiver criada, é iniciado automaticamente. Quando o portal mostra o estado como em execução, pode iniciar sessão para a máquina virtual. Para obter instruções, consulte um dos seguintes artigos:

- [Como iniciar sessão numa máquina de Virtual a executar o Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Como iniciar sessão numa máquina de Virtual com o Windows Server](../articles/virtual-machines/virtual-machines-windows-classic-connect-logon.md)

