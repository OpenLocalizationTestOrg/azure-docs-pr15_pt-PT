<properties
   pageTitle="Descrição geral de segurança do Internet das coisas | Microsoft Azure"
   description=" Azure internet dos serviços de coisas (IoT) fornecem uma vasta gama de funcionalidades. Este artigo ajuda-o a compreender como seguro as soluções do IoT no Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Descrição geral de segurança do Internet das coisas

Azure internet dos serviços de coisas (IoT) fornecem uma vasta gama de funcionalidades. Estes serviços de classe enterprise permitem-lhe:

- Recolher dados a partir de dispositivos
- Analisar dados sequências de movimento
- Armazenar e grandes conjuntos de dados da consulta
- Visualizar dados em tempo real e históricos
- Integrar com os sistemas de anterior do office

Para a prestação estas capacidades, pacotes de conjunto de aplicações do Azure IoT em conjunto vários serviços Azure com as extensões personalizadas como soluções pré-configurado. Estas soluções pré-configurado são base implementações do padrões de solução IoT comuns que o ajudam a reduzir o tempo de que tomar para entregar a sua IoT soluções. Utilizar os kits de desenvolvimento de software IoT, pode personalizar e expandir estas soluções aos seus requisitos. Também pode utilizar estas soluções como modelos ou exemplos quando está a desenvolver soluções de IoT novas.

O conjunto de aplicações do Azure IoT é uma solução poderosa para as suas necessidades IoT. No entanto, é de importância upmost que as soluções IoT destinam-se com a segurança deve ter em conta a partir do início. Devido ao número de IoT dispositivos, qualquer incidente de segurança rapidamente pode tornar-se um evento executarão com consequências significativas.

Para ajudar a compreender como seguro as soluções IoT, temos as seguintes informações.

## <a name="security-architecture"></a>Arquitetura de segurança

Ao estruturar um sistema, é importante compreender as potenciais ameaças a esse sistema e adicionar defesas adequadas, por conseguinte, tal como o sistema está concebido e concepção. É particularmente importante estruturar o produto desde o início com a segurança em mente porque Noções sobre como intruso poderá ser possível comprometer a um sistema de ajuda a tornar a tem a certeza adequadas atenuações estão no local do início.

Pode aprender sobre arquitetura de segurança do IoT lendo [Arquitetura de segurança Internet das coisas](../iot-suite/iot-security-architecture.md).

Este artigo aborda os seguintes tópicos:

- [Segurança começa com um modelo de ameaça](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Segurança nos IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [Ameaça modelação a arquitetura do Azure IoT referência](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Segurança aprofundadamente

O IoT constitui desafios exclusivos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário de tecnologia de cyber tradicional onde estes problemas baseiam-se à volta de software e como é implementado, IoT refere-se o que acontece quando a cyber e o mundo físico converge. Proteger as soluções IoT requer garantindo que aprovisionamento seguro dispositivos, conectividade segura entre nestes dispositivos e a nuvem e a proteção de dados segura na nuvem durante processamento e armazenamento. No entanto, trabalhar contra essa funcionalidade, são restringido recurso, distribuição geográfica dos híbridas, dispositivos e um grande número de dispositivos dentro de uma solução.

Pode obter informações sobre como gerir a segurança nestas áreas lendo [segurança Internet das coisas aprofundadamente](../iot-suite/securing-iot-ground-up.md).

O artigo aborda os seguintes tópicos:

- [Infraestrutura segura aprofundadamente](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure – infraestrutura de IoT segura para a sua empresa](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Melhores práticas

Proteger uma infraestrutura IoT requer uma estratégia de segurança no profundidade estritas. A partir do proteger os dados na nuvem, para proteger a integridade dos dados enquanto trânsito através da internet público e fornecer a capacidade de forma segura aprovisionar dispositivos, cada camada constrói maior assurance de segurança na infraestrutura de geral.

Pode obter informações sobre a segurança do Internet das coisas melhores práticas ao ler o artigo [melhores práticas de Internet de coisas segurança](../iot-suite/iot-security-best-practices.md).

O artigo aborda os seguintes tópicos:

- [IoT-fabricante do hardware/integrador](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Programador da solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Objecto de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Operador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
