<properties 
    pageTitle="Azure RemoteApp - como largura de banda de rede e a qualidade da experiência trabalhar em conjunto? | Microsoft Azure"
    description="Saiba como largura de banda de rede no Azure RemoteApp pode afetar o da qualidade seu utilizador da experiência."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - como largura de banda de rede e a qualidade da experiência trabalhar em conjunto?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Quando está a observar a [largura de banda de rede global](remoteapp-bandwidth.md) necessários para o Azure RemoteApp, tenha em atenção os seguintes fatores – estas são todas as parte de um sistema de dinâmico impactos a experiência do utilizador global. 

- **Largura de banda de rede disponíveis e condições atuais de rede** - um conjunto de parâmetros (perda de latência, atenuação) na mesma rede num dado momento pode afetar a aplicação de transmissão experiência, o que significa uma descida experiência do utilizador global. A largura de banda disponível na sua rede é uma função de congestionamento, perda aleatória, latência porque todos estes parâmetros afectam mecanismo de controlo congestionamento, que em Ativar controlos a velocidade de transmissão para evitar conflitos.  Por exemplo, uma rede com perda ou com uma latência alta irá tornar a experiência de utilizador incorretas mesmo numa rede com largura de banda de 1000 MB. A perda e latência podem variar com base no número de utilizadores que estão na mesma rede e o que os utilizadores estão a fazer (por exemplo, ver vídeos, transferir ou carregar ficheiros grandes, imprimir).
- **Cenário de utilização** - a experiência depende o que os utilizadores estão a fazer como indivíduos e como um grupo na mesma rede. Por exemplo, um diapositivo de leitura requer um pacote único a ser atualizados; Se o utilizador skims e desloca-se sobre o conteúdo de um documento de texto, que necessitam de um número mais alto de pacotes a ser atualizados por segundo. A comunicação e para trás para o servidor neste cenário, eventualmente, irá consumir mais largura de banda de rede. Tenha também em consideração representarem exemplo: vários utilizadores estão ver vídeos de alta definição (tal como a resolução de 4K), mantendo a tecla HD chamadas de conferência, reproduzir 3D jogos de vídeo ou trabalhar nos sistemas de CAD. Todos estes elementos podem fazer com que mesmo numa rede de largura de banda muito alta praticamente inutilizável.
- **Resolução de ecrã e o número de ecrãs** - mais largura de banda de rede é necessária para ecrãs de maiores de actualização completa que ecrãs mais pequenos. A tecnologia subjacente faz uma tarefa de codificação e transmitir apenas as regiões dos ecrãs que tiverem sido atualizados muito bom, mas uma vez em quando, todo o ecrã tem de ser atualizados. Quando o utilizador tem um ecrã resolução mais elevado (por exemplo Resolução 4K), essa actualização requer a largura de banda de rede mais que um ecrã com resolução inferior (como 1024x768px). Esta lógica mesmo aplicam-se de que utiliza mais do que um ecrã para redirecionamento. Necessita de largura de banda aumentar com o número de ecrãs.
- **Área de transferência e do dispositivo redirecionamento** - este é um problema não é muito óbvio, mas em muitos casos se um utilizador armazena um grande bloco de dados para a área de transferência, bastam um pouco de tempo dessas informações para o qual transferir a partir do cliente de ambiente de trabalho remoto no servidor. A experiência descendentes pode ser afetada pela experiência de enviar o conteúdo da área de transferência montante. O mesmo aplica-se para o redirecionamento de dispositivo - se um scanner ou câmara web produz muitos dados que tem de ser enviada montante no servidor ou uma impressora tem de receber um documento grande, armazenamento local tem de estar disponível para uma aplicação a ser executada em nuvem para copiar um ficheiro grande, utilizadores poderão reparar em pacotes ignorados ou temporariamente "fixa" vídeo porque os dados necessários para o redirecionamento de dispositivo está a aumentar a largura de banda de rede precisa. 

Ao avaliar as necessidades da largura de banda de rede, certifique-se a ter em consideração todos estes factores trabalhar como um sistema.

Agora, regresse ao [artigo da largura de banda de rede principal](remoteapp-bandwidth.md)ou avance para testar a sua [largura de banda de rede](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Saiba mais
- [Estimar a utilização de largura de banda de rede RemoteApp do Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - testar a utilização da largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)

- [Azure RemoteApp banda - orientações gerais (se de que não pode testar o seu próprio)](remoteapp-bandwidthguidelines.md)