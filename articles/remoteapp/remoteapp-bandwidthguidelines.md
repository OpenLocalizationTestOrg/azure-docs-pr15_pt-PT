<properties 
    pageTitle="Azure RemoteApp banda - orientações gerais | Microsoft Azure"
    description="Compreenda a algumas linhas de orientação de largura de banda de rede básica para o seu coleções RemoteApp do Azure e apps."
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
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp banda - orientações gerais (se de que não pode testar o seu próprio)

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se não tiver a hora ou a funcionalidade para executar os [testes de largura de banda de rede](remoteapp-bandwidthtests.md) para RemoteApp do Azure, aqui estão algumas orientações bastante genéricas que podem ajudar a estimativa de largura de banda de rede por utilizador.

Se tiver uma mistura destes cenários, não recomendamos nada menor (ou igual a) 10 MB por s como a largura de banda de rede mínimo para as aplicações ligado à Internet modernas, num ambiente remoto. (Embora, tal como é abordado, isto irá não garante uma melhor a experiência do utilizador médio.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint complexo, simple PowerPoint

Azure RemoteApp melhor faz no LAN 100 MB. No perfil de rede de 10 MB/s, quando interferências de duração dos acima 120 ms for mais do que 5%, o utilizador verá uma experiência de média. Em 1 MB/s as diferentes são flagrante - por exemplo, numa apresentação de diapositivos, o utilizador poderá não ver transições animadas sequer porque molduras são ignoradas.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, mista PDF, PDF, texto

Perfil de rede 10 MB/s é perto LAN na maioria dos aspectos. 1 MB/s irá fornecer uma experiência OK, embora possa ser algumas interferências de duração dos quando um utilizador se desloca apesar de existirem imagens no ecrã.

## <a name="flash-video-youtube"></a>Flash video (YouTube)

100 MB/s LAN fornece a melhor experiência, enquanto que 10 MB/s é aceitável (que significa que recomendamos acompanhar a taxa de moldura mas atenuação aumentos). Em MB 1/s, interferências de duração dos são muito alta e evidentes.

## <a name="word-typing-word-remote-input"></a>Word escrita (entrada de remota de Word)
Este é um cenário de largura de banda baixa utilização. Em 256 KB/s fornecemos tão bom de uma experiência como LAN.

## <a name="learn-more"></a>Saiba mais
- [Estimar a utilização de largura de banda de rede RemoteApp do Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - como largura de banda de rede e a qualidade da experiência trabalhar em conjunto?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting a utilização da largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)