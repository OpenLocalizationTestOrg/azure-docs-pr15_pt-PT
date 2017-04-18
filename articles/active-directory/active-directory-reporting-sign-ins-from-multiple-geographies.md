<properties
    pageTitle="Suplementos de início de sessão a partir de várias zonas geográficas"
    description="Um relatório que indica aos utilizadores onde duas inscrever-ins aparecia para provêm de diferentes regiões e o tempo entre o início de sessão que ins impossibilita a para o utilizador tem percorrida entre as regiões."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-from-multiple-geographies"></a>Inícios de sessão a partir de várias zonas geográficas

Este relatório inclui inícios de sessão bem sucedidos de um utilizador onde dois início de sessão-ins apareciam originar de diferentes regiões e o tempo entre os início de sessão-ins impossibilita a para o utilizador ter passado entre as regiões. As possíveis causas incluem:

- Utilizador está a partilhar respetiva palavra-passe com outros utilizadores

- Utilizador está a utilizar um ambiente de trabalho remoto para iniciar um browser para o início de sessão

- Um hacker tem sessão iniciada na conta de um utilizador de um país diferente

- Utilizador está a utilizar uma VPN ou proxy

- Utilizador tem sessão iniciada partir de vários dispositivos ao mesmo tempo, como um ambiente de trabalho e de um telemóvel, e o endereço IP do número de telemóvel é invulgar.

Resultados deste relatório irão apresentar os eventos com êxito iniciar sessão, juntamente com o tempo entre os início de sessão-ins, as regiões onde os início de sessão-ins apareciam para provêm e o tempo de deslocação estimado entre as regiões. O tempo de deslocação apresentado é apenas uma estimativa e pode ser diferente a partir do tempo de deslocação reais entre as localizações.


![Suplementos de início de sessão a partir de várias zonas geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)
