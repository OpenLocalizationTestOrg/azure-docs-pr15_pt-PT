<properties 
   pageTitle="Notas de lançamento de versão StorSimple 8000 solte | Microsoft Azure"
   description="Descreve as novas funcionalidades, problemas em aberto e soluções disponíveis para a Julho de 2014 lançamento do Microsoft Azure StorSimple."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notas de lançamento StorSimple 8000 série versão - Julho de 2014 

## <a name="overview"></a>Descrição geral

As notas de lançamento seguir identificam os problemas de abrir críticos para as séries de 8000 StorSimple lançamento de disponibilidade geral (das versões DG) de Julho de 2014 do Microsoft Azure StorSimple. Nesta versão corresponde à versão do software 6.3.9600.17215.  

Salvo caso contrário, estas notas de lançamento aplicar a todos os modelos do dispositivo StorSimple. As notas de lançamento são constantemente atualizadas; à medida que sejam detetados problemas críticos que exige uma solução, são adicionados. Antes de implementar a solução do Microsoft Azure StorSimple, considere as seguintes informações.  

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão
A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.  
 
| Não. | Funcionalidade | Problema | Solução/comentários | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Repor fábrica | Em alguns casos, ao efetuar a reposição de fábrica do mesmo, o dispositivo StorSimple poderá estar bloqueado e mostrar esta mensagem: **Repor o fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. | Prima CTRL + C depois de iniciar a reposição de fábrica do mesmo. Se já estiver a trabalhar neste estado, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 3 | Nuvem instantâneo falhas | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de cópia de segurança máximo instalações atingido**. Isto ocorre se exceder 255 clones onlinehttps no mesmo dispositivo, a partir do mesmo volume original que foi eliminado. | | Sim | Sim |
| 4 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. | Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 5 | Monitorizar os gráficos de dispositivo | No serviço StorSimple Gestor, os gráficos de monitorização do dispositivo não funciona quando estiver básicas ou a autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. | Modificar a configuração do proxy web para o dispositivo registado com o seu serviço de Gestor de StorSimple, por isso que a autenticação está definida para nenhum. Para fazer isto, execute o o Windows PowerShell para cmdlet StorSimple Set-HcsWebProxy. | Sim | Sim |
| 6 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador. | | Sim | Sim |
| 7 | Reposição de recurso | Reposição de uma recurso dentro de 24 horas de recuperação de falhas (DR) não é suportada. | | Sim | N |
| 8 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada. Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, nestes contentores de volume serão apresentada ou comportam-se de forma diferente quando visualizá-los no portal do Azure clássico. | | Sim | N |
| 9 | Instalação | Durante a StorSimple placa para a instalação do SharePoint, tem de fornecer um IP de dispositivo para o instalar para ser concluída com êxito. | | Sim | N |
| 10 | Interfaces de rede | Foram trocadas interfaces de rede dados 2 e 3 de dados no software. | Contacte o suportehttp Microsoft Support se precisar de configurar estas interfaces. | Sim | N |


 
