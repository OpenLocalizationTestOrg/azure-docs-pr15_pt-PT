<properties
    pageTitle="Utilize instantâneos utilizarão para cópia de segurança e recuperação de máquinas virtuais Azure | Microsoft Azure"
    description="Crie uma solução personalizada para a cópia de segurança e recuperação de discos máquina virtual Azure utilizando instantâneos utilizarão."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Criar uma cópia de segurança discos de máquina virtual Azure com instantâneos utilizarão

## <a name="overview"></a>Descrição geral

Armazenamento Azure fornece a capacidade de tirar um instantâneo de blobs. Instantâneos capturar o estado de BLOBs nesse momento. Neste artigo, descrevemos um cenário de como pode manter cópias de segurança de discos de máquina virtual utilizando instantâneos. Pode utilizar esta metodologia quando escolher não utilizar a cópia de segurança do Azure e o serviço de recuperação e pretende criar uma estratégia de cópia de segurança personalizada para os seus discos máquina virtual.

Azure máquina virtual discos são armazenados como blobs de página no armazenamento do Windows Azure. Uma vez que recomendamos estiver a falar sobre estratégia de cópia de segurança para máquina virtual discos neste artigo, iremos irá ser referir instantâneos no contexto de blobs de página. Para saber mais sobre instantâneos, consulte a [criação de um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?

Um instantâneo de BLOBs é a versão só de leitura de um blob que é capturado num ponto no tempo. Assim que um instantâneo ter sido criado,-pode ser ler, copiado, ou eliminado, mas não modificado. Instantâneos fornecem uma maneira de fazer cópia de segurança um blob tal como é apresentado um momento. Até resto versão 2015-04-05 teve a capacidade de copiar instantâneos completos. Com o resto versão 2015-07-08 e acima, também pode copiar instantâneos utilizarão.

## <a name="full-snapshot-copy"></a>Copiar instantâneo completo

Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter cópias de segurança do blob base. Também pode copiar um instantâneo ao longo do seu base blob, que é como restaurar o blob para uma versão anterior. Quando um instantâneo é copiado a partir de uma conta de armazenamento para outro, ocupa mesmo espaço de que o blob página base. Por conseguinte, copiar instantâneos todos a partir de uma conta de armazenamento para outra vai ser lentos e também irão consumir muitas espaço na conta de armazenamento de destino.

>[AZURE.NOTE] Se copiar o blob base para outro destino, instantâneos do blob não são copiados juntamente com o mesmo. Da mesma forma, se substituir um base blob com uma cópia, instantâneos associados com o blob base não são afetados e permanecem intactos em nome de BLOBs base.

### <a name="back-up-disks-using-snapshots"></a>Criar uma cópia de segurança discos utilizando instantâneos

Como uma estratégia de cópia de segurança para os seus discos máquina virtual, pode tirar instantâneos periódicos do disco ou página blob e copiá-los para outra conta de armazenamento utilizando ferramentas como [Cópia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) operação ou [AzCopy](storage-use-azcopy.md). Pode copiar um instantâneo para um blob de página de destino com um nome diferente. O blob de página de destino resultante é um blob página gravável e não um instantâneo. Neste artigo descrevemos passos a ter cópias de segurança de discos de máquina virtual utilizando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos utilizando instantâneos

Quando está na altura de restaurar o disco para uma versão anterior estável capturada dos instantâneos cópia de segurança, pode copiar um instantâneo sobre o blob página base. Depois do instantâneo é promovido para a página base blob permanece instantâneo, mas é substituída origem com uma cópia que pode ser tanto de leitura e escrita. Neste artigo descrevemos passos para restaurar uma versão anterior do seu disco a partir da sua instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Aplicação completo instantâneo cópia

Pode implementar uma cópia completa instantâneo ao efetuar o seguinte

-   Em primeiro lugar, tire um instantâneo do blob base utilizando a operação de [BLOBs do instantâneo](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Em seguida, copie o instantâneo para uma conta de armazenamento de destino utilizando [Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Repita este processo para manter cópias de segurança dos seus blob base.

## <a name="incremental-snapshot-copy"></a>Copiar utilizarão instantâneo

A nova funcionalidade no [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API fornece uma melhor forma para agregar instantâneos do seu blobs de página ou discos. API devolve a lista de alterações entre o blob base e os instantâneos. Este procedimento reduz a quantidade de espaço de armazenamento utilizado na conta de cópia de segurança. API suporta blobs de página no armazenamento Premium bem como o armazenamento padrão. Utilizar esta API, pode agora a criar soluções mais rápidas e eficaz de cópia de segurança para Azure VMs. Este estará disponível com a versão do resto 2015-07-08 e superior.

Cópia de instantâneo utilizarão permite-lhe copiar a partir de uma conta de armazenamento para outro a diferença entre,

-   Base blob e o instantâneo ou
-   Qualquer duas instantâneos do blob base

Caso as seguintes condições, sejam verifiquem

- O blob foi criado no Jan-1-2016 ou posterior.
- O blob não foi substituído com [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) ou [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre dois instantâneos.


**Nota**: esta funcionalidade está disponível para Premium e padrão do Azure página Blobs.

Quando tiver uma estratégia de cópia de segurança personalizada que utiliza instantâneos, copiar os instantâneos de uma conta de armazenamento para outro pode ser muito lenta e consome muitas espaço de armazenamento. Em vez de copiar o instantâneo todo a uma conta de armazenamento de cópia de segurança, pode escrever a diferença entre instantâneos consecutivas para um blob de cópia de segurança da página. Desta forma, o tempo para copiar e espaço para armazenar as cópias de segurança substancialmente é reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementar a cópia utilizarão instantâneo

Pode implementar instantâneo utilizarão cópia ao efetuar o seguinte

-   Tire um instantâneo do blob base utilizando [Instantâneo Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copie o instantâneo para a conta de armazenamento de cópia de segurança de destino utilizando [Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx). Este será o blob de cópia de segurança da página. Tire um instantâneo da blob página cópia de segurança e armazenar na conta de cópia de segurança.
-   Tire outra instantâneo do blob base utilizando instantâneo Blob.
-   Obter a diferença entre a primeiros e a segunda instantâneos dos blob base utilizando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Utilize o parâmetro novo **prevsnapshot** para especificar o valor de data/hora da instantâneo que pretende obter a diferença com. Quando este parâmetro estiver presente, a resposta resto irá incluir apenas as páginas que foram alteradas entre instantâneo de destino e instantâneo anterior, incluindo páginas limpar.
-   Utilize [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar estas alterações para o blob de cópia de segurança da página.
-   Por fim, tire um instantâneo do blob da página de cópia de segurança e armazená-lo na conta de armazenamento de cópia de segurança.

Na secção seguinte, será descrevemos mais detalhadamente como pode manter cópias de segurança de discos utilizando a função Copiar de instantâneo utilizarão

## <a name="scenario"></a>Cenário

Nesta secção descrevemos cenário que envolve uma estratégia de cópia de segurança personalizada de discos de máquina virtual utilizando instantâneos.

Considere uma VM de Azure DS série com um disco P30 do armazenamento de premium anexado. O disco de P30 denominado *mypremiumdisk* é armazenado numa conta de armazenamento premium denominada *mypremiumaccount*. Uma conta de armazenamento padrão denominada *mybackupstdaccount* será utilizada para armazenar a cópia de segurança *mypremiumdisk*. Gostaríamos manter um instantâneo do *mypremiumdisk* cada 12 horas.

Para saber mais sobre a criação de conta de armazenamento e discos, consulte [contas de armazenamento do Azure sobre](storage-create-storage-account.md).

Para saber mais sobre cópias de segurança Azure VMs, consulte [Planear VM Azure cópias de segurança](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passos para manter cópias de segurança de um disco de utilização de instantâneos utilizarão

Os passos descritos abaixo irão tirar instantâneos de *mypremiumdisk* e manter as cópias de segurança *mybackupstdaccount*. A cópia de segurança serão blob numa página padrão denominado *mybackupstdpageblob*. O blob de cópia de segurança de página sempre vai refletir as mesmo estado como o último instantâneo de *mypremiumdisk*.

1.  Primeiro, crie o blob da página de cópia de segurança para o disco de armazenamento premium. Para fazer isto, tire um instantâneo da *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2.  Copie esta instantâneo para mybackupstdaccount como um blob página denominado *mybackupstdpageblob*.
3.  Tire um instantâneo da *mybackupstdpageblob* denominado *mybackupstdpageblob_ss1*, utilizando [Instantâneo Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) e guardá-la na *mybackupstdaccount*.
4.  Durante a janela de cópia de segurança, criar outro instantâneo de *mypremiumdisk*, diga *mypremiumdisk_ss2*e guardá-la na *mypremiumaccount*.
5.  Obter as alterações utilizarão entre os dois instantâneos *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, utilizar [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) no *mypremiumdisk_ss2* com o parâmetro de **prevsnapshot** definido como o carimbo de data/hora da *mypremiumdisk_ss1*. Escreva estas alterações utilizarão a página de cópia de segurança de BLOBs *mybackupstdpageblob* no *mybackupstdaccount*. Se existirem intervalos eliminados no alterações de incremento, tem de ser limpo a partir do blob de cópia de segurança da página. Utilize [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para escrever alterações utilizarão o blob de cópia de segurança da página.
6.  Tire um instantâneo da página de cópia de segurança blob *mybackupstdpageblob*, denominado *mybackupstdpageblob_ss2*. Elimine o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7.  Repita os passos 4 a 6 todas as janelas de cópia de segurança. Desta forma, pode manter cópias de segurança das *mypremiumdisk* numa conta armazenamento padrão.

![Criar uma cópia de segurança disco utilizando utilizarão instantâneos](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passos para restaurar um disco a partir de instantâneos

Os passos descritos abaixo irão restaurar disco premium, *mypremiumdisk* para um instantâneo anterior, a partir de conta de armazenamento de cópia de segurança *mybackupstdaccount*.

1.  Identificar o ponto de hora que pretende restaurar o disco de premium para. Digamos que é instantâneo *mybackupstdpageblob_ss2*, que é armazenado no de conta de armazenamento de cópia de segurança *mybackupstdaccount*.
2.  No mybackupstdaccount, promova a instantâneo *mybackupstdpageblob_ss2* como a nova página de base de cópia de segurança blob *mybackupstdpageblobrestored*.
3.  Tire um instantâneo deste blob restaurado página cópia de segurança, denominado *mybackupstdpageblobrestored_ss1*.
4.  Copie a página restaurado blob *mybackupstdpageblobrestored* de *mybackupstdaccount* para *mypremiumaccount* como o novo premium disco *mypremiumdiskrestored*.
5.  Tire um instantâneo da *mypremiumdiskrestored*, denominado *mypremiumdiskrestored_ss1* para fazer cópias de segurança utilizarão futuras.
6.  Série de ponto DS VM para o restaurado *mypremiumdiskrestored* do disco e desanexar o antigo *mypremiumdisk* a partir da VM.
7.  Inicie o processo de cópia de segurança descrito na secção anterior para o disco restaurado *mypremiumdiskrestored*, utilizando o *mybackupstdpageblobrestored* como o blob de cópia de segurança da página.

![Restaurar o disco de instantâneos](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como criar instantâneos de um blob e planeamento da sua infraestrutura de cópia de segurança de VM utilizando as ligações abaixo.

- [Criação de um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planear a sua infraestrutura de cópia de segurança VM](../backup/backup-azure-vms-introduction.md)
