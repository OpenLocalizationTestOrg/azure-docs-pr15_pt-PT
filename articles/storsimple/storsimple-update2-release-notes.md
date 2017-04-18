<properties 
   pageTitle="Notas de lançamento StorSimple 8000 série Update 2 | Microsoft Azure"
   description="Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série Update 2."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de lançamento StorSimple 8000 série Update 2  

## <a name="overview"></a>Descrição geral

As notas de lançamento seguintes descrevem as novas funcionalidades e identificam os problemas críticos abrir para StorSimple 8000 série Update 2. Também contêm uma lista do software StorSimple, controlador e atualizações do disco firmware incluídas nesta versão. 

Actualização 2 pode ser aplicada a qualquer dispositivo StorSimple experimentar das versões lançamento (DG) ou uma atualização 0,1 1.2 de atualização. A versão de dispositivo associada Update 2 é 6.3.9600.17673.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

>[AZURE.IMPORTANT]
> 
- Demora cerca de 7 de 4 horas instala esta atualização (incluindo as atualizações do Windows). 
- Actualização 2 tem software, controlador LSI e SSD firmware atualizações.
- Para novas versões, poderá não conseguir ver atualizações imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Aguarde alguns dias e, em seguida, verificar a existência de atualizações novamente como estes irão tornar-se disponível mais rapidamente.


## <a name="whats-new-in-update-2"></a>Quais são as novidades no Update 2

Actualização 2 apresenta as novas funcionalidades.

- **Localmente afixados volumes** – em versões anteriores da série de StorSimple 8000, blocos de dados foram camadas para a nuvem com base em utilização. Não tiver nenhuma forma de garante que os blocos de seriam permaneça no local. No Update 2, quando cria um volume, pode designar um volume como dados localmente afixados e principais a partir desse volume não irão ser camados para a nuvem. Instantâneos dos volumes localmente afixados ainda serão copiados para a nuvem para cópia de segurança para que possa ser utilizada na nuvem para fins de recuperação de mobilidade e falhas de dados. Para além disso, pode alterar o tipo de volume (ou seja, converter camadas volumes de volumes localmente afixados e camadas de volumes de converter localmente afixado). 

- **Melhorias de dispositivo virtual StorSimple** – anteriormente, a série de StorSimple 8000 posicionado o dispositivo virtual como uma solução de recuperação ou desenvolvimento/testar falhas. Ocorreu apenas um modelo de dispositivo virtual (modelo 1100). Actualização 2 introduz dois modelos de dispositivo virtual: 

     - 8010 (anteriormente chamado a 1100) – não altere; tem uma capacidade de 30 TB e utiliza o Azure armazenamento padrão.
     - 8020 – tem uma capacidade de 64 TB e utiliza o armazenamento do Azure Premium para um desempenho melhorado.

    Existe um único VHD para ambos os modelos de dispositivo virtual (8010/8020). Quando inicia pela primeira vez que o dispositivo virtual, Deteta os parâmetros de plataforma e aplica-se a versão do modelo correto.

- **Melhorias de rede** -Update 2 contém as seguintes melhorias de redes:

     - NIC vários pode ser ativado para a nuvem para que activação pós-falha pode ocorrer se uma NIC falhar.
     - Melhoramentos de encaminhamento, métricas fixos para a nuvem ativada blocos.
     - Repetir online de recursos falhados antes de um activação pós-falha.
     - Novas alertas para falhas no serviço.

- **Atualizar melhorias** – de actualização 1.2 e versões anterior, a série de StorSimple 8000 foi atualizado através de dois canais: Windows Update para clusters, iSCSI e assim sucessivamente e o Microsoft Update para binários e firmware.
    Update 2 utiliza o Microsoft Update para todos os pacotes de actualização de. Isto deve conduzir a menos tempo correcção ou efetuar activações pós-falha. 

- **Atualizações de firmware** – o seguinte actualizações de firmware estão incluídas:
    - LSI: lsi_sas2.sys versão do produto 2.00.72.10
    - Apenas SSD (sem actualizações de disco): XMGG, XGEG, KZ50, F6C2 e VR08

- **Suporte pro-activos** – Update 2 permite à Microsoft separar informações adicionais de diagnóstico do dispositivo. Quando a nossa equipa de operações identifica os dispositivos que estão a ter problemas, estamos a melhor equipados para recolher informações a partir do dispositivo e diagnosticar problemas. **Ao aceitar Update 2, permitir-nos fornecer este suporte pro-activos**.    
 

## <a name="issues-fixed-in-update-2"></a>Problemas fixados na Update 2

