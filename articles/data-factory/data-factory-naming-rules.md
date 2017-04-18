<properties 
    pageTitle="Dados Factory - regras de nomenclatura | Microsoft Azure" 
    description="Descreve as regras de nomenclatura para entidades fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Dados Azure Factory - regras de nomenclatura 
A tabela seguinte fornece regras de nomenclatura para artefactos fábrica de dados.



Nome | Exclusividade de nome | Verificações de validação
:--- | :-------------- | :----------------
Fábrica de dados | Exclusivo através do Microsoft Azure. Os nomes estão maiúsculas e minúsculas, ou seja, MyDF e mydf referem-se para a mesma fábrica de dados. |<ul><li>Cada fábrica de dados está associada à exatamente uma subscrição Azure.</li><li>Nomes de objetos tem de começar com uma letra ou um número e podem conter apenas letras, números e o caráter de travessão (-).</li><li>Cada caráter de travessão (-) deve ser precedido imediatamente e seguido por uma letra ou um número. Hífenes consecutivos não são permitidos em nomes de contentor.</li><li>Nome pode ser 3-63 carateres de comprimento.</li></ul>
Serviços ligadas/tabelas/tubagens | Exclusivos com uma fábrica de dados. Nomes estão maiúsculas e minúsculas. | <ul><li>Número máximo de carateres no nome de uma tabela: 260.</li><li>Nomes de objetos tem de iniciar com um número de letra ou um carácter de sublinhado (_).</li><li>Sequência de carateres não são permitidas: ".", "+", "?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul>
Grupo de recursos | Exclusivo ao longo do Microsoft Azure. Nomes estão maiúsculas e minúsculas. | <ul><li>Número máximo de carateres: 1000.</li><li>Nome pode conter letras, dígitos e os seguintes carateres: "-", "_",","e".".</li></ul>