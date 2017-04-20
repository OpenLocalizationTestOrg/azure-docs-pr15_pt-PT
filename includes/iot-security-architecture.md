# <a name="internet-of-things-security-architecture"></a>Arquitectura de segurança de Internet de coisas

Quando conceber um sistema, é importante compreender as potenciais ameaças a esse sistema e adicionar defesas adequadas, por conseguinte, tal como o sistema concebido e concepção. É particularmente importante estruturar o produto desde o início com a segurança não se esqueça de porque compreender como um atacante poderá conseguir comprometer um sistema de ajuda a tornar a certificar-se adequado atenuações estão em vigor desde o início. 

## <a name="security-starts-with-a-threat-model"></a>Segurança começa com um modelo de ameaça
 
Microsoft longo utilizou modelos de ameaças para os seus produtos e efectuou modelagem a empresa de ameaças processo publicamente disponível. A experiência de empresa demonstra que modelização tem inesperados benefícios após a compreensão imediata das quais ameaças são mais relativas. Por exemplo, também cria um avenue para um debate aberto sobre com outras pessoas fora da equipa de desenvolvimento, que pode conduzir a novas ideias e melhoramentos no produto.
  
O objectivo de modelação de ameaça é compreender a forma como o intruso poderá conseguir comprometa um sistema e, em seguida, certifique-se adequado atenuações estão no local. Forças de modelação de ameaça a estrutura de equipa a considerar atenuações como destina-se o sistema em vez de após um sistema é implementado. Este facto é muito importante, porque as defesas de segurança para uma vasta gama de dispositivos no campo de reequipamento é inviável, sujeito a erros e irá deixar os clientes em risco.

Muitas equipas de desenvolvimento efectuar um trabalho excelente capturar os requisitos funcionais para o sistema que beneficiam clientes. No entanto, a identificação de formas não evidente que alguém poderá utilizá indevidamente o sistema é mais difícil. Modelação de ameaça pode ajudar a compreender o que um intruso pode fazer de equipas de desenvolvimento e porquê. Modelação de ameaça é um processo estruturado que cria um debate sobre a segurança decisões de concepção no sistema, bem como alterações à estrutura feitas ao longo da forma que a segurança impacto. Enquanto um modelo de ameaça é simplesmente um documento, esta documentação também representa uma forma ideal para assegurar a continuidade dos conhecimentos, retenção de lições aprendidas e ajuda nova equipa bordo rapidamente. Finalmente, o resultado de modelação de ameaça é para que possa considerar outros aspectos de segurança, tal como os compromissos de segurança que pretende fornecer aos clientes. Estes compromissos em conjunto com o threat modeling irão informar e unidade testes da solução de Internet de coisas (IoT).
 

### <a name="when-to-threat-model"></a>Quando o modelo de ameaças

[Threat modeling](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) proporciona o maior valor se for incorporada para a fase de concepção. Quando estiver a estruturar, terá uma maior flexibilidade para efectuar alterações a eliminar ameaças. Eliminar ameaças por predefinição é o resultado pretendido. É muito mais fácil do que adicionar atenuações, testá-las e assegurar permanecem actuais e além disso, essa eliminação não é sempre possível. Torna-se mais difícil de eliminar ameaças como um produto torna-se mais adultos e, por sua vez, finalmente, necessitará de mais trabalho e muito mais difícil variações de modelagem de ameaças antecipadamente no desenvolvimento.

### <a name="what-to-threat-model"></a>O modelo de ameaça

Deverá thread modelo a solução como um todo e concentrar-se igualmente nas seguintes áreas:

- As funcionalidades de segurança e privacidade
- As funcionalidades cujas falhas são pertinente de segurança
- As funcionalidades que envolvam um limite de fidedignidade 

### <a name="who-threat-models"></a>Que modelos de ameaças

Modelação de ameaça é um processo como qualquer outro.  É uma boa ideia para tratar o documento de modelo de ameaça como qualquer outro componente da solução e validá-lo. Muitas equipas de desenvolvimento efectuar um trabalho excelente capturar os requisitos funcionais para o sistema que beneficiam clientes. No entanto, a identificação de formas não evidente que alguém poderá utilizá indevidamente o sistema é mais difícil. Modelação de ameaça pode ajudar a compreender o que um intruso pode fazer de equipas de desenvolvimento e porquê.

### <a name="how-to-threat-model"></a>Como modelo de ameaças

