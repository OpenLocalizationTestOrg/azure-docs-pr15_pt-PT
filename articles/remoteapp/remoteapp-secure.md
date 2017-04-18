
<properties
    pageTitle="Proteger as aplicações e recursos no Azure RemoteApp | Microsoft Azure"
    description="Saiba como bloquear para baixo de aplicações e recursos na RemoteApp do Azure"
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



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Proteger as aplicações e recursos na RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp fornece aos utilizadores acesso às aplicações do Windows geridos centralmente, que permite-lhe controlar o que os utilizadores podem ou não fazer.  Este é particularmente útil quando o utilizador está a ligar a partir de um dispositivo não gerido (como os respetivos Macbook pessoal) e que pretende controlar o acesso do utilizador ou experiência.

Por exemplo, se estiver a utilizar o Active Directory para autenticação de utilizador e pretende impedir que os utilizadores copiar dados de uma aplicação, pode configurar uma política de grupo de ambiente de trabalho remoto para bloquear utilizadores copiem dados.

Outro exemplo é se pretende bloquear o acesso à internet para uma aplicação específica na sua coleção. Pode criar uma regra de Firewall do Windows que bloqueia o acesso ao criar a imagem para a coleção.

## <a name="implementation-options"></a>Opções de implementação

  Aqui estão as opções de implementação de chave que podem ser utilizadas individualmente ou em conjunto, conforme necessário:

1.  Se a sua coleção de RemoteApp for domínio aderido pode impor a qualquer [Política de grupo](https://technet.microsoft.com/library/cc725828.aspx) (com a exceção da inactivo e desligar tempo limite políticas descritas [aqui](../azure-subscription-service-limits.md)).
2.  Como alternativa à política de grupo (se a sua coleção de não for domínio associado ou não tem os privilégios direita no AD), pode configurar [Políticas locais](https://technet.microsoft.com/library/cc775702.aspx) para a sua imagem do modelo.  Tenha em atenção que ao grupo diretivas políticas locais trunfo quando existe um conflito.
3.  Algumas definições do sistema operativo/aplicação não são configuráveis através da política, mas podem ser através da chave de registo com a [ferramenta RegEdit](./remoteapp-hybridtrouble.md) ao configurar a sua imagem do modelo.
4.  Pode utilizar [A Firewall do Windows](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) para controlar o acesso de rede de computador e para onde a aplicação está em execução. Certifique-se apenas que não bloquear os URLs e portas definidas aqui.
5.  Pode utilizar [o AppLocker](https://technet.microsoft.com/library/hh831440.aspx) para controlar que aplicações e ficheiros que os utilizadores podem ser executados. Por exemplo, os utilizadores mais experientes podem descobrir como executar as aplicações que não publicar, mas que estão disponíveis na imagem que utilizou para criar a coleção de-AppLocker pode bloquear este.

## <a name="detailed-information"></a>Obter informações detalhadas

- As seguintes políticas RDS são ser as mais útil:
    - [Redirecionamento do recurso e de dispositivo](https://technet.microsoft.com/library/ee791794.aspx)
    - [Redirecionamento de impressora](https://technet.microsoft.com/library/ee791784.aspx)
    - [Perfis](https://technet.microsoft.com/library/ee791865.aspx).
- Tenha em atenção que configurar redireccionamento através do PowerShell a RemoteApp módulo (tal como se vê [aqui](./remoteapp-redirection.md)) baseia-se no computador do cliente para impor a política, por isso se a segurança for o principal objectivo irá que para impor a política através da política local da imagem de modelo ou através da política de grupo.
- [Windows Server 2012 R2 políticas](https://technet.microsoft.com/library/hh831791.aspx).
- [Diretivas do Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (incluindo [como personalizar a barra de ferramentas do Office](https://technet.microsoft.com/library/cc179143.aspx)).
