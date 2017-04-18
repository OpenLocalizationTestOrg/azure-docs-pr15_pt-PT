<properties 
    pageTitle="Azure RemoteApp - testar a utilização da largura de banda de rede com alguns cenários comuns | Microsoft Azure"
    description="Saiba mais sobre como cenários comuns de utilização que podem ajudar a descobrir as necessidades da largura de banda de rede para RemoteApp do Azure."
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
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - testar a utilização da largura de banda de rede com alguns cenários comuns

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Como é abordado na [utilização de largura de banda de rede RemoteApp do Azure estimativa](remoteapp-bandwidth.md), testa a melhor forma de descobriu o que é o impacto das RemoteApp do Azure à sua rede executar alguns a utilização. Executar estes testes para um período de tempo definido e medir a largura de banda necessária para cada cenário. Se tiver a capacidade de, também pode medir a rede pacote perda e de rede interferências de duração para compreender os padrões de rede que serão criados no seu ambiente específico.

    
Ao avaliar a utilização da largura de banda, lembre-se de que a utilização varia entre diferentes utilizadores na sua empresa. Por exemplo, leitores de texto e escritores normalmente consumam menos largura de banda que os utilizadores que funcionam com o vídeo. Para obter melhores resultados, as suas necessidades de utilizador de estudo e criar uma mistura de cenários seguintes que melhor representa os utilizadores na sua empresa. Lembre-se para [Rever os fatores que experiência de utilizador e a utilização da largura de banda de impacto](remoteapp-bandwidthexperience.md) - que irá ajudá-lo a identificar os testes ideais.

Primeiro leia sobre os testes, escolha o mix e, em seguida, executá-los. Pode utilizar a tabela abaixo para o ajudar a controlar o desempenho.

>[AZURE.NOTE] Se não pode fazer o seu próprio testes de rede ou não tiver o tempo para fazê-lo, consulte o artigo as nossas [estimativas de largura de banda de rede base, as recomendações](remoteapp-bandwidthguidelines.md). A quilometragem pode variar, no entanto, por isso se que *pode* executar o seus próprio testes, deve.


## <a name="the-usage-tests"></a>Os testes de utilização
Cada um destes testes execute para diferentes valores de tempo e teste diferentes funções/funcionalidades que consumir largura de banda de rede. Lembre-se escolher a mistura de teste que melhor corresponde aos utilizadores individuais da empresa.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint executivo/complexo - executar para 900 1000 segundos

Um utilizador apresenta entre diapositivos de alta fidelidade 45 a 50 utilizando o Microsoft Office PowerPoint no modo de ecrã inteiro. Os diapositivos devem conter imagens, transições (com animações) e imagens de fundo com gradação de cores que estão típicas para a sua empresa. O utilizador deve gasto, pelo menos, 20 segundos em cada diapositivo.
    
Este cenário cria o tráfego retardamento, quando um diapositivo transições para o próximo diapositivo na apresentação.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>Simple PowerPoint - executar para ~ 620 segundos

Um utilizador apresenta um ficheiro simple do PowerPoint com cerca de 30 diapositivos utilizando o Microsoft Office PowerPoint no modo de ecrã inteiro. Os diapositivos são mais texto com muitos que o cenário de PowerPoint executivo/complexo e têm mais simples de fundos e imagens (diagramas pretos). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - executar para ~ 250 segundos

Um utilizador navega na web ao utilizar o Internet Explorer. O utilizador navega e desloca-se através de uma mistura de texto, imagens naturais e algumas diagramas esquemáticos. As páginas web armazenados na unidade de disco local do servidor anfitrião da sessão ambiente de trabalho remoto (anfitrião da sessão de RD) como um. Ficheiro MHT. O utilizador desloca utilizar Page Up, Page Down, para cima e para baixo chaves, com intervalos variados para cada chave do tipo de deslocamento:
    
    - Para baixo - 250 batimentos de teclas muito 500 ms
    - Page Up - 36 batimentos de teclas cada ms 1000
    - Inactivo - 75 batimentos de teclas todas as 100 ms
    - Page Down - 20 batimentos de teclas cada 500 ms
    - Até - 120 batimentos de teclas cada 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Documento PDF - simple - executar para ~ 610 segundos
