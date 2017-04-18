<properties
    pageTitle="Resolução de problemas de pontos finais de Azure CDN devolver estado 404 | Microsoft Azure"
    description="Resolver problemas de 404 códigos de resposta com os pontos finais de Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Resolução de problemas de pontos finais CDN devolver 404 Estados

Este artigo ajuda-o a resolução de problemas com [os pontos finais CDN](cdn-create-new-endpoint.md) devolver 404 erros.

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [Azure o MSDN e os fóruns de pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Em alternativa, também pode preencher um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**.

## <a name="symptom"></a>Sintoma

Criou um perfil CDN e um ponto final, mas o seu conteúdo parece não estar disponíveis na CDN.  Os utilizadores que tentarem aceder ao conteúdo através do URL de CDN recebem HTTP 404 os códigos de estado. 

## <a name="cause"></a>Causa

Existem várias causas possíveis, incluindo:

- Origem do ficheiro não está visível para a CDN
- O ponto final está a mal configurado, a causar a CDN procurar no local errado
- Anfitrião do está a rejeitar o cabeçalho de anfitrião do CDN
- O ponto final não tivesse o tempo a serem propagadas em toda a CDN

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

> [AZURE.IMPORTANT] Depois de criar um ponto final de CDN, não imediatamente será disponível para utilização, como demora tempo para o registo a serem propagadas através de CDN.  Perfis de <b>CDN Azure a partir do Akamai</b> , de propagação normalmente é concluído dentro de um minuto.  Perfis de <b>CDN Azure a partir do Verizon</b> , de propagação normalmente irá concluir no prazo de 90 minutos, mas em alguns casos, pode demorar mais tempo.  Se concluir os passos neste documento e continuar a obter 404 respostas, considere a aguardar algumas horas para verificar novamente antes de abrir um bilhetes de suporte.

### <a name="check-the-origin-file"></a>Dar entrada do ficheiro de origem

Em primeiro lugar, podemos deve verificar esse ficheiro queremos em cache está disponível no nossa origem e está acessível publicamente.  A forma mais rápida para o fazer é abra um browser numa sessão de janela ou em privado e navegue diretamente para o ficheiro.  Basta escreva ou cole o URL na caixa Endereço e ver se que resulta num ficheiro esperado.  Neste exemplo, vou utilizar um ficheiro tenho numa conta de armazenamento do Windows Azure, acessível no `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como pode ver, transmite com êxito o teste.

![Sucesso!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Enquanto esta é a forma mais rápida e fácil para verificar o que seu ficheiro está disponível ao público, algumas configurações de rede na sua organização podem dar a ilusão que este ficheiro é publicamente disponível quando é de facto, só visível para os utilizadores da sua rede (mesmo se estão alojados no Azure).  Se tiver um browser externo a partir do qual pode testar o, tal como um dispositivo móvel que não está ligado à rede da sua organização ou uma máquina virtual no Azure, que seria melhor.

### <a name="check-the-origin-settings"></a>Verifique as definições de origem

Agora que recomendamos validar que o ficheiro está disponível ao público na internet, podemos deve verificar as definições do nosso origem.  No [Portal do Azure](https://portal.azure.com), navegue para o seu perfil CDN e clique no ponto final que está a resolução de problemas.  Na resultante pá de **ponto final** , clique em origem.  

![Pá de ponto final com origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

É apresentada a pá de **origem** . 

![Pá de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e o nome do anfitrião

Verifique o **tipo de origem** está correto e verifique se o **nome do anfitrião origem**.  No exemplo, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, a parte do nome do anfitrião do URL está `cdndocdemo.blob.core.windows.net`.  Como pode ver na captura de ecrã, este é correta.  Para o armazenamento do Windows Azure, Web App e origens de serviço na nuvem, o campo **Origin hostname** é uma lista pendente, pelo que não necessita de se preocupar corretamente a ortografia.  No entanto, se estiver a utilizar uma origem personalizada, é *realmente crítico* que o nome do anfitrião está escrito corretamente!

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS

A outra coisa para verificar aqui é o **HTTP** e **portas HTTPS**.  Na maioria dos casos, 80 e 443 estão corretos e irão necessitar sem alterações.  No entanto, se o servidor de origem está a escutar uma porta diferente, que terão de ser representado aqui.  Se não tiver a certeza, veja apenas o URL para o seu ficheiro de origem.  Especificações de HTTP e HTTPS especificar portas 80 e 443 como as predefinições. Na minha URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, uma porta não for especificada, para que é considerada a predefinição de 443 e as minhas definições estão corretas.  

No entanto, diga o URL para o ficheiro de origem que testado anterior é `http://www.contoso.com:8080/file.txt`.  Nota a `:8080` no final do segmento hostname.  Indica que o browser para utilizar a porta `8080` para ligar para o servidor web em `www.contoso.com`, pelo que terá de introduzir 8080 no campo **porta HTTP** .  É importante ter em atenção que estas definições de porta afetam apenas que porta o ponto final utiliza para obter informações a partir da origem.

> [AZURE.NOTE] Os pontos finais de **CDN Azure a partir do Akamai** não permitir o intervalo de portas TCP completo para origens diferentes.  Para obter uma lista de portas de origem que não são permitidos, consulte o artigo [CDN Azure a partir de portas de origem do Akamai permitidas](https://msdn.microsoft.com/library/mt757337.aspx).  
  
### <a name="check-the-endpoint-settings"></a>Verifique as definições de ponto final

Novamente no pá **ponto final** , clique no botão de **Configurar** .

![Pá de ponto final com o botão de configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

É apresentada a pá de **Configurar** o ponto final.

![Configurar pá](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos

Para **protocolos**, certifique-se de que o protocolo a ser utilizado pelos clientes está seleccionado.  O mesmo protocolo utilizado pelo cliente será a que utilizou para aceder à origem, pelo que é importante para que as portas de origem corretamente configuradas na secção anterior.  O ponto final aguarda apenas nas HTTP e HTTPS portas predefinidas (80 e 443), independentemente das portas de origem.

Vamos regressar ao nosso exemplo hipotético com `http://www.contoso.com:8080/file.txt`.  Como se recorde, Contoso especificado `8080` como os respetivos HTTP da porta, mas também Imaginemos elas especificadas `44300` como os respetivos porta HTTPS.  Se criado a um ponto final denominado `contoso`, os respetivos hostname de ponto final CDN seria `contoso.azureedge.net`.  Um pedido para `http://contoso.azureedge.net/file.txt` é um pedido de HTTP, por isso o ponto final utilizaria HTTP na porta 8080 para obtê-la a partir da origem.  Um pedido de seguro através de HTTPS, `https://contoso.azureedge.net/file.txt`, pode causar o ponto final utilizar HTTPS numa porta 44300 quando retriving o ficheiro a partir da origem.

#### <a name="origin-host-header"></a>Cabeçalho de anfitrião de origem

**Cabeçalho de anfitrião de origem** é o valor de cabeçalho de anfitrião enviado para a origem com cada pedido.  Na maioria dos casos, isto deve ser igual **origem hostname** podemos verificados anteriormente.  Um valor incorrecto neste campo não geralmente causar 404 Estados, mas é provável que fazer com que outros Estados 4xx, consoante o que a origem espera.

#### <a name="origin-path"></a>Caminho de origem

Por fim, vamos deve verificar nosso **caminho de origem**.  Por predefinição, está em branco.  Apenas deve utilizar este campo se pretender limitar o âmbito dos recursos à alojado de origem que pretende disponibilizar na CDN.  

Por exemplo, no meu ponto final, posso pretendia todos os recursos na minha conta de armazenamento para estar disponível, pelo que para a esquerda **caminho de origem** em branco.  Isto significa que um pedido para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` resulta numa ligação a partir do meu ponto final para `cdndocdemo.core.windows.net` que pede `/publicblob/lorem.txt`.  Da mesma forma, um pedido para `https://cdndocdemo.azureedge.net/donotcache/status.png` resulta num pedido de ponto final `/donotcache/status.png` a partir da origem.

Mas o que acontece se não quiser utilizar a CDN para cada caminho no meu origem?  Diga I só queria para expor o `publicblob` caminho.  Se */publicblob* devo introduzir no meu campo **caminho de origem** , que irão provocar o ponto final inserir */publicblob* antes de cada pedido para a origem.  Isto significa que o pedido de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` realmente agora irá demorar a parte do pedido de URL, `/publicblob/lorem.txt`e acrescentar `/publicblob` para o início. Isto resulta num pedido para `/publicblob/publicblob/lorem.txt` a partir da origem.  Se não resolver o caminho para um ficheiro de real, a origem irá devolver um estado 404.  O URL correto para obter lorem.txt neste exemplo realmente seria `https://cdndocdemo.azureedge.net/lorem.txt`.  Tenha em atenção que recomendamos não incluir o caminho */publicblob* sequer, porque a parte do pedido do URL é `/lorem.txt` e adiciona o ponto final `/publicblob`, o que resulta no `/publicblob/lorem.txt` o pedido transmitido à origem.
