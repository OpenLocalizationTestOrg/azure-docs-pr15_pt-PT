<properties
    pageTitle="Atividade de início de sessão irregular"
    description="Um relatório que inclui assinar ins identificados como discordantes ao nossa algoritmos de aprendizagem automática."
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

# <a name="irregular-sign-in-activity"></a>Atividade de início de sessão irregular

Início de sessão-ins irregulares são aqueles que tenham sido identificadas por nossa algoritmos, com base numa condição "viagem impossibilita" combinado com um sinal de discordantes na localização e o dispositivo de aprendizagem automática. Isto pode indicar que um hacker com êxito tem sessão iniciada no utilizando esta conta.
Vamos enviar uma notificação de e-mail para os administradores globais se encontrar podemos 10 ou mais discordantes início de sessão no eventos dentro de um intervalo de 30 dias ou menos. Certifique-se de incluir aad-alerts-noreply@mail.windowsazure.com na sua lista de remetentes seguros.
