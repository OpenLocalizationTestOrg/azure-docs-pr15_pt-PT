Azure Marketplace disponibiliza uma vasta gama de aplicações web populares desenvolvidos pela Microsoft, empresas de terceiros e iniciativas de software de abrir origem. Aplicações Web criadas a partir do Azure Marketplace não necessitam de instalação de qualquer software que não seja o browser utilizado para ligar para o [Portal de pré-visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

Neste tutorial, irá aprender:

- Como criar uma nova aplicação web através do Azure Marketplace.

- Como implementar a aplicação web através do Portal de pré-visualização do Azure.
 
Irá criar um blogue de WordPress que utiliza um modelo predefinido. A ilustração seguinte mostra a aplicação concluída:


![Blogue de WordPress][13]

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="create-a-web-app-in-the-portal"></a>Criar uma aplicação web no portal

1. Iniciar sessão no Portal do Azure pré-visualização.

2. Abra o Azure Marketplace quer ao clicar no ícone de **Marketplace** :

    ![Ícone de Marketplace][marketplace]

    Ou ao clicar no ícone de **Novo** no canto superior direito do dashboard e selecionar **Marketplace** na bottow da lista.
    
    ![Criar novas][5]
    
3. Selecione **Web + Mobile**. Procurar **WordPress** e clique no ícone de **WordPress** .

    ![WordPress a partir da lista][7]
    
5. Depois de ler a descrição da aplicação WordPress, selecione **Criar**.

6. Clique em aplicação **Web**e forneça os valores necessários para configurar a sua aplicação web.
    
    ![configurar a sua aplicação][8]

7. Clique na **base de dados**e forneça os valores necessários para configurar a sua base de dados do MySQL. 

    ![configurar base de dados][database]

8. Forneça um nome para um novo grupo de recursos.

    ![Grupo de recursos do conjunto][groupname]

8. Se for necessário, clique em **subscrição**e, especifique a subscrição para utilizar. 

7. Quando tiver terminado a definição do web app, clique em **Criar**e aguarde enquanto a nova aplicação web é criada.

   Quando a aplicação foi criada, irá ver o grupo de recursos que contém web app e base de dados.

   ![grupo Mostrar][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciação e gerir a sua aplicação web do WordPress
    
1. Clique na sua nova aplicação web para ver detalhes sobre a sua aplicação.

    ![dashboard de iniciação][10]

2. Na página **Essentials** , clique em **Procurar** ou na ligação em **Url** para abrir a página de boas-vindas do web app.

    ![URL do site][browse]

3. Se não tiver instalado WordPress, introduza as informações de configuração apropriada uma ferramenta necessária ao WordPress e clique em **Instalar WordPress** para finalizar a configuração e abra a página de início de sessão do web app.

4. Clique em **início de sessão** e introduza as suas credenciais.  

5. Terá uma WordPress aplicação web do que um aspeto semelhante a aplicação web abaixo.    

    ![o site de WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
