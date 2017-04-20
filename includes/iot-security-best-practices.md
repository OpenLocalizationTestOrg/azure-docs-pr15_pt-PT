# <a name="internet-of-things-security-best-practices"></a>Procedimentos recomendados de Internet de coisas segurança

Para proteger uma Internet de coisas (IoT) infra-estrutura requer uma estratégia de segurança profunda rigoroso. Esta estratégia necessita de proteger os dados na nuvem, proteger a integridade de dados em trânsito através da internet pública e aprovisionar uma forma segura os dispositivos. Cada camada cria maiores garantias de segurança na infra-estrutura global.

## <a name="secure-an-iot-infrastructure"></a>Proteger uma infra-estrutura de IoT

Esta estratégia de segurança profunda pode ser desenvolvida e executada com a participação activa dos diversos intervenientes com o fabrico, desenvolvimento e implementação de infra-estrutura e IoT dispositivos. Segue-se uma descrição detalhada destes leitores.  

- **Fabricante de hardware IoT/integrador**: normalmente, estes são os fabricantes de hardware IoT a ser implementada, integradores de montagem de hardware de vários fabricantes ou fornecedores que fornecem hardware para uma implementação IoT fabricados ou integrado por outros fornecedores.
- **Programador da solução IoT**: O desenvolvimento de uma solução IoT é normalmente efectuado por um programador da solução. Este programador poderá parte de uma equipa interna ou de um integrador de sistema (SI) especializados nesta actividade. O programador da solução IoT pode desenvolver os vários componentes da solução IoT de raiz, integrar vários componentes disponíveis ou código-fonte aberto ou adoptar soluções pré-configuradas com adaptações menores.
- **Objecto de solução de IoT**: após IoT uma solução é desenvolvida, necessita de ser implementado no campo. Isto envolve a implementação de hardware, interligação de dispositivos e implementação de soluções em dispositivos de hardware ou nuvem.
- **Operador de solução de IoT**: após IoT solução é implementada, requer operações a longo prazo, acompanhamento, actualizações e manutenção. Isto pode ser feito por uma equipa de laboratório é composto por especialistas de tecnologias de informação, operações de hardware e equipas de manutenção e especialistas de domínio que controlam o comportamento correcto da infra-estrutura de IoT global.

As secções que se seguem fornecem procedimentos recomendados para cada um destes jogadores para ajudar a desenvolver, implementar e utilizar uma infra-estrutura de IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante de hardware IoT/integrador

Seguem-se os procedimentos recomendados para os fabricantes de hardware IoT e integradores de hardware.

- **Hardware de âmbito aos requisitos mínimos**: A concepção de hardware deve incluir as funcionalidades mínimas necessárias para o funcionamento do hardware e nada mais. Um exemplo é incluir as portas USB apenas se for necessário para o funcionamento do dispositivo. Estas funcionalidades adicionais abrir o dispositivo dos vectores de ataque indesejados que deve ser evitada.
- **Certifique hardware alterasse a prova**: construir nos mecanismos para detectar a adulteração físicos, tais como abrir da capa do dispositivo ou remover uma parte do dispositivo. Estes alterasse sinais podem fazer parte de fluxo de dados enviado para a nuvem, que pode alertar os operadores destes eventos.
- **Criar em torno de hardware seguro**: CMV se permite, criar funcionalidades de segurança como armazenamento seguro e encriptado ou funcionalidade com base no Trusted Platform Module (TPM) de arranque. Estas funcionalidades tornam a dispositivos mais seguro e ajudam a proteger a infra-estrutura de IoT global.
- **Efectuar actualizações seguras**: actualizações de Firmware durante a vida útil do dispositivo são inevitáveis. Criação de dispositivos com caminhos seguros para actualizações e de garantia criptográfica de versões de firmware permitirá que o dispositivo garantir a segurança durante e após actualizações.

## <a name="iot-solution-developer"></a>Programador da solução IoT

Seguem-se os procedimentos recomendados para programadores de soluções IoT:

- **Metodologia de desenvolvimento de software seguro siga**: desenvolvimento de software de segurança requer o solo de pensar sobre a segurança, desde o início do projecto até à sua implementação, teste e implementação. As opções de plataformas, idiomas e as ferramentas são influenciadas com este método. O ciclo de vida de desenvolvimento de segurança Microsoft fornece uma abordagem passo a passo para criação de software seguro.
- **Software de código-fonte aberto de escolha com cuidado**: software de código-fonte aberto fornece uma oportunidade para desenvolver soluções rapidamente. Quando estiver a escolher software de código-fonte aberto, considere o nível de actividade da Comunidade para cada componente de código-fonte aberto. Uma Comunidade active garante que o software é suportado e que problemas são detectados e os destinatários. Em alternativa, um software de código-fonte aberto obscura e inactivo não pode ser suportado e provavelmente não serão detectados problemas.
- **Integrar com cuidado**: várias falhas de segurança de software existem no limite de APIs e bibliotecas. Funcionalidade que não possam ser necessária para a implementação actual poderá estar ainda disponível através de uma camada da API. Para garantir a segurança global, certifique-se verificar todas as interfaces dos componentes que está a ser integrados para falhas de segurança.      