Um utilizador lê e procura um documento PDF de várias formas ao utilizar o Adobe Acrobat Reader. O documento deve ser composto tabelas, gráficos simples e vários tipos de letra do texto. O documento é 35 40 páginas por extenso. O utilizador desloca-se através de com taxas diferentes duas, para trás e reencaminha, tamanhos de zoom diferentes quatro (ajustar à página, ajustar à largura, 100% e outro da sua escolha). O zoom assegura que o texto (tipo de letra) compõe os tamanhos diferentes. Deslocamento não está funcional utilizando a página para cima, Page Down, para cima e para baixo chaves, com intervalos variados para cada deslocamento.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Use - mista - durante ~ 320 segundos de documentos de PDF
Um utilizador lê e procura um documento PDF de várias formas ao utilizar o Adobe Acrobat Reader. O documento é composta por imagens de alta qualidade (incluindo fotografias), tabelas, gráficos simples e vários tipos de letra do texto. O utilizador desloca-se através de com taxas diferentes duas, para trás e reencaminha, tamanhos de zoom diferentes quatro (ajustar à página, ajustar à largura, 100% e outro da sua escolha). O zoom assegura que o texto (tipo de letra) compõe os tamanhos diferentes. Deslocamento não está funcional utilizando a página para cima, Page Down, para cima e para baixo chaves, com intervalos variados para cada deslocamento.

### <a name="flash-video-playback---run-for-180-seconds"></a>Reprodução de vídeo Flash - executar para ~ 180 segundos
Um utilizador vê um vídeo do Adobe Flash codificada incorporado numa página web. A página web é armazenado na unidade de disco rígido local do servidor de sistema anfitrião da sessão. O vídeo é reproduzido no Internet Explorer por um leitor incorporado Plug-in.

Este cenário emula aos utilizadores visualizar rich páginas web conteúdo que contém multimédia. A maior parte dos dados deve bo através de VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Escrever remoto do Word – executar para ~ 1800 segundos
Um utilizador tipos de um documento através de uma sessão RDP. Combinações de teclas são enviadas a partir do lado do cliente através da sessão RDP para um documento no Microsoft Word executar na sessão remota. A taxa de escrita é um caráter cada ms 250 (total 7050 carateres). 

Este é um dos cenários mais comuns para um trabalho de dados de conhecimento. Este cenário testa a capacidade de resposta de um utilizador escrevê num processador de trabalho Moderno. Este cenário é sensível a pares pequenas alterações a utilização de largura de banda.

## <a name="tracking-the-test-results"></a>Os resultados dos testes de controlo

Pode utilizar a seguinte tabela para avaliar os cenários no seu ambiente. Os dados fornecidos abaixo destina-se apenas ilustração - pode ser largamente diferente do que é observar. 

Para simplificar, podemos partem do princípio de que todos os cenários são testados utilizando a mesma resolução de ecrã de 1920x1080 pixels e transportes TCP numa rede com uma latência (atraso) abaixo 200 ms e de rede atenuação na 120 ms + marca de cerca de 1%.

Sobre a tabela:
- **Experiência de média** contém a largura de banda de rede onde produtividade do utilizador não é significativamente afectada mas não excluir ocasionais falhas de áudio ou vídeo. O sistema é recuperar rapidamente tirando partido da lógica dinâmico. A largura de banda estimativas tentativa de rede para garantir a qualidade da experiência do utilizador.
 - **Noticeable problemas (ponto de interrupção)** contém a largura de banda de rede onde os utilizadores poderão reparar em problemas significativos na sua experiência e sua produtividade é afetada para períodos de tempo determinada com uma precisão. Neste momento dos algoritmos RDP estão a debater e não podem garantir da qualidade do utilizador da experiência devido a largura de banda de rede suficiente.
 - **Recomendado** contém a largura de banda de rede recomendada para os bom ou excelente experiência. É normalmente um passo superior ao valor na coluna **média experiência** correspondente.
 - **Notas** incluem observações e comentários.
 
| Teste                  | Experiência média | Problemas evidentes (ponto de interrupção) | Largura de banda de rede recomendada | Notas                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| PPT executivo/complexo | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | Em MB 1/s muitas animações perdem-se                                   |
| Simple PPT            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | Em 256 KB/s os diapositivos carregar com o atraso evidente                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | Na linha 1 MB/s vídeos web são desfocados e irregular e rápido deslocamento tem problemas |
| Simple PDF            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | Em 256 KB/s ocorre o mais tempo a carregar a página                       |
| PDF misto             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | Em 256 KB/s a página leva-o até uma considerável quantidade de tempo a carregar    |
| Flash reprodução de vídeo  | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | Em MB 1/s é granular o vídeo e algumas pacotes desaparecem           |
| Escrever remoto do Word    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | Com 256 KB/s utilizador poderá notar o tempo entre as combinações de teclas             |

Para avaliar a largura de banda de rede por utilizador, crie uma mistura dos cenários acima e a proporção correspondente da largura de banda de rede necessários. Escolha o número mais alto necessário para os cenários. Uma vez que os utilizadores quase nunca utilizam o sistema por si só, considere algumas reserva para utilizadores que trabalhar em simultâneo na mesma rede.
     
## <a name="learn-more"></a>Saiba mais
- [Estimar a utilização de largura de banda de rede RemoteApp do Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - como largura de banda de rede e a qualidade da experiência trabalhar em conjunto?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp banda - orientações gerais (se de que não pode testar o seu próprio)](remoteapp-bandwidthguidelines.md)