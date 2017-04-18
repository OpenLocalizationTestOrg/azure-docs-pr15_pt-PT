<properties
    pageTitle="Migrar de serviços móveis para uma aplicação móvel do serviço de aplicação"
    description="Saiba como migrar facilmente a sua aplicação de serviços móveis para uma aplicação do aplicação do serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Migrar o serviço móvel do Azure existente para o serviço de aplicação do Azure

Com a [disponibilidade geral da aplicação de serviço de Azure], sites de serviços de Mobile Azure podem ser facilmente migradas no local para tirar partido de todas as funcionalidades do serviço de aplicação do Azure.  Este documento explica o que esperar quando migrar o seu site a partir do Azure Mobile serviços para a aplicação de serviço de Azure.

## <a name="what-does-migration-do"></a>O que faz migração para o seu site

Migração de conteúdo do seu serviço móvel do Azure transforma o serviço móvel de uma aplicação de [Serviço de aplicação do Azure] sem afetar o código.  A notificação concentradores, SQL ligação de dados, as definições de autenticação, as tarefas agendadas e nome de domínio que permaneça inalterados.  Utilizar o seu serviço móvel do Azure os clientes móveis continuam a funcionar normalmente.  Migração reinicia o seu serviço quando é transferido para a aplicação de serviço de Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que motivo devo migrar o seu site

Microsoft é recomendar migrar o seu serviço móvel do Azure tirar partido das funcionalidades do serviço de aplicação Azure, incluindo:

  *  Novas funcionalidades de anfitrião, incluindo [WebJobs] e [nomes de domínio personalizado].
  *  Conectividade para os recursos no local com [VNet] além [Híbrido ligações].
  *  A monitorização e resolução de problemas com o novo Relic ou [Informações de aplicação].
  *  Ferramentas de DevOps incorporadas, incluindo [faixas de transição], voltará e testes de produção.
  *  [Dimensionar automaticamente], balanceamento de carga e [monitorização de desempenho].

Para obter mais informações sobre as vantagens da aplicação de serviço de Azure, consulte o tópico de [Serviço móvel do serviços vs. aplicação] .

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar qualquer trabalho principal no seu site, deverá [criar uma cópia de segurança serviço móvel do seu] scripts e base de dados SQL.

## <a name="migrating-site"></a>Migrar os seus sites

O processo de migração migra todos os sites numa única região Azure.

Para migrar o seu site:

  1.  Inicie a sessão [Portal clássica Azure].
  2.  Selecione um serviço móvel na região que pretende migrar.
  3.  Clique no botão **Migrar para a aplicação de serviço** .

    ![O botão migrar][0]

  4.  Leia a migrar para a caixa de diálogo serviço de aplicação.
  5.  Introduza o nome do seu serviço móvel na caixa fornecida.  Por exemplo, se o seu nome de domínio for contoso.azure mobile.net, em seguida, introduza _contoso_ na caixa fornecida.
  6.  Clique no botão de escala.

Monitorize o estado da migração no monitor de atividade. O site é listado como *Migrar* no Portal clássica do Azure.

  ![Monitor de atividade de migração][1]

Cada migração pode tomar qualquer local a partir de 3 para 15 minutos por serviço móvel do ser migrada.  O site permanece disponível durante a migração.
O site é reiniciado no final do processo de migração.  O site não está disponível durante o processo de reiniciar, poderá última apenas alguns segundos.

## <a name="finalizing-migration"></a>Finalizar a migração

Plano para testar o seu site a partir de um cliente móvel na conclusão do processo de migração.  Certifique-se de que pode executar todas as ações de cliente comuns sem alterações para o cliente móvel.  

### <a name="update-app-service-tier"></a>Selecione um serviço de aplicação apropriada preços de camadas

Ter mais flexibilidade na preços após migrar para o serviço de aplicação do Azure.

  1.  Inicie a sessão [portal do Azure].
  2.  Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3.  Abre o pá definições por predefinição.
  4.  Clique em **Plano de serviço de aplicação** no menu definições.
  5.  Clique no mosaico de **Preços de camadas** .
  6.  Clique no mosaico adequado com os seus requisitos, em seguida, clique em **Selecionar**.  Poderá ter de clique em **Ver tudo** para ver os preços disponíveis camadas.

