<properties
    pageTitle="Notas de lançamento para serviços de Azure BizTalk | Serviços de BizTalk do Microsoft Azure"
    description="Listas de problemas conhecidos para serviços de BizTalk do Azure" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Notas de lançamento do Azure BizTalk serviços

As notas de lançamento para os serviços do Microsoft Azure BizTalk contenham os problemas conhecidos nesta versão.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>O que há de novo na atualização de Novembro de serviços de BizTalk
* Encriptação no resto pode ser ativada no Portal de serviços do BizTalk. Consulte o artigo [Ativar encriptação em repouso no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Atualizar histórico

### <a name="october-update"></a>Actualização de Outubro

* Contas organizacionais são suportadas:  
 * **Cenário**: se registou uma implementação do serviço de BizTalk utilizando uma conta Microsoft (como user@live.com). Neste cenário, apenas os utilizadores do Microsoft Account podem gerir o serviço de BizTalk utilizando o portal de serviços de BizTalk. Uma conta institucional não pode ser utilizada.  
 * **Cenário**: se registou uma implementação do serviço de BizTalk utilizando uma conta institucional num Azure Active Directory (como user@fabrikam.com ou user@contoso.com). Neste cenário, apenas os utilizadores do Azure Active Directory dentro da mesma empresa podem gerir o serviço de BizTalk utilizando o portal de serviços de BizTalk. Não pode ser utilizada uma conta Microsoft.  
* Quando cria um serviço de BizTalk no portal clássico do Azure, são registados automaticamente no Portal de serviços do BizTalk.
 * **Cenário**: Inicie sessão no portal do Azure clássico, crie um serviço de BizTalk e, em seguida, selecione **Gerir** pela primeira vez. Quando abre o portal de BizTalk serviços, o serviço de BizTalk regista automaticamente e está pronto para sua implementações.  
 Consulte o artigo [Registar e atualizar uma implementação BizTalk serviço no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Actualização 14 de Agosto
* Contrato e bridge desacoplamento – negociação acordos de parceiro e pontes são agora desacoplados no Portal de serviços do BizTalk. Agora criar acordos e pontes em separado, e o tempo de execução pontes ser resolvido para um contrato com base em valores na mensagem editar. Consulte o artigo [Criar acordos no Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [crie uma bridge editar através do Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [criar uma bridge AS2 através do Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx), e [como pontes resolver a acordos o tempo de execução?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A opção para criar modelos para acordos é descontinuada.  
* Para o contrato de enviar lado, pode agora especificar conjuntos delimitador diferente para cada esquema. Esta configuração é especificada em definições de protocolo para enviar lado contrato. Para obter mais informações, consulte o artigo [criar uma X12 contrato nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [criar um contrato EDIFACT no Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Duas novas entidades também são adicionadas à TPM OM API para o mesmo efeito. Consulte o artigo [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) e [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Padrão XSD construções, incluindo tipos de derivado, agora são suportadas. Consulte o artigo [utilizar padrão XSD constrói nos seus mapas](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [Utilizar derivado tipos de mapeamento de cenários e exemplos](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 suporta novos algoritmos de Microfone para assinar mensagem nova algoritmos e encriptação. Consulte o artigo [criar um contrato AS2 nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Problemas conhecidos

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividade após BizTalk dos serviços de actualização de Portal

  Se tiver o Portal de serviços de BizTalk abrir enquanto BizTalk serviços é atualizado para recuperar as alterações ao serviço, poderá cara problemas de conectividade com o Portal de serviços de BizTalk.  
  Como solução, pode reiniciar o browser, eliminar a cache do browser ou iniciar o portal no modo privado.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE não consegue localizar o artefacto se clicar num erro ou aviso num projeto BizTalk serviços
Instale o Visual Studio 2012 atualização 3 RC 1 para corrigir o problema.  

### <a name="custom-binding-project-reference"></a>Referência de projecto enlace personalizado
Considere com um projeto de serviços de BizTalk numa solução Visual Studio seguintes situações:  
* Na mesma solução Visual Studio, existe um projeto de serviços de BizTalk e um projeto enlace personalizado. O projeto de serviço de BizTalk tem uma referência a este ficheiro de projeto enlace personalizado.
* O projeto de serviço de BizTalk tem uma referência a um enlace/comportamento personalizado DLL.

'Constrói' solução no Visual Studio com êxito. Em seguida, 'Reconstruir' ou 'Limpar' a solução. Após esta ação, quando reconstruir ou limpar novamente, ocorre o seguinte erro:  
  Não é possível copiar o ficheiro <Path to DLL> para "bin\Debug\FileName.dll". O processo não é possível aceder ao ficheiro 'bin\Debug\FileName.dll' porque está a ser utilizado por outro processo.  

#### <a name="workaround"></a>Solução
* Se estiver instalado o [Visual Studio 2012 actualização 3](https://www.microsoft.com/download/details.aspx?id=39305) , tem duas opções seguintes:

  * Reinicie o Visual Studio, ou

  * Reinicie a solução. Em seguida, execute apenas uma compilação sobre a solução.  

* Se não estiver instalado o [Visual Studio 2012 actualização 3](https://www.microsoft.com/download/details.aspx?id=39305) , abrir Gestor de tarefas, clique no separador processos, clique no processo de MSBuild.exe e, em seguida, clique no botão Terminar processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Encaminhamento para os pontos finais de BasicHttpRelay não é suportado a partir do pontes e BizTalk serviços Portal se carateres não imprimíveis são promovidos como cabeçalhos de HTTP

Se utilizar carateres não imprimíveis como parte das propriedades promovidas para mensagens, essas mensagens não podem ser encaminhadas para destinos reencaminhamento que utilizam o enlace BasicHttpRelay. Além disso, as propriedades promovidas que estão disponíveis como parte do controlo são URL codificada para blobs e barra codificado para destinos.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN é enviada modo assíncrono mesmo se a opção MDN assíncrona enviar está desmarcada  
Considere este cenário – se selecionar a caixa de verificação **Enviar MDN assíncrona** e, especificar um URL para enviar a assíncrona MDN e, em seguida, desmarque a caixa de verificação **Enviar MDN assíncrona** novamente, o MDN ainda é enviado para o URL especificado apesar da opção para enviar assíncrona MDNs não está selecionada.  
Como solução, tem de limpar o URL especificado antes de ao desmarcar a caixa de verificação **Enviar MDN assíncrona** e, em seguida, implementar o contrato de AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Carateres de espaço em branco para além de um intercâmbio válido causam uma mensagem vazia sejam enviadas para o ponto final suspender  
Se existirem finais para além de um segmento AIE, o desintegrador trata como final do intercâmbio atual e analisa o próximo conjunto de finais como uma mensagem seguinte. Uma vez que esta não é válido intercâmbio, poderá observar que uma mensagem com êxito é enviada para o destino da rota e uma mensagem vazia é enviada o ponto final suspender.  
### <a name="tracking-in-biztalk-services-portal"></a>Controlo no Portal de serviços de BizTalk  
Controlo eventos são captados por excesso para o processamento de mensagem de editar e qualquer correlação. Se uma mensagem falhar fora da fase de protocolo, controlo irá mostrar uma conclusão com êxito. Esta situação, consulte a secção registo sob a coluna de **Detalhes** no **controlo** para detalhes do erro.
O X12 receber e enviar as definições ([criar uma X12 contrato nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornecem informações sobre a fase de protocolo.  

### <a name="update-agreement"></a>Contrato de actualização  
O Portal de serviços de BizTalk permite-lhe modificar o qualificador de uma identidade quando um contrato está configurado. Isto pode resultar em Propriedades de inconsistente. Por exemplo, é um contrato com ZZ:1234567 e ZZ:7654321 o qualificador. Nas definições de perfil BizTalk serviços Portal, alterar ZZ:1234567 para ser 01:ChangedValue. Abrir o contrato e 01:ChangedValue é apresentado em vez de ZZ:1234567.
Para modificar o qualificador de uma identidade, eliminar o contrato, atualize **identidades** no perfil de parceiro e, em seguida, recriar o contrato.  
> AZURE. AVISO este comportamento une X12 e AS2.  

### <a name="as2-attachments"></a>AS2 anexos  
Anexos para AS2 mensagens não são suportadas no enviar ou recebem. Especificamente, anexos são ignorados silenciosamente e o corpo da mensagem é processado como uma mensagem de AS2 normal.  
### <a name="resources-remembering-path"></a>Recursos: Caminho de lembrar  
Ao adicionar **recursos**, na janela de diálogo poderá não Lembre-se o caminho anteriormente utilizado para adicionar um recurso. Se lembrar do caminho utilizado anteriormente, experimente adicionar o web site de Portal de serviços de BizTalk aos **Sites fidedignos** no Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se mudar o nome o nome da entidade de uma bridge e feche o projecto sem guardar as alterações, voltar a abri-a entidade resulta num erro
Considere um cenário pela seguinte ordem:  
* Adicionar uma bridge (por exemplo uma ponte de One-way XML) a um projeto de serviço de BizTalk  

* Mudar o nome da ponte ao especificar um valor para a propriedade do nome da entidade. Muda o nome o ficheiro .bridgeconfig associado com o nome que especificou.  

* Feche o ficheiro de .bcs (fechando separador no Visual Studio) sem guardar as alterações.  

* Abra o ficheiro de .bcs novamente a partir do Explorador de solução.  
Irá reparar que enquanto o ficheiro associado .bridgeconfig tem o novo nome que especificou, o nome da entidade na superfície da estrutura é ainda o nome antigo. Se tentar abrir a configuração de Bridge fazendo o componente de bridge, obtém o seguinte erro:  
  '<old name>'Entidade do associados ficheiro'<old name>.bridgeconfig' não existe  
Para evitar que este cenário, certifique-se de que guarda alterações depois de mudar o nome de entidades num projeto BizTalk serviço.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Projecto de serviço do BizTalk constrói com êxito, mesmo se um artifício foi excluído a partir de um projeto do Visual Studio
Considere um cenário onde adicionar um artifício (por exemplo, um ficheiro XSD) a um projeto de serviço de BizTalk, incluir esse artefacto a configuração de Bridge (por exemplo, especificando-lo como um tipo de mensagem de pedido) e, em seguida, exclui-lo do projeto do Visual Studio. Neste caso, construir o projeto não atribuirá qualquer erro desde o artefacto eliminado está disponível no disco na mesma localização a partir de onde foi incluído no projeto do Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>O projeto de serviço de BizTalk não verificar a disponibilidade de esquema, ao configurar as pontes
Num projeto de serviço de BizTalk, se for um esquema que é adicionado ao projeto importado outro esquema, o projeto de serviço de BizTalk não verifica se o esquema importado é adicionado ao projeto. Se tentar criar essa um projeto, não receberá algum erro de compilação.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A mensagem de resposta para uma Bridge de pedido de resposta XML é sempre do conjunto de caracteres UTF-8
Nesta versão, o conjunto de caracteres da mensagem de resposta a partir de um Bridge de pedido de resposta XML sempre está definido para UTF-8.
### <a name="user-defined-datatypes"></a>Tipos de dados definidos pelo utilizador
Os adaptadores BizTalk adaptador Pack dentro a funcionalidade de serviço de adaptador BizTalk podem utilizar tipos de dados definidos pelo utilizador para operações de adaptador.
Ao utilizar tipos de dados definidos pelo utilizador, copie os ficheiros (. dll) para unidade: \Programas\Microsoft BizTalk adaptador Service\BAServiceRuntime\bin\ ou para a assemblagem Cache Global (GAC) no servidor que aloja o serviço do serviço de adaptador BizTalk. Caso contrário, o seguinte erro poderá ocorrer no cliente:  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Recomenda-se para utilizar GACUtil.exe para instalar um ficheiro na Cache de assemblagem Global. GACUtil.exe documentos como utilizar esta ferramenta e as opções de linha de comandos do Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar o BizTalk adaptador serviço Web Site
Instalar o **Tempo de execução de serviço de placa de BizTalk** * cria a * *BizTalk placa de serviço* * web site no IIS que contém o * *BAService* * aplicação.* *BAService** aplicação utiliza internamente encadernação de reencaminhamento para expandir o alcance do ponto final de serviço no local para a nuvem. Para um serviço alojado no local, o ponto final de reencaminhamento correspondente será registado no serviço Bus apenas quando inicia o serviço no local.  

Se parar e iniciar uma aplicação, a configuração para iniciar uma aplicação automática não é respeitada. Por isso, quando está parado **BAService** , tem sempre de reiniciar o **BizTalk placa de serviço** web site em vez disso. Não iniciar ou parar a aplicação **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Carateres especiais não devem ser utilizados para o endereço e entidade nomes dos componentes LOB
Não deve utilizar carateres especiais para os nomes de endereço e entidade dos componentes LOB. Se fazê-lo, obterá um erro durante a implementar o projeto BizTalk serviço. Para determinados carateres como '%', o Web site BizTalk adaptador serviço poderá entrar num estado parado e tem de iniciar manualmente.
### <a name="test-map-with-get-context-property"></a>Mapa de teste com obter contexto propriedade
Se uma transformação contiver uma operação de mapa de **Obter a propriedade de contexto** , **Teste mapa** irá falhar. Como solução temporária, substitua a operação de mapa de **Propriedade de contexto de obter** uma cadeia concatenar mapa operação que contêm dados fictícios. Isto irá povoar o esquema de destino e permitir que a teste primeiro outras funcionalidades de transformação.
### <a name="test-map-property-does-not-display"></a>Teste mapear propriedade não apresenta
As propriedades de **Mapa de teste** não mostrar no Visual Studio. Isto pode ocorrer se a janela de **Propriedades** e a janela do **Explorador de solução** não são em simultâneo ancorados. Para resolver esta questão, ancore as **Propriedades** e a **Solução Explorador** do windows.  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Seta de lista pendente DateTime reformatar está desativada
Quando uma operação de mapa de reformatar de data/hora é adicionada para a superfície de desenho e configurada, a lista pendente formato pode estar desativada. Isto pode acontecer se o computador visualização estiver definido **Médio – 125%** ou **maior – 150%**. Para resolver, defina a apresentação para **mais pequena – (predefinição) a 100%** utilizando os passos abaixo:  
1. Abra o **Painel de controlo** e clique em **aspeto e personalização**.
2. Clique em **apresentar**.
3. Clique em **mais pequena – 100% (predefinição)** e clique em **Aplicar**.

Lista pendente **formato** deverá agora funcionar conforme esperado.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicar acordos no Portal de serviços do BizTalk
Considere o seguinte cenário:
1. Crie um contrato de utilizar a API OM negociação de gestão de parceiro.
2. Abra o contrato no Portal de serviços do BizTalk em dois separadores diferentes.
3. Implemente o contrato de ambos os separadores.
4. Como resultado, ambos os acordos são implementados que resulta em entradas duplicadas no Portal de serviços do BizTalk

**Solução**. Abra qualquer um dos acordos duplicados no Portal de serviços do BizTalk e anular a implementação.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Pontes não utilizar certificado actualizado, mesmo depois de um certificado foi atualizado na loja artefacto
Considere os cenários seguintes:  

**Cenário 1: Utilizar certificados baseados em impressão digital para proteger a transferência da mensagem a partir de uma bridge para um ponto final de serviço**  
Considere a hipótese de um cenário que utilizar certificados baseados em impressão digital no projeto BizTalk serviço. Atualizar o certificado no Portal de serviços do BizTalk com o mesmo nome mas uma impressão digital diferentes, mas não são atualizadas projeto BizTalk serviço em conformidade. Cenário, poderá continuar a bridge processar as mensagens dado os dados mais antigos do certificado que poderão ainda estar na cache de canal. Após esta ação, processamento de mensagens falha.  

**Solução**: Atualize o certificado no projeto BizTalk serviço e implementar o projeto.  

**Cenário 2: Utilizar comportamentos baseados em nome para identificar certificados para proteger a transferência da mensagem a partir de uma bridge para um ponto final de serviço**

Considere a hipótese de um cenário que utilizar comportamentos baseados em nome para identificar certificados no projeto BizTalk serviço. Actualizar o certificado no Portal de serviços do BizTalk mas não são atualizadas projeto BizTalk serviço em conformidade. Cenário, poderá continuar a bridge processar as mensagens dado os dados mais antigos do certificado que poderão ainda estar na cache de canal. Após esta ação, processamento de mensagens falha.  

**Solução**: Atualize o certificado no projeto BizTalk serviço e implementar o projeto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Pontes continuam a processar mensagens mesmo quando a base de dados do SQL está offline
Pontes BizTalk serviços continuar a processar as mensagens para algum tempo, mesmo que a Microsoft Azure SQL base de dados (que armazena as informações em execução como artefactos implementados e tubagens), está offline. Isto acontece porque BizTalk serviços utiliza o artefactos em cache e configuração de bridge.
Se não pretender que o pontes para processar todas as mensagens quando a base de dados do SQL está offline, pode utilizar os cmdlets do PowerShell de serviços de BizTalk parar ou suspender o serviço de BizTalk. Consulte o artigo [Azure BizTalk serviço de gestão de amostra](http://go.microsoft.com/fwlink/p/?LinkID=329019) para os cmdlets do Windows PowerShell para gerir operações.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Ler a mensagem XML no componente de código personalizado uma bridge inclui um caráter de BOM extra
Considere a hipótese de um cenário em que pretende ler uma mensagem XML dentro de código personalizado uma bridge. Se utilizar o System.Text.Encoding.UTF8.GetString(bytes) API .NET um caráter de BOM extra é incluído no resultado no início da mensagem. Por isso, se não pretender que a saída para incluir o caráter BOM extra, tem de utilizar o ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Enviar mensagens para uma bridge utilizando WCF não dimensionar
As mensagens enviadas para um bridge utilizando WCF não dimensionar. Deve utilizar HttpWebRequest em vez disso, se pretender que um cliente dimensionável.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ATUALIZAÇÃO: Erro de fornecedor Token após atualizar do pré-visualização de serviços de BizTalk das versões geral disponibilidade (DG)
Existe uma editar ou AS2 contrato com secções ativas. Quando o serviço de BizTalk é atualizada a versão de pré-visualização das versões DG, pode ocorrer o seguinte procedimento:
* Erro: O fornecedor de tokens não foi possível fornecer um token de segurança. Fornecedor de tokens devolvido mensagem: não foi possível resolver o nome remoto.

* Tarefas de lote são canceladas.

**Solução**: após o serviço de BizTalk é atualizado das versões geral disponibilidade (DG), voltar a implementar o contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ATUALIZAÇÃO: Caixa de ferramentas mostra os ícones de bridge antigo depois de atualizar o SDK do BizTalk Services
Depois de atualizar numa versão anterior do BizTalk Services SDK, que tinha antigos ícones que representa os pontes, continua a caixa de ferramentas Mostrar os ícones antigos para as pontes. No entanto, se adicionar um bridge para superfície estruturador do serviço de BizTalk projeto, superfície mostra o novo ícone.  

**Solução**. Pode contornar este problema ao eliminar os ficheiros de .tbd em <system drive>: \Users\<utilizador > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ATUALIZAÇÃO: Atualização de BizTalk Portal de pré-visualização para das versões DG poderá mostrar um erro que indica que a capacidade de editar não está disponível
Se iniciada no Portal de serviços do BizTalk enquanto os serviços de BizTalk é atualizada da versão pré-visualização das versões DG, poderá receber a seguinte mensagem de erro no portal:  

Esta funcionalidade não está disponível como parte nesta edição do Microsoft Azure BizTalk Services. Para utilizar estas capacidades de mudar para uma edição adequada.  

**Resolução**: terminar sessão a partir do portal, fechar e abrir o browser e, em seguida, iniciar sessão no portal.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ATUALIZAÇÃO: Rastreio novos dados não aparecer depois de serviços de BizTalk é atualizado para das versões DG
Partem do pressuposto de um cenário em que tenha uma bridge XML implementada na subscrição dos serviços de BizTalk pré-visualização. Enviar mensagens para o bridge e o correspondente dados de controlo está disponível no Portal de serviços de BizTalk. Agora, se os bits runtime BizTalk serviços de Portal e serviços de BizTalk são actualizados para o das versões DG e enviar uma mensagem para o ponto final bridge mesmo implementado anterior, os dados de controlo não aparecer para mensagens enviadas após atualização.  

### <a name="pipelines-vs-bridges"></a>Tubagens v/s pontes
Em todo o documento, o termo 'tubagens' e 'pontes' são utilizados alternadamente. Ambos média, essencialmente, a mesma coisa que é uma unidade de processamento de mensagem implementada nos serviços de BizTalk.  

### <a name="concepts"></a>Conceitos  

[Serviços de BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
