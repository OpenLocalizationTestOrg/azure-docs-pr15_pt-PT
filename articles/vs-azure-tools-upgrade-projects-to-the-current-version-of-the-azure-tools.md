<properties
   pageTitle="Como atualizar projetos para a versão atual das ferramentas do Azure | Microsoft Azure"
   description="Saiba como atualizar um projeto Azure no Visual Studio para a versão atual das ferramentas do Azure"
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

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Como atualizar projetos para a versão atual das ferramentas do Azure para Visual Studio

## <a name="overview"></a>Descrição geral

Depois de instalar a versão atual das ferramentas do Azure (ou uma versão anterior, que é mais recente que 1.6), quaisquer projectos que foram criados ao utilizar um ferramentas Azure liberte antes de 1.6 (de Novembro de 2011) será automaticamente atualizada assim a abri-los. Se tiver criado projetos utilizando a versão (de Novembro de 2011) 1.6 de entre as ferramentas e continua a ter essa edição instalada, pode abrir esses projetos na versão mais antiga e mais tarde, decidir se pretende atualizá-los.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Como o project altera quando atualiza o-lo

Se um projeto é atualizado automaticamente ou pode especificar que pretende atualizá-lo, o projeto é modificado para trabalhar com versões atuais dos determinados conjuntos e algumas propriedades também são alteradas como esta secção descreve. Se o seu projeto exigir outras alterações para ser compatível com a versão mais recente das ferramentas, tem de fazer essas alterações manualmente.

- O ficheiro da Web. config para funções de web e o ficheiro de App para funções de trabalho são atualizadas para fazer referência a versão mais recente do Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Os conjuntos de Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll são actualizados para as novas versões.

- Publicar perfis que foram guardados num ficheiro do projeto Azure (.ccproj) são movidos para um ficheiro em separado, com .azurePubXml extensão, no subdirectório **Publicar** .

- Algumas propriedades no perfil de publicar são atualizadas para suportar funcionalidades novas e alteradas. **AllowUpgrade** é substituído por **DeploymentReplacementMethod** uma vez que pode atualizar um serviço na nuvem implementado em simultâneo ou incrementada.

- A propriedade **UseIISExpressByDefault** é adicionada e definida como FALSO, para que o servidor web que é utilizado para depuração não altera automaticamente a partir de serviços de informação Internet (IIS) para IIS Express. IIS Express é o servidor web predefinido para projetos que são criados com as versões mais recentes das ferramentas.

- Se Azure colocação em cache estiver alojada num ou mais das funções do seu projeto, algumas propriedades na configuração do serviço (ficheiro .cscfg) e definição de serviço (ficheiro .csdef) são alteradas quando um projeto é atualizado. Se o project utiliza o pacote do Azure colocação em cache NuGet, o projeto é atualizado para a versão mais recente do pacote. Deve abrir o ficheiro da Web. config e certifique-se de que a configuração do cliente foi mantida corretamente durante o processo de atualização. Se tiver adicionado as referências para conjuntos de cliente Azure colocação em cache sem utilizar o pacote de NuGet, estes conjuntos não serão atualizados; tem de atualizar manualmente estas referências para as versões novas.

>[AZURE.IMPORTANT] Para F # projetos, terá de actualizar manualmente referências aos conjuntos Azure para que que fazem referência as versões mais recentes aos conjuntos.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Como atualizar um projeto Azure para a versão atual

1. Instalar a versão atual das ferramentas do Azure para a instalação do Visual Studio que pretende utilizar para o projeto atualizado e, em seguida, abra o projeto que pretende atualizar. Se o projeto foi criado com uma ferramentas Azure liberte antes de 1.6 (de Novembro de 2011), o projeto automaticamente é atualizado para a versão atual. Se o projeto foi criado com Novembro de 2011 disponibilização e esse lançamento ainda se encontra instalado, o project abre esse lançamento.

1. No Explorador de solução, abrir o menu de atalho para o nó do projeto, selecione **Propriedades**e, em seguida, selecione o separador da **aplicação** da caixa de diálogo que aparece.

    No separador da **aplicação** mostra a versão de ferramentas que está associada o projetos. Se a versão atual do Azure ferramentas for apresentada, o projeto já foi actualizado. Se instalou uma versão mais recente das ferramentas de que o que é apresentado no separador, aparece um botão **de atualização** .

1. Selecione o botão **Atualizar** para atualizar um projeto para a versão atual das ferramentas.

1. Criar o projeto e, em seguida, resolva os eventuais erros que resultam de alterações da API. Para obter informações sobre como modificar o seu código para a nova versão, consulte a documentação para a API específica.
