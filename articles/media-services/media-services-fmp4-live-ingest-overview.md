<properties 
    pageTitle="Azure dos serviços de multimédia MP4 fragmentados live ingerir esta última especificação | Microsoft Azure" 
    description="Esta especificação descreve o protocolo e o formato para MP4 fragmentado com base em direto ingestão transmissão para serviços de multimédia do Microsoft Azure. Serviços de multimédia do Microsoft Azure fornece um serviço de transmissão direto que permite que os clientes transmitir em fluxo direto eventos e difundir conteúdo em tempo real a utilizar o Microsoft Azure como a plataforma na nuvem. Este documento aborda também melhores práticas na construir altamente redundantes e robusta live ingerir esta última mecanismos." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure dos serviços de multimédia MP4 fragmentados live ingerir esta última especificação

Esta especificação descreve o protocolo e o formato para MP4 fragmentado com base em direto ingestão transmissão para serviços de multimédia do Microsoft Azure. Serviços de multimédia do Microsoft Azure fornece um serviço de transmissão direto que permite que os clientes transmitir em fluxo direto eventos e difundir conteúdo em tempo real a utilizar o Microsoft Azure como a plataforma na nuvem. Este documento aborda também melhores práticas na construir altamente redundantes e robusta live ingerir esta última mecanismos.


##<a name="1-conformance-notation"></a>1. notação de conformidade

As palavras-chave "tem", "Não tem", "REQUIRED", "Será", "Não serão", "SHOULD", "Não deve", "Recomendado", "Poderá" e "Opcional" neste documento estão a ser interpretado conforme descrito no RFC 2119.

##<a name="2-service-diagram"></a>2. diagrama de serviço de 

O diagrama abaixo mostra a arquitetura de alto nível do serviço de transmissão direto nos serviços de multimédia do Microsoft Azure:

1.  Descodificador direto emite feeds direto para os canais que são criados e aprovisionados através do Microsoft Azure Media serviços SDK.
2.  Canais, programas e transmissão ponto final dos serviços de multimédia do Microsoft Azure alça todas as funcionalidades transmissão direto incluindo ingest, formatação, nuvem DVR, segurança, escalabilidade e redundância.
3.  Opcionalmente, poderiam escolha clientes implementar uma camada CDN entre o ponto final de transmissão e os pontos finais de cliente.
4.  Sequência de pontos finais de cliente a partir do ponto final de transmissão utilizando os protocolos HTTP ajustável transmissão (por exemplo, a transmissão suaves, travessão, HDS ou HLS).