As tabelas seguintes fornece um resumo dos problemas que foram introduzidas no atualizações 2.    

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfaces de rede | Após uma atualização para atualização 1, o serviço de Gestor de StorSimple comunicou que as portas dados2 e Data3 Ocorreu uma falha num controlador. Este problema foi resolvido. | Sim | N |
| 2 | Atualizações | Após uma atualização para atualização 1, alertas sonoros alarme ocorreram no portal do Azure clássico em múltiplos dispositivos. Este problema foi resolvido. | Sim | N |
| 3 | Autenticação Openstack | Ao utilizar Openstack como o seu fornecedor de serviços na nuvem, poderá receber um erro que foi demasiado tempo a cadeia de autenticação na nuvem. Este erro foi corrigido. | Sim | N |


## <a name="known-issues-in-update-2"></a>Problemas conhecidos do Update 2

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários / solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento subscreve offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. | Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 3 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador.|  | Sim | Sim |
| 4 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada. Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, nestes contentores de volume serão apresentada ou comportam-se de forma diferente quando visualizá-los no portal do Azure clássico. | | Sim | N |
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
| 15 | Os cmdlets do PowerShell Azure e volumes localmente afixados | Não é possível criar um volume localmente afixado via os cmdlets do Azure PowerShell. (Qualquer volume que criar através do Azure PowerShell irá camada.) |Utilize sempre o serviço do Gestor de StorSimple para configurar volumes localmente afixados.| Sim | N |
| 16 |Espaço disponível para volumes localmente afixados | Se eliminar um volume localmente afixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gestor de StorSimple atualiza o local espaço disponível (Product Key) a cada hora.| Aguarde para uma hora antes de tentar criar o novo volume. | Sim | N |
| 17 | Volumes localmente afixados | Tarefa de restauro expõe a cópia de segurança temporário instantâneo no catálogo de cópia de segurança, mas apenas para a duração da tarefa restaurar. Para além disso,-expõe um grupo de disco virtual com prefixo **tmpCollection** na página **Políticas de cópia de segurança** , mas apenas para a duração da tarefa restaurar. | Este comportamento pode ocorrer se a tarefa de restaurar apenas localmente tem afixados volumes ou uma mistura de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, este comportamento não irá ocorrer. É necessária o sem intervenção do utilizador. | Sim | N |
| 18 | Volumes localmente afixados | Se cancelar uma tarefa de restaurar e um controlador activação pós-falha ocorre imediatamente posteriormente, a tarefa de restauro mostrará **falhou** em vez de **Cancelado**. Se uma tarefa de restaurar falhar e um controlador activação pós-falha ocorre imediatamente posteriormente, irá mostrar a tarefa de restauro **Cancelado** em vez de **Falha**. | Este comportamento pode ocorrer se a tarefa de restaurar apenas localmente tem afixados volumes ou uma mistura de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, este comportamento não irá ocorrer. É necessária o sem intervenção do utilizador. | Sim | N |
| 19 |Volumes localmente afixados | Se cancelar uma tarefa de restaurar ou se um restauro falha e, em seguida, ocorre um controlador activação pós-falha, é apresentada uma tarefa de restauro adicionais na página **tarefas** . | Este comportamento pode ocorrer se a tarefa de restaurar apenas localmente tem afixados volumes ou uma mistura de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, este comportamento não irá ocorrer. É necessária o sem intervenção do utilizador. | Sim | N |
| 20 |Volumes localmente afixados | Se tentar converter um volume em camadas (criado e clonado com 1.2 de atualização ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha na nuvem, em seguida, o clone(s) pode ficar danificado.| Este problema ocorre apenas com volumes que foram software criado e clonado com pré-Update 2. Isto deve ser um pouco frequente cenário.|
| 21 | Conversão de volume | Não são atualizadas ACRs anexados a um volume enquanto uma conversão de volume está em curso (camadas para localmente afixados ou vice versa). Atualizar as ACRs poderá resultar em danos nos dados. | Se for necessário, atualize ACRs antes da conversão de volume e não faça quaisquer outras atualizações ACR enquanto a conversão está em curso. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Controlador de e firmware atualizações no Update 2

Nesta versão atualiza o controlador e o firmware disco no seu dispositivo.
 
- Para mais informações sobre o firmware LSI atualizar, consulte o artigo da base de dados de conhecimento da Microsoft 3121900. 
- Para mais informações sobre o firmware disco atualizar, consulte o artigo da base de dados de conhecimento da Microsoft 3121899.
 
## <a name="virtual-device-updates-in-update-2"></a>Atualizações de dispositivo virtual no Update 2

Não é possível aplicar esta atualização para o dispositivo virtual. Novos dispositivos virtuais terá de ser criado. 

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar o Update 2](storsimple-install-update-2.md) no seu dispositivo StorSimple.
