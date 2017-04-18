<properties   
    pageTitle="Migrar servidosBizTalk Editar soluções para o guia de técnica dos serviços de BizTalk | Microsoft Azure"
    description="Migrar Editar para MAK, MAB; Serviços de BizTalk do Microsoft Azure"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrar servidosBizTalk Editar soluções para serviços de BizTalk: guia técnico

Autor: Miguel Wieman e Nitin Mehrotra

Os revisores: Karthik Bharthy

Escritos utilizando: solte do Microsoft Azure BizTalk Services – de Fevereiro de 2014.

## <a name="introduction"></a>Introdução

Intercâmbio de dados Eletrónicos (editar) é um dos meios mais prevalecentes por dados do exchange que empresas eletronicamente, também chamados como Business-para-Business ou B2B operações. Servidor BizTalk teve suporte Editar para uma década, desde o lançamento inicial BizTalk Server. Com os serviços de BizTalk, a Microsoft continua o suporte técnico para obter soluções de editar a plataforma do Microsoft Azure. Transações B2B principalmente estão externas com uma organização e, consequentemente, é mais fácil implementar se este foi aplicado numa plataforma na nuvem. Microsoft Azure fornece esta capacidade através dos serviços de BizTalk.

Enquanto alguns clientes observe BizTalk serviços como uma plataforma "greenfield" de novas soluções de editar, muitos clientes tem soluções BizTalk Server editar atuais, que poderá pretender migrar para o Azure. Uma vez que BizTalk serviços editar é concepção com base nas mesmas entidades chaves como arquitetura BizTalk Server Editar (negociação parceiros, entidades, acordos), é possível migrar artefactos BizTalk Server Editar para serviços de BizTalk.

