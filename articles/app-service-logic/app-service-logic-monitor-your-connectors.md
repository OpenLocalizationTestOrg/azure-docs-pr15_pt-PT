<properties
    pageTitle="Gerir e monitorizar o seu conectores e Apps API na aplicação de serviço de | Microsoft Azure"
    description="Ver o desempenho dos seus conectores e Apps API nas aplicações de lógica; arquitetura de microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Gerir e monitorizar o seu incorporada aplicações API e conectores

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

O utilizador criou uma aplicação de API incorporada. E agora?

No Azure, cada aplicação API é um web site separado alojado no Azure. Como resultado, pode facilmente ver quantas solicitações feitas e ver a quantidade de dados está a ser utilizado pelo conector. Também pode fazer cópia de segurança a aplicação de API, criar alertas, ativar Tinfoil segurança e adicionar utilizadores e funções.

Este tópico descreve algumas das opções de diferentes para gerir a sua aplicação API.

Para ver estas funcionalidades incorporadas, abra a sua aplicação API no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Se estiver a aplicação API no seu startboard, selecione-o para abrir as propriedades. Pode também selecionar **Procurar**, selecione **Aplicações API**e, em seguida, selecione a sua aplicação API:

![][browse]

## <a name="see-the-properties-you-entered"></a>Ver as propriedades que introduziu

Quando abre a aplicação API, existem várias tarefas e funcionalidades disponíveis:

![][settings]

Pode:

- **Definições** mostra informações específicas sobre a aplicação API, incluindo os detalhes da subscrição e lista os utilizadores que tem acesso à sua aplicação API. Também pode aumentar ou diminuir o número de instâncias da sua aplicação de API utilizando a funcionalidade de escala entre outras funcionalidades.
- Utilize os botões **Iniciar** e **Parar** para controlar a aplicação de API.
- Quando são feitas atualizações de produto para os ficheiros subjacentes utilizados pelo sua aplicação API, pode clicar em **Atualizar** para obter as versões mais recentes. Por exemplo, se existir um fix ou uma atualização de segurança disponibilizada pela Microsoft, clicando em **Atualizar** automaticamente atualiza a aplicação de API para incluir este fix.
- Selecione **Alterar planear** a atualização ou uma diminuição no nível com base na utilização de dados da aplicação API. Também pode utilizar esta funcionalidade para ver a utilização de dados.
- Quando cria uma conexão que tem a tabelas, como o conector SQL, opcionalmente, pode introduzir um nome da tabela para ligar à. Um esquema baseado na tabela é automaticamente criado e disponível quando clica em **Esquemas transferir**. Em seguida, pode utilizar este esquema transferida para criar uma transformação ou um mapa.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Alterar o seu conexão ou valores de configuração de API que introduziu

Depois de configurado ou criado a conexão incorporada, pode alterar os valores introduzidos. Por exemplo, se tiver configurado o conector de SQL e que pretende alterar o nome do SQL Server ou o nome da tabela, que pode fazer isto no pá a aplicação de API para a conexão.

Passos incluem:

1. Abra a conexão ou uma aplicação API. Quando o fizer, é aberta a pá API aplicação.
2. Na **Essentials**, clique na hiperligação na propriedade do anfitrião. A hiperligação é denominado por algo como *slackconnector* ou *microsoftsqlconnector123*:

    ![][apiapphost]

3. Na pá API anfitrião de aplicação, selecione **Definições**. Na pá definições, selecione **Definições da aplicação**. Os valores de configuração são listados em **Definições da aplicação**:

    ![][hostsettings]

4. Clique na definição que pretende alterar, introduza o novo valor e **guarde** as suas alterações.


## <a name="install-the-hybrid-connection-manager---optional"></a>Instalar o Gestor de ligação híbrido - opcional

![][hcsetup]

O Gestor de ligação híbrido dá-lhe a capacidade de ligar a um sistema no local, como o SQL Server ou do SAP. Este conectividade híbrido utiliza Azure Service Bus para ligar e para controlar a segurança entre os recursos do Azure e os recursos no local.

