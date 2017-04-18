<properties 
   pageTitle="Notas de lançamento StorSimple 8000 série atualização 2.2 | Microsoft Azure"
   description="Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série atualização 2.2."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de lançamento StorSimple 8000 série atualização 2.2  

## <a name="overview"></a>Descrição geral

As notas de lançamento seguintes descrevem as novas funcionalidades e identificam os problemas críticos abrir para StorSimple 8000 série atualização 2.2. Também contêm uma lista das atualizações de software de StorSimple incluídas nesta versão. 

Atualização 2.2 pode ser aplicada a qualquer dispositivo StorSimple experimentar das versões lançamento (DG) ou uma atualização 0,1 2.1 de atualização. A versão de dispositivo associada 2.2 atualização é 6.3.9600.17708.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

>[AZURE.IMPORTANT]
> 
> - Actualização 2.2 tem atualizações de software apenas. Demora cerca de 2 de 1,5 horas instala esta atualização. 

> - Se estiver a executar 2.1 de atualização, recomendamos que aplicar atualização 2.2 mais cedo possível.

> - Para novas versões, poderá não conseguir ver atualizações imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Aguarde alguns dias e, em seguida, verificar a existência de atualizações novamente como estes irão tornar-se disponível mais rapidamente.


## <a name="whats-new-in-update-22"></a>Quais são as novidades no 2.2 de actualização

As seguintes melhorias chaves foram efetuadas no 2.2 de atualização.

 
- **Automatizadas espaço valorização otimização** – quando são eliminados dados de volumes de pequenos aprovisionados, os blocos de armazenamento não utilizadas tem de ser recuperadas. Nesta versão melhorou o processo de valorização espaço a partir da nuvem que resulta no espaço não utilizado está a ficar disponível mais rápido em comparação com as versões anteriores.


- **Melhorias no desempenho instantâneo** – atualização 2.2 melhorou o tempo para processar uma nuvem instantâneo em determinados cenários onde grandes volumes estão a ser utilizados e há mínimas para vasilha sem dados. Cenário que iria beneficiem das vantagens deste melhoramento seria os volumes de arquivo.


- **Reunir protecção de suporte pacote** – foram melhorias na forma como o pacote de suporte reuniu e carregado nesta versão. 


- **Atualizar as melhorias de fiabilidade** – nesta versão tem correções de erros que resultam uma fiabilidade melhorada de atualização.

  
 

## <a name="issues-fixed-in-update-22"></a>Problemas fixados na 2.2 de actualização

As tabelas seguintes fornece um resumo dos problemas que foram fixo na atualizações 2.2 e 2.1.    

| N | Funcionalidade                                    | Problema                                                                                                                                                                                                                                                                                        | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Desempenho do sistema anfitrião                      | Na versão anterior, problemas de desempenho do lado do anfitrião foram observados durante a criação de um volume localmente afixada e durante a conversão de um volume em camadas para um localmente afixada volume. Estes problemas são corrigidos nesta versão assim que resulta numa melhoria no desempenho anfitrião durante os procedimentos de criação e de conversão de volume.                                                                        | Sim                        | N                        |
| 2  | Volumes localmente afixados                     | Em casos raros, o sistema seria falhar ao criar um volume localmente afixado. Este erro foi corrigido nesta versão.                                                                                                                                                               | Sim                        | N                        |
| 3  | Tiering                                    | Que ocorreram esporádica causa uma falha quando os metadados para eletrodomésticos de nuvem StorSimple (8010 e 8020) camadas para a nuvem. Este problema for resolvido nesta versão.                                                                                                                              | N                         | Sim                       |
| 4  | Criação de instantâneo                          | Ocorreram problemas relacionados com a criação de instantâneos utilizarão em cenários com grandes volumes e mínima para vasilha sem dados. Estes problemas são corrigidos nesta versão.                                                                                                                 | Sim                        | Sim                       |
| 5  | Autenticação Openstack                   | Ao utilizar Openstack enquanto o fornecedor de serviços na nuvem, o utilizador faria ocorrer um pouco frequente erros relacionados com a autenticação onde o analisador JSON resulta numa falha. Este erro é corrigido nesta versão.                                                                                                                              | Sim                        | N                        |
| 6  | Cópia do lado do anfitrião                             | Em versões anteriores do software, um pouco frequente erros relacionados com a temporização de ODX foram visto ao copiar os dados a partir de um volume para outro volume. O resultado seria um controlador activação pós-falha e o sistema potencialmente conseguiu iniciar o modo de recuperação. Este erro foi corrigido nesta versão. | Sim                        | N       |
| 7  | Instrumentação de gestão do Windows (WMI) | Em versões anteriores do software, foram várias instâncias de falha de proxy web com a exceção "<ManagementException> falha do carregamento fornecedor". Este erro foi atribuído a uma perda de memória WMI e está fixa.                                                               | Sim                        | N                        |
| 8  | Actualização                                     | Em certos casos raros, nas versões anteriores do software, o utilizador recebida "CisPowershellHcsscripterror" ao tentar digitalize ou instalar atualizações. Este problema for resolvido nesta versão.                                                                                        | Sim                        | Sim                       |
| 9  | Pacote de suporte                            | Neste lançamento, ter sido melhoramentos no modo como o pacote de suporte reuniu e carregado.                                                                                                                                                                                                      | Sim                        | Sim                                    |


## <a name="known-issues-in-update-22"></a>Problemas conhecidos nas atualização 2.2

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
| 20 |Volumes localmente afixados | Se tentar converter um volume em camadas (criado e clonado com 1.2 de atualização ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha na nuvem, em seguida, o clone(s) pode ficar danificado.| Este problema ocorre apenas com volumes que foram criadas e clonado com actualizar pré 2.1 software. Isto deve ser um pouco frequente cenário.|
| 21 | Conversão de volume | Não são atualizadas ACRs anexados a um volume enquanto uma conversão de volume está em curso (camadas para localmente afixados ou vice versa). Atualizar as ACRs poderá resultar em danos nos dados. | Se for necessário, atualize ACRs antes da conversão de volume e não faça quaisquer outras atualizações ACR enquanto a conversão está em curso. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controlador de e firmware atualizações no 2.2 de actualização

Nesta versão tem as atualizações de software. No entanto, se estiver a atualizar a partir de uma versão anterior ao Update 2, terá de instalar o controlador Storport, Spaceport e (em alguns casos) no disco firmware as atualizações no seu dispositivo.
 
Para mais informações sobre como instalar o controlador, Storport, Spaceport e atualizações do disco firmware, consulte o artigo [instalar 2.2 de atualização](storsimple-install-update-21.md) no seu dispositivo StorSimple.

 
## <a name="virtual-device-updates-in-update-22"></a>Atualizações de dispositivo virtual no 2.2 de actualização

Não é possível aplicar esta atualização para o dispositivo virtual. Novos dispositivos virtuais terá de ser criado. 

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar a atualização 2.2](storsimple-install-update-21.md) no seu dispositivo StorSimple.
