<properties
    pageTitle="Suporte de partilha (CORS) de recursos de publicação em origem | Microsoft Azure"
    description="Saiba como ativar o suporte do CORS para os serviços de armazenamento do Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Recurso de origem da publicação em partilha (CORS) suporte para os serviços de armazenamento Azure

Começando com a versão 2013-08-15, os serviços de armazenamento Azure suportam partilha de recursos por comparação origem (CORS) para os serviços de BLOBs, tabela, fila de espera e ficheiro. CORS é uma funcionalidade HTTP que permite uma aplicação web em execução num domínio para aceder aos recursos no outro domínio. Os browsers implementam uma restrição de segurança conhecida como [mesma origem política](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impede uma página web a partir de chamada APIs num domínio diferente; CORS fornece uma forma segura para permitir que um domínio (origem) ligar APIs no outro domínio. Consulte a [especificação de CORS](http://www.w3.org/TR/cors/) para obter detalhes sobre CORS.

Pode definir regras CORS individualmente para cada um dos serviços de armazenamento, ao contactar o suporte [Definir propriedades do serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila de espera](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço da tabela](https://msdn.microsoft.com/library/hh452240.aspx). Depois de definir as regras CORS no serviço, um pedido de corretamente autenticado criado contra o serviço a partir de um domínio diferente será avaliado para determinar se é permitida acordo com as regras que especificou.

>[AZURE.NOTE] Tenha em atenção que CORS não é um dispositivo de autenticação. Qualquer pedido relativamente a um recurso de armazenamento quando é activado CORS têm de ter uma assinatura de autenticação adequada ou deve ser feito relativamente a um recurso público.

## <a name="understanding-cors-requests"></a>Noções sobre CORS pedidos

Um pedido de CORS a partir de um domínio de origem pode consistir de pedidos de separada dois:

- Um pedido prévia, que consultas as restrições de CORS impostas pelo serviço. O pedido prévia é necessário a menos que o método de pedido é um [método simples](http://www.w3.org/TR/cors/), o que significa que GET, HEAD ou mensagem.

- O pedido real, efetuado contra o recurso pretendido.

### <a name="preflight-request"></a>Pedido de prévia

As consultas de pedido prévia as restrições de CORS que foram estabelecidas para o serviço de armazenamento pelo proprietário da conta. O browser (ou outros agente do utilizador) envia um pedido de opções que inclui a cabeçalhos de pedido, o método e origem domínio. O serviço de armazenamento avalia a operação pretendida baseada num conjunto de regras CORS que especificar quais os domínios de origem, pedido métodos e cabeçalhos de pedido podem ser especificados a um pedido real relativamente a um recurso de armazenamento pré-configurada.

Se CORS estiver ativado para o serviço e existe uma regra CORS que corresponda ao pedido prévia, o serviço responde com código de estado 200 (OK) e inclui os cabeçalhos de controlo de acesso necessários na resposta.

Se CORS não estiver ativado para o serviço ou nenhuma regra CORS corresponde ao pedido de prévia, o serviço irá responder com código de estado 403 (proibido).

Se o pedido de opções não contém os cabeçalhos CORS necessários (dos cabeçalhos de origem e pedido de método de acesso de controlo), o serviço irá responder com código de estado 400 (pedido inválido).

Tenha em atenção que um pedido de prévia é avaliado contra o serviço (Blob, fila de espera e tabela) e não contra o recurso pedido. O proprietário da conta tem de ter ativada CORS como parte das propriedades de serviço de conta para que o pedido seja concluída com êxito.

### <a name="actual-request"></a>Pedido de real

Quando o pedido prévia é aceite e a resposta é devolvida, browser irá enviar trabalhos de pedido de real contra o recurso de armazenamento. Browser irá recusar o valor real pedir imediatamente se o pedido de prévia é rejeitado.

O pedido real é tratado como pedido normal contra o serviço de armazenamento. A presença do cabeçalho da origem de indica que o pedido for um pedido de CORS e o serviço irá verificar as regras de CORS correspondência. Caso uma correspondência for encontrada, os cabeçalhos de controlo de acesso são adicionados à resposta e enviados novamente para o cliente. Se não for encontrada uma correspondência, os cabeçalhos de controlo de acesso CORS não são devolvidos.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Ativar CORS para os serviços de armazenamento do Windows Azure

Regras CORS são definidas ao nível do serviço, pelo que necessita ativar ou desativar CORS para cada serviço (Blob, tabela de fila e) em separado. Por predefinição, é desativada CORS para cada serviço. Para ativar CORS, tem de definir as propriedades de serviço adequado a utilizar a versão 2013-08-15 ou posterior e adicionar regras CORS para as propriedades do serviço. Para obter detalhes sobre como ativar ou desativar CORS para um serviço e como definir regras CORS, consulte [Definir propriedades do serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila de espera](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço da tabela](https://msdn.microsoft.com/library/hh452240.aspx).

Eis um exemplo de uma regra de CORS única especificado através de uma operação de definir propriedades de serviço:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Cada elemento incluído na regra CORS é descrito abaixo:

- **AllowedOrigins**: os domínios de origem que tiverem permissão para fazer um pedido de serviço de armazenamento através de CORS. O domínio de origem é o domínio que originou o pedido. Tenha em atenção que a origem tem de ser uma correspondência exata entre maiúsculas e minúsculas com a origem que envia a idade de utilizador para o serviço. Também pode utilizar o caráter universal ' *' para permitir que todos os domínios de origem fazer pedidos através da CORS. No exemplo acima, a domínios [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) podem fazer pedidos de serviço utilizando CORS.

- **AllowedMethods**: os métodos (verbos de pedido HTTP) que pode utilizar o domínio de origem para um pedido de CORS. No exemplo acima, apenas os pedidos de colocar e obter são permitidos.

- **AllowedHeaders**: os cabeçalhos de pedido que pode especificar o domínio de origem no pedido de CORS. No exemplo acima, todos os cabeçalhos de metadados começando x-ms-metadado de x-ms meta destino e x-ms meta abc são permitidos. Tenha em atenção que o caráter universal ' *' indica que é permitido qualquer início cabeçalho com o prefixo especificado.

- **ExposedHeaders**: os cabeçalhos de resposta que podem ser enviados na resposta ao pedido de CORS e expostos pelo browser para o emissor pedido. No exemplo acima, browser é indicado para expor qualquer começando de cabeçalho x-ms-meta.

- **MaxAgeInSeconds**: pedir o tempo máximo que num browser deve ter em cache as opções prévia.

Os serviços de armazenamento Azure suportam especificando o prefixo cabeçalhos para elementos a **AllowedHeaders** e o **ExposedHeaders** . Para permitir que uma categoria de cabeçalhos, pode especificar um prefixo comuns para nessa categoria. Por exemplo, especificar *x-ms-meta** como um cabeçalho prefixed estabelece uma regra que irá corresponder todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações aplicam-se para regras de CORS:

- Pode especificar até cinco regras CORS por serviço de armazenamento (Blob, tabela e fila).
- O tamanho máximo de todas as CORS regras definições no pedido de, excluindo tags XML, não deve exceder 2 KB.
- O comprimento de um cabeçalho permitido, cabeçalho exposto ou origem permitida não deve exceder 256 carateres.
- Os cabeçalhos permitidos e exposta poderão ser:
  - Cabeçalhos literais, onde o nome de cabeçalho exata é fornecido, tal como **processadas de x ms meta**. Um máximo de 64 cabeçalhos literais pode ser especificado no pedido.
  - Cabeçalhos, onde for fornecido um prefixo do cabeçalho da, tal como **x ms-metadados**o prefixo *. Especificar um prefixo desta forma permite ou expõe qualquer cabeçalho que começa com o prefixo determinado. Um máximo de dois cabeçalhos prefixo pode ser especificado no pedido.
- Os métodos (ou verbos de HTTP) especificado no elemento **AllowedMethods** estar em conformidade com os métodos de suportados pelo serviço de armazenamento Azure APIs. Métodos suportados são eliminar, GET, cabeça, impressão em série, publicar, opções e colocar.

## <a name="understanding-cors-rule-evaluation-logic"></a>Noções sobre a lógica de avaliação do CORS regra

Quando um serviço de armazenamento recebe um pedido de prévia ou real, é avaliado esse pedido com base em regras CORS estabelecidos para o serviço através da operação de definir propriedades do serviço apropriada. Regras CORS são avaliadas pela ordem em que foram definidas no corpo do pedido da operação de definir propriedades do serviço.

Regras CORS são avaliadas da seguinte forma:

1. Em primeiro lugar, é verificado o domínio de origem do pedido de contra os domínios listados para o elemento **AllowedOrigins** . Se o domínio de origem é incluído na lista ou todos os domínios são permitidos com o caráter universal ' *', regras, em seguida, a receita de avaliação. Se o domínio de origem não for incluído, o pedido falhar.

2. Em seguida, o método (ou verbo de HTTP) do pedido é verificado contra métodos listados no elemento de **AllowedMethods** . Se o método for incluído na lista, em seguida, avaliação de regras avança; caso contrário, em seguida, o pedido irá falhar.

3. Se o pedido corresponde a uma regra no seu domínio de origem e o seu método, nessa regra está selecionada para processo que são avaliadas pedido e não existem novas regras. No entanto, antes do pedido pode ser concluída com êxito, quaisquer cabeçalhos especificados no pedido de são comparados com os cabeçalhos listados no elemento de **AllowedHeaders** . Se os cabeçalhos de enviados não corresponder os cabeçalhos permitidos, o pedido falhar.

Uma vez que as regras são processadas na ordem estão presentes no corpo do pedido, melhores práticas recomendado que especificar as regras mais restritivas relativamente às origens diferentes pela primeira vez na lista, para que estes são avaliados pela primeira vez. Especificar as regras que são menos restritivas – por exemplo, uma regra para permitir todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo – CORS regras de avaliação

O exemplo seguinte mostra um corpo do pedido parcial para uma operação definir regras CORS para os serviços de armazenamento. Consulte o artigo [Definir propriedades do serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila de espera](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço da tabela](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre como construir o pedido.

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Em seguida, tenha em atenção os seguintes pedidos de CORS:

Pedido||| Resposta||
---|---|---|---|---
**Método** |**Origem** |**Pedido de cabeçalhos** |**Correspondência de regra** |**Resultado**
**COLOCAR** | http://www.contoso.com |x-ms-blob-tipo de conteúdo | Primeira regra |Sucesso
**OBTER** | http://www.contoso.com| x-ms-blob-tipo de conteúdo | Segunda regra |Sucesso
**OBTER** | http://www.contoso.com| x-ms-blob-tipo de conteúdo | Segunda regra | Falha

O primeiro pedido corresponde a primeira regra – o domínio de origem corresponde ao origens permitidas, o método corresponde aos métodos de permitidos e o cabeçalho corresponde aos cabeçalhos permitidos – e por isso, é concluída com êxito.

O segundo pedido não corresponder a primeira regra porque o método não corresponde os métodos de permitidos. No entanto,, corresponder a segunda regra, para que a mesma é concluída com êxito.

O pedido de terceiro corresponde a segunda regra no seu domínio de origem e método, para que não existem novas regras são avaliadas. No entanto, o *cabeçalho x-ms-cliente-pedido-id* não é permitido pela segunda regra, para que o pedido falhar, apesar do facto de que a semântica da terceira regra teria permitido-o para ser concluída com êxito.

>[AZURE.NOTE] Apesar deste exemplo mostra uma regra menos restritiva antes de um mais restritivo, em geral a melhor prática é listar as regras mais restritivas pela primeira vez.

## <a name="understanding-how-the-vary-header-is-set"></a>Noções sobre como está definido de cabeçalho de variação

O cabeçalho de *variação* é um cabeçalho HTTP/1.1 padrão que consiste um conjunto de campos de cabeçalho de pedido aconselhar o agente de browser ou utilizador sobre os critérios que foram selecionadas pelo servidor para processar o pedido. Cabeçalho de *variação* é principalmente utilizado para colocação em cache proxies, browsers e CDNs, que utilizam para determinar como a resposta deve ser colocada em cache. Para obter detalhes, consulte o artigo a especificação de para o [cabeçalho de variação](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o browser ou outro agente do utilizador coloca a resposta a partir de um pedido de CORS, o domínio de origem é colocada em cache, como a origem permitida. Quando um domínio de segundo emite mesmo pedido para um recurso de armazenamento enquanto a cache está ativa, o agente de utilizador obtém o domínio de origem em cache. O domínio de segundo não corresponder a do domínio em cache, pelo que o pedido de falha quando caso contrário, teria teve êxito. Em certos casos, armazenamento do Windows Azure define o cabeçalho de variação de **origem** para indicar ao agente do utilizador para enviar o pedido CORS subsequente ao serviço quando o domínio pedido difere da origem em cache.

Armazenamento Azure define o cabeçalho de *variação* como **origem** para pedidos de obter/HEAD reais nos casos seguintes:

- Quando a origem do pedido corresponde exatamente a origem permitida definida por uma regra CORS. Para que seja uma correspondência exata, a regra CORS não pode incluir um caráter universal ' * ' caráter.

- Não existe nenhuma regra correspondência a origem do pedido, mas CORS está ativada para o serviço de armazenamento.

No caso de onde um pedido GET/HEAD corresponde a uma regra CORS que permite que todas as origens, a resposta indica que são permitidas todas as origens, e a cache de agente do utilizador irá permitir os pedidos subsequentes a partir de qualquer domínio de origem, enquanto a cache está ativa.

Repare que, para pedidos de através de métodos que não seja GET/cabeça, os serviços de armazenamento não definirá cabeçalho variação, uma vez que as respostas a convites para estes métodos não são armazenadas em cache por agentes do utilizador.

A tabela seguinte indica como Azure armazenamento irá responder a pedidos de obter/cabeça com base em casos mencionados anteriormente:

Pedido|Definição da conta e o resultado da avaliação das regras|||Resposta|||
---|---|---|---|---|---|---|---|---
**Cabeçalho de origem presente no pedido** | **Regra (s) CORS especificada para este serviço** | **Existe uma regra de correspondência que permite que todas as origens (*)** | **Regra de correspondência existe correspondência exata origem** | **Resposta inclui o cabeçalho de variação definido como origem** | **Resposta inclui acesso controlo-permitido-origem: "*"** | **Resposta inclui acesso-controlo-expostos-cabeçalhos**
N|N|N|N|N|N|N
N|Sim|N|N|Sim|N|N
N|Sim|Sim|N|N|Sim|Sim
Sim|N|N|N|N|N|N
Sim|Sim|N|Sim|Sim|N|Sim
Sim|Sim|N|N|Sim|N|N
Sim|Sim|Sim|N|N|Sim|Sim


## <a name="billing-for-cors-requests"></a>Faturação para pedidos CORS

Pedidos de prévia com êxito são faturados se ativou o CORS para qualquer um dos serviços de armazenamento da sua conta (ao contactar o suporte [Definir propriedades do serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila de espera](https://msdn.microsoft.com/library/hh452232.aspx)ou [Definir propriedades da tabela serviço](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar os custos, considere a definir o elemento **MaxAgeInSeconds** nas suas regras CORS a um valor de grande, para que o agente do utilizador coloca em cache o pedido.

Tentativas de pedidos de prévia não irão ser faturadas.

## <a name="next-steps"></a>Próximos passos

[Definir as propriedades de serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir as propriedades de serviço de fila de espera](https://msdn.microsoft.com/library/hh452232.aspx)

[Configurar as propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx)

[Recurso de origem da publicação em W3C especificação de partilha](http://www.w3.org/TR/cors/)
