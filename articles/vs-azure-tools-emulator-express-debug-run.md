<properties
   pageTitle="Utilizando o emulador Express para executar e depurar um serviço na nuvem, num computador local | Microsoft Azure"
   description="Utilizando o emulador Express para executar e depurar um serviço na nuvem, num computador local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Utilizando o emulador Express para executar e depurar um serviço na nuvem, num computador local

Ao utilizar emulador Express, pode testar e depurar um serviço na nuvem sem executar o Visual Studio como administrador. Pode configurar as definições de projeto para utilizar emulador Express ou o emulador completo, consoante os requisitos do seu serviço de nuvem. Para mais informações sobre o emulador completo, consulte o artigo [executar uma aplicação do Azure no emulador calcular](./storage/storage-use-emulator.md). Emulador Express foi incluída pela primeira vez no Azure SDK 2.1 e partir Azure SDK 2.3, é o emulador predefinido.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Utilizar o Visual Studio IDE emulador Express

Quando cria um novo projeto no Azure SDK 2.3 ou posterior, emulador Express já está selecionada. Projetos existentes que foram criados com uma versão anterior do SDK, siga estes passos para seleccionar emulador Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Para configurar um projecto para utilizar o emulador Express

1. No menu de atalho para o projeto Azure, selecione **Propriedades**e, em seguida, selecione o separador **Web** .

1. Em **Servidor de desenvolvimento Local**, selecione o botão de **opção utilizar IIS Express** . Emulador Express não é compatível com o servidor do IIS Web.

1. Em **emulador**, selecione o botão de opção **Utilizar emulador Express** .

    ![Emulador Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Procurar ClipArt emulador Express numa linha de comandos

Numa linha de comandos, pode iniciar a versão do Azure calcular emulador, csrun.exe, expressa utilizando a opção /useemulatorexpress.

## <a name="limitations"></a>Limitações

Antes de utilizar emulador Express, deverá ter em atenção algumas limitações:

- Emulador Express não é compatível com o servidor do IIS Web.

- O serviço de nuvem pode conter várias funções, mas cada função está limitada a uma instância.

- Não consegue aceder números de porta abaixo 1000. Por exemplo, se utilizar um fornecedor de autenticação que normalmente utiliza uma porta abaixo 1000, poderá ter de alterar este valor para um número de porta for superior a 1000.

- Quaisquer limitações que se aplicam ao emulador de Azure calcular também se aplicam ao emulador Express. Por exemplo, não pode ter mais de 50 instâncias de função por implementação. Consulte o artigo [executar uma aplicação no emulador de cluster Azure](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Próximos passos

[Depuração de serviços em nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)
