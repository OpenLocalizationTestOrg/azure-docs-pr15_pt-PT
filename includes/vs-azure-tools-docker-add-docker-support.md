1. No **Explorador de soluções**do Visual Studio, com o botão direito do projeto e selecione **Adicionar > suporte Docker** no menu de contexto.

    ![Adicionar o menu de contexto de suporte Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Web project adicionar suporte Docker a um Core ASP.NET resulta na adição de vários ficheiros relacionados com Docker ser adicionado ao projeto, incluindo ficheiros compor Docker, scripts de implementação do Windows PowerShell e ficheiros de propriedade Docker. 

    ![Ficheiros de docker adicionados ao projeto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Se utilizar o [Docker para versão Beta do Windows](https://beta.docker.com), abra Properties\Docker.props, remova o valor predefinido e reinicie o Visual Studio relativamente ao valor entre em vigor.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
