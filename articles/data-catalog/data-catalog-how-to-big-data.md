<properties
   pageTitle="Como trabalhar com origens de dados 'dados grande' | Microsoft Azure"
   description="Realçar padrões para utilizar o catálogo de dados do Azure com origens de dados 'dados grande', incluindo o armazenamento de Blobs do Azure, Lake de dados do Azure e Hadoop HDFS o artigo sobre como utilizar."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Como trabalhar com origens de dados grande no catálogo de dados do Azure

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o **Catálogo de dados do Azure** é tudo sobre pessoas de ajuda para descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir do seus origens de dados existentes, incluindo todos os dados grandes.

**Catálogo de dados do Azure** suporta o registo de blobs do Azure blogue armazenamento e directórios, bem como ficheiros Hadoop HDFS e directórios. A natureza semiestruturada estes de origens de dados dá flexibilidade excelente, mas também significa que os utilizadores tem de considerar como as origens de dados estão organizadas para obter o valor a maior parte da registá-los com o **Catálogo de dados do Azure**.

## <a name="directories-as-logical-data-sets"></a>Diretórios como lógicos conjuntos de dados

Um padrão comum para organizar origens de dados grande é tratar directórios como lógicos conjuntos de dados. Diretórios de nível superiores são utilizados para definir um conjunto de dados, enquanto subpastas definem a partições e os ficheiros que contêm armazenam os dados propriamente ditos.

Um exemplo deste padrão poderá ser:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam lógicos conjuntos de dados. Cada uma das seguintes pastas contém os ficheiros de dados que estão organizados por ano e mês em subpastas. Cada uma das seguintes pastas potencialmente poderia conter centenas ou milhares de ficheiros.

Neste padrão, registar ficheiros individuais com o **Catálogo de dados do Azure** provavelmente não fazer sentido. Em vez disso, registe-se os directórios que representam os conjuntos de dados que será significativos para os utilizadores a trabalhar com os dados.

## <a name="reference-data-files"></a>Ficheiros de dados de referência

É um padrão complementar armazenar os conjuntos de dados de referência como ficheiros individuais. Estes conjuntos de dados pode ser considerados como o lado «pequeno» dos dados grandes e frequentemente são semelhantes às dimensões num modelo de dados analítica. Ficheiros de dados de referência contêm registos que são utilizados para fornecer contexto para em volume dos ficheiros de dados armazenados noutro local no arquivo de dados grande.

Um exemplo deste padrão poderá ser:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando uma cientista analista ou dados está a trabalhar com os dados contidos nas estruturas de diretório do maiores, os dados nestes ficheiros de referência podem ser utilizados para fornecer informações mais detalhadas para entidades que são referidas apenas por nome ou o ID no conjunto de dados maior.

Este padrão, tornará sentido para registar os ficheiros de dados individuais de referência com o **Catálogo de dados do Azure**. Cada ficheiro representa um conjunto de dados e pode ser anotada e descoberta individualmente cada uma delas.

## <a name="alternate-patterns"></a>Padrões alternativos

Os padrões de descrito acima são apenas dois formas possíveis de que um arquivo de dados grande pode ser organizado, mas cada implementação serão diferente. Independentemente de como origens de dados estão estruturadas, quando registar origens de dados grande com o **Catálogo de dados do Azure**, concentrar-se sobre como registar os ficheiros e directórios que representam os conjuntos de dados que será de valor para outras pessoas na sua organização. Registar todos os ficheiros e directórios pode correio secundário do catálogo, tornando mais difícil para encontrar o que precisam os utilizadores.

## <a name="summary"></a>Resumo
Registar origens de dados com o **Catálogo de dados do Azure** torna-as mais fáceis de detetar e compreender. Registar e anotar grande ficheiros de dados e directórios que representam lógicos conjuntos de dados, pode ajudar a localizar e utilizar as origens de dados grande que necessitam de utilizadores.
