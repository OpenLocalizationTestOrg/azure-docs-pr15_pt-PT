<properties 
   pageTitle="Limites do sistema StorSimple | Microsoft Azure"
   description="Descreve os limites de sistema e tamanhos recomendados para ligações e StorSimple 8000 componentes de série."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

## <a name="overview"></a>Descrição geral

StorSimple fornece armazenamento dimensionável e flexível para o Centro de dados. No entanto, existem alguns limites que deve ter em mente à medida que planear, implementar e trabalhar com a sua solução StorSimple. A tabela seguinte descreve esses limites e fornece algumas recomendações para que pode obter o máximo partido da sua solução StorSimple.

| Identificador do limite | Limite | Comentários |
|----------------- | ------|--------- |
| Número máximo de credenciais da conta de armazenamento | 64 | |
| Número máximo de contentores de volume | 64 | |
| Número máximo de volumes | 255 | |
| Número máximo de volumes localmente afixados | 32 | |
| Número máximo de agendas por modelo de largura de banda | 168 | Uma agenda para cada hora, todos os dias da semana (24 * 7). |
| Tamanho máximo de um volume em camadas em dispositivos físicos | 64 TB para 8100 e 8600 | 8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure | 30 TB para 8010 <br></br> 64 TB para 8020 | 8010 e 8020 são dispositivos virtuais no Azure que utilizam armazenamento padrão e armazenamento de Premium respetivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos | 8,5 TB para 8100 <br></br> 22,5 TB para 8600 | 8100 e 8600 são dispositivos físicos. |
| Número máximo de ligações iSCSI | 512 | |
| Número máximo de ligações de iSCSI a partir de iniciadores | 512 | |
| Número máximo de registos de controlo de acesso por dispositivo | 64 | |
| Número máximo de volumes por política de cópia de segurança | 24 | |
| Número máximo de cópias de segurança retidas por agenda (numa política de cópia de segurança) | 64 | |
| Número máximo de agendas por política de cópia de segurança | 10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume | 256 | Este número inclui instantâneos locais e na nuvem instantâneos. |
| Número máximo de instantâneos que podem estar presentes no qualquer dispositivo | 10.000 | |
| Número máximo de volumes que podem ser processadas em paralelo para cópia de segurança, restaurar ou clonar | 16 |<ul><li>Se existirem mais do que 16 volumes, estes são processados sequencialmente como faixas de processamento ficam disponíveis.</li><li>Não pode ocorrer novas cópias de segurança de uma clonado ou um volume em camadas restaurado até terminar a operação. No entanto, para um local volume, cópias de segurança permitidas depois do volume está online.</li></ul>|
| Restaurar e clonar recuperar tempo para volumes em camadas | < 2 minutos | <ul><li>O volume é disponibilizado dentro de minutos de 2 de operação de restaurar ou clonar, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente poderá ser mais lento que normal, tal como a maior parte dos dados e metadados ainda se encontra na nuvem. Pode aumentar o desempenho como fluxos de dados a partir da nuvem para o dispositivo StorSimple.</li><li>O tempo total para transferir metadados depende o tamanho do volume atribuída. Metadados são automaticamente importados para o dispositivo em segundo plano à taxa de 5 minutos por TB de dados do volume atribuída. Esta taxa poderá ser afectada ao largura de banda de Internet na nuvem.</li><li>A operação de clonar ou restaurar está concluída quando todos os metadados estão no dispositivo.</li><li>Operações de cópia de segurança não podem ser efetuadas até o restauro ou clonar operação está totalmente concluída.|
| Restaurar recuperar tempo para volumes localmente afixados | < 2 minutos | <ul><li>O volume é disponibilizado dentro de minutos 2 da operação de restaurar, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente poderá ser mais lento que normal, tal como a maior parte dos dados e metadados ainda se encontra na nuvem. Pode aumentar o desempenho como fluxos de dados a partir da nuvem para o dispositivo StorSimple.</li><li>O tempo total para transferir metadados depende o tamanho do volume atribuída. Metadados são automaticamente importados para o dispositivo em segundo plano à taxa de 5 minutos por TB de dados do volume atribuída. Esta taxa poderá ser afectada ao largura de banda de Internet na nuvem.</li><li>Ao contrário volumes em camadas, para volumes localmente afixados, os dados do volume também são transferidos localmente no dispositivo. A operação de restauro é concluída quando todos os dados de volume tem foram importados para o dispositivo.</li><li>As operações de restaurar podem ser longas. O tempo total para concluir o restauro irá variam consoante o tamanho de volume local aprovisionado, largura de banda da Internet e os dados existentes no dispositivo. Operações de cópia de segurança no volume localmente afixada são permitidas enquanto a operação de restauro está em curso.|
|Taxa de processamento de instantâneos na nuvem| 15 minutos/TB | <ul><li>Pronto para carregar, por TB de dados de volume atribuída na cópia de segurança do instantâneo mínimo tempo para assegurar a nuvem. </li><li> Tempo de instantâneo nuvem total é calculado ao adicionar este período de tempo para a hora de carregamento instantâneo, que é afectada pela largura de banda da Internet para o cloud.
| Débito de leitura/escrita cliente máximo (quando servida a partir da camada SSD) * | 920/720 MB/s com uma única interface de rede GbE 10 | Até 2 x com MPIO e duas interfaces de rede. |
| Débito de leitura/escrita cliente máximo (quando servida a partir da camada de disco) * | 120/250 MB/s |
| Máximo de clientes leitura/escrita débito (quando servida a partir da camada na nuvem)* para a atualização 3 e versões posteriores** | 40/60 MB/s para volumes de camadas<br><br>MB de 60/80/s para camadas volumes com a opção de arquivo seleccionada durante a criação de volume | Débito leitura depende clientes gerar e manter as suficiente profundidade de fila de espera e/s. <br><br>Velocidade atingida depende da velocidade da conta de armazenamento subjacente utilizada. | 

& #42; Débito máximo por tipo e/s foi medido com 100 por cento leitura e escrita 100 por cento cenários. Débito real podem ser inferior e depende e/s misturar e condições de rede.

& #42; & #42; Números de desempenho antes de atualizar 3 podem ser inferiores.

## <a name="next-steps"></a>Próximos passos

Reveja os [requisitos de sistema StorSimple](storsimple-system-requirements.md). 
