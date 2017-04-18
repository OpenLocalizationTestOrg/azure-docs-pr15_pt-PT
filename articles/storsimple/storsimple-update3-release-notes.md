<properties 
   pageTitle="Notas de lançamento StorSimple 8000 série atualização 3 | Microsoft Azure"
   description="Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série atualização 3."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>Notas de lançamento StorSimple 8000 série atualização 3  

## <a name="overview"></a>Descrição geral

As notas de lançamento seguintes descrevem as novas funcionalidades e identificam os problemas críticos abrir para StorSimple 8000 série atualização 3. Também contêm uma lista das atualizações de software de StorSimple incluídas nesta versão. 

Actualização 3 pode ser aplicada a qualquer dispositivo StorSimple experimentar das versões lançamento (DG) ou uma atualização 0,1 2.2 de atualização. A versão de dispositivo associada 3 de atualização é 6.3.9600.17759.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

>[AZURE.IMPORTANT]
> 
> - Actualização 3 tem software de dispositivo, controlador LSI e firmware e Storport e Spaceport atualizações. Demora cerca de 2 de 1,5 horas instala esta atualização. 

> - Para novas versões, poderá não conseguir ver atualizações imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Aguarde alguns dias e, em seguida, verificar a existência de atualizações novamente como estes irão tornar-se disponível mais rapidamente.


## <a name="whats-new-in-update-3"></a>Quais são as novidades no 3 de actualização

As seguintes melhorias chaves e correções de erros foram efetuadas no 3 de atualização.

 
- **Automatizadas espaço valorização alterações** – começando 3 de atualização, dos algoritmos valorização espaço executar no modo de espera controlador do sistema que resulta em execução mais rápida. Para mais informações sobre as portas que são necessários para trabalhar com valorização de espaço, consulte os [requisitos da rede StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Melhorias no desempenho** – atualização 3 melhorou desempenho de leitura e escrita para a nuvem.

- **Melhorias relacionadas com a migração** – nesta versão, várias correções de erros e melhorias foram concluído para a funcionalidade de migração a partir de dispositivos de série de 5000/7000 para 8000 dispositivos de série. Para obter mais informações sobre como utilizar a funcionalidade de migração, aceda a [migração a partir do dispositivo de série de 5000/7000 para o dispositivo de série 8000](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- **Monitorização correções relacionadas** - neste lançamento, erros relacionados com a monitorização de gráficos, dashboard de serviço e dashboard dispositivo foram corrigidos.


## <a name="issues-fixed-in-update-3"></a>Problemas fixados na atualização 3

As tabelas seguintes fornece um resumo dos problemas que foram fixo na atualização 3.    

| N | Funcionalidade                                    | Problema                                                                                                                                                                                                                                                                                        | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Migração de dados do lado do anfitrião                      | Na versão anterior, StorSimple nuvem aparelho foi ficar offline durante uma migração de dados do lado do anfitrião. Este problema for resolvido nesta versão.                                                | N                        | Sim                        |
| 2  | Volumes localmente afixados                     | Na versão anterior, que ocorreram problemas relacionados com falhas, falhas de conversão de volume e falhas datapath para volumes localmente afixados e/s. Estes problemas foram causados de raiz e fixa nesta versão.                | Sim                        | N                       |
| 3  | Monitorização                                    | Existem vários problemas relacionados com relatórios unidades e assim como gráficos de dashboard do dispositivo onde informações incorretas foram apresentadas para localmente afixados volumes de monitorização. Estes problemas são corrigidos nesta versão. | Sim                         | N                       |
| 4  | E de escritas grossa/s                          | Quando utilizar StorSimple para das cargas de trabalho que envolvam escritas muito carregadas, o utilizador seria ocorrer um erro pouco frequente onde o conjunto de trabalho foi a ser camado para a nuvem. Este erro é corrigido nesta versão. | Sim                        | Sim                       |
| 5  | Cópia de segurança                                     | Em certos casos raros, nas versões anteriores do software, quando o utilizador tenha apontado uma cópia de segurança de uma clonar remoto, funcionam erros na nuvem e a operação seria erro saída. Neste lançamento, o problema é corrigido e a operação for concluída com êxito.             | Sim                        | Sim                       |
| 6  | Política de cópia de segurança                                     | Em certos casos raros, as versões anteriores do software, Ocorreu um erro relacionado com a eliminação de política de cópia de segurança. Este problema for resolvido nesta versão.       | Sim                        | Sim                       |



## <a name="known-issues-in-update-3"></a>Problemas conhecidos nas atualização 3

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários / solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento subscreve offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. | Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 3 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador.|  | Sim | Sim |
| 4 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada. Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, estes contentores de volume irão aparecer ou comportam-se de forma diferente quando vê-los no portal do Azure clássico. | | Sim | N |
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
| 20 |Volumes localmente afixados | Se tentar converter um volume em camadas (criado e clonado com 1.2 de atualização ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha na nuvem, em seguida, o clone(s) pode ficar danificado.| Este problema ocorre apenas com volumes que foram criadas e clonado com actualizar pré 2.1 software. Isto deve ser um pouco frequente cenário.|
| 21 | Conversão de volume | Não são atualizadas ACRs anexados a um volume enquanto uma conversão de volume está em curso (camadas para localmente afixados ou vice versa). Atualizar as ACRs poderá resultar em danos nos dados. | Se for necessário, atualize ACRs antes da conversão de volume e não faça quaisquer outras atualizações ACR enquanto a conversão está em curso. |
| 22 | Atualizações | Quando aplicar 3 de atualização, a página de **Manutenção** no portal do Azure clássica será apresentada a seguinte mensagem relacionados com Update 2 - "StorSimple 8000 série Update 2 inclui a capacidade para a Microsoft para importante recolher informações de registo do seu dispositivo quando poderemos detectar potenciais problemas". Isto é induzir em erro que indica que o dispositivo está a ser atualizado para Update 2. Depois do dispositivo está succeesfully atualizada para a atualização 3, vai desaparecer esta mensagem. | Este comportamento será corrigido num lançamento futuro. | Sim | N |


## <a name="controller-and-firmware-updates-in-update-3"></a>Controlador de e firmware atualizações no 3 de actualização

Nesta versão tem LSI controlador e firmware atualizações. Para mais informações sobre como instalar o controlador de LSI e atualizações firmware, consulte o artigo [instalar actualizações 3](storsimple-install-update-3.md) no seu dispositivo StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Atualizações de dispositivo virtual no 3 de actualização

Esta atualização não pode ser aplicada ao StorSimple nuvem aparelho (também conhecido como o dispositivo virtual). Novos dispositivos virtuais terá de ser criado. 


## <a name="next-step"></a>Passo seguinte

Saiba como [instalar o 3 de atualização](storsimple-install-update-3.md) no seu dispositivo StorSimple.
