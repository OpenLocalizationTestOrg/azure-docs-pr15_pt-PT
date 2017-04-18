<properties 
   pageTitle="Notas de lançamento StorSimple Virtual matriz atualizações | Microsoft Azure"
   description="Descreve problemas em aberto críticos e resoluções para a matriz de Virtual StorSimple executar actualização 0,3."
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
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas de lançamento 0,3 StorSimple Virtual actualização de matriz

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas de abrir críticos e as questões resolvidas para atualizações do Microsoft Azure StorSimple Virtual matriz.

As notas de lançamento são constantemente atualizadas e como sejam detetados problemas críticos que exige uma solução, são adicionados. Antes de implementar a sua matriz de Virtual StorSimple, reveja cuidadosamente as informações contidas nas notas de lançamento.

Atualização 0,3 corresponde à versão de software **10.0.10288.0**.

> [AZURE.NOTE] As atualizações são desorganização e reinicie o seu dispositivo. Se forem e/s em curso, o dispositivo como tempo de inatividade.


## <a name="whats-new-in-the-update-03"></a>O que há de novo na atualização 0,3

Actualização 0,3 é principalmente uma compilação corrigir erros. Nesta versão, foram resolvidos vários erros que resulta em falhas de cópia de segurança na versão anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas corrigidos na atualização 0,3

A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não.  | Funcionalidade                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Cópias de segurança                                |Um problema foi visto na versão anterior onde as cópias de segurança seriam a falhar de conclusão de uma partilha de ficheiros. Se tiver ocorrido este problema, ocorrerá uma falha a tarefa de cópia de segurança e um alerta crítico foi elevado o serviço de Gestor de StorSimple para notificar o utilizador. Este problema não vai afetar os dados na partilhas ou acesso aos dados. A causa de raiz foi identificada e fixa nesta versão. <br></br> A fix não se aplica com efeitos a partilhas já estiver a ver este problema. Clientes que estiver a ver este problema deverão pela primeira vez aplicar atualização 0,3, em seguida, contacte o Microsoft Support para efetuar uma cópia de segurança completa do sistema para corrigir o problema. Em vez de contactar a Microsoft Support, clientes também podem restaurar para uma nova partilha a partir de uma cópia de segurança Saudável para partilhas afetadas.                                                                                                                                                                                 |
| 2    | iSCSI                         | Um problema foi visto na versão anterior, onde os volumes desaparecerão ao copiar dados para um volume na matriz StorSimple Virtual. Este problema foi resolvido nesta versão. <br></br> As correções aplicada apenas nos criado recentemente volumes. As correções não são aplicadas com efeitos a volumes que já estiver a ver este problema. Trazer os volumes afetados online através do portal do Azure clássico, efetuar uma cópia de segurança para estes volumes e, em seguida, restaurar estes volumes para novos volumes aconselhável clientes.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problemas conhecidos na atualização 0,3

A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas de referir lançamento de versões anteriores. 


| Não. | Funcionalidade | Problema | Solução/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Não podem ser atualizados os dispositivos virtuais criados numa versão de pré-visualização para uma versão suportada do disponibilidade geral. | Estes dispositivos virtuais devem ser Ocorreu uma falha ao longo para a versão de disponibilidade geral utilizando um fluxo de trabalho do falhas recuperação (DR). |
| **2.** | Disco aprovisionada dados | Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo de virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. Tentar fazer resultados na perda de todos os dados das camadas locais do dispositivo. |   |
| **3.** | Política de grupo | Quando um dispositivo está associado ao domínio, aplicar uma política de grupo afetam a operação de dispositivo. | Certifique-se de que a sua matriz virtual destina-se na sua própria unidade organizacional (OU) do Active Directory e sem objectos de política de grupo (GPO) são aplicados ao mesmo.|
| **4.** | Web local da IU | Se estiverem ativadas funcionalidades de segurança avançadas no Internet Explorer (IE ESC), algumas páginas de IU local web como resolução de problemas ou manutenção poderão não funcionar corretamente. Botões nestas páginas também poderão não funcionar. | Desative funcionalidades de segurança avançadas no Internet Explorer.|
| **5.** | Web local da IU | Numa máquina virtual Hyper-V, as interfaces de rede na web IU são apresentados como 10 Gbps interfaces. | Este comportamento é um reflexo do Hyper-V. Hyper-V Mostra sempre 10 Gbps para adaptadores de rede virtual. |
| **6.** | Em camadas volumes ou partilhas | Intervalo de byte bloqueio para as aplicações que funcionam com o StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de byte estiver ativado, StorSimple tiering não funciona. | Medidas recomendadas incluem: <br></br>Desative o intervalo de bytes bloqueio na lógica da aplicação.<br></br>Selecione esta opção para colocar dados para esta aplicação volumes localmente afixados por oposição a volumes em camadas.<br></br>*Truque*: quando utilizar localmente afixados volumes e byte intervalo bloqueio está ativado, o volume localmente afixado pode ser online mesmo antes da restaurar está concluída. Nesses casos, se um restauro estiver em curso, em seguida, tem de aguardar restaurar concluir. |
| **7.** | Acções em camadas | Trabalhar com ficheiros de grandes dimensões poderá resultar em camadas lenta saída. | Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é mais pequeno do que 3% do tamanho partilhar. |
| **8.** | Capacidade para partilhas de utilizado | Poderá ver partilhe consumo quando não haja dados na partilha. Isto acontece porque a capacidade de utilizados para partilhas inclui metadados. |   |
| **9.** | Recuperação de falhas | Só pode realizar a recuperação de falhas de servidor de ficheiros para o mesmo domínio como que o dispositivo de origem. Recuperação de falhas para um dispositivo de destino no outro domínio não é suportada neste lançamento. | Isto é implementado com uma versão posterior. |
| **10.** | Azure PowerShell | Os dispositivos virtuais StorSimple não podem ser geridos durante o PowerShell Azure nesta versão. | Todos os a gestão dos dispositivos virtuais deve ser feita através do portal clássico do Azure e da IU da web local. |
| **11.** | Alterar palavra-passe | Consola do dispositivo de matriz virtual aceita apenas as entradas no formato de teclado en-US. |   |
| **12.** | CHAP | Credenciais CHAP depois de criado não podem ser removidas. Para além disso, se modificar as credenciais CHAP, tem de colocar os volumes offline e, em seguida, coloque-os online para que a alteração entre em vigor. | Este problema é dirigido com uma versão posterior. |
| **13.** | servidor iSCSI  | O «utilizado armazenamento» apresentados para um volume iSCSI pode ser diferente no serviço do Gestor de StorSimple e anfitrião do iSCSI. | Anfitrião do iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos de atribuída quando o volume era com o tamanho máximo.|
| **14.** | Servidor de ficheiros  | Se um ficheiro numa pasta tem uma alternativa dados da cadeia (anúncios de) associada, os anúncios não está cópia de segurança ou restaurado através de recuperação de falhas, clonar e recuperação de nível do Item.| |


## <a name="next-step"></a>Passo seguinte

[Instale a atualização 0,3](storsimple-ova-install-update-01.md) no seu matriz de Virtual StorSimple.

## <a name="references"></a>Referências

Está à procura de uma nota de lançamento mais antiga? Vá para: 

- [Notas de lançamento do StorSimple matriz Virtual atualização 0,1 e 0,2 mais próximo](storsimple-ova-update-01-release-notes.md)

- [Notas de lançamento de disponibilidade geral matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)
 

