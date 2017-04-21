
O Gestor de ligação híbrido permite que o computador de no local ligar ao Azure e reencaminhamento tráfego TCP. Tem de instalar o Gestor de um computador local que pode ligar-se para a sua instância do SQL Server.

1. A ligação que acabou de criar deve ter **Estado** **no premesis configuração incompleta**. Clique nesta ligação e clique em **configuração no local**.

    ![O programa de configuração no local](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Clique em **instalar e configurar**.

    Este procedimento instala uma instância personalizada do Gestor de ligação, que já está a ser pré-configurada para trabalhar com a ligação híbrido que acabou de criar.

3. Conclua o resto dos passos de configuração para o Gestor de ligação.

    Após a instalação estiver concluída, o estado da ligação híbrido será alterado para **1 instância ligado**. Poderá ter de Atualize o browser e aguarde alguns minutos. 

A configuração de ligação híbrido agora está concluída.