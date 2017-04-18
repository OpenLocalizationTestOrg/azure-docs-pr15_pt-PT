 (cofres cópia de segurança<properties
   TítuloPágina = "Azure Backup limits table"
   Descrição = "descreve os limites de sistema do Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


Os seguintes limites de aplicam a cópia de segurança do Azure.

| Identificador do limite | Limite predefinido |
|---|---|
|Número de servidores/máquinas que podem ser registados contra cada Cofre|50 para o Windows Server/cliente/SCDPM <br/> 200 para IaaS VMs|
|Tamanho de uma origem de dados para os dados armazenados no armazenamento cofre Azure|Máx 54400 GB<sup>1</sup>|
|Número de cópia de segurança cofres que podem ser criados de cada subscrição Azure|25 (cofres de cópia de segurança) <br/> Serviços de recuperação de 25 Cofre de palavras por região|
|Número de vezes que a cópia de segurança pode ser agendada por dia|3 por dia para o Windows Server/Client <br/> 2 por dia para SCDPM <br/> Uma vez por dia para IaaS VMs|
|Discos de dados ligados a uma máquina virtual Azure para cópia de segurança|16|

- <sup>1</sup> O limite de 54400 GB não se aplica a cópia de segurança IaaS VM.

