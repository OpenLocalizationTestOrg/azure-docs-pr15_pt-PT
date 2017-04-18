<properties 
   pageTitle="Notas de lançamento matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve problemas em aberto críticos e resoluções para a matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Notas de lançamento StorSimple Virtual matriz

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas críticos abrir para a versão de disponibilidade geral (das versões DG) Março de 2016 do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo virtual do StorSimple no local ou o dispositivo virtual StorSimple). Nesta versão corresponde à versão do software 10.0.10271.0.

As notas de lançamento são constantemente atualizadas e como sejam detetados problemas críticos que exige uma solução, são adicionados. Antes de implementar o seu dispositivo virtual StorSimple, reveja cuidadosamente as informações contidas nas notas de lançamento. 

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.


| Não. | Funcionalidade | Problema | Solução/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Não podem ser atualizados os dispositivos virtuais criados numa versão de pré-visualização para uma versão suportada do disponibilidade geral. | Estes dispositivos virtuais devem ser Ocorreu uma falha ao longo para a versão de disponibilidade geral utilizando um fluxo de trabalho do falhas recuperação (DR). |
| **2.** | Disco aprovisionada dados | Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo de virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. Tentar fazê-lo irá resultar na perda de todos os dados das camadas locais do dispositivo. |   |
| **3.** | Política de grupo | Quando um dispositivo está associado ao domínio, aplicar uma política de grupo afetam a operação de dispositivo. | Certifique-se de que a sua matriz virtual destina-se na sua própria unidade organizacional (OU) do Active Directory e sem objectos de política de grupo (GPO) são aplicados ao mesmo.|
| **4.** | Web local da IU | Se estiverem ativadas funcionalidades de segurança avançadas no Internet Explorer (IE ESC), algumas páginas de IU local web como resolução de problemas ou manutenção poderão não funcionar corretamente. Botões nestas páginas também poderão não funcionar. | Desative funcionalidades de segurança avançadas no Internet Explorer.|
| **5.** | Web local da IU | Numa máquina virtual Hyper-V, as interfaces de rede na web IU são apresentados como 10 Gbps interfaces. | Este comportamento é um reflexo do Hyper-V. Hyper-V Mostra sempre 10 Gbps para adaptadores de rede virtual. |
| **6.** | Em camadas volumes ou partilhas | Intervalo de byte bloqueio para as aplicações que funcionam com o StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de byte estiver ativado, não irá funcionar StorSimple tiering. | Medidas recomendadas incluem: <br></br>Desative o intervalo de bytes bloqueio na lógica da aplicação.<br></br>Selecione esta opção para colocar dados para esta aplicação volumes localmente afixados por oposição a volumes em camadas.<br></br>*Truque*: Se utilizar localmente afixados volumes e bloqueio de intervalo de byte é activado, tenha em atenção que o volume localmente afixado pode estar online mesmo antes da restaurar está concluída. Nesses casos, se um restauro estiver em curso, em seguida, tem de aguardar restaurar concluir. |
| **7.** | Acções em camadas | Trabalhar com ficheiros de grandes dimensões poderá resultar em camadas lenta saída. | Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é mais pequeno do que 3% do tamanho partilhar. |
| **8.** | Capacidade para partilhas de utilizado | Poderá ver partilhar consumo na ausência de quaisquer dados na partilha. Isto acontece porque a capacidade de utilizados para partilhas inclui metadados. |   |
| **9.** | Recuperação de falhas | Só pode realizar a recuperação de falhas de servidor de ficheiros para o mesmo domínio como que o dispositivo de origem. Recuperação de falhas para um dispositivo de destino no outro domínio não é suportada neste lançamento. | Serão executada com uma versão posterior. |
| **10.** | Azure PowerShell | Os dispositivos virtuais StorSimple não podem ser geridos durante o PowerShell Azure nesta versão. | Todos os a gestão dos dispositivos virtuais deve ser feita através do portal clássico do Azure e da IU da web local. |
| **11.** | Alterar palavra-passe | Consola do dispositivo de matriz virtual aceita apenas as entradas no formato de teclado en-US. |   |
| **12.** | CHAP | Credenciais CHAP depois de criado não podem ser removidas. Para além disso, se modificar as credenciais CHAP, terá de colocar os volumes offline e, em seguida, coloque-os online para que a alteração entre em vigor. | Estes serão endereçadas com uma versão posterior. |
| **13.** | servidor iSCSI  | O «utilizado armazenamento» apresentados para um volume iSCSI pode ser diferente no serviço do Gestor de StorSimple e anfitrião do iSCSI. | Anfitrião do iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos de atribuída quando o volume era com o tamanho máximo.|
