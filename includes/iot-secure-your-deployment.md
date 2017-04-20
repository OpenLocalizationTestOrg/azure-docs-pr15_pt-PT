# <a name="securing-your-iot-deployment"></a>Proteger a sua implementação IoT

Este artigo fornece o nível seguinte de detalhe para proteger a infra-estrutura baseada em Azure IoT Internet de coisas (IoT). Liga a detalhes de nível de implementação para configurar e implementar cada componente. Também fornece as comparações e opções de entre vários métodos de concorrentes.

Proteger a implementação de Azure IoT pode ser dividida nas seguintes áreas de segurança de três:

- **Dispositivo de segurança**: proteger o dispositivo IoT enquanto está implementado no meio natural.
- **Segurança de ligação**: assegurar a todos os dados transmitidos entre o dispositivo de IoT e IoT concentrador é confidencial e inviolável.
- **Segurança de nuvem**: fornecendo um meio para proteger os dados enquanto move através de e é armazenado na nuvem.

![Três áreas de segurança][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Dispositivo seguro de aprovisionamento e autenticação

O conjunto de IoT Azure protege IoT dispositivos através dos seguintes dois métodos:

- Ao fornecer uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, que pode ser utilizado pelo dispositivo, para comunicar com o concentrador IoT.

- Utilizando um [certificado x. 509] de-dispositivo[ lnk-x509] e a chave privada como meio para autenticar o dispositivo ao concentrador IoT. Este método de autenticação assegura que a chave privada no dispositivo não é conhecida fora do dispositivo a qualquer momento, fornecendo um nível mais elevado de segurança.

O método de token de segurança fornece autenticação para cada chamada efectuada pelo dispositivo ao concentrador IoT, associando a chave simétrica para cada chamada. Autenticação baseada em x. 509 permite uma autenticação de um dispositivo de IoT na camada física como parte do estabelecimento de ligação TLS. O método de base de token de segurança pode ser utilizado sem a autenticação de x. 509 que é um padrão menos seguro. A escolha entre os dois métodos principalmente é ditada pelo grau de segurança necessita de autenticação do dispositivo ser e disponibilidade de armazenamento segura no dispositivo (para armazenar a chave privada de forma segura).

## <a name="iot-hub-security-tokens"></a>Tokens de segurança IoT concentrador

Concentrador IoT utiliza tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, os tokens de segurança estão limitadas no prazo de validade e âmbito de aplicação. Azure IoT concentrador SDKs gerar automaticamente tokens sem necessidade de qualquer configuração especial. No entanto, alguns cenários, requerem que o utilizador gerar e utilizar directamente os tokens de segurança. Estas incluem a utilização directa das superfícies MQTT, AMQP ou HTTP ou a execução do padrão de serviço de tokens.

Pode encontrar mais detalhes sobre a estrutura do token de segurança e a respectiva utilização nos seguintes artigos:

-   [Estrutura do token de segurança][lnk-security-tokens]
-   [Utilizar tokens de associações de segurança como um dispositivo][lnk-sas-tokens]

Cada concentrador IoT tem um [Registo da identidade de dispositivo] [ lnk-identity-registry] que podem ser utilizados para criar recursos por dispositivo no serviço, por exemplo, uma fila que contém mensagens cloud-a-dispositivo em voo e para permitir o acesso para os pontos finais virada para o dispositivo. O registo da identidade de concentrador IoT fornece um armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Individuais ou grupos de identidades do dispositivo podem ser adicionados a uma lista de permissões ou uma lista de bloqueio, activar o controlo completo sobre o acesso de dispositivo. Os seguintes artigos fornecem mais detalhes sobre a estrutura do registo da identidade do dispositivo e suportadas operações.

[Concentrador IoT suporta protocolos como o HTTP, AMQP e MQTT][lnk-protocols]. Cada um destes protocolos de forma diferente, utilize tokens de segurança do dispositivo IoT IoT concentrador:

- AMQP: Segurança de simples e baseada em afirmações de AMQP de SASL ({policyName}@sas.root.{iothubName} no caso de tokens de nível de concentrador; {deviceId} em caso de tokens no âmbito do dispositivo).

- MQTT: Ligar utiliza pacotes {deviceId} como {ClientId}, {IoThubhostname} / {deviceId} no campo **nome de utilizador** e um token de associações de segurança no campo **palavra-passe** .

- HTTP: Token válido está no cabeçalho do pedido de autorização.

Registo de identidade de dispositivo de concentrador IoT pode ser utilizado para configurar as credenciais de segurança por dispositivo e controlo de acesso. No entanto, se uma solução IoT já tiver um investimento significativo num [esquema de registo e/ou autenticação de identidade do dispositivo personalizado][lnk-custom-auth], pode ser integrada numa infra-estrutura existente com concentrador IoT através da criação de um serviço de tokens.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação de dispositivo baseado em certificado x. 509

A utilização de um [certificado baseado no dispositivo de x. 509] [ lnk-use-x509] e seu associado privada e pública par de chaves permite uma autenticação adicional na camada física. A chave privada é armazenada em segurança no dispositivo e não seja detectável fora do dispositivo. O certificado x. 509 contém informações sobre o dispositivo, como ID de dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada utilizando a chave privada.

Fluxo de aprovisionamento de alto nível de dispositivo:

- Associe um identificador para um dispositivo físico – identificação de dispositivo e/ou certificado x. 509 associado ao dispositivo durante o fabrico ou de entrada em serviço de dispositivo.

- Crie um movimento de identidade correspondente no concentrador IoT – dispositivos identidades e informações de dispositivo associado no registo de dispositivo do concentrador IoT.

- Armazene em segurança impressão digital do certificado x. 509 no registo de dispositivo do concentrador IoT.

### <a name="root-certificate-on-device"></a>Certificado de raiz no dispositivo

Ao estabelecer uma ligação TLS segura com concentrador IoT, o dispositivo IoT autentica IoT concentrador utilizando um certificado de raiz que faz parte do dispositivo SDK. Para o cliente C SDK o certificado está localizado na pasta "\\c\\certs" na raiz da repo. Apesar destes certificados de raiz são duradouras, que ainda podem expirar ou ser revogados. Se não existir nenhuma forma de actualizar o certificado no dispositivo, o dispositivo poderá não conseguir ligar posteriormente para o concentrador IoT (ou qualquer outro serviço de nuvem). Ter um meio para actualizar o certificado de raiz, uma vez que o dispositivo IoT é implementado efectivamente atenuará este risco.

## <a name="securing-the-connection"></a>Proteger a ligação

Ligação à Internet entre o dispositivo de IoT e IoT concentrador é protegida utilizando o padrão de Transport Layer Security (TLS). Azure IoT suporta [TLS 1.2][lnk-tls12], TLS 1.1 e o TLS 1.0, por esta ordem. Suporte para TLS 1.0 é fornecido apenas para retro-compatibilidade. É recomendável utilizar TLS 1.2, uma vez que fornece maior segurança.

Azure IoT Suite suporta conjuntos de cifras seguintes, por esta ordem.

| Conjunto de cifras | Comprimento |
|--------------|--------|
| TLS\_ECDHE\_RSA\_com\_AES\_256\_CBC\_SHA384 (0xc028) ELLIPTIC secp384r1 (EQ FS 7680 bits RSA) | 256    |
| TLS\_ECDHE\_RSA\_com\_AES\_128\_CBC\_SHA256 (0xc027) ELLIPTIC secp256r1 (EQ FS 3072 bits RSA) | 128    |
| TLS\_ECDHE\_RSA\_com\_AES\_256\_CBC\_SHA (0xc014) secp384r1 ELLIPTIC (EQ FS 7680 bits RSA)           | 256    |
| TLS\_ECDHE\_RSA\_com\_AES\_128\_CBC\_SHA (0xc013) secp256r1 ELLIPTIC (EQ FS 3072 bits RSA)           | 128    |
| TLS\_RSA\_com\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_com\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_com\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_com\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_com\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_com\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_com\_3DES\_EDE\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Proteger a nuvem

Concentrador de IoT Azure permite a definição das [políticas de controlo de acesso] [ lnk-protocols] para cada chave de segurança. Utiliza o seguinte conjunto de permissões para conceder acesso a cada um dos pontos finais de IoT concentrador. Permissões de limitam o acesso a um concentrador de IoT com base na funcionalidade.

- **RegistryRead**. Concede acesso de leitura para o registo de identidade do dispositivo. Para mais informações, consulte o [Registo da identidade de dispositivo][lnk-identity-registry].

- **RegistryReadWrite**. Subsídios de leitura e acesso de escrita no registo de identidade do dispositivo. Para mais informações, consulte o [Registo da identidade de dispositivo][lnk-identity-registry].

- **ServiceConnect**. Concede acesso a nuvem virada para o serviço de comunicação e pontos finais de monitorização. Por exemplo, se concede permissão para os serviços de nuvem de back-end para receber mensagens de nuvem de dispositivo, enviar mensagens de cloud-a-dispositivo e obter as confirmações de entrega correspondente.

- **DeviceConnect**. Concede acesso para pontos finais de comunicação virada para o dispositivo. Por exemplo, se concede permissão para enviar mensagens de nuvem de dispositivo e receber mensagens de cloud-a-dispositivo. Esta permissão é utilizada pelos dispositivos.

Existem duas formas de obter as permissões de **DeviceConnect** com concentrador IoT com [tokens de segurança][lnk-sas-tokens]: a utilizar uma chave de identidade do dispositivo ou uma chave de política de acesso partilhado. Além disso, é importante ter em atenção que todas as funcionalidades acessíveis a partir de dispositivos é exposta pela estrutura em pontos finais com o prefixo `/devices/{deviceId}`.

[Componentes de serviço só podem gerar tokens de segurança] [ lnk-service-tokens] utilizar partilhados políticas de acesso conceder as permissões adequadas.

Concentrador de IoT Azure e outros serviços que podem fazer parte da solução de permitem a gestão de utilizadores utilizando o Azure Active Directory.

Dados ingeridos pelos Azure IoT concentrador podem ser consumidos por uma variedade de serviços, tais como Azure de visitas e armazenamento de Azure blob. Estes serviços permitem o acesso de gestão. Leia mais sobre estes serviços e as opções disponíveis abaixo:

- [Azure DocumentDB][lnk-docdb]: um serviço de base de dados escalável e totalmente indexados para os dados estruturados que gere os metadados para os dispositivos Aprovisiona, por exemplo, atributos, configuração e propriedades de segurança. DocumentDB oferece o processamento de alto desempenho e de elevado débito, a indexação dos dados e uma interface de consulta SQL rich esquema desconhecido.

- [Análise de visitas Azure][lnk-asa]: a nuvem que permite-lhe rapidamente desenvolver e implementar uma solução de baixo custo analytics para descobrir informações em tempo real a partir dos dispositivos, sensores, infra-estrutura e as aplicações de processamento de fluxo em tempo real. Os dados deste serviço totalmente gerida podem ser escalado para qualquer volume enquanto ainda alcançar débito alto, baixa latência e resistência.

- [Serviços de aplicação Azure][lnk-appservices]: uma plataforma de nuvem para construir web poderosa e aplicações móveis que ligar a dados em qualquer ponto; na nuvem ou no local. Crie exercem aplicações móveis para iOS, Android e no Windows. Integre com o Software como um serviço (AAS) e aplicações empresariais com conectividade out of box a dúzias de baseada na nuvem serviços e aplicações empresariais. O código do idioma favorito e IDE (.NET, NodeJS, PHP, Python ou Java) para criar aplicações web e API mais rapidamente do que nunca.

- [Lógica de aplicações][lnk-logicapps]: A lógica aplicações de serviço de aplicação Azure ajuda a integrar a solução IoT para os sistemas de linha de negócio existentes e automatizar processos de fluxo de trabalho. Lógica de aplicações permite aos programadores fluxos de trabalho de concepção que iniciar a partir de um accionador e, em seguida, executar uma série de passos, regras e acções que utilizam conectores poderosas para integrar com os processos de negócio. Lógica de aplicações oferece conectividade out of box uma ecossistema vasta de AAS, baseada na nuvem e aplicações no local.

- [Armazenamento de Azure blob][lnk-blob]: armazenamento de nuvem fiável e económico para os dados que os dispositivos de enviar para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece detalhes de nível para conceber e implementar uma infra-estrutura de IoT utilizando Azure IoT de descrição geral da aplicação. Configurar cada componente para garantir a segurança é a chave em proteger a infra-estrutura de IoT global. As opções de apresentação disponíveis na Azure IoT fornecem algum nível de flexibilidade e escolha; No entanto, cada escolha poderá ter implicações de segurança. Recomenda-se que cada uma destas opções ser avaliados através de uma avaliação de risco/custo.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
