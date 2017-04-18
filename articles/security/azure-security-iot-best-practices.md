<properties
   pageTitle="Internet das coisas segurança melhores práticas | Microsoft Azure"
   description="O artigo fornece uma lista curated de Microsoft Internet de coisas segurança melhores práticas e recomendações gerais."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet das coisas segurança melhores práticas

Proteger a infraestrutura de Internet coisas (IoT) é um compromisso crítico para todas as pessoas envolvidas com IoT soluções. Devido ao número de dispositivos envolvidos e da natureza distribuída destes dispositivos, o impacto um evento de segurança relacionadas com comprometer de milhões de dispositivos IoT é que não sejam comum e pode ter impacto executarão.

Por este motivo, IoT necessidades de segurança uma abordagem de segurança no profundidade. Dados tem de estar segura na nuvem e como move-se através de redes públicas e privadas. Métodos precisam de ser num local aprovisionamento de forma segura os dispositivos de IoT próprios. Cada camada, a partir do dispositivo, a rede, para a cloud back-end tem garantias de segurança forte.

Práticas recomendadas IoT podem ser categorizadas da seguinte forma:

- Fabricante do hardware IoT ou integrador
- Programador da solução IoT
- Objecto de solução IoT
- Operador de solução IoT

Este artigo resume [De coisas segurança procedimentos recomendados na Internet](../iot-suite/iot-security-best-practices.md). Fazer referência a esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricante do hardware IoT ou integrador

Se for um fabricante do hardware IoT ou um integrador hardware, siga as melhores práticas abaixo:

- **Hardware âmbito aos requisitos mínimos**: a estrutura de hardware deve incluir funcionalidades mínimas necessárias para a operação de hardware e nada mais. 
- **Tornar o hardware interferir prova**: construir nos mecanismos para detetar física adulteração de hardware, tais como abrir a capa do dispositivo, remover uma parte do dispositivo, etc. 
- **Construir à volta de hardware seguro**: se [vendas](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitirem, criar funcionalidades de segurança, como o armazenamento seguro e encriptado e a funcionalidade de arranque baseada em fidedignos Platform Module TPM.
- **Tornar atualiza os seguro**: atualizar o firmware durante a vida útil do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Programador da solução IoT

Se for um programador de solução IoT, siga os procedimentos recomendados abaixo:

- **Seguir segura de software development metodologia**: desenvolver segura de software requer terra de segurança pensar em segurança desde o início do projeto completamente para a sua implementação, teste e implementação.
- **Selecione abrir origem software com cuidado**: Abrir origem software fornece uma oportunidade rapidamente desenvolver soluções.
- **Integrar com cuidado**: muitos dos falhas de segurança de software existam no limite da APIs e bibliotecas. 

## <a name="iot-solution-deployer"></a>Objecto de solução IoT

Se for um objecto de solução IoT, siga os procedimentos recomendados abaixo:

- **Implementar em segurança hardware**: IoT implementações precisem de hardware para ser implementada nas localizações não seguras, tal como públicos espaços ou regiões deficiente.
- **Manter as chaves de autenticação**: durante a implementação, cada um dos dispositivos requer IDs de dispositivo e associada de chaves de autenticação geradas pelo serviço na nuvem. Manter estas teclas física seguros mesmo após a implementação. Qualquer chave comprometida pode ser utilizada por um dispositivo malicioso para fazem como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

Se for um operador de solução IoT, siga os procedimentos recomendados abaixo:

- **Manter sistemas atualizados**: Certifique-se de que sistemas operativos de dispositivo e todos os controladores do dispositivo são actualizados para as versões mais recentes. 
- **Proteger contra atividade maliciosa**: se o sistema operativo o permitir, colocar as capacidades de software antivírus e antimalware mais recentes em cada sistema operativo do dispositivo. 
- **Auditoria frequentemente**: IoT infraestrutura de auditoria para relacionadas com a segurança problemas é chave quando responde a incidentes de segurança.
- **Proteger física a infraestrutura de IoT**: os pior ataques de segurança contra IoT infraestrutura são iniciados a utilizar o access física para dispositivos.
- **Credenciais da nuvem de proteger**: credenciais de autenticação de nuvem utilizadas para configurar e utilizar uma implementação IoT possivelmente são a forma mais fácil para obter acesso e comprometer a um sistema de IoT. 
