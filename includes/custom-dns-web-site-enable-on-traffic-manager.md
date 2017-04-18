Depois de tem propagadas os registos para o seu nome de domínio, deverá conseguir utilizar o seu browser para verificar se o seu nome de domínio personalizado pode ser utilizada para aceder à sua aplicação web na aplicação de serviço de Azure.

> [AZURE.NOTE] Pode demorar algum tempo para o seu CNAME a serem propagadas através do sistema DNS. Pode utilizar um serviço como o <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

Se ainda não tiver a aplicação web do adicionado como um ponto final de tráfego Gestor, deve fazê-lo antes de resolução do nome irá funcionar, como as rotas de nome de domínio personalizado para o tráfego Manager. Gestor de tráfego, em seguida, rotas para a sua aplicação web. Utilize as informações em [Adicionar ou eliminar pontos finais](../articles/traffic-manager/traffic-manager-endpoints.md) para adicionar a sua aplicação web como um ponto final no seu perfil do Gestor de tráfego.

> [AZURE.NOTE] Se a sua aplicação web não estiver listada ao adicionar um ponto final, certifique-se de que está configurado para o modo de plano de serviço de aplicação **padrão** . Tem de utilizar o modo **padrão** para a sua aplicação web para poder trabalhar com o tráfego Manager.

1. No seu browser, abra o [Portal do Azure](https://portal.azure.com).

1. No separador **Web Apps** , clique no nome da sua aplicação web, selecione **Definições**e, em seguida, selecione **domínios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. Na pá **domínios personalizados** , clique em **Adicionar nome do anfitrião**.
    
1. Utilize as caixas de texto **nome do anfitrião** para introduzir o nome de domínio do Gestor de tráfego para associar esta aplicação web.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Clique em **Validar** para guardar a configuração do nome de domínio.

7.  Após clicar em **Validar** Azure o irá iniciar fluxo de trabalho de verificação de domínios. Isto irá procurar a propriedade do domínio, bem como sucesso de disponibilidade e relatório do nome do anfitrião ou detalhada do erro com aconselhar à sobre como corrigir o erro.    

8.  Após a validação com êxito **hostname adicionar** botão irão tornar-se ativa e poderão atribuir o nome do anfitrião. Agora, navegue para o seu nome de domínio personalizado num browser. Agora deverá ver os em execução aplicação com o seu nome de domínio personalizado. 

    Assim que tiver concluído a configuração, o nome de domínio personalizado será listado na secção de **nomes de domínio** da sua aplicação web.

Neste momento, deverá conseguir introduzir o nome de nome de domínio do Gestor de tráfego no seu browser e consulte o artigo que-lo com êxito leva-o para a sua aplicação web.