Modelagem de processos é composta por quatro passos; os passos são:

- A aplicação do modelo
- Enumerar ameaças
- Atenuar ameaças
- Validar as atenuações

#### <a name="the-process-steps"></a>Os passos do processo

Regras três de folhear a ter em consideração quando criar um modelo de ameaça:

1. Crie um diagrama de arquitectura de referência. 
2. Inicie primeiro o boca. Obter uma descrição geral e compreender o sistema como um todo, antes de consultar profunda.  Isto ajuda a garantir que aprofundada nos locais correctos.
3. O processo de unidade, não deixe que o processo de unidade. Se encontrar um problema na fase de modelação e pretender explorá-la, vá para o mesmo!  Não pode ter de seguir estes passos slavishly.  

#### <a name="threats"></a>Ameaças

Os elementos de quatro principais de um modelo de ameaça são:

- Processos (serviços web, serviços Win32 * nix daemons, etc. Tenha em atenção que algumas entidades complexas (por exemplo, gateways de campo e sensores) podem ser captadas como um processo quando um técnico pormenorizar nestes domínios não é possível.
- Armazena dados (onde os dados são armazenados, tal como um ficheiro de configuração ou a base de dados)
- Fluxo de dados (em que dados são movidos entre outros elementos da aplicação)
- Entidades externas (tudo o que interage com o sistema, mas não está sob o controlo da aplicação, os exemplos incluem utilizadores e via satélite feeds)

Todos os elementos no diagrama da arquitectura estão sujeitos a diversas ameaças; Utilizaremos a mnemónica STRIDE. Ler [Ameaça modelação novamente, transpor](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) para obter mais informações sobre os elementos STRIDE.

Diferentes elementos do diagrama da aplicação estão sujeitas a determinadas ameaças STRIDE:

- Os processos são objecto de STRIDE
- Fluxos de dados estão sujeitos a TID
- Arquivos de dados estão sujeitos TID e por vezes, R, se os arquivos de dados são ficheiros de registo.
- Entidades externas estão sujeitas a SRD

## <a name="security-in-iot"></a>Segurança em IoT

Dispositivos de finalidade especial ligados têm um número significativo de potenciais áreas de superfície de interacção e padrões de interacção, os quais devem ser considerados para fornecer um quadro para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir de todas as operações são efectuadas através da interacção de dispositivo directa em que a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com um bloqueio na porta. Enquanto não se pode negar acesso físico a utilizar o software e hardware, possam ser tomadas medidas para impedir o acesso físico dê a interferências de sistema. 

Como são explicados os padrões de interacção, vamos abordar "device control" e "dados do dispositivo" com o mesmo nível de atenção. "Controlo de dispositivo" pode ser classificado como qualquer informação que é fornecida para um dispositivo por qualquer das partes com o objectivo de alterar ou que influenciam o respectivo comportamento no sentido do seu estado ou do seu ambiente. "Dados do dispositivo" podem ser classificados como quaisquer informações que um dispositivo emite qualquer outra parte sobre o estado e o estado observado do seu ambiente.
   
Para optimizar os procedimentos recomendados de segurança, recomenda-se que uma arquitectura IoT típica ser dividido em vários componentes/zonas como parte de modelagem de exercício. Estas zonas são totalmente descritas nesta secção e incluem:

-   Dispositivo,
-   Gateway de campo,
-   Nuvem gateways, e
-   Serviços.

As zonas são abrangente para segmento de uma solução; cada zona tem frequentemente as suas próprias exigências de dados e autenticação e autorização. Zonas também podem ser utilizado para danos de isolamento e restringir o impacto das zonas de fidedignidade baixo nas zonas de fidedignidade mais elevadas.

Cada zona é separada por um limite de confiança, que é de notar que a linha vermelha pontilhada no diagrama abaixo. Representa uma transição de dados/informações de uma origem para outro. Durante esta transição, os dados/informações pode ser objecto de Ocultação de conteúdos, Tampering, rejeição, divulgação de informações, Denial of Service e elevação de privilégios (passo).

![Zonas de segurança IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes mencionados no cada limite também sejam submetidos a STRIDE, permitindo uma 360 completo a Modelagem de vista da solução. As secções abaixo investigações sobre cada um dos componentes e preocupações de segurança específico e soluções que deverão ser colocadas no lugar.

As secções que se segue irão explicar a componentes padrão, encontrado normalmente nestas zonas.

### <a name="the-device-zone"></a>A zona de dispositivo

O ambiente de dispositivo é o espaço físico imediato à volta do dispositivo quando física acesso e/ou "rede local" peer-to-peer digital access para o dispositivo é viável. "Rede local" é assumida como sendo uma rede que é distinta e isolada de – mas potencialmente pontes estabelecidas para a Internet pública e inclui qualquer tecnologia de curta distância rádio sem fios que permite a comunicação ponto-a-ponto de dispositivos. Que *não* incluem qualquer tecnologia de Virtualização de rede criar a ilusão de uma rede local e também não incluem redes de operador público que necessitem de quaisquer dois dispositivos comuniquem através de espaço de rede pública se fossem introduzir uma relação de comunicação to-peer.

### <a name="the-field-gateway-zone"></a>A zona de Gateway de campo

Campo gateway é um dispositivo/aparelho ou de algum software de computador do servidor de uso geral que funciona como activador de comunicação e, potencialmente, um sistema de controlo de dispositivo e o concentrador de processamento de dados do dispositivo. A zona de gateway de campo inclui o gateway de campo propriamente dito e todos os dispositivos que estão ligados. Como o nome implica, o campo gateways agir instalações de tratamento de dados dedicada fora, são normalmente localização vinculada, são potencialmente sujeitas a intrusão física e redundância operacional serão limitada. Todos para dizer que um gateway de campo é normalmente uma coisa um toque e sabotage apesar de saber o que é a sua função. 

Um gateway de campo é diferente de um router de tráfego simples que tenha tido um papel activo na gestão de acesso e fluxo de informações, que significa que é uma aplicação os destinatários da ligação de rede e a entidade ou sessão de terminal. Um dispositivo NAT ou firewall, por outro lado, não se qualificam como gateways de campo uma vez que não são ligação explícita ou terminais de sessão, mas sim uma ligação de rota (ou bloco) ou sessões efectuadas através dos mesmos. O gateway de campo tem duas áreas distintas de superfície. Um virada para os dispositivos que estão ligados e representa o interior da zona e o outro virada para todas as partes externas e é a extremidade da zona.   

### <a name="the-cloud-gateway-zone"></a>A zona de gateway de nuvem

Nuvem gateway é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes através de espaço de rede pública, normalmente, no sentido de um controlo baseado em nuvem e sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem poderá imediatamente facilitar o acesso aos dispositivos de finalidade especial dos terminais por exemplo, mesas de dados ou de telefones. No contexto referido neste artigo, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicada que não está vinculado ao mesmo site que os dispositivos ligados ou gateways de campo. Também numa zona nuvem, medidas operacionais impedir o acesso físico alvo e não é necessariamente exposto a uma infra-estrutura de "nuvem pública".  

Um gateway de nuvem pode potencialmente ser mapeado para uma sobreposição de Virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem propriamente dito é nem um sistema de controlo de dispositivo nem uma transformação ou instalações de armazenamento de dados do dispositivo; as instalações de interface com o gateway de nuvem. A zona de gateway de nuvem inclui o gateway de nuvem juntamente com todos os gateways de campo e dispositivos directa ou indirectamente ligados ao mesmo. O limite da zona é uma área de superfície distinta em que todas as partes externas comuniquem através da.

### <a name="the-services-zone"></a>A zona de serviços

"Serviço" está definido para este contexto de qualquer componente de software ou módulo que está a interagir dispositivos através de um campo ou nuvem de gateway de recolha e análise de dados, bem como de comando e de controlo.  Os serviços estão mediadores. Actuam sob a sua identidade no sentido de gateways e outros subsistemas, armazenam e analisar dados, autónoma emitir comandos para dispositivos com base nos conhecimentos aprofundados de dados ou agendas e expõem informações e ao controlam capacidades para utilizadores finais autorizados.

### <a name="information-devices-vs-special-purpose-devices"></a>Dispositivos de informação vs. dispositivos de finalidade especial

PCs, telefones e tablets são principalmente os dispositivos de informações interactivo. Telefones e digitalizadores explicitamente são optimizados em torno de maximizar a duração da bateria. Se, de preferência, desactivem parcialmente quando não seja imediatamente interagir com uma pessoa ou quando não forneçam serviços como reproduzir música ou orientar o seu proprietário para uma localização específica. Numa perspectiva de sistemas, estes dispositivos de tecnologia de informação estão essencialmente a actuar como proxies no sentido de pessoas. São "accionadores de pessoas" que sugere acções e "sensores de pessoas" recolher entrada. 

Dispositivos de finalidade especial, provenientes de sensores de temperatura simples a linhas de produção da fábrica complexos com milhares de componentes no interior, são diferentes. Estes dispositivos são muito mais no âmbito do objectivo e mesmo que fornecem alguns interface de utilizador, são em grande medida no âmbito para uma interface com o ou integradas activos no mundo físico. Estes medem e comunicam as circunstâncias ambientais, activar válvulas, controlam servos, alarmes de som, mudar luzes e executar muitas outras tarefas. Estes ajudam a trabalhar para o qual um dispositivo de informações é demasiado genérico, demasiado caro, demasiado grande ou demasiado frágil. O objectivo de betão dita imediatamente as técnicas de concepção como também o orçamento disponível monetário para a sua produção e a operação de duração agendada. A combinação deste dois factores chave restringe o computador disponível de orçamento de energia, requisitos de espaço físico e, por conseguinte, armazenamento disponível, de funcionamento e capacidades de segurança.  

Se algo de "produto em questão incorrecto" com dispositivos controláveis automatizados ou remotos, por exemplo, defeitos físicos ou lógica de controlo defeitos willful intrusões não autorizadas e manipulação. Os lotes de produção podem ser destruídos, edifícios podem ser looted ou gravados para baixo e as pessoas poderão estar die ferido ou até mesmo. Isto é, claro, uma classe completamente diferente dos danos que alguém maxing limite de um cartão crédito roubado. A barra de segurança para dispositivos que tornar o mover bem como para os dados de sensor que eventualmente resulta em comandos que fazem com que itens mover, tem de ser superior no cenário de operações bancárias ou de comércio electrónico. 


### <a name="device-control-and-device-data-interactions"></a>Controlo de dispositivo e interacções de dados do dispositivo

Dispositivos de finalidade especial ligados têm um número significativo de potenciais áreas de superfície de interacção e padrões de interacção, os quais devem ser considerados para fornecer um quadro para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir de todas as operações são efectuadas através da interacção de dispositivo directa em que a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com um bloqueio na porta. Enquanto não se pode negar acesso físico a utilizar o software e hardware, possam ser tomadas medidas para impedir o acesso físico dê a interferências de sistema. 
 
Como são explicados os padrões de interacção, vamos abordar "device control" e "dados do dispositivo" com o mesmo nível de atenção durante a modelação de ameaça. "Controlo de dispositivo" pode ser classificado como qualquer informação que é fornecida para um dispositivo por qualquer das partes com o objectivo de alterar ou que influenciam o respectivo comportamento no sentido do seu estado ou do seu ambiente. "Dados do dispositivo" podem ser classificados como quaisquer informações que um dispositivo emite qualquer outra parte sobre o estado e o estado observado do seu ambiente. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>A arquitectura de referência Azure IoT modelagem de ameaças

A Microsoft utiliza o quadro acima a ameaça de modelização para Azure IoT. Na secção abaixo utilizamos, por conseguinte, o exemplo concreto da arquitectura de referência de IoT Azure para demonstrar como de pensar sobre a ameaça de modelização para IoT e como resolver as ameaças identificadas. No nosso caso identificámos quatro áreas principais do foco:

-   Dispositivos e origens de dados,
-   Transporte de dados,
-   Dispositivo e processamento de eventos, e
-   Apresentação

![Modelagem de ameaças para Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png) 

O diagrama abaixo fornece uma vista simplificada de arquitectura de IoT da Microsoft utilizando um modelo de diagrama de fluxo de dados que é utilizado pela ferramenta de modelação do Microsoft ameaça:

![Modelagem de ameaças para IoT Azure utilizando a ferramenta de modelação de ameaça de MS](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante notar que a arquitectura separa as capacidades do dispositivo e gateway. Isto permite ao utilizador tirar partido de dispositivos de gateway que são mais seguros: sejam capazes de comunicar com o gateway de nuvem utilizando protocolos seguros, que normalmente exige maior carga de processamento que um dispositivo nativo - por exemplo, um termóstato - poderia fornecer sozinho. Na zona de serviços Azure, vamos assumir que o Gateway de nuvem é representado pelo serviço Azure IoT concentrador.

### <a name="device-and-data-sourcesdata-transport"></a>Transporte de fontes/dados de dispositivos e dados

Esta secção explora a arquitectura acima através de vidro de modelação de ameaça e fornece uma descrição geral de como podemos são endereçamento algumas das preocupações inerentes. Iremos destacar os elementos essenciais de um modelo de ameaça:

- Processos (os sob o controlo e itens externos)
- Comunicação (também chamada de fluxos de dados)
- Armazenamento (também chamado arquivos de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitectura Azure IoT, tentaremos atenuar um número de ameaças diferentes entre os diferentes estádios dados/informação existe em: processo de comunicação e armazenamento. Em baixo apresentamos uma descrição geral das mais comuns para a categoria "processo", seguido de uma descrição geral do modo como estes podem ser melhor atenuadas: 

**Fraude de identidade (S)**: um intruso pode extrair o material de chave criptográfico de um dispositivo, quer ao nível do software ou hardware, e posteriormente o sistema com outro dispositivo físico ou virtual sob a identidade do dispositivo de material da chave de acesso foram retirado. Uma boa ilustração é controlos remotos que pode activar qualquer Televisor e que são ferramentas de prankster mais populares.

**Negação de serviço (D)**: um dispositivo pode ser composto incapaz de funcionar ou comunicar ao interferir com frequências de rádio ou de fios de corte. Por exemplo, uma câmara de vigilância que tinha a sua potência ou ligação de rede intencionalmente knocked out não apresentarão, de todo.

**Adulteração (T)**: um intruso pode parcial ou totalmente substituir o software em execução no dispositivo, poderia potencialmente permitir a aproveitar a identidade genuína do dispositivo, se o material de chave ou nas instalações de criptografia exploração materiais chaves estavam disponíveis para o programa ilegal do software substituído. Por exemplo, um intruso poderá tirar partido do material da chave extraído para interceptar e suprimir dados do dispositivo no caminho de comunicação e substituí-lo por dados falsos que tiver sido autenticados com o material de chave roubado.

**Divulgação de informações (I)**: se o dispositivo está a executar software manipulada, esse software manipulada potencialmente permitir fugas dados a terceiros não autorizados. Por exemplo, um intruso poderá tirar partido do material da chave extraído para injectar propriamente dito no caminho de comunicação entre o dispositivo e um controlador ou campo gateway ou gateway de nuvem para Sifão correspondente desactivar informações.

**Elevação de privilégios (E)**: um dispositivo que efectua a função específica pode ser forçado a efectuar uma operação. Por exemplo, uma válvula que está programada para abrir a meio curso pode ser enganado fraudulentos para o abrir.

| **Componente** | **Ameaça** | **Atenuação**                                                                                                                                                | **Risco**                                                                                                                                                                                                    | **Aplicação**                                                                                                                                                                                                                                                                                                                                     |
|---------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dispositivo        | S          | Atribuição de identidade para o dispositivo e o dispositivo de autenticação                                                                                                | Substituir o dispositivo ou parte do dispositivo com outro dispositivo. Como é que sei que estiver a falar para o dispositivo correcto?                                                                                           | Autenticar o dispositivo, utilizando o Transport Layer Security (TLS) ou IPSec. Infra-estrutura deverá suportam a utilização de chave pré-partilhada (PSK) nesses dispositivos que não consegue processar a criptografia assimétrica completo. Efeito de alavanca Azure AD, [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt)                             |
|               | TRID       | Aplica mecanismos de tamperproof para o dispositivo por exemplo, tornando muito difícil para impossíveis de extrair chaves e outro material criptográfico do dispositivo. | O risco é se alguém está a adulteração do dispositivo (interferências física). Como estamos tem a certeza, esse dispositivo não adulterado.                                                                                 | Medidas de atenuação mais eficaz é uma capacidade de module (TPM) de plataforma fidedigna que permite armazenar as chaves em especial no chip circuitos de que as chaves não não possível ler, mas só podem ser utilizadas para operações criptográficas que utilizam a chave, mas nunca divulgar a chave. Encriptação de memória do dispositivo. Gestão de chaves para o dispositivo. O código de início de sessão. |
|               | E          | Com o controlo de acesso do dispositivo. Esquema de autorização.                                                                                                    | Se o dispositivo permite acções individuais a efectuar com base nos comandos a partir de uma origem externa, ou mesmo comprometidos sensores, permitirá o ataque efectuar operações não acessível. | Com o esquema de autorização para o dispositivo                                                                                                                                                                                                                                                                                                             |
| Gateway de campo | S          | Autenticar o gateway de campo para o Gateway de nuvem (cert com base, PSK, afirmação com base,...)                                                                           | Se alguém pode falsificar Gateway de campo, em seguida, ele pode ser apresentada como qualquer dispositivo.                                                                                                                               | TLS RSA/PSK, IPSe, [4279 de RFC](https://tools.ietf.org/html/rfc4279). Os mesmos preocupações chave de armazenagem e de certificação de dispositivos em geral – melhor caso é utilizar o TPM. Extensão de 6LowPAN para IPSec suportar redes de Sensor de sem fios (WSN).                                                                                                              |
|               | TRID       | Proteger o Gateway de campo contra adulteração (TPM)?                                                                                                            | Fraude de ataques que levar o pensar de gateway de nuvem comunicam ao gateway do campo poderá resultar na divulgação de informações e manipulação de dados                                                             | Do memória encriptação, TPM, a autenticação.                                                                                                                                                                                                                                                                                                              |
|               | E          | Mecanismo de controlo de acesso para o campo Gateway                                                                                                                    |                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                        |

Eis alguns exemplos de ameaças nesta categoria:

Fraude de identidade: Um intruso pode extrair material de chave criptográfico de um dispositivo, quer no software ou hardware nível e subsequentemente acesso que tenha sido tomado no sistema com outro dispositivo físico ou virtual sob a identidade do dispositivo de material da chave de.

**Negação de serviço**: um dispositivo pode ser composto incapaz de funcionar ou comunicar ao interferir com frequências de rádio ou de fios de corte. Por exemplo, uma câmara de vigilância que tinha a sua potência ou ligação de rede intencionalmente knocked out não apresentarão, de todo.

**Tampering**: um intruso pode parcial ou totalmente substituir o software em execução no dispositivo, poderia potencialmente permitir a aproveitar a identidade genuína do dispositivo, se o material de chave ou nas instalações de criptografia exploração materiais chaves estavam disponíveis para o programa ilegal do software substituído.

**Tampering**: uma câmara de vigilância que esteja a mostrar uma imagem no espectro visível de um corredor vazia foi destinada a fotografia de tal um corredor. Um sensor de fumo ou de incêndio poderia devolver alguém mantendo um mais claro sob a mesma. Em qualquer caso, o dispositivo pode ser tecnicamente totalmente fidedigno para o sistema, mas irá comunicar informações manipuladas.

**Tampering**: um intruso poderá tirar partido do material da chave extraído para interceptar e suprimir dados do dispositivo no caminho de comunicação e substituí-lo por dados falsos que tiver sido autenticados com o material de chave roubado.

**Tampering**: um intruso pode parcial ou totalmente substituir o software em execução no dispositivo, poderia potencialmente permitir a aproveitar a identidade genuína do dispositivo, se o material de chave ou nas instalações de criptografia exploração materiais chaves estavam disponíveis para o programa ilegal do software substituído.
   
**Divulgação de informações**: se o dispositivo está a executar software manipulada, esse software manipulada potencialmente permitir fugas dados a terceiros não autorizados.

**Divulgação de informações**: um intruso poderá tirar partido do material da chave extraído para injectar propriamente dito no caminho de comunicação entre o gateway de dispositivo e um controlador ou um campo ou gateway de nuvem para Sifão correspondente desactivar informações.

**Negação de serviço**: O dispositivo pode ser desactivado ou activado num modo em que a comunicação não é possível (que é intencional em muitas máquinas industriais).

**Tampering**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controlo (fora de parâmetros conhecidos de calibração) e, assim, fornecer dados que podem ser interpretados

**Elevação de privilégios**: um dispositivo que efectua a função específica pode ser forçado a efectuar uma operação. Por exemplo, uma válvula que está programada para abrir a meio curso pode ser enganado fraudulentos para o abrir.

**Negação de serviço**: O dispositivo pode ser transformado num Estado em que a comunicação não é possível.

**Tampering**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controlo (fora de parâmetros conhecidos de calibração) e, assim, fornecer dados que podem ser interpretados.
 
**Tampering/Ocultação de conteúdos/rejeição**: Se não protegido (que raramente é o caso dos controlos remotos do consumidor) um intruso pode manipular o estado de um dispositivo de forma anónima. Uma boa ilustração é controlos remotos que pode activar qualquer Televisor e que são ferramentas de prankster mais populares.

#### <a name="communication"></a>Comunicação

Ameaças à volta do caminho de comunicação entre dispositivos, dispositivos e gateways de campo e o dispositivo e nuvem gateway. A tabela abaixo tem algumas orientações à volta de sockets abertos no dispositivo/VPN:

| **Componente**               | **Ameaça** | **Atenuação**                                      | **Risco**                                                                                                      | **Aplicação**                                                                                                                                                                                                                                                                                                                                                               |
|-----------------------------|------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dispositivo IoT concentrador              | TID        | (D) TLS (PSK/RSA) para encriptar o tráfego             | Escuta ou interferir a comunicação entre o dispositivo e o gateway                             | Segurança ao nível do protocolo. Com protocolos personalizados, é necessário perceber como protegê-los. Na maioria dos casos, a comunicação é efectuada do dispositivo ao concentrador IoT (dispositivo inicia a ligação).                                                                                                                                                                 |
| Dispositivo               | TID        | (D) TLS (PSK/RSA) para encriptar o tráfego.            | Leitura de dados em trânsito entre dispositivos. Adulteração dos dados. Sobrecarga no dispositivo com novas ligações | Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, é necessário perceber como protegê-los. É a medida atenuante para a ameaça DoS dispositivos através de um gateway de campo ou nuvem de peer e mantê-las apenas acto como clientes no sentido da rede. O efectuado pode resultar numa ligação directa entre os peers após ter sido controlada pelo gateway |
| Dispositivo de entidade externa      | TID        | Emparelhamento seguras da entidade externa para o dispositivo | Escuta a ligação ao dispositivo. A comunicação a interferir com o dispositivo                     | A entidade externa para o dispositivo Bluetooth/SNF LE a emparelhar com segurança. Controlar o painel operacional do dispositivo (física)                                                                                                                                                                                                                                                  |
| Gateway de nuvem de Gateway de campo | TID        | TLS (PSK/RSA) para encriptar o tráfego.               | Escuta ou interferir a comunicação entre o dispositivo e o gateway                             | Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, é necessário perceber como protegê-los.                                                                                                                                                                                                                                                       |
| Gateway de nuvem de dispositivo        | TID        | TLS (PSK/RSA) para encriptar o tráfego.               | Escuta ou interferir a comunicação entre o dispositivo e o gateway                             | Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, é necessário perceber como protegê-los.                                                                                                                                                                                                                                                       |

Eis alguns exemplos de ameaças nesta categoria:

**Negação de serviço**: dispositivos restritos são geralmente ameaçados DoS quando eles escutam activamente para ligações de entrada ou datagramas não solicitadas numa rede, porque um intruso pode abrir muitas ligações em paralelo e não de serviço-los ou serviço de forma muito lenta, ou o dispositivo pode ser alagado com tráfego não solicitado. Em ambos os casos, o dispositivo pode efectivamente ser composto deixe de funcionar na rede.

**Ocultação de conteúdos, divulgação de informações**: dispositivos de restritos e usos especiais têm, muitas vezes, instalações de segurança de um-para-all como protecção de PIN ou palavra-passe ou inteiramente recorrerão confiantes na rede, que significa que irão conceder acesso a informações quando um dispositivo está na mesma rede, e essa rede frequentemente só está protegido por uma chave partilhada. Isto significa que, quando o segredo partilhado para o dispositivo ou rede é divulgado, é possível controlar o dispositivo ou observar dados emitidos a partir do dispositivo.  

**Ocultação de conteúdos**: um atacante pode interceptar ou parcialmente substituir a difusão e ocultasse conteúdos nocivos na origem (intromissão)

**Tampering**: um atacante pode interceptar ou parcialmente substituir a difusão e enviar informações falsas 

**Divulgação de informações:** um atacante pode interceptar uma difusão e obter informações sem autorização **Denial of Service:** um intruso poderá encravamento o sinal de difusão e negar a distribuição de informações

#### <a name="storage"></a>Armazenamento

Cada dispositivo e o campo gateway tem alguma forma de armazenamento (temporário para colocação em fila de dados, armazenamento de imagens de sistema operativo).

| **Componente**                            | **Ameaça** | **Atenuação**                       | **Risco**                                                                                                                                                                                                                                                                                                                | **Aplicação**                                                                                                                                                     |
|------------------------------------------|------------|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Armazenamento do dispositivo                           | TRID       | Encriptação de armazenamento, os registos de início de sessão | A ler dados a partir do armazenamento (dados PII), manipulação com dados de telemetria. Adulteração em fila de espera ou em cache dados de controlo de comando. Adulteração com pacotes de actualização de configuração ou firmware enquanto em cache ou colocados em fila localmente pode conduzir a componentes de sistema operativo e/ou sistema ser comprometidas                                         | Encriptação, o código de autenticação de mensagem (MAC) ou a assinatura digital. Sempre que o controlo de acesso possíveis e segura através de acesso a recursos controlar listas (ACLs) ou as permissões. |
| Imagem do SO do dispositivo                          | TRID       |                                      | Adulteração SO / substituição de componentes do sistema operativo                                                                                                                                                                                                                                                                         | Partição de sistema operativo só de leitura, assinado a imagem do sistema operativo, encriptação                                                                                                                    |
| Armazenamento de Gateway de campo (os dados de colocação em fila) | TRID       | Encriptação de armazenamento, os registos de início de sessão | Ler dados a partir do armazenamento (dados PII), violação com dados de telemetria, adulteração em fila de espera ou em cache dados de controlo de comando. Adulteração com pacotes de actualização de configuração ou o firmware (destinadas a dispositivos ou gateway de campo) enquanto em cache ou colocados em fila localmente pode conduzir a componentes de sistema operativo e/ou sistema ser comprometidas | BitLocker                                                                                                                                                              |
| Imagem do sistema operativo de Gateway de campo                   | TRID       |                                      | Adulteração SO / substituição de componentes do sistema operativo                                                                                                                                                                                                                                                                          | Partição de sistema operativo só de leitura, assinado a imagem do sistema operativo, encriptação                                                                                                                    |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zona de gateway de processamento/nuvem dispositivo e eventos

Um gateway de nuvem é sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes através de espaço de rede pública, normalmente, no sentido de um controlo baseado em nuvem e sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem poderá imediatamente facilitar o acesso aos dispositivos de finalidade especial dos terminais por exemplo, mesas de dados ou de telefones. No contexto referido neste artigo, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicada que não está vinculado ao mesmo site que os dispositivos ligados ou gateways de campo e onde evitar medidas operacionais direccionado acesso físico, mas não é necessariamente uma infra-estrutura de "nuvem pública".  Um gateway de nuvem pode potencialmente ser mapeado para uma sobreposição de Virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem propriamente dito é nem um sistema de controlo de dispositivo nem uma transformação ou instalações de armazenamento de dados do dispositivo; as instalações de interface com o gateway de nuvem. A zona de gateway de nuvem inclui o gateway de nuvem juntamente com todos os gateways de campo e dispositivos directa ou indirectamente ligados ao mesmo.

Gateway de nuvem é principalmente personalizado parte incorporada do software a ser executado como um serviço com pontos finais expostos à qual ligam dispositivos de gateway de campo e. Como tal devem ser concebido com segurança em mente. Siga o processo [SDL](http://www.microsoft.com/sdl) para estruturar e construir este serviço. 

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controlo (ou o controlador) é uma solução de software que interage com um dispositivo, ou um gateway de campo ou gateway de nuvem a fim de controlar um ou vários dispositivos de e/ou para recolher, armazenar ou analisar dados do dispositivo para a apresentação ou efeitos de controlo subsequente. Sistemas de controlo são as entidades apenas no âmbito deste debate que imediatamente pode facilitar a interacção com pessoas. A excepção são intermédios superfícies de controlo físicos em dispositivos, tal como um parâmetro que permite que um utilizador desactivar o dispositivo ou alterar outras propriedades e para que não existe equivalência funcional que possa ser acedida digitalmente. 

Superfícies de controlo físicos intermédios são aqueles em que qualquer tipo de matéria lógica restringe a função da superfície de controlo física, tal que uma função equivalente pode ser iniciada remotamente ou podem ser evitados conflitos de entrada com entrada remota – essas superfícies de controlo intermediated são conceptualmente ligado a um sistema de controlo local que tira partido a mesma funcionalidade subjacente que qualquer outro sistema de controlo remoto que o dispositivo pode ser ligado em paralelo. Principais ameaças para a nuvem que computing pode ser lido na página de [Nuvem Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) .


## <a name="additional-resources"></a>Recursos adicionais

Consulte os seguintes artigos para obter informações adicionais:

- [Ferramenta de modelação de ameaça SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
- [Arquitectura de referência do Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
 