Como ponto de partida, recomendamos que as camadas seguintes:

| Serviço móvel preços camada | Camadas de preços de aplicação de serviço |
| :-------------------------- | :----------------------- |
| Gratuito                        | F1 gratuito                  |
| Básicas                       | B1 Basic                 |
| Padrão                    | S1 padrão              |

Há considerável flexibilidade no escolher direita preços camada para a sua aplicação.  Consulte [Preços de serviço de aplicação] para detalhes completos sobre preços do seu novo serviço de aplicação.

> [AZURE.TIP]A camada de aplicação de serviço padrão contém acesso a várias funcionalidades que pretende utilizar, incluindo [faixas de transição], cópias de segurança automáticas e a escala automática.  Veja as novas capacidades enquanto estão lá!

### <a name="review-migration-scheduler-jobs"></a>Rever o migrados Programador de tarefas

Programador de tarefas não estarão visível até cerca de 30 minutos após a migração.  Tarefas agendadas continuam a ser executado em segundo plano.
Para ver as tarefas agendadas após fiquem visíveis novamente:

  1.  Inicie a sessão [portal do Azure].
  2.  Selecione **Procurar >**, introduza **agenda** na caixa _filtro_ , em seguida, selecione **Programador de coleções de sites**.

Existem um número limitado de programador gratuito tarefas disponíveis pós-migração.  Reveja os [Planos de programador do Azure]e a utilização do.

### <a name="configure-cors"></a>Configurar CORS caso seja necessário

Partilha de recursos de publicação em origem é uma técnica para permitir que um Web site aceder a uma API Web num domínio diferente.  Se estiver a utilizar serviços de Mobile Azure com um Web site associado, em seguida, tem de configurar CORS como parte da migração.  Se está a aceder ao Azure Mobile serviços exclusivamente a partir de dispositivos móveis, em seguida, CORS não necessita de ser configurado exceto em alguns casos raros.

As definições de CORS migradas estão disponíveis como a definição de aplicação **MS_CrossDomainWhitelist** .  Para migrar o seu site para a funcionalidade de CORS de serviço de aplicação:

  1.  Inicie a sessão [portal do Azure].
  2.  Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3.  Abre o pá definições por predefinição.
  4.  Clique em **CORS** no menu de API.
  5.  Introduza quaisquer origens permitidos na caixa fornecida, premindo Enter após cada um deles.
  6.  Assim que a sua lista de origens diferentes permitido estiver correta, clique no botão guardar.