## <a name="iot-solution-deployer"></a>Objecto de solução de IoT

Seguem-se procedimentos recomendados para programadores de solução de IoT:

- **Implementar o hardware com segurança**: IoT implementações poderão requerer hardware a ser implementado em localizações não seguras, tal como espaços ou regiões deficiente. Nestas situações, certifique-se de que a implementação de hardware é inviolável até ao limite máximo. Se estiverem disponíveis no hardware USB ou outras portas, certifique-se de que estão cobertos em segurança. Muitos vectores de ataque podem utilizá-los como pontos de entrada.
- **Manter as chaves de autenticação seguro**: durante a implementação, cada dispositivo necessita de ID de dispositivo e associados a chaves de autenticação geradas pelo serviço de nuvem. Manter estas chaves fisicamente seguro mesmo depois da implementação. Qualquer chave comprometida pode ser utilizado por um dispositivo malicioso para fingem um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Seguem-se os procedimentos recomendados para os operadores de solução IoT:

- **Manter o sistema actualizado**: Certifique-se de que os sistemas operativos de dispositivo e todos os controladores de dispositivo são actualizados para as versões mais recentes. Se activar as actualizações automáticas no Windows 10 (IoT ou outros SKUs), Microsoft mantém-lo actualizado, prevê um sistema operativo IoT dispositivos. Manter outros sistemas operativos (tais como Linux) actualizada ajuda a garante que também estão protegidos contra ataques maliciosos.
- **Proteger contra actividades maliciosas**: se o sistema operativo permite, instale as capacidades mais recentes do antivírus e de protecção contra Malware em cada sistema operativo do dispositivo. Isto pode ajudar a atenuar ameaças mais externas. Pode proteger sistemas operativos mais modernos contra ameaças, tomar as medidas adequadas.
- **Auditoria com frequência**: auditoria IoT infra-estrutura para problemas relacionados com segurança é a chave quando responder a incidentes de segurança. A maior parte dos sistemas operativos fornecem o registo de eventos incorporada que deve ser revisto com frequência para se certificar de que não ocorreu nenhuma falha de segurança. Informações de auditoria podem ser enviadas como uma sequência de telemetria separado para o serviço de nuvem onde podem ser analisado.
- **Proteger fisicamente a infra-estrutura de IoT**: os piores ataques de segurança contra IoT infra-estrutura iniciados utilizando acesso físico aos dispositivos. É uma prática de segurança importante proteger contra a utilização maliciosa de portas USB e a outro acesso físico. Uma chave para revelação das falhas que poderão ter ocorrido é o registo do acesso físico, tal como a utilização de porta USB. Novamente, Windows 10 (IoT e outros SKUs) permite que o registo destes eventos detalhado.
- **Proteger credenciais de nuvem**: credenciais de autenticação de nuvem utilizadas para configurar e utilizar uma implementação IoT possivelmente são a forma mais fácil aceder e comprometa um sistema de IoT. Proteger as credenciais, alterando a palavra-passe com frequência e abster-se utilizando estas credenciais em computadores públicos.

Capacidades de diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores com sistemas operativos do ambiente de trabalho comuns e, alguns dispositivos poderão estar a executar sistemas operativos de peso muito claro. Os procedimentos recomendados de segurança descritos anteriormente poderão ser aplicáveis a estes dispositivos por vários graus. Se for fornecido, devem ser seguidos adicionais de segurança e implementação de procedimentos recomendados de fabricantes destes dispositivos.

Alguns dispositivos legacy e restritos podem não ter sido concebidos especificamente para implementação IoT. Estes dispositivos podem não ter capacidade para encriptar dados, ligar à Internet ou fazer a auditoria avançadas. Nestes casos, um gateway de campo moderna e seguro pode agregar dados a partir de dispositivos legacy e fornecer a segurança requerida para ligar estes dispositivos através da Internet. Gateways de campo podem fornecer uma autenticação segura, a negociação de sessões encriptados, a recepção dos comandos a partir da nuvem e muitas outras funcionalidades de segurança.
