<properties
   pageTitle="Configurar um projeto de serviço de nuvem Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como configurar um projeto de serviço de nuvem Azure no Visual Studio, dependendo do seu requisitos para que o project."
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

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurar um projeto de serviço de nuvem Azure com o Visual Studio

Pode configurar um projecto de serviço de nuvem Azure, dependendo do seu requisitos para que o project. Pode definir propriedades do projeto para as seguintes categorias:

- **Publicar um serviço na nuvem no Azure**

  Pode definir uma propriedade para se certificar de que um serviço de nuvem existente implementado Azure não é eliminado acidentalmente.

- **Executar ou depuração de um serviço na nuvem no computador local**

  Pode selecionar uma configuração de serviço para utilizar e indique se pretende iniciar o emulador armazenamento Azure.

- **Validar um pacote de serviço de nuvem, quando é criado**

  Pode optar por trata os avisos como erros para que pode certificar-se de que o pacote de serviço de nuvem irá implementar sem problemas. Este procedimento reduz o seu tempo de espera, se implementar e, em seguida, descobrir que ocorreu uma falha.

A ilustração seguinte mostra como selecionar uma configuração para utilizar quando executar ou depurar localmente o seu serviço de nuvem. Pode definir alguma das propriedades do projeto que requerem a partir desta janela, conforme mostrado na ilustração.

![Configurar um projeto do Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Para configurar um projeto de serviço de nuvem Azure

1. Para configurar um projecto de serviço de nuvem a partir do **Explorador de solução**, abra o menu de atalho para o projeto de serviço de nuvem e, em seguida, selecione **Propriedades**.

  É apresentada uma página com o nome do projeto de serviço na nuvem no editor do Visual Studio.

1. Selecione o separador de **desenvolvimento** .

1. Para se certificar de que não elimina acidentalmente uma implementação existente no Azure, na linha de comandos do antes de o eliminar uma lista de implementação existente, selecione **Verdadeiro**.

1. Para selecionar a configuração do serviço que pretende utilizar quando executar ou depurar localmente, o seu serviço de nuvem na lista de **configuração do serviço** , selecione a configuração do serviço.

  >[AZURE.NOTE] Se pretende criar uma configuração de serviço para utilizar, consulte o artigo como: Gerir configurações do serviço e perfis. Se pretender modificar uma configuração de serviço para uma função, consulte o artigo [como configurar as funções para um serviço em nuvem Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para iniciar o armazenamento Azure emulador quando executa ou depurar localmente, o seu serviço de nuvem no **emulador de armazenamento do Azure iniciar**, escolha **Verdadeiro**.

1. Para se certificar de que não é possível publicar se existem erros de validação do pacote, no **avisos tratar como erros**, selecione **Verdadeiro**.

1. Para se certificar de que ao seu cargo web utiliza a mesma porta de cada vez que for iniciado localmente no IIS Express, nos **utilizam portas de projeto web**, selecione **Verdadeiro**. Para utilizar uma porta específica para um projeto de web específica, abrir o menu de atalho para o projecto da web, selecione o separador **Propriedades** , selecione o separador **Web** e alterar o número de porta na definição **Url do Project** , na secção **IIS Express** . Por exemplo, introduza `http://localhost:14020` como o URL do project.

1. Para guardar as alterações que fez às propriedades do projeto de serviço de nuvem, selecione o botão **Guardar** na barra de ferramentas.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como configurar projetos de serviço de nuvem Azure no Visual Studio, consulte o artigo [configurar o Azure projeto utilizando vários configurações do serviço](vs-azure-tools-multiple-services-project-configurations.md).
