<properties 
   pageTitle="Configurar e utilizar o armazenamento emulador com o Visual Studio | Microsoft Azure"
   description="Configurar e utilizar o armazenamento emulador com o Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurar e utilizar o armazenamento emulador com o Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral
O ambiente de desenvolvimento do Azure SDK inclui o emulador de armazenamento, um utilitário que simula serviços de armazenamento Blob, fila de espera e tabela disponíveis no Azure no seu computador de desenvolvimento local. Se for construir um serviço na nuvem que utiliza os serviços de armazenamento Azure ou escrever qualquer aplicação externa que liga para os serviços de armazenamento, pode testar o seu código localmente contra emulador de armazenamento. As ferramentas do Azure para o Microsoft Visual Studio integrar gestão do emulador armazenamento no Visual Studio. As ferramentas do Azure iniciar a base de dados do armazenamento emulador na primeira utilização, inicia o serviço de armazenamento do emulador ao executar ou depurar o seu código do Visual Studio e oferece um acesso só de leitura aos dados emulador armazenamento através do Explorador de armazenamento do Azure.

Para informações detalhadas sobre o emulador de armazenamento, incluindo os requisitos de sistema e de instruções de configuração personalizado, consulte o artigo [utilizar o Azure armazenamento emulador desenvolvimento e testar](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Existem algumas diferenças de funcionalidade entre a simulação de emulador de armazenamento e os serviços de armazenamento Azure. Consulte o artigo [diferenças entre armazenamento emulador e dos serviços de armazenamento do Azure](./storage/storage-use-emulator.md) na documentação do Azure SDK para obter informações sobre as diferenças específicas.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de ligação para o emulador de armazenamento

Para aceder ao emulador de armazenamento de código dentro de uma função, irá pretende configurar uma cadeia de ligação que aponta para o emulador de armazenamento e que mais tarde pode ser alterado para apontar para uma conta de armazenamento Azure. Uma cadeia de ligação é uma definição de configuração ao seu cargo pode ler o tempo de execução para ligar a uma conta de armazenamento. Para mais informações sobre como criar cadeias de ligação, consulte o artigo [Configurar a aplicação do Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Pode devolver uma referência para a conta de emulador de armazenamento a partir do seu código, utilizando a propriedade **DevelopmentStorageAccount** . Esta abordagem funciona corretamente se pretender aceder emulador de armazenamento do seu código, mas se planeia publicar a sua aplicação ao Azure, terá de criar uma cadeia de ligação para aceder à sua conta de armazenamento Azure e modificar o seu código para utilizar essa cadeia de ligação antes de a publicar. Se está a mudar entre a conta de emulador de armazenamento e uma conta de armazenamento Azure frequentemente, uma cadeia de ligação será simplificar a este processo.

## <a name="initializing-and-running-the-storage-emulator"></a>A inicialização e executar o emulador de armazenamento

Pode especificar que quando executar ou depurar o seu serviço no Visual Studio, Visual Studio inicia automaticamente o emulador de armazenamento. No Explorador de solução, abra o menu de atalho para o seu projeto do **Azure** e selecione **Propriedades**. No separador de **desenvolvimento** , na lista de **Iniciar o Azure armazenamento emulador** , selecione **Verdadeiro** (se já não está definida para esse valor).

A primeira vez que execute ou depurar o seu serviço a partir do Visual Studio, o emulador armazenamento inicia um processo de inicialização. Este processo reservas portas locais para o emulador de armazenamento e cria a base de dados do armazenamento emulador. Depois de concluído, este processo não é necessário executar novamente a menos que a base de dados do armazenamento emulador é eliminado.

>[AZURE.NOTE] Começar com a versão de Junho de 2012 das ferramentas do Azure, emulador de armazenamento é executado, por predefinição, no SQL Express LocalDB. Lançamentos anteriores das ferramentas do Azure emulador de armazenamento é executado contra uma instância predefinida do SQL Express 2005 ou 2008, que tem de instalar antes pode instalar o SDK do Azure. Também pode executar o emulador armazenamento contra uma com nome instância do SQL Express ou com um nome ou instância predefinida do Microsoft SQL Server. Se precisar de configurar o emulador de armazenamento para executar contra uma instância que não seja a instância predefinida, consulte o artigo [utilizar o Azure armazenamento emulador desenvolvimento e testar](./storage/storage-use-emulator.md).

O emulador armazenamento fornece uma interface de utilizador para ver o estado dos serviços de armazenamento local e para iniciar, parar e repô-las. Assim que o serviço de armazenamento do emulador foi iniciado, pode apresentar a interface de utilizador ou iniciar ou parar o serviço clicando com o ícone de área de notificação do Microsoft Azure emulador na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualização de dados de emulador de armazenamento no Explorador do servidor

O nó de armazenamento do Windows Azure no servidor Explorer permite-lhe ver dados e alterar as definições para os dados blob e tabela nas suas contas de armazenamento, incluindo o emulador de armazenamento. Para mais informações, consulte [a navegação e gestão de recursos de armazenamento com o Explorador de servidor](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
