<properties
    pageTitle="Azure replicação de armazenamento | Microsoft Azure"
    description="Dados na sua conta de armazenamento do Microsoft Azure são replicados para durabilidade e elevada disponibilidade. Opções de replicação incluem armazenamento localmente redundante (LRS), armazenamento zona redundantes (ZRS), armazenamento geo redundantes (GRS) e armazenamento geo redundantes acesso de leitura (GRS RT)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Azure replicação de armazenamento

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir durabilidade e disponibilidade de alta. A replicação copia os seus dados, no Centro de dados do mesmo ou a um segundo Centro de dados, consoante a opção replicação que escolher. A replicação protege os seus dados e preserva o tempo de cima aplicação trabalho falhas de hardware breves. Se os seus dados for de replicadas para um segundo Centro de dados, que também protege os seus dados relativamente a uma falha grave na localização principal.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de nível de serviço (SLA) para o armazenamento do](https://azure.microsoft.com/support/legal/sla/storage/) mesmo no falhas. Consulte o artigo que SLA para obter informações sobre o armazenamento do Windows Azure garantias para durabilidade e disponibilidade. 

Quando cria uma conta de armazenamento, pode selecionar uma das seguintes opções de replicação:  

- [Armazenamento localmente redundante (LRS)](#locally-redundant-storage)
- [Armazenamento de zona redundantes (ZRS)](#zone-redundant-storage)
- [Armazenamento geo redundantes (GRS)](#geo-redundant-storage)
- [Acesso de leitura armazenamento geo redundantes (GRS RT)](#read-access-geo-redundant-storage)

Acesso de leitura armazenamento geo redundantes (GRS RT) é a opção predefinida quando cria uma nova conta de armazenamento.

A tabela seguinte fornece uma descrição geral rápida das diferenças entre LRS, ZRS, GRS e RT-GRS, enquanto secções subsequentes endereço cada tipo de replicação mais detalhadamente.

| Estratégia de replicação                                                               | LRS | ZRS | GRS | GRS RT |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Dados são replicados através de vários centros de dados.                                     | N  | Sim | Sim | Sim    |
| Podem ser lidos dados a partir da localização secundária e desde a localização principal. | N  | N  | N  | Sim    |
| Número de cópias de dados mantidas em nós separados.                             | 3   | 3   | 6   | 6      |

Consulte o artigo [Azure preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/) para estimados para as opções de redundância diferente.

>[AZURE.NOTE] Armazenamento de Premium suporta apenas localmente redundante armazenamento (LRS). Para obter informações sobre o armazenamento de Premium, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

Armazenamento localmente redundante (LRS) replica os seus dados três vezes dentro de uma unidade de escala de armazenamento que está alojado num centro de dados na região em que criou a sua conta de armazenamento. Um pedido de escrita com êxito devolve apenas depois de ter sido escrito para todas as três réplicas. Estas três réplicas residem na falhas separada domínios e atualização dentro de unidade de escala de um armazenamento. 

Uma unidade de escala de armazenamento é uma coleção de esquis de nós de armazenamento. Um domínio de falhas (DF) é um grupo de nós que representam uma unidade física de falha e pode ser considerado como nós pertencentes ao mesmo bastidor físico. Um domínio de atualização (UD) é um grupo de nós que são atualizados em conjunto durante o processo de uma atualização do serviço (implementação). As três réplicas são distribuir por UDs e FDs dentro de uma unidade de escala de armazenamento para se certificar de que os dados estão disponíveis mesmo se uma falha de hardware impactos um único bastidor ou quando os nós são actualizados durante uma implementação. 

LRS é a opção de custo mais baixa e oferece durabilidade, pelo menos, comparado com outras opções. Eventualidade de um centro de dados nível (fire, transbordo, etc.), todas as três réplicas poderão ser perdidas ou irrecuperável. Para mitigar este risco Geo redundantes armazenamento (GRS) é recomendado para a maioria das aplicações.

Armazenamento localmente redundante ainda pode ser conveniente em determinados cenários: 

- Fornece mais alta largura de banda máxima das opções de replicação de armazenamento do Windows Azure.

- Se a sua aplicação armazena os dados que podem ser recriados facilmente, pode optar por permitir LRS.

- Algumas aplicações estão limitadas a replicação de dados apenas dentro de um país devido a requisitos de governação de dados. Pode ser uma região emparelhada noutro país; consulte o artigo [regiões Azure](https://azure.microsoft.com/regions/) para obter informações sobre os pares de região.

## <a name="zone-redundant-storage"></a>Armazenamento de zona redundantes

Armazenamento de zona redundantes (ZRS) replica modo assíncrono os seus dados através de centros de dados dentro de um ou dois regiões além armazenar três réplicas semelhantes ao LRS, assim, fornecendo durabilidade mais elevada que LRS. Dados armazenados num ZRS são resistentes mesmo se o Centro de dados principal não está disponível ou irrecuperável.
Clientes que pretenda utilizar ZRS devem ter em mente que: 

- ZRS só está disponível para blobs bloco nas contas de armazenamento geral finalidade e são suportados apenas nas versões de serviço de armazenamento de 2014-02-14 e posterior. 

- Uma vez que replicação assíncrona envolve um atraso, um local eventualidade é possível que as alterações que ainda não foi replicadas secundária serão perdidas se os dados não podem ser recuperados a página principal.

- A réplica poderá não estar disponível até que a Microsoft inicia activação pós-falha para secundária.

- Contas ZRS não não possível converter mais tarde LRS ou GRS. Do mesmo modo, uma conta existente do LRS ou GRS não é possível converter a uma conta ZRS.

- Contas ZRS não possui métricas ou funcionalidade de registo. 

## <a name="geo-redundant-storage"></a>Armazenamento geo redundantes

Armazenamento geo redundantes (GRS) replica os seus dados para uma região secundária que seja centenas de milhas afastando-o a região principal. Se a sua conta de armazenamento tiver GRS ativado, os seus dados são resistentes mesmo no caso de uma falha de regional concluída ou uma falhas em que a região principal não é recuperável.

Para uma conta de armazenamento com GRS ativado, uma atualização pela primeira vez está empenhada em da região principal, onde é replicada três vezes. Em seguida, a atualização é replicada modo assíncrono à região de secundário, onde também é replicada três vezes. 

Com GRS regiões principais e secundárias gerir réplicas em vários domínios falhas separadas e actualizar domínios dentro de uma unidade de escala de armazenamento, tal como descrito com LRS.

Considerações sobre:

- Uma vez que replicação assíncrona envolve um atraso, um eventualidade regionais é possível que as alterações que ainda não foi replicadas à região de secundário serão perdidas se os dados não podem ser recuperados da região do principal.

- A réplica não está disponível a menos que a Microsoft inicia activação pós-falha à região de secundário.

- Se pretender que uma aplicação ler a partir da região secundária o utilizador deve ativar GRS RT. 

Quando cria uma conta de armazenamento, selecionar a região principal para a conta. Região de secundário é determinado com base na região do principal e não pode ser alterado. A tabela seguinte mostra os pares de região principais e secundários.

| Principal             | Secundário           |
|---------------------|---------------------|
| América do Norte Central (EUA)    | Sul Central (EUA)    |
| Sul Central (EUA)    | América do Norte Central (EUA)    |
| Leste dos EUA             | Ocidental dos e.u.a.             |
| Ocidental dos e.u.a.             | Leste dos EUA             |
| Leste dos EUA 2           | Central (EUA)          |
| Central (EUA)          | Leste dos EUA 2           |
| Europa Norte        | Europa Ocidental         |
| Europa Ocidental         | Europa Norte        |
| Sudeste asiático     | Este asiático           |
| Este asiático           | Sudeste asiático     |
| Texto da Ásia China          | China norte         |
| China norte         | Texto da Ásia China          |
| Japão leste          | Japão oeste          |
| Japão oeste          | Japão leste          |
| Sul do Brasil        | Sul Central (EUA)    |
| Leste Austrália      | Sudeste Austrália |
| Sudeste Austrália | Leste Austrália      |
| Sul Índia         | Índia Central       |
| Índia Central       | Sul Índia         |
| Iowa de administração pública dos e.u.a.         | Virginia de administração pública dos e.u.a.     |
| Virginia de administração pública dos e.u.a.     | Iowa de administração pública dos e.u.a.         |
| Canadá Central      | Leste Canadá          |
| Leste Canadá         | Canadá Central      |
| Reino Unido oeste             | Sul Reino Unido            |
| Sul Reino Unido            | Reino Unido oeste             |
| Alemanha Central     | Alemanha no terreno da Nordeste   |
| Alemanha no terreno da Nordeste   | Alemanha Central     |
| US Ocidental 2           | Central Ocidental (EUA)     |
| Central Ocidental (EUA)     | US Ocidental 2           |

Para obter informações atualizadas sobre regiões suportados por Azure, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Armazenamento de geo redundantes de acesso de leitura

Acesso de leitura armazenamento geo redundantes (GRS RT) maximiza a disponibilidade para a sua conta de armazenamento, ao fornecer acesso só de leitura para os dados na localização secundário, para além da replicação entre as duas regiões fornecida pela GRS. 

Quando ativa o acesso só de leitura para os seus dados na região secundário, os seus dados estão disponíveis num ponto final secundário, para além do ponto final principal para a sua conta de armazenamento. O ponto final secundário é semelhante ao ponto final principal, mas acrescenta o sufixo `–secondary` ao nome da conta. Por exemplo, se o seu ponto final principal para o serviço de BLOBs é `myaccount.blob.core.windows.net`, então o ponto final secundário é `myaccount-secondary.blob.core.windows.net`. As teclas de acesso para a sua conta de armazenamento são os mesmos para os pontos de finais principais e secundários.

Considerações sobre:

- Tem a aplicação para gerir o ponto final interagir com ao utilizar GRS RT. 

- RT GRS destina-se para fins de alta disponibilidade. Para obter orientações de escalabilidade, reveja a [lista de verificação de desempenho](storage-performance-checklist.md).

## <a name="next-steps"></a>Próximos passos

- [Preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Sobre contas de armazenamento Azure](storage-create-storage-account.md)
- [Escalabilidade do armazenamento Azure e o desempenho destinos](storage-scalability-targets.md)
- [Opções de redundância de armazenamento do Microsoft Azure e acesso de leitura Geo armazenamento redundante](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Papel SOSP - armazenamento Azure: Um altamente disponível serviço na nuvem armazenamento com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