Consulte o artigo [utilizar o Gestor de ligação híbrido no Azure de aplicação de serviço](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Gestor de ligação de híbrido é necessário apenas se estiver a ligar a um recurso no local atrás da firewall. Se não estiver a ligar a um sistema no local, o Gestor de ligação híbrido poderão não ser listado na sua pá de conexão.

## <a name="monitor-the-performance"></a>Monitorizar o desempenho
Métricas de desempenho são funcionalidades incorporadas e incluído com cada aplicação API que cria. Estes métricas são específicas a sua aplicação alojada no Azure API. Métricas de exemplo:

![][monitoring]

Pode:

- Selecione **os pedidos e erros** para adicionar métricas de desempenho diferentes, incluindo códigos de erro HTTP conhecidos frequentemente, como 200, 400 ou 500 códigos de estado HTTP. Também pode ver os tempos de resposta, consulte pedidos quantas são efectuados à aplicação do API e consulte quantidade de dados é recebida e apagar quantidade de dados. Com base de métricas de desempenho, pode criar alertas de e-mail se uma métrica excede um limiar da sua escolha.
- Em **utilização**, pode ver quanto **CPU** é utilizado pela aplicação do API, reveja a **Quota de utilização** de atual em MB e ver a utilização do máxima de dados com base na sua camada de custo. **Passam a estimativa** pode ajudar a determinar os custos potenciais básicos para realizar a sua aplicação API.
- Selecione **processos** para abrir o Explorador de processo. Isto mostra as instâncias de web e respetivas propriedades, incluindo a utilização de memória e de contagem de tópico.

Utilizar estas ferramentas, pode determinar se o plano de serviço de aplicação deverão ser dimensionado para cima ou para baixo, dimensionado com base nas necessidades da sua empresa. Estas funcionalidades estão incorporadas para o portal com sem ferramentas adicionais necessárias.

## <a name="control-the-security"></a>Controlar a segurança

API aplicações utilize baseado em funções de segurança. Estas funções aplicam-se para a experiência de Azure toda, incluindo as aplicações do API e outros recursos Azure. As funções incluem:

Função | Descrição
--- | ---
Proprietário | Ter acesso completo à experiência de gestão e conceder acesso a outros utilizadores ou grupos.
Contribuinte | Ter acesso total a experiência de gestão de. Não pode conceder acesso a outros utilizadores ou grupos.
Leitor | Pode ver todos os recursos exceto segredos.
Administrador de acesso de utilizadores | Pode ver todos os recursos, criar/gerir funções e criar/gerir pedidos de suporte.

Consulte o artigo [controlo de acesso baseado em funções no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Pode adicionar utilizadores e atribui-las funções específicas para a sua aplicação API facilmente. O portal mostra-lhe os utilizadores que têm acesso e os respetivos função atribuída:

![][access]  

- Selecione **os utilizadores** para adicionar um utilizador, atribuir uma função e remover um utilizador.
- Selecione **funções** para ver todos os utilizadores uma função específica, adicionar um utilizador a uma função e remover um utilizador a partir de uma função.


## <a name="more-good-stuff"></a>Mais conteúdo bom
- Selecione a **definição de API** para abrir o ficheiro de Swagger criado automaticamente para a sua aplicação API específico.
- Selecione **dependências** para ver os ficheiros necessários pela sua aplicação API. Por exemplo, se estiver a utilizar o conector SAP, instalar alguns ficheiros adicionais sobre o Gestor de ligação de híbrido no local. Estas dependências são apresentadas na sua pá de aplicação API.

>[AZURE.IMPORTANT] Quando abrir as propriedades da aplicação API e procure em **Essentials**, existem **anfitrião** e **Gateway** ligações que abrem pás novos:
>
> ![][host]
>
>Estas propriedades são específicas para o Web site que aloja a sua aplicação API. Ao utilizar uma aplicação de API incorporados ou uma conexão, a maior parte destas propriedades realmente não se aplicam e recomendamos que não atualizar a estas propriedades. Se criou a sua própria aplicação API no Visual Studio e implementado-lo à sua subscrição do Azure, em seguida, pode utilizar as lâminas anfitrião e o Gateway. <br/><br/>


>[AZURE.NOTE] Para começar a lógica Apps antes de inscrever-se para uma conta do Azure, aceda à [Aplicação de lógica tente](https://tryappservice.azure.com/?appservice=logic). Pode criar uma aplicação de lógica curto starter. Sem cartões de crédito obrigatórias e sem compromissos.

## <a name="read-more"></a>Saiba mais

[Monitorize as suas aplicações de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Lista de aplicações API na aplicação de serviço e conectores](app-service-logic-connectors-list.md)<br/>
[Controlo de acesso baseado em funções no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[Utilizar o Gestor de ligação híbrido no Azure de aplicação de serviço](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
