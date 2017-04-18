<properties
   pageTitle="Gestão de funções na nuvem Azure dos serviços de projetos com o Visual Studio | Microsoft Azure"
   description="Saiba como adicionar novas funções ao seu projeto do serviço de nuvem Azure ou remover funções existentes utilizando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gerir funções nos projectos de serviços na nuvem Azure com o Visual Studio

Depois de ter criado o projeto de serviço de nuvem Azure, pode adicionar novas funções ao mesmo ou remover funções existentes. Também pode importar um projeto existente e convertê-la a uma função. Por exemplo, pode importar uma aplicação web do ASP.NET e designá-la como uma função da web.

## <a name="adding-or-removing-roles"></a>Adicionar ou remover funções

**Para adicionar uma função**

No **Explorador de soluções**, abra o menu de atalho para o nó **funções** no seu projeto do serviço de nuvem e selecione **Adicionar**. Pode selecionar uma função de web existente ou trabalho a partir de solução atual ou criar um novo projeto de funções web ou de trabalho. Ou pode selecionar um projeto adequado, tal como um projeto de aplicação web do ASP.NET e associá-lo a um projeto de função.

**Para remover uma associação de função**

No nó **funções** do projeto de serviço na nuvem no Explorador de solução, abra o menu de atalho para a função a remover e selecione **Remover**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Remover e adicionar funções no seu serviço de nuvem

Se remover uma função a partir do seu projeto do serviço de nuvem, mas mais tarde, decidir adicionar a função novamente ao projeto, apenas a declaração de função e atributos básicos, tais como os pontos finais e diagnósticos de informações, são adicionados. Sem recursos adicionais ou referências são adicionadas ao ficheiro ServiceDefinition.csdef ou para o ficheiro ServiceConfiguration.cscfg. Se pretender adicionar esta informação, terá de adicioná-lo manualmente novamente nestes ficheiros.

Por exemplo, pode remover uma função de serviço web e mais tarde decidir voltar a adicionar esta função a solução. Se efetuar o seguinte, irá ocorrer um erro. Para evitar este erro, tem de adicionar o `<LocalResources>` elemento apresentado na seguinte XML novamente para o ficheiro de ServiceDefinition.csdef. Utilize o nome da função de serviço web que tenha adicionado novamente no projeto como parte do atributo nome para o **<LocalStorage>** elemento. Neste exemplo, o nome da função de serviço web é **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como configurar funções no Visual Studio ao ler o artigo [configurar as funções para um serviço em nuvem Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