Este documento aborda algumas das diferenças envolvidas com artefactos de BizTalk Server editar migrar a BizTalk serviços. Este documento assume conhecimentos sobre o funcionamento de processamento de BizTalk Server editar e os acordos do parceiro negociação. Para mais informações sobre BizTalk Server editar, consulte o artigo [Negociação parceiro Management utilizando BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Qual a versão do artefactos BizTalk Server editar pode ser migrada para serviços de BizTalk?

O módulo BizTalk Server editar significativamente foi melhorado para BizTalk Server 2010, quando-foi remodeled para incluir parceiros, perfis e acordos. Serviços de BizTalk utiliza o mesmo modelo para organizar os parceiros de negociação e divisões empresas dentro esses parceiros comerciais. Como resultado, migrar artefactos de editar a partir do BizTalk Server 2010 e versões posteriores para serviços de BizTalk, é um processo de reencaminhar diretamente muito mais. Para migrar os erros de editar associados à versões antes de BizTalk Server 2010, tem primeiro de actualizar BizTalk Server 2010 e, em seguida, migrar o seu artefactos Editar para BizTalk serviços.

## <a name="scenariosmessage-flow"></a>Fluxo de cenários/mensagens

Como com o servidor BizTalk, editar processamento nos serviços do BizTalk baseia-se à volta de uma solução de gestão de parceiro negociação (TPM). A solução TPM tem os seguintes componentes principais:

- Negociação parceiros, que representam organização numa transação B2B.
- Perfis, que representam divisões dentro de um parceiro comercial.
- Negociação convenções de parceiro (ou acordos), que representam o contrato de empresas entre dois/perfis de parceiros.

A ilustração seguinte ilustra semelhanças, bem como as diferenças, entre uma solução de BizTalk Server editar e a solução BizTalk serviços editar:

![][EDImessageflow]

Os principais diferenças e semelhanças, entre um fluxo de solução editar no servidor BizTalk e serviços de BizTalk são:

- Tal como servidosBizTalk utiliza um "pipeline EDIReceive" para receber uma mensagem de editar e um "pipeline EDISend" para enviar uma mensagem de editar, BizTalk serviços utiliza uma editar receber bridge para receber e enviar editar bridge para enviar mensagens de editar. Em servidor de BizTalk, as tubagens estão associadas um contrato utilizando enviar ou recebem portas. Serviços de BizTalk, acordo propriamente dito indica a enviar ou receber bridge.
- No servidor BizTalk, depois de tubagem EDIReceive processa a mensagem de editar, a mensagem é copiada para uma base de dados do SQL Server. Pipeline de EdiSend, em seguida, escolhe a mensagem a partir da base de dados do SQL Server, processa-lo e, em seguida, envia-lo para o parceiro de negociação.

    Serviços de BizTalk, depois do editar receber bridge processa a mensagem de editar, -encaminha a mensagem para um processo externo. O processo externo, poderá ser executado no Microsoft Azure ou no local. O processo externo deve encaminhar a mensagem para ponte de enviar editar; a enviar bridge não separar implicitamente a mensagem. Depois de processar a mensagem, o bridge de enviar editar encaminha a mensagem para o parceiro de negociação.

Serviços de BizTalk fornece uma experiência de configuração de fácil utilização para rapidamente criar e implementar um contrato B2B entre negociação parceiros sem configurar qualquer calcular do Microsoft Azure instâncias (funções Web ou de trabalho), quaisquer bases de dados do Microsoft Azure SQL ou quaisquer outras contas de armazenamento do Microsoft Azure. Cenários mais complexos irão necessitar associando fluxos de trabalho ou outros processamento do serviço "em torno dos contornos" de um contrato de negociação parceiro, ou seja, antes ou depois de processamento de bridge negociação Editar contrato do parceiro. Em detalhe, as seguintes sequências de eventos ocorrerem durante uma mensagem de editar processamento nos serviços do BizTalk.

1. É recebida uma mensagem de editar a partir do parceiro, Fabrikam comercial.  Para receber mensagens de editar a partir de parceiros comerciais, serviços BizTalk suporta protocolos de transporte como FTP, SFTP, AS2 e HTTP/S.

2. O processamento de lado receber contrato de parceiro negociação desassembla a mensagem de editar a formato XML.  Pode encaminhar a mensagem de editar desmontada (em formato XML) para os pontos finais de serviço Bus como um ponto final de serviço Bus reencaminhamento, serviço Bus tópico, fila de Bus serviço ou uma bridge BizTalk serviços.

3. As mensagens XML desmontadas, em seguida, poderão ser recebidas partir do ponto final para transformação subsequente personalizado.  Estes pontos finais foi processadas por um componente no local ou de uma instância do Microsoft Azure calcular processo mais a mensagem no serviço de fluxo de trabalho do Windows (WF) ou Windows Communication Foundation (WCF), por exemplo.

4. "Processamento do lado do enviar" do parceiro negociação de contrato, em seguida, reúne a mensagem XML para formato de editar e envia-a negociação parceiro, Contoso.  Para enviar mensagens de editar a parceiros comerciais, serviços BizTalk suporta os mesmos protocolos idênticos utilizado para receber mensagens de editar.

Este documento ainda mais fornece orientações conceptual em migrar alguns dos erros BizTalk Server editar diferentes para serviços de BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Marcar envio/receção portas para os parceiros comerciais

No servidor BizTalk configurar localizações de receber receber todas as portas e receber mensagens de editar/XML de parceiros comerciais e configurar as portas de enviar para enviar mensagens de editar/XML para parceiro comercial. Em seguida, ocupar estas portas para um contrato parceiro negociação utilizando a consola de administração do servidor BizTalk. Serviços de BizTalk, as localizações onde receber mensagens de parceiros comerciais e onde enviar mensagens para os parceiros comerciais estão configuradas como parte de negociação parceiro acordo propriamente dito (como parte das definições de transporte) no Portal de serviços do BizTalk.  Por isso, não realmente tem o conceito de "Enviar portas" e "receber localizações", por si, nos serviços do BizTalk. Para mais informações, consulte o artigo [Criar acordos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Tubagens (pontes)

No BizTalk Server editar, tubagens são entidades de processamento de mensagem que também podem incluir lógica personalizada para capacidades de processamento específico, conforme necessário pela aplicação. Para os serviços de BizTalk, o equivalente seria um bridge editar. No entanto nos serviços do BizTalk, por agora, pontes editar são "fechados".  Ou seja, não pode adicionar o seus próprio atividades personalizadas a um bridge editar. Qualquer processamento personalizado deve ser feito fora da bridge de editar na sua aplicação, antes ou depois da mensagem introduz o bridge configurado como parte do contrato de negociação parceiro. Pontes endereços EAI tem a opção para efetuar o processamento personalizado. Se pretender processamento personalizado, pode utilizar pontes endereços EAI antes ou depois da mensagem é processada por ponte a editar. Para mais informações, consulte o artigo [como incluir código personalizado no pontes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Pode inserir um fluxo de publicar/subscrever com código personalizado e/ou utilizar o serviço Bus mensagens filas e tópicos antes do contrato de parceiro negociação recebe a mensagem ou após o acordo processa a mensagem e encaminha-o para um ponto final de serviço Bus.

Consulte o artigo **Fluxo de cenários/mensagens** neste tópico para o padrão de fluxo de mensagem.

## <a name="agreements"></a>Acordos

Se estiver familiarizado com o servidor 2010 Trading parceiro acordos BizTalk utilizado para processamento de editar, procure muito familiares serviços BizTalk negociação os acordos do parceiro. A maior parte das definições do contrato é os mesmos e utilize a terminologia mesmo. Em alguns casos, as definições de acordo são muito mais simples comparada com as mesmas definições no servidor BizTalk. Suporta de serviços da Microsoft Azure BizTalk X12, EDIFACT e AS2 de transporte.

Serviços da Microsoft Azure BizTalk também fornece uma ferramenta de **Migração de dados TPM** migrar negociação parceiros e os acordos de módulo do parceiro. negociação servidor BizTalk serviços portal. A ferramenta de migração de dados TPM está disponível como parte de um pacote de ferramentas, que podem ser transferido do [SDK MAK, MAB](http://go.microsoft.com/fwlink/p/?LinkId=235057). O pacote também inclui um ficheiro Leia-me que fornece instruções sobre como utilizar a ferramenta e informações básicas de resolução de problemas para a ferramenta.

## <a name="schemas"></a>Esquemas

Serviços de BizTalk fornece esquemas de editar que podem ser utilizados em soluções BizTalk serviços.  Além disso, esquemas de BizTalk Server editar podem também ser utilizados com os serviços de BizTalk, porque o nó de raiz do esquema editar é o mesmo através de BizTalk Server, bem como BizTalk serviços. Assim sendo, irá conseguirá diretamente levar os seus esquemas BizTalk Server editar e utilizá-los nas soluções de editar desenvolver a utilizar os serviços de BizTalk. Também pode transferir os esquemas do [SDK MAK, MAB](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapas (transformações)

Mapas no servidor BizTalk chamam transformações nos serviços do BizTalk. Migrar mapas a partir do servidor BizTalk para BizTalk serviços poderão ser um as tarefas mais complexas para alcançar (consoante a complexidade de mapa). A ferramenta de mapeamento utilizada para os serviços de BizTalk é a diferença entre o mapeamento de BizTalk pontos. Apesar do mapeamento de pontos principalmente tenha o mesmo aspeto, o formato de mapa subjacente for diferente. Functoids (denominados **Operações de mapa** nos serviços do BizTalk) disponíveis para os utilizadores também são diferentes.  Na verdade, não pode utilizar diretamente um mapa BizTalk nos serviços do BizTalk. Além disso, nem todos os functoids disponíveis no servidor BizTalk estão disponíveis como operações de mapa nos serviços do BizTalk.

### <a name="new-transform-operations"></a>Operações de transformação de novo

Enquanto a lista mapa de operações de transformação disponíveis parecer bastante diferente a partir do mapeamento de pontos de servidor BizTalk, transformações de serviços de BizTalk ter novas formas de consegui-as mesmas tarefas. Por exemplo, transformações de serviços de BizTalk ter **Operações de lista** disponíveis. Não foi disponível no mapeamento de BizTalk pontos.  As **Operações de lista** permitem-lhe criar e trabalhar com numa "lista", sempre que uma lista é um conjunto de itens (também conhecido como "linhas") e que cada item pode ter vários membros (também conhecido como "colunas").  Pode ordenar a lista, selecione itens com base numa condição, etc.

Outro exemplo de nova funcionalidade no transformações de serviços de BizTalk são as **Operações de ciclo**.  É difícil criar ciclos aninhados no mapeamento de pontos de BizTalk Server.  Assim, as operações de mapa de ciclo são adicionadas para transformações de serviços de BizTalk.

Ainda outro exemplo é a operação de mapa de expressão **Else If-em seguida** .  Efetuar uma operação if-then-else era possível com o mapeamento de BizTalk pontos, mas -obrigatório vários functoids para realizar uma tarefa aparentemente simple.

### <a name="migrating-biztalk-server-maps"></a>Conjunto mapas do servidor de BizTalk migrar

Serviços da Microsoft Azure BizTalk fornece uma ferramenta para migrar servidosBizTalk mapas para serviços de BizTalk transformações. O **BTMMigrationTool** está disponível como parte do pacote **Ferramentas** fornecido com o [BizTalk Services SDK Transferir](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para mais informações sobre a ferramenta, consulte o artigo [converter um mapa de BizTalk para uma transformação de serviços de BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Também pode observe uma amostra por Sandro Pereira, MVP do BizTalk, sobre como [Migrar mapas de BizTalk Server para serviços de BizTalk transformações](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations

Se precisar de migrar orchestration servidosBizTalk processamento para Microsoft Azure, os orchestrations seriam necessário ser reprogramados porque o Microsoft Azure não suporta a execução servidosBizTalk orchestrations.  Foi reescrever a funcionalidade de orchestration num serviço Windows fluxo de trabalho Foundation 4.0 (WF4).  Seria reescrita de concluída, porque não existe atualmente não migração do orchestrations BizTalk Server para WF4. Eis alguns recursos de fluxo de trabalho do Windows:

- [*Como integrar um serviço de fluxo de trabalho WCF com o serviço Bus filas e tópicos*](https://msdn.microsoft.com/library/azure/hh709041.aspx) por Paolo Salvatori. 

- [ *Edifício aplicações com o Windows Workflow Foundation e o Azure* sessão](http://go.microsoft.com/fwlink/p/?LinkId=237314) a partir da conferência de compilação 2011.

- [*Windows Workflow Foundation Developer Center*](http://go.microsoft.com/fwlink/p/?LinkId=237315) no MSDN.

- [*Documentação de 4 de Foundation de fluxo de trabalho do Windows (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) no MSDN.

## <a name="other-considerations"></a>Outras considerações

Seguem-se algumas considerações que terá de fazer enquanto estiver a utilizar os serviços de BizTalk.

### <a name="fallback-agreements"></a>Acordos de contingência

Processamento de BizTalk Server editar tem o conceito de "Acordos de contingência".  Serviços de BizTalk é que **não** têm um conceito de contingência contrato até ao momento.  Consulte BizTalk tópicos de documentação [A função de acordos no processamento de editar](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [configurar o Administrador Global ou de contingência contrato propriedades](https://msdn.microsoft.com/library/bb245981.aspx) para obter informações sobre como acordos de contingência são utilizados no servidor BizTalk.

### <a name="routing-to-multiple-destinations"></a>Encaminhamento para vários destinos

Pontes BizTalk Services, no seu estado atual não suporta o encaminhamento de mensagens para vários destinos utilizando uma publicação-subscrever o modelo. Em vez disso, pode encaminhar mensagens de uma bridge BizTalk serviços a um tópico de serviço Bus, em seguida, pode ter múltiplas subscrições para receber a mensagem mais do que um ponto final.

## <a name="conclusion"></a>Conclusão

Serviços da Microsoft Azure BizTalk é atualizado em marcos normais para adicionar mais funcionalidades e capacidades. Com cada atualização, olharmos avançar para a maior funcionalidade para facilitar a criação de soluções de fim-para-end utilizando os serviços de BizTalk e outras tecnologias Azure de apoio.

## <a name="see-also"></a>Consulte também

[Desenvolver aplicações empresariais com Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
