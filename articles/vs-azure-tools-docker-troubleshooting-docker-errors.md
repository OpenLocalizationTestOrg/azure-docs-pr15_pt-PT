<properties
   pageTitle="Resolução de problemas de erros de cliente Docker no Windows utilizando o Visual Studio | Microsoft Azure"
   description="Resolução de problemas que encontrar quando utilizando o Visual Studio para criar e implementar web apps para Docker no Windows utilizando o Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Resolução de problemas de desenvolvimento do Visual Studio Docker

Quando trabalha com Visual Studio Tools for Docker Preview, poderá encontrar alguns problemas devido a natureza de pré-visualização.
Seguem-se alguns problemas de comuns e resoluções.


## <a name="unable-to-validate-volume-mapping"></a>Não é possível validar o mapeamento de volume
Mapeamento de volume é necessário para partilhar o código fonte e binários da sua aplicação com a pasta de aplicação no contentor.  Mapeamentos de volume específico estão contidos os ficheiros docker compose.dev.debug.yml e docker compose.dev.release.yml. Tal como ficheiros são alterados no seu computador anfitrião, os contentores refletem estas alterações numa estrutura de pasta semelhante.

Para ativar o mapeamento de volume, abra **definições de...** a partir do ícone de tabuleiro de "moby" Docker para Windows e, em seguida, selecione o separador **Partilhado unidades** .  Certifique-se de que a letra da unidade que aloja o seu projeto, bem como a letra da unidade onde reside % de perfil de utilizador são partilhados verificação-los e, em seguida, clicando em **Aplicar**.

Para testar se mapeamento de volume está a funcionar, assim que tenham sido partilhadas as unidades de, reconstruir e F5 a partir do Visual Studio ou experimente o seguinte procedimento a partir de um comando pede:

*Numa linha de comandos do Windows*

*[Nota: esta assume a sua pasta de utilizadores está localizada na unidade de "C" e que foi partilhado.  Atualizar conforme necessário, se tiver partilhado uma unidade diferente]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*No contentor Linux*

```
/ # ls
```

Deverá ver uma listagem a partir da pasta de utilizadores/público do directório.
Se os ficheiros não são apresentados e a pasta /c/Users/Public não está vazia, mapeamento de volume não está configurado corretamente. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Alterar para o diretório de túnel espacial para ver os conteúdos do `/c/Users/Public` diretório:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Nota:** *Quando trabalha com Linux VMs, o sistema de ficheiros do contentor é sensível a maiúsculas.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Compilar: "PrepareForBuild" Falha inesperada da tarefa.

Microsoft.DotNet.Docker.CommandLine.ClientException: Ocorreu um erro está a tentar ligar:

Verifique se que está a ser executado o anfitrião de docker predefinido. Abra uma linha de comandos e executar:

```
docker info
```

Se esta devolve um erro, em seguida, tentativa para iniciar a aplicação de ambiente de trabalho de **Docker para Windows** .  Se a aplicação de ambiente de trabalho estiver em execução, em seguida, o ícone de **moby** no tabuleiro do deverá estar visível. Clique com o botão direito do rato no ícone do tabuleiro e abra **Definições**.  Clique no separador **Repor** e, em seguida, **reiniciar Docker...**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Atualizar manualmente a partir de versão 0.31 para 0,40


1. Fazer cópia de segurança do projecto
1. Elimine os seguintes ficheiros do projeto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Feche a solução e remova as linhas seguintes do ficheiro .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Voltar a abrir a solução
1. Remova as linhas seguintes do ficheiro Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Remova os seguintes ficheiros relacionadas com Docker a partir de project.json na publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Desinstale a versão anterior e instale Docker ferramentas 0,40 e, em seguida, **Adicionar -> Docker suporte** novamente a partir do menu de contexto para o seu Web do ASP.Net Core ou a aplicação de consola. Isto irá adicionar novos erros Docker necessários para o seu projeto. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre quando tentar **Adicionar -> Docker suporte** ou depurar (F5) uma aplicação do Core ASP.NET num contentor

Vamos ocasionalmente viu desinstalar e que instalar extensões, cache do Visual Studio MEF (gerido expansão Framework) pode ficar danificado. Quando ocorre pode fazer com que o erro várias caixas de diálogo ao adicionar Docker suporte e/ou tentar executar ou depurar (F5) sua aplicação de Core ASP.NET. Como solução temporária, execute os seguintes passos para eliminar e a cache MEF a gerar.

1. Feche todas as instâncias do Visual Studio
1. Abrir %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Elimine as seguintes pastas
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abrir Visual Studio
1. Tentativa novamente o cenário 
