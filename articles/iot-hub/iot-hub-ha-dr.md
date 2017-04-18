<properties
 pageTitle="Concentrador IoT HA e DR | Microsoft Azure"
 description="Descreve as funcionalidades que ajudam a criar soluções de IoT altamente disponíveis com falhas capacidades de recuperação."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Recuperação de disponibilidade e falhas alta no concentrador IoT

Como um serviço Azure, IoT concentrador fornece elevada disponibilidade (HA) utilizando despedimentos ao nível da região Azure, sem qualquer trabalho adicional necessário à solução. Além disso, o Azure oferece várias funcionalidades que ajudam a criar soluções com capacidades de recuperação (DR) falhas ou a disponibilidade de publicação em região, se necessário. Tem de estruturar e preparar o seu soluções para tirar partido destas funcionalidades de DR se quiser fornecer global, a publicação em região elevada disponibilidade para dispositivos ou utilizadores. Este artigo [Orientação técnica do Azure empresas continuidade](../resiliency/resiliency-technical-guidance.md) descreve as funcionalidades incorporadas no Azure para continuidade do negócio e DR. O papel de [recuperação de falhas e elevada disponibilidade para aplicações do Azure][] arquitetura dá-lhe no estratégias para aplicações do Azure para alcançar HA e DR.

## <a name="azure-iot-hub-dr"></a>Azure IoT concentrador DR
Para além de região dentro HA, IoT concentrador implementa mecanismos de activação pós-falha de recuperação de falhas que requerem sem intervenção do utilizador. IoT concentrador DR personalizada é iniciada e tem um objectivo de tempo de recuperação (RTO) das horas de 2-26 e os seguintes objectivos de ponto de recuperação (RPOs).

| Funcionalidade | RPO |
| ------------- | --- |
| Disponibilidade do serviço para operações de registo e comunicação | Possível perda de CName |
| Dados de identidade no registo de identidade do dispositivo | 0-5 minutos perda de dados |
| Nuvem de dispositivo de mensagens | Perdem-se todas as mensagens não lidas |
| Operações monitorizar mensagens | Perdem-se todas as mensagens não lidas |
| Mensagens de dispositivo de nuvem | 0-5 minutos perda de dados |
| Fila de dispositivo de nuvem comentários | Perdem-se todas as mensagens não lidas |

## <a name="regional-failover-with-iot-hub"></a>Regional activação pós-falha com IoT concentrador

Um tratamento completo das topologias de implementação em IoT soluções está fora do âmbito deste artigo, mas para elevada disponibilidade e recuperação de falhas podemos irá considerar o modelo de implementação *regional activação pós-falha* .

Num modelo de activação pós-falha regionais, solução back-end está a ser executado principalmente numa localização do Centro de dados, mas um concentrador de IoT adicional e back-end são implementadas noutra localização do Centro de dados para fins de activação pós-falha, caso o concentrador IoT o Centro de dados principal sofre uma falha ou a conectividade de rede do dispositivo para o Centro de dados principal ficou for interrompida. Os dispositivos utilizam um ponto final de serviço secundário sempre que o gateway principal não é possível alcançar. Com uma funcionalidade de publicação em região activação pós-falha a disponibilidade de solução pode ser melhorada para além da alta disponibilidade de uma única região.

De alto nível para implementar um modelo de activação pós-falha regionais com IoT Hub, terá o seguinte.

* **Um concentrador de IoT secundário e do dispositivo encaminhamento de lógica**: no caso de uma interrupção do serviço na sua região principal, dispositivos tem de iniciar a ligar à sua região secundário. Dada da natureza tenha em atenção o estado da maioria dos serviços em questão, é comum para que os administradores de solução acionar o processo de região dependências entre activação pós-falha. A melhor forma para comunicar o novo ponto final para dispositivos, mantendo controlo do processo, é Consulte regularmente um serviço de *apoio* para o ponto final do active atual. O serviço de apoio pode ser uma aplicação web simples que é replicada e mantida alcançáveis utilizar técnicas de redirecionamento de DNS (por exemplo, utilizando o [Gestor de tráfego Azure][]).
* **Replicação de registo de identidade** - para poder ser utilizada, a IoT secundário concentrador tem de conter todas as identidades do dispositivo que podem ligar-se para a solução. A solução deve manter replicadas geo cópias de segurança de identidades do dispositivo e, carregue-os para o concentrador IoT secundário antes de mudar o ponto final ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do concentrador de IoT é muito útil neste contexto. Para mais informações, consulte o artigo [Guia de programador do concentrador IoT - registo de identidade][].
* **Intercalar lógica** - quando a região primária fica disponível novamente, todas as estado e os dados que foram criados no site secundário tem de ser migrados novamente a região principal. Isto principalmente se relaciona com identidades do dispositivo e meta-dados da aplicação, que têm de ser intercaladas com o concentrador IoT principal e outros arquivos específicos da aplicação na região principal. Para simplificar a este passo, normalmente, recomendamos que utilize idempotent operações. Isto minimiza efeitos secundários não só a partir do eventual distribuição consistente dos eventos, mas também a partir de duplicados ou fora da ordem entrega de eventos. Além disso, a lógica da aplicação deve ser concebida para tolerar potenciais inconsistências ou "ligeiramente" fora do Estado de data. Isto é devido ao tempo adicional necessário para que o sistema de "heal" baseado em objetivos de ponto de recuperação (RPO).

## <a name="next-steps"></a>Próximos passos

Siga estas ligações para mais informações sobre o Azure IoT concentrador:

- [Introdução ao IoT concentradores (Tutorial)][lnk-get-started]
- [O que é o Azure IoT concentrador?][]

[Recuperação de falhas e elevada disponibilidade para aplicações do Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Gestor de tráfego Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guia de programador do concentrador de IoT - registo de identidade]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Azure IoT concentrador?]: iot-hub-what-is-iot-hub.md
