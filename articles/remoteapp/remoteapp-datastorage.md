
<properties
    pageTitle="Nunca armazenar dados confidenciais no imagens personalizadas para RemoteApp do Azure | Microsoft Azure"
    description="Saiba mais sobre as diretrizes para armazenar os dados em imagens personalizadas no RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="never-store-sensitive-data-on-custom-images"></a>Nunca armazenar dados sensíveis a maiúsculas e em imagens personalizadas

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Ao aloja os seu próprios aplicação no Azure RemoteApp, o primeiro passo é criar uma imagem personalizada. Utilizamos essa imagem personalizada para criar instâncias de VM servem as suas aplicações para os seus utilizadores. A imagem personalizada deve conter apenas aplicações e dados nunca importantes que podem ser perdidos, tais como bases de dados SQL, ficheiros de pessoal ou ficheiros de dados especiais como os ficheiros de empresa do QuickBooks. Todos os dados confidenciais devem residem externos à RemoteApp do Azure num servidor de ficheiros, outro Azure VM, ou no SQL Azure. A imagem apenas deve alojar a aplicação que liga-se à origem de dados e apresenta os dados. Consulte [os requisitos para RemoteApp do Azure imagens](remoteapp-imagereqs.md) para obter mais informações. 

Para compreender por que motivo não deve armazenar dados sensíveis, é necessário compreender como funciona a RemoteApp do Azure. Quando uma coleção de é criada ou atualizada, nos bastidores várias clones ou cópias da imagem são criados. Todas as instâncias estes VM são réplicas exatas da imagem personalizada; Quando os utilizadores iniciam aplicações estes estão ligados a uma dessas ocorrências VM. Mas na mesma instância não se garante e não deve importante porque são não persistente. As instâncias VM alojamento as aplicações são não persistentes e podem ser destruídos ou eliminados baseada, por exemplo, durante a atualização de coleções de sites. 

Assim que a coleção de está aprovisionada e os utilizadores começam a ligar-se para os VMs, dados de utilizador são persistentes e protegidos porque este será guardado no armazenamento separado dentro de um VHD que chamamos de um [disco de perfil de utilizador (ACT)](remoteapp-upd.md), que é o perfil de utilizador no c:\users\<perfil de utilizador >. Quando uma aplicação for iniciada, o ACT é instalado e Tratado tal como um perfil de utilizador local pelo sistema operativo. Leia mais sobre como [RemoteApp do Azure guarda os dados de utilizador e definições](remoteapp-upd.md).

Dados de exemplo que não devem residem na imagem:

- Dados para os utilizadores acedam partilhados
- DB do SQL ou do QuickBooks DB
- Quaisquer dados existentes na D:\

Dados de exemplo que podem residem no perfil predefinido a ser copiado para Act todos os utilizadores:

- Ficheiros de configuração por utilizador
- Scripts que os utilizadores seriam necessário preservados no respetivo Act

Pontos-chave:

- Nunca arquivo de dados confidenciais que podem perder-se da imagem quando criar uma imagem personalizada.
- Dados confidenciais devem sempre residem num servidor de ficheiros separada, separado Azure VM, na nuvem e sempre externas para as instâncias VM alojamento suas aplicações de RemoteApp do Azure. 
- Dados de utilizador são guardados e persistir no disco de perfil de utilizador (ACT)