> [AZURE.TIP]Uma das vantagens da utilização de um serviço de aplicação do Azure é que pode executar o web site e o serviço móvel no mesmo local.  Para mais informações, consulte a secção [os passos seguintes](#next-steps) .

### <a name="download-publish-profile"></a>Transferir um novo perfil de publicação

O perfil do seu site de publicação é alterado quando migrar para o serviço de aplicação do Azure.  Se pretende publicar o seu site a partir do Visual Studio, precisa de um novo perfil de publicação.  Para transferir o novo perfil de publicação:

  1.  Inicie a sessão [portal do Azure].
  2.  Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3.  Clique em **obter publicar perfil**.

O ficheiro de PublishSettings é transferido para o seu computador.  Normalmente é designado por _nome do site_. PublishSettings.  Importe as definições de publicar para o seu projeto existente:

  1.  Abrir o Visual Studio e do seu projeto de serviço do Azure móvel.
  2.  Clique no separador projeto no **Explorador de soluções** e selecione **publicar...**
  3.  Clique em **Importar**
  4.  Clique em **Procurar** e selecione o seu transferido publicar o ficheiro de definições.  Clique em **OK**
  5.  Clique em **Validar ligação** para garantir que o trabalho de definições de publicar.
  6.  Clique em **Publicar** para publicar o seu site.


## <a name="working-with-your-site"></a>Trabalhar com o seu pós-migração de site

Começar a trabalhar com o seu novo serviço de aplicação na migração pós [Azure portal] .  Seguem-se algumas notas no operações específicas que utilizou para efetuar no [Portal clássica Azure], juntamente com o seu equivalente da aplicação de serviço.

### <a name="publishing-your-site"></a>Transferir e o seu migrado site de publicação

O site está disponível através do git ou ftp e pode ser artigos com vários mecanismos diferentes, incluindo WebDeploy, TFS,, GitHub e FTP.  As credenciais de implementação são migradas com o resto do seu site.  Se não tiver definido as suas credenciais de implementação ou não se lembra-los, pode repor-las:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Abre o pá definições por predefinição.
  4. Clique em **credenciais de implementação** na publicação menu.
  5. Introduza as novas credenciais de implementação nas caixas fornecidas e, em seguida, clique no botão guardar.

Pode utilizar estas credenciais para clonar o site com git ou configurar implementações automatizadas GitHub, TFS ou com.  Para mais informações, consulte a [documentação de implementação do serviço de aplicação do Azure].

### <a name="appsettings"></a>Definições da aplicação

A maioria das definições para um serviço móvel migrado estão disponíveis através as definições da aplicação.  Pode obter uma lista das definições de aplicação a partir do [Azure portal].
Para ver ou alterar as definições de aplicação:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Abre o pá definições por predefinição.
  4. Clique em **definições da aplicação** no menu geral.
  5. Desloque-se para a secção definições na aplicação e localize a definição de aplicação.
  6. Clique com o valor da definição de aplicação para editar o valor.  Clique em **Guardar** para guardar o valor.

Pode atualizar as definições da aplicação vários ao mesmo tempo.

> [AZURE.TIP]Existem duas definições da aplicação com o mesmo valor.  Por exemplo, poderá ver _ApplicationKey_ e _MS\_ApplicationKey_.  Atualize ambas as definições da aplicação ao mesmo tempo.

### <a name="authentication"></a>Autenticação

Todas as definições de autenticação estão disponíveis como as definições da aplicação no seu site migrado.  Para atualizar as definições de autenticação, tem de alterar as definições de aplicação adequado.  A tabela seguinte mostra as definições de aplicação adequado para o seu fornecedor de autenticação:

| Fornecedor          | ID de cliente                 | Secreta de cliente                | Outras definições             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Conta Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Nota: **MS\_AadTenants** está armazenado como uma lista separados por vírgulas dos domínios do inquilino (os campos de "Inquilinos permitido" no portal do serviços Mobile).

> [AZURE.WARNING] **Não utilize os mecanismos de autenticação no menu Definições**
>
> Aplicação de serviço de Azure fornece um separada "sem código" autenticação e autorização sistema no _autenticação / autorização_ atrás do texto e a opção de _Autenticação Mobile_ (preterida) no menu definições.  Estas opções são incompatíveis com um serviço móvel do Azure migrado.  Pode [atualizar o seu site](app-service-mobile-net-upgrading-from-mobile-services.md) para tirar partido da autenticação de aplicação de serviço do Azure.

### <a name="easytables"></a>Dados

No separador _dados_ nos serviços do Mobile foi substituído pelo _Fácil tabelas_ no interior do Azure portal.  Para aceder às tabelas fácil:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Abre o pá definições por predefinição.
  4. Clique em **tabelas fácil** no menu de dispositivos móvel.

Pode adicionar uma tabela ao clicar no botão **Adicionar** ou aceder ao seu tabelas existentes ao clicar em nome de uma tabela.  Existem várias operações que pode fazer a partir deste pá, incluindo:

* Alterar as permissões de tabela
* Editar os scripts operacionais avançados
* Gerir o esquema de tabela
* Eliminar a tabela
* Desmarcar o conteúdo da tabela
* Eliminar linhas específicas da tabela

### <a name="easyapis"></a>API

No separador _API_ nos serviços do Mobile foi substituído pelo _APIs fácil_ no interior do Azure portal.  Para aceder às APIs fácil:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Abre o pá definições por predefinição.
  4. Clique em **APIs fácil** no menu de dispositivos móvel.

Os APIs migrados já estão listadas na pá.  Também pode adicionar uma API deste pá.  Para gerir um API específico, clique em API.
A partir do novo pá, pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Programador de tarefas

Todos os do Programador de tarefas está disponível através da secção de coleções de sites do Scheduler tarefa.  Para aceder ao seu do Programador de tarefas:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **Procurar >**, introduza **agenda** na caixa _filtro_ , em seguida, selecione **Programador de coleções de sites**.
  3. Selecione a coleção de tarefa para o seu site.  _Nome do site_é denominado-tarefas.
  4. Clique em **Definições**.
  5. Clique em **Programador de tarefas** em GERIR.

Tarefas agendadas estão listadas com a frequência especificada antes da migração.  Tarefas da pedido são desativadas.  Para executar uma tarefa a pedido:

  1. Selecione a tarefa que pretende executar.
  2. Se for necessário, clique em **Ativar** para ativar a tarefa.
  3. Clique em **Definições**e, em seguida **agenda**.
  4. Selecione uma periodicidade de **uma vez**, em seguida, clique em **Guardar**

As tarefas da pedido estão localizadas no `App_Data/config/scripts/scheduler post-migration`.  Recomendamos que converter todas as tarefas da pedido [WebJobs] ou [funções].  Escreva o novo do Programador de tarefas como [WebJobs] ou [funções].

### <a name="notification-hubs"></a>Notificação concentradores

Serviços Mobile utiliza concentradores de notificação para notificações push.  As seguintes definições de aplicação são utilizadas para criar uma ligação do concentrador de notificação para o serviço Mobile após a migração:

| Definição da aplicação                    | Descrição                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | O espaço de nomes do concentrador de notificação           |
| **MS\_NotificationHubName**             | O nome do concentrador de notificação                |
| **MS\_NotificationHubConnectionString** | A cadeia de ligação do Centro de notificação   |
| **MS\_NamespaceName**                   | Um alias para MS_PushEntityNamespace      |

O concentrador de notificação é gerido através do [Azure portal].  Tenha em atenção o nome do concentrador de notificação (que pode encontrar esta utilizando as definições de aplicação):

  1. Inicie a sessão [portal do Azure].
  2. Selecione **Procurar**>, em seguida, selecione **Concentradores de notificação**
  3. Clique no nome do concentrador de notificação associado ao serviço móvel.

> [AZURE.NOTE]Se o seu centro de notificação é um tipo de "Mistos", não estiver visível.  "Mistas" escreva notificação concentradores utilizam concentradores de notificação e funcionalidades de serviço Bus legadas.  [Converter os espaços de nomes mistos] antes de continuar.  Assim que a conversão estiver concluída, o seu centro de notificação é apresentada no [portal do Azure].

Para mais informações, consulte a documentação [Concentradores de notificação] .

> [AZURE.TIP]Funcionalidades de gestão de concentradores notificação no [portal do Azure] ainda estão em pré-visualização.  O [Portal clássica Azure] permanecem disponíveis para gerir todos os seus concentradores de notificação.

### <a name="legacy-push"></a>Definições de Push legado

Se configurou Push no seu serviço móvel antes da introdução no concentradores de notificação, estiver a utilizar _push legado_.  Se estiver a utilizar o Push e não vir um concentrador de notificação listados na sua configuração, em seguida, é provável que está a utilizar _push legado_.  Esta funcionalidade é migrada com todas as outras funcionalidades.  No entanto, recomendamos que atualizar para o notificação concentradores logo após a migração estiver concluída.

Entretanto, todas as definições de legado push (com a exceção do certificado de APNS) estão disponíveis nas definições de aplicação.  Atualize o certificado APNS, substituindo o ficheiro adequado no sistema de ficheiros.

### <a name="app-settings"></a>Outras definições de aplicação

As seguintes definições de aplicação adicionais são migrados do seu serviço de Mobile e estão disponíveis em *Definições* > *As definições da aplicação*:

| Definição da aplicação              | Descrição                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | O nome da sua aplicação                    |
| **MS\_MobileServiceDomainSuffix** | O prefixo do domínio. seja Azure mobile.net |
| **MS\_ApplicationKey**            | Tecla de aplicação                    |
| **MS\_chave mestra**                 | A chave de modelo global de aplicação                     |

A tecla de aplicação e o modelo global de chave são idênticos às teclas de aplicação a partir do seu serviço móvel do original.  Em particular, a tecla de aplicação é enviada por clientes móveis para validar a sua utilização de API móvel.

### <a name="cliequivalents"></a>Equivalentes da linha de comandos

Já pode utilizar o comando _azure móvel_ para gerir o seu site dos serviços do Azure Mobile.  Em vez disso, foram substituídos muitas das funções com o comando _azure site_ .  Utilize a tabela seguinte para encontrar equivalentes de comandos comuns:

| _Azure Mobile_ Comando                     | Comando equivalente do _Site do Azure_            |
| :----------------------------------------- | :----------------------------------------- |
| localizações móveis                           | lista de localização do site                         |
| lista de dispositivos móvel                                | lista de sites                                  |
| _nome_ da apresentação de dispositivos móveis                         | _nome_ da apresentação de site.                           |
| reiniciar móvel _nome_                      | reiniciar o site _nome_                        |
| no _nome_ do novo lançamento móvel                     | site implementação novo lançamento _commitId_ _nome_ |
| chave móvel definir _nome_ do _tipo de_ _valor_       | de _chave do_ _nome_ do site appsetting eliminar <br/> site appsetting adicionar _chave_=_valor de_ _nome_ |
| configuração móvel da lista de _nome_                  | lista _nome_ do site appsetting                |
| configuração móvel obter _nome_ _chave_             | site appsetting da _chave de_ apresentação de _nome_          |
| configuração móvel definir _nome_ _chave_             | de _chave do_ _nome_ do site appsetting eliminar <br/> site appsetting adicionar _chave_=_valor de_ _nome_ |
| _nome_ da lista domínio móvel.                  | lista _nome_ do site domínio                    |
| domínio móvel adicionar de _nome de_ _domínio_          | domínio site adicionar o _nome_ do _domínio_            |
| domínio móvel eliminar _nome_                | _nome_ do _domínio_ do site domínio eliminar         |
| _nome_ da apresentação de escala móvel.                   | _nome_ da apresentação de site.                           |
| alterar _nome_ da escala móvel                 | _nome_ do _modo_ do site escala modo <br /> de _instâncias_ de instâncias do site escala _nome_ |
| _nome_ da lista appsetting móvel.              | lista _nome_ do site appsetting                |
| appsetting móvel adicionar _nome_ _chave_ _valor_ | site appsetting adicionar _chave_=_valor de_ _nome_   |
| appsetting móvel eliminar _nome_ _chave_      | de _chave do_ _nome_ do site appsetting eliminar        |
| de _nome_ de apresentação de appsetting móvel _chave_        | de _chave do_ _nome_ do site appsetting eliminar        |

Atualizar a autenticação ou definições de notificação de push ao atualizar a definição de aplicação apropriada.
Editar ficheiros e publicar o seu site através de ftp ou git.

### <a name="diagnostics"></a>Registo de diagnóstico e

Registo de diagnóstico normalmente é desativado num serviço de aplicação do Azure.  Para ativar o registo de diagnóstico:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Abre o pá definições por predefinição.
  4. Selecione **Registos de diagnóstico** no menu de funcionalidades.
  5. Clique **em** para registos que se seguem: **Aplicação registo (sistema de ficheiros)**, **mensagens de erro detalhada** **falhou pedido rastreio** e
  6. Clique em **Sistema de ficheiros** de registo do servidor Web
  7. Clique em **Guardar**

Para ver os registos:

  1. Inicie a sessão [portal do Azure].
  2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome do seu serviço móvel do migrado.
  3. Clique no botão **Ferramentas**
  4. Selecione a **sequência de registos** no menu de observar.

Os registos são apresentados na janela à medida que são geradas.  Também pode transferir os registos para análise posterior com as suas credenciais de implementação. Para mais informações, consulte a documentação de [registo] .

## <a name="known-issues"></a>Problemas conhecidos

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Eliminar um clonar de aplicação Mobile migradas faz com que uma falha de site

Se lhe clonar o serviço móvel migradas através do Azure PowerShell, em seguida, eliminar o clonar, a entrada DNS do seu serviço de produção é removida.  O site é já não estar acessíveis a partir da Internet.  

Resolução: Se pretender clonar o seu site, fazê-lo através do portal.

### <a name="changing-webconfig-does-not-work"></a>Alterar a Web. config não funciona

Se tiver um site do ASP.NET, muda para a `Web.config` ficheiro não obter aplicado.  O serviço de aplicação do Azure constrói um adequado `Web.config` ficheiro durante o arranque para suportar o tempo de execução de serviços móveis.  Pode substituir determinadas definições (tais como cabeçalhos personalizados) ao utilizar um ficheiro de transformação XML.  Criar um ficheiro em denominado `applicationHost.xdt` -este ficheiro tem de terminar para cima o `D:\home\site` directório o serviço do Azure.  Carregar o `applicationHost.xdt` de ficheiros através de um script de implementação personalizada ou utilizar diretamente Kudu.  A imagem seguinte mostra um documento de exemplo:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para mais informações, consulte a documentação de [XDT transformar amostras] no GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Serviços de Mobile migrados não pode ser adicionados ao Gestor de tráfego

Quando cria um perfil do Gestor de tráfego, diretamente não é possível selecionar um serviço móvel migradas para o perfil.  Utilizar um "externo ponto final."  O ponto final externo só pode ser adicionado através do PowerShell.  Para obter mais informações, consulte o [tutorial de tráfego Gestor](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Próximos passos

Agora que a aplicação é migrada para o serviço de aplicação, existem ainda mais funcionalidades que pode utilizar:

  * Implementação de [faixas de transição] permitem-lhe para fase alterações ao seu site e executar A / B testes.
  * [WebJobs] fornecem uma substituição para os projectos a pedido agendada.
  * Pode [Implementar continuamente] o site ao ligar o seu site para GitHub, TFS ou com.
  * Pode utilizar a [Aplicação informações] para monitorizar o seu site.
  * Servir um Web site e uma API Mobile a partir do mesmo código.

### <a name="upgrading-your-site"></a>Atualizar o seu site de serviços móveis para o Azure Mobile aplicações SDK

  * Para os projectos Node.js com base no servidor, o novo [Mobile aplicações Node.js SDK] fornece várias funcionalidades novas. Por exemplo, pode agora fazer desenvolvimento local e depurar, utilizar qualquer versão Node.js acima 0,10 e personalizar com qualquer software Express.js intermédio.

  * Para. Projetos líquido com base no servidor, os novos [Mobile aplicações SDK NuGet pacotes](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ter mais flexibilidade no NuGet dependências.  Estes pacotes suportam uma autenticação a nova aplicação de serviço e compõem com qualquer projeto ASP.NET. Para saber mais sobre a atualização, consulte o artigo [atualizar o seu serviço móvel do .NET existente para a aplicação de serviço](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Aplicação de serviço de preços]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Informações de aplicação]: ../application-insights/app-insights-overview.md
[Dimensionar automaticamente]: ../app-service-web/web-sites-scale.md
[Azure de aplicação de serviço]: ../app-service/app-service-value-prop-what-is.md
[Documentação de implementação do Azure serviço de aplicação]: ../app-service-web/web-sites-deploy.md
[Azure Portal clássico]: https://manage.windowsazure.com
[Portal do Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Planos do Scheduler do Azure]: ../scheduler/scheduler-plans-billing.md
[implementar continuamente]: ../app-service-web/app-service-continuous-deployment.md
[Converter os espaços de nomes mistos]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomes de domínio personalizado]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidade geral do serviço de aplicação do Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Ligações de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Funcionalidade de registo]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Aplicações móveis Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Serviço móvel do serviços vs. aplicação]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notificação concentradores]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitorização de desempenho]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Criar uma cópia de segurança do seu serviço Mobile]: ../mobile-services/mobile-services-disaster-recovery.md
[faixas de transição]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Exemplos de transformação XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funções]: ../azure-functions/functions-overview.md
