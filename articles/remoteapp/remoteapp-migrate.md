
<properties
    pageTitle="Migrar dados de utilizador a partir do Azure RemoteApp | Microsoft Azure"
    description="Saiba como migrar os seus dados de utilizador e terminar RemoteApp do Azure."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Como migrar dados para e terminar RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Pode utilizar vários ferramentas diferentes e métodos para transferir [dados de utilizador](remoteapp-upd.md) para e terminar RemoteApp do Azure. Eis alguns métodos:

- Copiar e colar através da partilha de área de transferência
- Copiar ficheiros e dados para um servidor de ficheiros
- Copiar ficheiros para o OneDrive para empresas através de um browser
- Copiar ficheiros de utilização do redirecionamento

>[AZURE.NOTE] 
> Não é possível ativar o OneDrive para empresas ou do consumidor agentes de sincronização - [não são suportadas](remoteapp-onedrive.md) no Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Utilize a funcionalidade copiar e colar no Explorador de ficheiros

Copiar e colar utilizando a área de transferência é activado no RemoteApp implementações [por predefinição](remoteapp-redirection.md). Isto permite aos utilizadores copiar ficheiros entre das aplicações de PC e RemoteApp locais. Muitas vezes, através do curso normal de utilização de aplicações RemoteApp, os utilizadores tiverem guardado ficheiros para os respetivos UPDs - mover que dados fora RemoteApp são fácil:

1. [Publicar o Explorador de ficheiros como uma aplicação](remoteapp-publish.md) numa coleção de RemoteApp. (Tenha em atenção que se trata de uma tarefa administrativa.)
2. Direccionar os seus utilizadores para iniciar a aplicação do Explorador de ficheiros publicado e para utilizá-la para copiar e colar ficheiros para o respetivo Act e terminá-lo.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Carregar ficheiros e dados para um servidor de ficheiros utilizando a cópia do ficheiro de rede padrão

Organizações com frequência utilizam servidores de ficheiros para armazenar dados geral. Se souber o nome do servidor ou localização, os seus utilizadores podem procurar na rede local para o servidor de e, em seguida, copie os respetivos ficheiros, muito que tinham acima. Novamente irá pretende publicar RemoteApp Explorador de ficheiros e, em seguida, partilhe-o com os seus utilizadores.

>[AZURE.NOTE] 
> Tem de ser o servidor de ficheiros na rede encaminhável que foi implementado RemoteApp para.

## <a name="copy-files-to-onedrive-for-business"></a>Copiar ficheiros no OneDrive para empresas
Apesar de não é possível ativar o OneDrive para agente de sincronização de negócio no RemoteApp, pode ainda copiar ficheiros a partir do seu Act ao OneDrive para empresas através de um browser. 

1. Publicar RemoteApp Explorador de ficheiros e, em seguida, informe os utilizadores para aceder aos ficheiros através da aplicação que. 
2. É mais fácil a transferência de ficheiros se comprimidos de origem, para que os utilizadores devem criar um ficheiro. zip que contém todos os ficheiros para mover para o OneDrive para empresas.
3. Peça aos utilizadores para aceda ao portal do Office 365 e, em seguida, aceda ao OneDrive e carregue o ficheiro. zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiar ficheiros utilizando o redirecionamento de unidade

Se ativou o [redirecionamento de unidade](remoteapp-redirection.md), já tiver criado uma unidade mapeada para os seus utilizadores. Neste caso, pode dos seus ficheiros na unidade de redirecionado zip e, em seguida, guardá-los ao seu PC local.