![image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. formato de sequência de bits – ISO 14496-12 fragmentado MP4

O formato de fio para transmissão direto ingerir esta última abordados neste documento baseia [ISO-14496-12]. Fórum consultar [[MS SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) para obter uma explicação detalhada do formato MP4 fragmentado e extensões para ambos os ficheiros de invés e live transmissão ingestão.

###<a name="live-ingest-format-definitions"></a>Live ingerir esta última definições de formato 

Segue-se uma lista do formato especial de definições que se aplicam a live ingerir esta última dos serviços de multimédia do Microsoft Azure:

1. O 'ftyp', LiveServerManifestBox e caixa 'moov' devem ser enviados com cada pedido de (HTTP POST).  DEVE ser enviada no início do fluxo de e sempre o codificador tem restabelecer a ligação para retomar a sequência ingerir esta última.  Consulte a secção 6 no [1] para obter mais detalhes.
2. Secção 3.3.2 [1] Define uma caixa opcional denominada StreamManifestBox para direto ingerir esta última. Devido a lógica encaminhamento do Balanceador de carga do Microsoft Azure, a utilização desta caixa é preterida não deve estar presentes e quando ingesting para o serviço de multimédia do Microsoft Azure. Se esta caixa estiver presente, dos serviços de multimédia do Azure silenciosamente ignora-a.
3. TrackFragmentExtendedHeaderBox definida na 3.2.3.2 no [1] deve estar presente para cada fragmento.
4. A versão 2 a TrackFragmentExtendedHeaderBox devem ser utilizadas para gerar segmentos de multimédia com URLs idênticos em vários centros de dados. O campo de índice remissivo fragmento é necessário para o Centro de dados em várias failover da transmissão com base no índice remissivo formatos como Apple HTTP Live transmissão (HLS) e MPEG-TRAÇO com base no índice.  Para ativar o Centro de dados em várias activação pós-falha, o índice de fragmento têm de ser sincronizado em múltiplas codificadores e aumentar por 1 para cada fragmento de multimédia sucessivas, até mesmo em falhas ou codificador reinicia.
5. Secção 3.3.6 [1] Define caixa denominada MovieFragmentRandomAccessBox ('mfra') que pode ser enviada no final da ingestão direto para indicar EO (fim da sequência) para o canal. Devido a lógica ingest dos serviços de multimédia do Azure, a utilização da EO (fim da sequência) é preterida e a caixa de 'mfra' ingestão direto não deve ser enviada. Se enviada, dos serviços de multimédia do Azure silenciosamente ignora-lo. Recomenda-se para utilizar o [Canal repor](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) para repor o estado do ponto de ingest e também recomenda-se para utilizar o [Programa parar](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para terminar uma apresentação e sequência.
6. A duração de fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos cliente e melhorar heurística de transferência do cliente através da utilização de repetições etiquetas.  A duração, pode variar para poder o Adquirente na íntegra para as taxas de moldura não inteiros.
7. A duração de fragmento MP4 deve estar entre aproximadamente 2 e 6 segundos.
8. Selos de tempo de fragmento de MP4 e os índices (TrackFragmentExtendedHeaderBox fragment_ absolute_ tempo e fragment_index) devem chegam são enviadas por ordem crescente.  Apesar de ser e são para fragmentos duplicados dos serviços de multimédia do Azure, tem muito limitado a capacidade de reordenar fragmentos de acordo com a linha cronológica de multimédia.

##<a name="4-protocol-format--http"></a>4. formato de protocol – HTTP

ISO fragmentado MP4 com base no live ingerir esta última para utilizações dos serviços de multimédia do Microsoft Azure um padrão de execução longa que bloqueiam HTTP POST pedir para transmitir dados de multimédia codificado compactados num formato MP4 fragmentado ao serviço. Cada mensagem de HTTP envia uma concluída fragmentado MP4 sequência de bits ("da cadeia") começando começando com caixas de cabeçalho (caixa "Live servidor manifesto caixa" e 'ftyp' e 'moov'), prosseguindo com uma sequência de fragmentos (caixas 'moof' e 'mdat'). Consulte a secção em 9.2 [1] para a sintaxe de URL para o pedido de HTTP POST. Um exemplo do URL da mensagem é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Requisitos de

Eis os requisitos detalhados:

1. Descodificador deverá começar a difusão ao enviar um pedido de HTTP POST com um vazio "corpo" (comprimento zero conteúdo) utilizando o mesmo URL ingestão. Isto pode ajudar a detetar rapidamente se o ponto final ingestão direto é válido e se existir qualquer autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não poderá enviar resposta de HTTP anterior até que o pedido todo, incluindo o corpo da mensagem for recebido. Dada a natureza longa do evento direto, neste passo, o codificador poderá não conseguir detetar algum erro até estar concluída enviar todos os dados.
2. Descodificador deve processar quaisquer erros ou a autenticação são os desafios mais estatístico como resultado da (1). Se (1) é concluída com êxito com uma resposta 200, continuar.
3. Descodificador tem de iniciar um novo pedido de HTTP POST com a sequência de MP4 fragmentada.  TEM de iniciar a carga de útil com as caixas de cabeçalho seguidas de fragmentos.  Nota a caixa de "Live servidor manifesto caixa" e 'ftyp' e 'moov' (por esta ordem) deve ser enviada com cada pedido, mesmo se o codificador tem restabelecer a ligação de uma vez que o pedido anterior foi terminado antes do fim da sequência. 
4. Descodificador tem de utilizar blocos transferir codificação para carregar uma vez que é impossível prever o comprimento do conteúdo inteiro do evento direto.
5. Quando o evento é ao longo do, depois de enviar o fragmento de último codificador correctamente tem de terminar a sequência de mensagens blocos transferir codificação (a maioria dos pilhas de cliente HTTP processar automaticamente). Descodificador tem de aguardar do serviço devolver o código de resposta final e, em seguida, terminar a ligação. 
6. Descodificador não pode utilizar o substantivo Events() conforme descrito em 9.2 [1] para ingestão direto dos serviços de multimédia do Microsoft Azure.
7. Se o pedido de HTTP POST termina ou o tempo limite antes do fim da sequência com um erro TCP, o codificador tem um novo pedido de mensagem com uma nova ligação de problema e siga os requisitos acima com os requisitos adicionais que o codificador tem reenviar a anterior duas MP4 de fragmentos para cada faixa na sequência e retomar sem a introdução de avaliação da linha cronológica de multimédia.  Reenviar os dois últimos fragmentos de MP4 para cada faixa assegura que não existe sem perda de dados.  Por outras palavras, se uma sequência de contém ambas as uma pista de áudio e vídeo e o pedido de mensagem atual falhar, o codificador tem restabelecer a ligação e reenviar os dois últimos fragmentos para a faixa de áudio, anteriormente foram enviadas com êxito, e os dois últimos fragmentos da faixa de vídeo, que foram anteriormente enviados com êxito, para se certificar de que não existe sem perda de dados.  O codificador tem de manter uma "reencaminhar" memória intermédia de fragmentos de multimédia, reenvios-lo quando voltar a ligar.

##<a name="5-timescale"></a>5. escala temporal 

[[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descreve a utilização de "Escala temporal" para SmoothStreamingMedia (secção 2.2.2.1), StreamElement (secção 2.2.2.3), StreamFragmentElement(2.2.2.6) e LiveSMIL (secção 2.2.7.3.1). Se o valor de escala temporal não existir, o valor predefinido utilizado é 10.000.000 (10 MHz). Apesar de suaves especificação do formato de transmissão não bloquear a utilização da outros valores de escala temporal, a maior parte das utilizações implementações codificador este valor predefinido (10 MHz) para gerar a transmissão suaves ingerir esta última dados. Devido a funcionalidade de [Embalagens de dinâmico de multimédia do Azure](media-services-dynamic-packaging-overview.md) , recomenda-se para utilizar escala temporal de 90 kHz para sequências de vídeo e kHz 44.1 ou 48.1 para sequências de áudio. Se forem utilizados valores de escala temporal diferente para sequências de diferentes, a escala temporal nível de sequência deve ser enviada. Consulte [[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. definição de "Da cadeia"  

"Sequência" é a unidade básica de operação ingestão direto para a apresentação ao vivo a compor, processamento activação transmissão e cenários redundância. "Sequência" é definida como um exclusiva fragmentado MP4 sequência de bits que pode conter uma faixa única ou várias faixas. Uma apresentação ao vivo completo pode conter um ou mais sequências dependendo da configuração da encoder(s) direto. Os exemplos abaixo ilustram várias opções de utilização de stream(s) para compor uma apresentação ao vivo completa.

**Exemplo:** 

Pretende cliente criar uma apresentação de transmissão direto que inclui o bitrates de áudio/vídeo seguinte:

Vídeo – 3000kbps, 1500kbps, 750kbps

Áudio – 128Kbps

###<a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as pistas numa sequência de

Esta opção, um único codificador gera todas as pistas de áudio/vídeo e juntá-los para um fragmentado MP4 sequência de bits que obtém enviada através de uma ligação de HTTP POST única. Neste exemplo, não existe apenas uma transmissão em fluxo para esta apresentação ao vivo:

![image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa numa sequência separada

Esta opção, encoder(s) apenas colocar um controlar para cada sequência MP4 fragmento de bits e publique todas as sequências em várias ligações de HTTP separadas. Isto pode ser feito com codificadores uma ou várias codificadores. A partir do ponto de vista do live ingestão, esta apresentação ao vivo é composta por sequências de quatro.

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Juntar faixa de áudio com a pista de vídeo de velocidade mais baixa para uma sequência de

Esta opção, o cliente escolhe juntar a faixa de áudio com a pista de vídeo de velocidade mais baixa para um fragmento MP4 sequência de bits e deixe o outro monitoriza o vídeo duas cada da sua própria cadeia a ser. 


![image4][image4]


###<a name="summary"></a>Resumo

O que é apresentado acima não é uma lista exaustiva de todas as opções de ingestão possíveis para este exemplo. Como um matter de facto, qualquer agrupamento de faixas para sequências é suportado pela ingestão direto. Clientes e descodificador fornecedores, podem escolher os seus próprios implementações com base em complexidade engenharia, a capacidade de codificador e redundância e considerações activação pós-falha. No entanto referir-se na maioria dos casos existe apenas uma faixa de áudio para toda a apresentação direto pelo que é importante para se certificar de salubridade da sequência ingest que contém a faixa de áudio. Este consideração frequentemente resulta em colocar em faixa de áudio para o seu próprio da cadeia (como opção 2) ou uni-lo com a pista de vídeo de velocidade mais baixa (como opção 3). Também para melhor redundância e tolerância a falhas, enviar a controlar o áudio mesmo em dois fluxos diferentes (opção 2 com redundantes faixas de áudio) ou unir a faixa de áudio, pelo menos, duas das mais baixos velocidade vídeo faixas (opção 3 com áudio agrupado na sequências de vídeo, pelo menos, dois) é vivamente recomendada para live ingerir esta última dos serviços de multimédia do Microsoft Azure.

##<a name="7-service-failover"></a>7. activação pós-falha do serviço 

Tendo em conta a natureza da transmissão direto, suporte de mudança de boa é fundamental para assegurar a disponibilidade do serviço. Serviços de multimédia do Microsoft Azure foi concebido para processar vários tipos de falhas incluindo erros de rede, os erros do servidor, problemas de armazenamento, etc. Quando utilizado juntamente com inicial maiúscula activação pós-falha lógica do lado codificador live, o cliente pode obter um serviço de transmissão direto altamente fiável a partir da nuvem.

Nesta secção, discutimos cenários de activação pós-falha do serviço. Neste caso, a falha acontece algures dentro do serviço e manifestos propriamente dito como um erro de rede. Aqui estão algumas recomendações para a execução de codificador para processamento activação pós-falha do serviço:


1. Utilize um tempo limite da segunda 10 para estabelecer a ligação TCP.  Se tentar estabelecer a ligação demorar mais de 10 segundos, a operação a interrupção e tente novamente. 
2. Utilize um limite de tempo breve para enviar o pedido de HTTP segmentos de mensagem.  Se a duração de fragmento de destino MP4 for segundos N, utilize um limite de tempo enviar entre segundos N e 2 n; Por exemplo, utilize um tempo limite de 6 a 12 segundos se a duração de fragmento MP4 for 6 segundos.  Se ocorrer um limite de tempo, repor a ligação, abra uma nova ligação e retomar a sequência ingerir esta última na nova ligação. 
3. Manter um intervalo de tempo ficha técnica em que contém os dois últimos fragmentos, para cada faixa enviadas com êxito e completamente para o serviço.  Se o pedido de HTTP POST para uma sequência de for terminado ou o tempo limite anteriores ao final da sequência de abrir uma nova ligação e começar a outro pedido de HTTP POST, reenviar os cabeçalhos da cadeia, reenviar os dois últimos fragmentos para cada faixa e retomar a sequência de sem a introdução de uma lacunas na linha de tempo de multimédia.  Isto irá reduzir as hipóteses de perda de dados.
4. Recomenda-se que o codificador não limitar o número de tentativas para estabelecer uma ligação ou retomar a transmissão após ocorre um erro de TCP.
5. Depois de um erro TCP:
    1. A ligação atual tem de ser fechada e, tem de ser criada uma nova ligação para um novo pedido de HTTP POST.
    2. O novo HTTP POST URL tem de ser o mesmo que o URL da mensagem inicial.
    3. O novo HTTP mensagem deve incluir cabeçalhos de sequência (caixa "Live servidor manifesto caixa" e 'ftyp' e 'moov') idênticos aos cabeçalhos da sequência da mensagem inicial.
    4. Os dois últimos fragmentos enviados para cada faixa devem ser enviado de novo e transmissão retomada sem a introdução de uma lacunas na linha de tempo de multimédia.  As marcas de hora de fragmento MP4 têm de aumentar de forma contínua, até mesmo em pedidos de HTTP POST.
6. O codificador deverá terminar o pedido de HTTP POST se não estiver a ser enviados dados taxa de juro proporcionais com a duração de fragmento MP4.  Um pedido de HTTP POST não enviar dados pode impedir que dos serviços de multimédia do Azure desligar rapidamente o codificador trabalho uma atualização de serviço.  Por este motivo, a mensagem de HTTP para dispersos faixas (sinal de ad) devem ser breves viveu, assim que o fragmento de disperso é enviado a terminar.

##<a name="8-encoder-failover"></a>8. activação pós-falha de codificador

Descodificador activação pós-falha é o segundo tipo de cenário de activação pós-falha que precisa de ser resolvidos para entrega de transmissão direto ponto a ponto. Neste cenário, a condição de erro aconteceu no lado codificador. 

![image5][image5]


Abaixo estão as expectativas a partir do ponto final ingestão direto quando codificador activação pós-falha acontece:

1. DEVERÁ ser criada uma nova instância codificador para poder continuar a transmissão, conforme ilustrado no diagrama acima (transmissão em fluxo para 3000 k vídeo com a linha tracejada).
2. O novo codificador tem de utilizar o mesmo URL para pedidos de HTTP POST de como a instância falha.
3. Pedido POST o novo codificador tem de incluir as mesmo caixas de cabeçalho de MP4 fragmentadas como a instância falha.
4. O novo codificador têm de ser sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação direto para gerar sincronizadas amostras de áudio/vídeo com limites de fragmento alinhados.
5. A nova sequência tem de ser semanticamente equivalente com a sequência de anterior e permutáveis ao nível de cabeçalho e fragmento.
6. O novo codificador deverá tentar minimizar as perdas de dados.  A fragment_absolute_time e fragment_index de fragmentos multimédia devem aumentar a partir do ponto onde o codificador última parado.  A fragment_absolute_time e fragment_index devem aumentar de uma forma contínua, mas é permissíveis para apresentar uma lacunas se for necessário.  Azure dos serviços de multimédia irá ignorar fragmentos que já tenha recebido e processada, por isso, é melhor mensagem lateral reenviar fragmentos que ao apresentar avaliação da linha cronológica de multimédia. 

##<a name="9-encoder-redundancy"></a>9. redundância codificador 

Crítico para determinados eventos ao vivo que pedido mesmo maior disponibilidade e qualidade da experiência, recomenda-se para empregam activo activo codificadores redundantes para alcançar totalmente integrada activação pós-falha sem perda de dados.

![image6][image6]

Tal como ilustrado no diagrama acima, existem dois grupo de codificadores conduza duas cópias de cada da cadeia em simultâneo no serviço direto. Esta configuração é suportada porque dos serviços de multimédia do Microsoft Azure tem a capacidade para filtrarem os duplicados fragmentos com base em sequência ID e fragmento de data/hora. O fluxo direto resultante e arquivo será um único de cópia de todas as sequências que é a melhor agregação possível de duas origens. Por exemplo, no caso de extremo hipotético, desde que existe um descodificador (não tem de ser a mesma) a ser executada em qualquer ponto determinado em vez para cada sequência, o fluxo direto resultante de serviço será contínuo sem perda de dados. 

O requisito para este cenário é praticamente igual os requisitos de codificador activação pós-falha maiúscula com a exceção de que o segundo conjunto de codificadores estiver a executar ao mesmo tempo, como os codificadores principais.

##<a name="10-service-redundancy"></a>10. redundância de serviço  

Para uma distribuição global altamente redundante,-, por vezes, é necessário ter região da publicação em cópia de segurança para processar catástrofes regionais. Os clientes expandir na topologia da "Codificador redundância", podem optar por ter uma implementação do serviço redundantes numa região diferente que está ligado com o conjunto 2º de codificadores. Os clientes podem também funcionam com um fornecedor CDN para implementar um GTM (Gestor de tráfego Global) à frente de implementações do duas serviço para encaminhar o tráfego de cliente de forma totalmente integrada. Os requisitos para os codificadores são iguais a maiúscula "Codificador redundância" com a única exceção de que o segundo conjunto de codificadores precisam de ser indicada uma diferentes live ingerir esta última ponto final. O diagrama abaixo mostra este programa de configuração:

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. tipos de formatos de ingestão especiais 

Esta secção descreve alguns tipo especial de formatos de ingestão direto que foram concebidos para processar alguns cenários específicos.

###<a name="sparse-track"></a>Registar disperso

Quando executar uma apresentação de transmissão live com experiência do cliente avançada, muitas vezes é necessário para transmitir hora sincronizada eventos ou sinais de quatro com os dados de multimédia principal. Um exemplo de isto é dinâmica inserção anúncios direto. Este tipo de evento sinalização é diferente do normal de áudio/vídeo transmissão devido à sua natureza dispersa. Por outras palavras, os dados sinalização normalmente não ocorrer continuamente e o intervalo pode ser difícil de prever. O conceito de registar dispersos foi concebido especificamente para ingerir esta última e difundir quatro sinalização dados.

Segue-se uma implementação recomendada para ingesting registar disperso:

1. Crie uma separada fragmentado MP4 sequência de bits que contém apenas via dispersas sem faixas de áudio/vídeo.
2. Na "Live caixa servidor de manifesto" tal como foi definido na secção 6 no [1], utilize o parâmetro de "parentTrackName" para especificar o nome da faixa principal. Consulte a secção 4.2.1.2.1.2 no [1] para obter mais detalhes.
3. Na "Live caixa servidor de manifesto", manifestOutput tem de estar definida como "true".
4. Dada a natureza dispersa do evento sinalização, é recomendável que:
    1. No início do evento direto, codificador envia as caixas de cabeçalho inicial para o serviço que pretende permitir que o serviço registar a faixa dispersa no manifesto do cliente.
    2. O codificador deve termina o pedido de HTTP POST quando não está a ser enviados dados.  Numa mensagem de HTTP longa que não enviar dados pode impedir que dos serviços de multimédia do Azure de desligar o codificador rapidamente trabalho um reinício de atualização ou servidor de serviço, como o servidor de multimédia serão temporariamente bloqueado numa operação de receção no socket. 
    3. Durante o tempo quando sinalização dados não estiverem disponíveis, feche o codificador SHOULD HTTP POST pedir.  Enquanto o pedido POST está ativo, o codificador deve enviar dados 
    4. Quando enviar dispersos fragmentos, codificador pode definir cabeçalho explícita de tipos de conteúdo de comprimento se estiver disponível.
    5. Quando enviar disperso fragmento de com uma nova ligação, codificador deverá começar a enviar a partir das caixas de cabeçalho seguidas por novos fragmentos. Este é para processar as maiúsculas/minúsculas onde activação pós-falha aconteceu entre e a nova ligação dispersa está a ser estabelecida para um novo servidor que não tem a vista a faixa dispersa antes.
    6. O fragmento de registar dispersos será disponibilizado para o cliente quando principal correspondente controlar fragmento de que tem igual ou maior valor de data/hora é disponibilizado para o cliente. Por exemplo, se o fragmento de disperso tem um carimbo de data/hora da t = 1000, é esperado depois do cliente vê o vídeo (assumindo que o nome da faixa principal está vídeo) fragmento carimbo de data/hora 1000 ou para além, pode transferir o fragmento de disperso t = 1000. Tenha em atenção que o sinal real muito bem pode ser utilizado para uma posição diferente da linha cronológica de apresentação, para a sua finalidade designada. No exemplo acima, é possível que o fragmento de disperso de t = 1000 tem uma carga de útil XML que é para inserir um anúncio numa posição que é de aguardar alguns segundos mais tarde.
    7. Pode ser a carga útil fragmento de registar dispersos em diferentes formatos diferentes (por exemplo, XML ou texto ou binário, etc.) dependendo cenários diferentes. 


###<a name="redundant-audio-track"></a>Faixa de áudio redundante

Um típica HTTP ajustável transmissão cenário (por exemplo, a transmissão suaves ou travessão), muitas vezes existe apenas uma faixa de áudio de toda a apresentação. Ao contrário das tracks vídeos que tenham vários níveis de qualidade para o cliente à escolha no condições de erro, a faixa de áudio pode ser um ponto de falha único se ingestão da sequência que contém a faixa de áudio é interrompida. 

Para resolver este problema, dos serviços de multimédia do Microsoft Azure suporta direto ingestão de redundantes faixas de áudio. A ideia é que a mesma faixa de áudio pode ser enviada várias vezes em fluxos de diferentes. Enquanto o serviço será apenas registar a faixa de áudio uma vez no manifesto do cliente, é possível utilizar redundantes faixas de áudio como cópias de segurança para obter o áudio fragmentos se a primária faixa de áudio está a ter problemas. Para poder ingerir esta última redundantes faixas de áudio, o codificador precisa de:

1. Crie a mesma faixa de áudio no bit-sequências de fragmento MP4 múltiplas. Faixas de áudio redundantes tem de ser semanticamente equivalente com exatamente as mesmas fragmento marcas de hora e permutáveis ao nível de cabeçalho e fragmento.
2. Certifique-se de que o "áudio" entrada o Live servidor manifesto (6 de secção no [1]) ser a mesma para todas as pistas de áudio redundantes.

Abaixo encontra-se uma implementação recomendada para redundantes faixas de áudio:

1. Envie cada faixa de áudio exclusiva numa sequência por si.  Também enviar uma sequência redundante para cada um destes sequências de pista de áudio, onde a sequência de 2. difere a 1. º apenas pelo identificador no URL de mensagem HTTP: {protocolo} :// {endereço do servidor} / {path}/Streams({identifier}) do ponto de publicação.
2. Utilize fluxos de separada para enviar as duas bitrates mais baixos de vídeos. Cada um dos fluxos destes também deve conter uma cópia de cada exclusiva faixa de áudio.  Por exemplo, quando vários idiomas são suportados, estes sequências devem conter faixas de áudio para cada idioma.
3. Utilize instâncias do servidor separado (codificador) para codificar e enviar as sequências redundantes mencionadas (1) e (2). 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 