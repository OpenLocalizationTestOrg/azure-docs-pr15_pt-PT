<properties 
   pageTitle="Notas de lançamento StorSimple 8000 série atualização 1.2 | Microsoft Azure"
   description="Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série atualização 1.2."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>Notas de lançamento StorSimple 8000 série atualização 1.2  

## <a name="overview"></a>Descrição geral

As notas de lançamento seguintes descrevem as novas funcionalidades e identificam os problemas críticos abrir para StorSimple 8000 série atualização 1.2. Também contêm uma lista de software StorSimple, controlador e atualizações do disco firmware incluídas nesta versão. 

Actualização 1.2 pode ser aplicada a qualquer dispositivo StorSimple em execução das versões lançamento (DG), atualização 0,1, atualização 0,2 mais próximo ou software Update 0,3. Actualização 1.2 não está disponível se o seu dispositivo está a executar atualização 1 ou 1.1 de atualização. Se o seu dispositivo estiver em execução das versões lançamento (DG), utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para o ajudar com a instalação esta atualização.

A tabela seguinte lista as versões de software de dispositivo correspondente ao atualizações 1, 1.1 e 1.2.

| Se executar a atualização... | Esta é a versão do seu software de dispositivo. |
|---------------------|---------------------------------------|
| Atualizar 1.2          | 6.3.9600.17584                        |
| Atualizar 1.1          | 6.3.9600.17521                        |
| Atualizar 1.0          | 6.3.9600.17491                        |

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple. Para obter mais informações, consulte o artigo sobre como [instalar o 1.2 de atualização no seu dispositivo StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Demora cerca de 5 10 horas instala esta atualização (incluindo as atualizações do Windows). 
- Actualização 1.2 tem software, controlador LSI e disco firmware atualizações. Para instalar, siga as instruções em [instalar a atualização 1.2 no seu dispositivo StorSimple](storsimple-install-update-1.md).
- Para novas versões, poderá não conseguir ver atualizações imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Verificar a existência de atualizações em alguns dias novamente como estas ficará disponível mais rapidamente.


## <a name="whats-new-in-update-12"></a>Quais são as novidades no 1.2 de actualização

Estas funcionalidades foram disponibilizadas pela primeira vez com 1 atualização que foi disponibilizados para um conjunto de utilizadores limitado. Com a versão 1.2 de atualização, a maior parte dos utilizadores StorSimple verá as seguintes funcionalidades novas e melhoramentos:

- **Migração da série de 5000 7000 a 8000 dispositivos de série** – nesta versão apresenta uma nova funcionalidade de migração que permite a StorSimple 5000 7000 série aparelho aos utilizadores migrar os respetivos dados para um aparelho físico da série de StorSimple 8000 ou um equipamento virtual. A funcionalidade de migração tem duas propostas de valor de chave:                                                                  

    - **Continuidade de negócios**, ao ativar migração dos dados existentes no eletrodomésticos de série de 5000 7000 para eletrodomésticos 8000 série.
    - **Melhorado funcionalidade ofertas dos eletrodomésticos série 8000**, tal como uma gestão centralizada eficiente das várias eletrodomésticos através do serviço do Gestor de StorSimple, melhor classe de hardware e atualizadas firmware, eletrodomésticos virtuais, mobilidade de dados e funcionalidades no guia futura.

    Consulte o [Guia de migração](http://www.microsoft.com/download/details.aspx?id=47322) para obter detalhes sobre como migrar uma série de 5000 7000 StorSimple para um dispositivo de série 8000. 

- **Disponibilidade no Portal de administração pública do Azure** – StorSimple está agora disponível no portal do Azure Governo. Consulte o artigo como [Implementar um dispositivo StorSimple no Portal de administração pública do Azure](storsimple-deployment-walkthrough-gov.md).

- **Suporte para outros fornecedores de serviço de nuvem** – os outros nuvem fornecedores de serviços suportados são Amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).

- **Atualização para o mais recente de armazenamento de APIs** – com neste lançamento, StorSimple foi atualizado para o serviço de armazenamento do Windows Azure APIs mais recente. Dispositivos de série StorSimple 8000 que estiver a executar o versões de software de pré-lançamento atualização 1 (versão 0.1, 0,2 e 0,3) estão a utilizar versões das APIs do serviço de armazenamento do Azure mais antigas do que 17 de Julho de 2009. Tal como indicado no atualizada [anúncio sobre remoção de versões de serviço de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), por 1 de Agosto de 2016, estas APIs irá preteridos. É obrigatório que se aplicam StorSimple 8000 série atualização 1 antes de 1 de Agosto de 2016. Se conseguir fazê-lo, StorSimple dispositivos deixarão de funcionar corretamente.

- **Suporte para zona redundantes armazenamento (ZRS)** – com a atualização para a versão mais recente do API do armazenamento, a série de StorSimple 8000 irá suporta zona redundantes armazenamento (ZRS) para além de localmente redundantes armazenamento (LRS) e armazenamento Geo redundantes (GRS). Consulte este [artigo sobre as opções de redundância de armazenamento do Windows Azure](../storage/storage-redundancy.md) para obter detalhes ZRS.

- **Avançada experiência de implementação e atualização inicial** – nesta versão, a instalação e processos de atualização foram melhorados. A instalação através do Assistente de configuração é melhorada para fornecer comentários ao utilizador se a configuração de rede e as definições da firewall estão incorretas. Cmdlets de diagnóstico adicionais foi fornecidos para o ajudar com a resolução de problemas de rede do dispositivo. Consulte o [artigo de implementação de resolução de problemas](storsimple-troubleshoot-deployment.md) para obter mais informações sobre os cmdlets diagnóstico novos utilizados para resolver o problema.

## <a name="issues-fixed-in-update-12"></a>Problemas fixados na atualização 1.2

A tabela seguinte fornece um resumo dos problemas que foram fixo na atualizações 1.2, 1.1 e 1.    


| Não. | Funcionalidade | Problema | Fixo na atualização | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell para StorSimple | Quando um utilizador acedida remotamente o dispositivo StorSimple ao utilizar o Windows PowerShell para StorSimple e, em seguida, iniciado o Assistente de configuração, uma falha de sistema Ocorreu o mais cedo IP foram introduzido dados 0. Este erro agora fica fixo na atualização 1. | Atualização 1 | Sim | Sim |
| 2 | Repor fábrica | Em alguns casos, quando executou a reposição de fábrica do mesmo, o dispositivo StorSimple ficou bloqueado e apresentada esta mensagem: **Repor o fábrica está em curso (fase 8)**. Isto aconteceu se premido CTRL + C, enquanto o cmdlet estava em curso. Este erro está fixa.| Atualização 1 | Sim | N |
| 3 | Repor fábrica | Depois de repor uma fábrica de falha de controlador duplo, foram permitida para continuar com o registo de dispositivo. Isto resultou em configuração de um sistema não suportadas. Na atualização 1, é apresentada uma mensagem de erro e registo está bloqueado num dispositivo que tenha uma fábrica falha repor. | Atualização 1 | Sim | N |
| 4 | Repor fábrica | Em alguns casos, alertas de erro de correspondência falso positivo foram elevadas. Alertas de um erro de correspondência incorretos já não serão geradas em dispositivos a atualização 1. | Atualização 1 | Sim | N |
| 5 | Repor fábrica | Se uma fábrica reposição tiver sido interrompida antes de conclusão, o dispositivo introduzido o modo de recuperação e não lhe permite aceder ao Windows PowerShell para StorSimple. Este erro está fixa. | Atualização 1 | Sim | N |
| 6 | Recuperação de falhas | Um erro de recuperação (DR) falhas foi corrigido onde DR ocorrerá uma falha durante a deteção de cópias de segurança do dispositivo de destino. | Atualização 1 | Sim | Sim |
| 7 | Monitorização LEDs | Em certos casos, monitorização LEDs por trás aparelho não indicar Estado correto. O LED azul foi desativado. DADOS 0 e 1 os dados LEDs foram intermitente mesmo quando estas interfaces não foram configuradas. O problema foi corrigido e LED monitorização indicam agora o estado correto.  | Atualização 1 | Sim | N |
| 8 | Monitorização LEDs | Em certos casos, depois de aplicar a atualização 1, a luz azul no controlador ativo desativada tornando disco rígido identificar o controlador de ativo. Este problema foi corrigido neste lançamento de patches.| Atualizar 1.2 | Sim | N |
| 9 | Interfaces de rede | Em versões anteriores, um dispositivo StorSimple configurado com um gateway não encaminháveis poderia ficar offline. Neste lançamento, a métrica do encaminhamento para os dados 0 tiver sido efetuadas mais baixo; Por conseguinte, mesmo que existam outras interfaces de rede com capacidade de nuvem, todo o tráfego de nuvem do dispositivo será encaminhado através de dados 0. | Atualização 1 | Sim | Sim | 
| 10 | Cópias de segurança | Um erro na atualização 1 que provocam a falha após a 24 dias as cópias de segurança de causaram foi corrigido na versão correcção 1.1 de atualização. | Atualizar 1.1 | Sim | Sim |
| 11 | Cópias de segurança | Um erro em versões anteriores resultou no desempenho fraco para instantâneos nuvem com taxas de alteração de baixa. Este erro foi corrigido neste lançamento de patches.| Atualizar 1.2 | Sim | Sim |
| 12 | Atualizações | Um erro na atualização 1 que comunicado uma falha na atualização e causados os controladores iniciar o modo de recuperação, foi corrigido neste lançamento de patches.| Atualizar 1.2 | Sim | Sim |


## <a name="known-issues-in-update-12"></a>Problemas conhecidos nas atualização 1.2

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Solução/comentários | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. | Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 3 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador. | Sim | Sim |
| 4 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada. Dispositivo activação pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, estes contentores de volume irão aparecer ou comportam-se de forma diferente quando vê-los no portal do Azure clássico. | | Sim | N |
| 5 | Instalação | Durante a StorSimple placa para a instalação do SharePoint, tem de fornecer um IP de dispositivo para que a instalação concluir com êxito.    | | Sim | N |
| 6 | Proxy da Web | Se a sua configuração do proxy web tiver HTTPS como o protocolo especificado, em seguida, a serviço de dispositivos de comunicação, será afectada e o dispositivo irão offline. Pacotes de suporte também serão geradas no processo de consumir recursos significativos no seu dispositivo. | Certifique-se de que o URL do web proxy HTTP, como o protocolo especificado. Para obter mais informações, consulte [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | N |
| 7 | Proxy da Web | Se configurar e activar o proxy web num dispositivo registado, terá de reiniciar o controlador de ativo no seu dispositivo. | | Sim | N |
| 8 | Latência alta na nuvem e elevada carga de trabalho e/s | Quando o seu dispositivo StorSimple encontra uma combinação de latências nuvem muito alta (ordem de segundos) e a carga de trabalho e/s alta, os volumes de dispositivo vão para o estado degradado e o/s pode falhar com um erro "dispositivo não está pronto". | É necessário reiniciar o computador os controladores do dispositivo ou executar um dispositivo activação pós-falha para recuperar desta situação manualmente. | Sim | N |
| 9 | Azure PowerShell | Quando utilizar o cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential & #124; Select-Object - aguardar pela primeira vez 1 -** para selecionar o primeiro objecto para que possa criar um novo objeto **VolumeContainer** , o cmdlet devolve todos os objetos existentes. | Moldar o cmdlet entre parênteses da seguinte forma: **(Get-Azure StorSimpleStorageAccountCredential) & #124; Select-Object - aguardar pela primeira vez 1 -** | Sim | Sim |
| 10| Migração | Quando vários contentores de volume são transmitidos migração, ATE para cópia de segurança mais recente é preciso apenas para o primeiro contentor de volume. Para além disso, paralela migração será iniciada após as cópias de segurança em primeiro lugar 4 no primeiro contentor volume são migradas. | Recomendamos que migrar um contentor de volume cada vez. | Sim | N |
| 11| Migração | Depois da restaurar, volumes não são adicionados à política de cópia de segurança ou ao grupo de disco virtual. | Terá de adicionar estes volumes a uma política de cópia de segurança para poder criar cópias de segurança. | Sim | Sim |
| 12| Migração | Depois da migração estiver concluída, o dispositivo de série de 5000/7000 não tem de aceder os contentores de dados migrados. | Recomendamos que elimine os contentores de dados migrados após a migração completas e consolidada. | Sim | N |
| 13| Clonar e DR | Um dispositivo de StorSimple em execução atualização 1 não é possível clonar ou efectuar a recuperação de falhas para um dispositivo de executar 1 software anterior à atualização. | Terá de atualizar o dispositivo de destino para atualizar 1 para permitir que estas operações | Sim | Sim |
| 14 | Migração | Cópia de segurança de configuração de migração poderá falhar num dispositivo série 5000 7000 quando existem grupos de volume com sem volumes associados. | Eliminar todos os grupos de volume vazia com sem volumes associados e, em seguida, repita a cópia de segurança de configuração.| Sim | N |

## <a name="physical-device-updates-in-update-12"></a>Atualizações de dispositivo físico no 1.2 de actualização

Se atualizar patches 1.2 é aplicado a um dispositivo físico (em execução versões antes de atualizar 1), irá alterar a versão do software para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controlador de e firmware atualizações no 1.2 de actualização

Nesta versão atualiza o controlador e o firmware disco no seu dispositivo.
 
- Para mais informações sobre a atualização do controlador de SA, consulte o artigo [Atualizar 1 para os controladores LSI SA no Microsoft Azure StorSimple aparelho](https://support.microsoft.com/kb/3043005). 

- Para mais informações sobre o firmware disco atualizar, consulte o artigo [firmware disco actualizações 1 para o Microsoft Azure StorSimple aparelho](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Atualizações de dispositivo virtual na atualização 1.2

Não é possível aplicar esta atualização para o dispositivo virtual. Novos dispositivos virtuais terá de ser criado. 

## <a name="next-steps"></a>Próximos passos

- [Instalar 1.2 de atualização no seu dispositivo](storsimple-install-update-1.md).
 
