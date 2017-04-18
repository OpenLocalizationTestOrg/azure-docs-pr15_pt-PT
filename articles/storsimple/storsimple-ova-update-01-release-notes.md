<properties 
   pageTitle="Notas de lançamento StorSimple Virtual matriz atualizações | Microsoft Azure"
   description="Descreve problemas em aberto críticos e resoluções para a matriz de Virtual StorSimple executar Update 0,2 mais próximo e 0,1."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de lançamento do StorSimple Virtual matriz atualização 0,2 e 0,1

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas de abrir críticos e as questões resolvidas para atualizações do Microsoft Azure StorSimple Virtual matriz. (Matriz virtuais do Microsoft Azure StorSimple é também conhecido como o dispositivo virtual do StorSimple no local ou o dispositivo virtual StorSimple.) 

As notas de lançamento são constantemente atualizadas e como sejam detetados problemas críticos que exige uma solução, são adicionados. Antes de implementar o seu dispositivo virtual StorSimple, reveja cuidadosamente as informações contidas nas notas de lançamento.

Atualização 0,2 corresponde ao de versão software **10.0.10280.0**; Actualização de 0,1 é versão **10.0.10279.0**. As secções abaixo da lista as alterações para cada atualização. 

> [AZURE.NOTE] As atualizações são desorganização e reinicie o seu dispositivo. Se forem e/s em curso, o dispositivo será assumir o tempo de inatividade.

## <a name="issues-fixed-in-the-update-02"></a>Problemas fixados na actualização de 0,2 mais próximo
Actualização 0,2 inclui todas as alterações do Update 0,1 além fix descrito na tabela seguinte:

Funcionalidade                              | Problema                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Atualizações                                 | Na versão de última atualizações não foram detetadas automaticamente no portal do Azure clássico, para que tinha que utilizar a IU local da Web para instalar atualizações. Este problema for resolvido nesta versão. Depois de instalar a atualização 0,2, pode instalar actualizações futuras através do portal clássico Azure.                       

## <a name="whats-new-in-the-update-01"></a>O que há de novo na actualização de 0,1

Actualização 0,1 contém as seguintes correções de erros e melhoramentos. 

- **RDP melhoradas para falhas de nuvem**: nesta versão tem várias correções de erros em redor de recuperação de falhas, cópia de segurança, restaurar e tiering trabalho uma interrupção de conectividade na nuvem. 

- **Restaurar o desempenho melhorado**: nesta versão tem correções de erros que tenham significativamente cortar para baixo a hora de conclusão das tarefas restaurar.

- **Otimização de valorização de espaço automática**: quando dados são eliminados de volumes de pequenos aprovisionados, os blocos de armazenamento não utilizadas precisa de ser recuperadas. Nesta versão melhorou o processo de valorização espaço a partir da nuvem que resulta no espaço não utilizado está a ficar disponível mais rápido em comparação com as versões anteriores.

- **Imagens de disco virtual novo**: VHD novo, VHDX e VMDK estão agora disponíveis através do portal do Azure clássico. Pode transferir estas imagens para aprovisionar novos dispositivos de atualização 0,1.

- **Melhorar a precisão do estado das tarefas no portal**: na versão anterior do software, não era granular elaboração de relatórios no portal do Estado da tarefa. Este problema for resolvido nesta versão.

- **Experiência de associação de domínio**: correções de erros relacionados com a participação de domínio e ao mudar o nome do dispositivo.


## <a name="issues-fixed-in-the-update-01"></a>Problemas fixados na actualização de 0,1

A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não.  | Funcionalidade                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | Em algumas versões VMware, o disco SO foi visto como dispersos a causar alertas e perturbar operações normais. Este foi corrigido nesta versão.                                                                                                                                                                                    |
| 2    | servidor iSCSI                         | Na última edição, o utilizador foi necessárias para especificar um gateway para cada interface de rede activadas do seu dispositivo virtual StorSimple. Este comportamento é alterado nesta versão, pelo que o utilizador tem de configurar pelo menos um gateway para todas as interfaces de rede activadas.                                                                              |
| 3    | Pacote de suporte                      | Na versão anterior do software, coleções de sites do suporte pacote Ocorreu uma falha quando os tamanhos de pacote foram superiores a 1 GB. Este problema for resolvido nesta versão.                                                                                                                                                                               |
| 4    | Acesso à nuvem                         |  Na última versão, se a matriz Virtual StorSimple não tem a conectividade da rede e foi reiniciada, a IU local teria de problemas de conectividade. Este problema é corrigido nesta versão.                                                                                                                            |
| 5    | Gráficos de monitorização                    | Na versão anterior, seguindo uma falha na ligação dispositivo, os gráficos de utilização da capacidade de nuvem apresentadas valores incorretos no portal do Azure clássico. Isto fica fixo na versão atual.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problemas conhecidos da actualização de 0,1

A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas de referir lançamento de versões anteriores. **A versão de problemas indicada neste lançamento são marcados com um asterisco. Tem transportados quase todos os problemas nesta lista a partir da versão das versões DG de matriz Virtual StorSimple.**


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
| **14.** | Servidor do ficheiro *  | Se um ficheiro numa pasta tem uma alternativa dados da cadeia (anúncios de) associada, os anúncios não está cópia de segurança ou restaurado através de recuperação de falhas, clonar e recuperação de nível do Item.| |


## <a name="next-step"></a>Passo seguinte

[Instalar as atualizações](storsimple-ova-install-update-01.md) no seu matriz de Virtual StorSimple.
