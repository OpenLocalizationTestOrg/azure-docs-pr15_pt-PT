Depois de tem propagadas os registos para o seu nome de domínio, tem de associá-los com a aplicação Web. Utilize os passos seguintes para ativar os nomes de domínio com o browser.

> [AZURE.NOTE] Pode demorar algum tempo para registos TXT criada nos passos anteriores para propagar através do sistema DNS. Não pode adicionar o nome de domínio para a sua aplicação web até que o registo TXT tem propagadas. Se estiver a utilizar um registo, não pode adicionar o nome de domínio registo A para a sua aplicação web até que o registo TXT que criou no passo anterior tem propagadas.
>
> Pode utilizar um serviço como o <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o registo TXT está disponível.

1. No seu browser, abra o [Portal do Azure](https://portal.azure.com).

2. No separador **Web Apps** , clique no nome da sua aplicação web e, em seguida, selecione **domínios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Na pá **domínios personalizados** , clique em **Adicionar nome do anfitrião**.
    
4. Utilize as caixas de texto **nome do anfitrião** para introduzir os nomes de domínio para associar esta aplicação web.

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Clique em **Validar**.

7.  Após clicar em **Validar** Azure o irá iniciar fluxo de trabalho de verificação de domínios. Isto irá procurar a propriedade do domínio, bem como sucesso de disponibilidade e relatório do nome do anfitrião ou detalhada do erro com aconselhar à sobre como corrigir o erro.    

Neste momento, deverá conseguir introduza o nome de domínio personalizado no seu browser e ver que-lo com êxito leva-o para a sua aplicação web.
