
<properties 
    pageTitle="Estimar a utilização de largura de banda de rede RemoteApp do Azure | Microsoft Azure"
    description="Saiba mais sobre os requisitos de largura de banda de rede para o seu coleções RemoteApp do Azure e apps."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Estimar a utilização de largura de banda de rede RemoteApp do Azure 

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp utiliza o protocolo de ambiente de trabalho remoto (RDP) para comunicar entre aplicações a ser executada em nuvem Azure e os seus utilizadores. Este artigo fornece algumas orientações básicas que pode utilizar para estimar a utilização de rede e potencialmente avaliar a utilização da largura de banda de rede por utilizador RemoteApp do Azure.

Estimar a utilização da largura de banda por utilizador é muito complexo e requer a execução do várias aplicações em simultâneo em cenários de tarefas onde aplicações poderão ter impacto uns dos outros desempenho com base no seu pedido de largura de banda de rede. Mesmo o tipo de cliente de ambiente de trabalho remoto (por exemplo, o cliente de Mac versus HTML5 cliente) pode conduzir a resultados de largura de banda diferente. Para ajudar a resolver estes complicações, podemos vai dividir os cenários de utilização em várias das categorias para criar uma réplica reais cenários comuns. (Onde o cenário reais é, claro, uma mistura de categorias e difere pelo utilizador.)

Antes de avançar ainda mais - necessita de nota que vamos assumir que RDP fornece indicado à excelente experiência para cenários de utilização da maior parte das redes com uma latência abaixo 120 ms e largura de banda superior 5 MB – isto é baseado na capacidade de RDP para ajustar dinamicamente utilizando a largura de banda de rede disponíveis e a largura de banda estimado da aplicação. Este artigo vai para além das "maioria dos cenários de utilização" para ver a margem, onde cenários começam para desenrolamento e experiência de utilizador começa a diminuir.

Consulte os seguintes artigos para obter mais detalhes, incluindo fatores a ter em conta, agora recomendações do plano base e o que recomendamos não incluiu no nossas estimativas.

- [Como largura de banda de rede e a qualidade da experiência trabalhar em conjunto?](remoteapp-bandwidthexperience.md)
- [Testar a utilização da largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)
- [Diretrizes rápidas se não tiver a hora ou a capacidade de teste](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>O que são podemos excluindo?

Quando rever os testes de proposta e as nossas recomendações gerais (e admittedly genéricas), esteja ciente de que não existem vários fatores que não considerar que recomendamos. Por exemplo, os problemas de experiência do utilizador fornecidos da natureza assimétrica do carregamento vs. Transferir largura de banda. Da natureza assimétrica da maior parte das redes Wi-Fi para além disso irá afetar o desempenho e a cobrança de experiência do utilizador. Para obter cenários interativos o tráfego descendentes poderá prioridades inferiores o montante, que podem aumentar o número de molduras perdidas de áudio ou vídeo e, por conseguinte, afetar a cobrança de utilizador da experiência do transmissão. Pode executar a suas própria experiências para ver o que é boa para o caso de utilização específicos e de rede.

Apesar de discutimos redirecionamento de dispositivo, podemos não ter em consideração o impacto da largura de banda o tráfego da rede causado por dispositivos ligados, como o armazenamento, impressoras, scanners, câmaras web e outros dispositivos USB. O efeito dos dispositivos, normalmente pontas temporariamente as necessidades de largura de banda e desaparece quando a tarefa estiver concluída. Mas se concluído frequentemente, que exige a largura de banda pode ser bastante evidente.

Também não discutimos como um utilizador pode afetar a outros utilizadores na rede da mesma. Por exemplo, um utilizador consumir 4K vídeo numa rede 100 MB/s significativamente pode afetar outros utilizadores nessa mesma rede tentar efetuar a mesma tarefa. Infelizmente obtém gradualmente mais difícil de determinar o impacto da utilização em simultâneo para dar uma recomendação comuns ou que abrange todas as informações sobre como o sistema executa em agregar. Tudo o que pode dizer é que a tecnologia subjacente do protocolo irá tornar a melhor utilização da largura de banda de rede disponíveis, mas tem as suas limitações.