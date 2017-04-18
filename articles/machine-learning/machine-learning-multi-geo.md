<properties
   pageTitle="Documentação de ajuda do multi-Geo | Microsoft Azure"
   description="Saiba como criar uma área de trabalho e publicar um serviço web numa região Azure diferente a partir dos Estados Unidos Central Sul (SCUS) região Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Documentação de ajuda do multi-Geo

Este artigo descreve como pode criar uma área de trabalho e publicar um serviço web noutras regiões Azure.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

1. Inicie sessão no Portal do Azure clássico.

2.  Clique em **+ Novo** > **Serviços de dados** > **formação de máquina** > **criar rápida**.  Em **localização** Selecione outro região, tal como **Sudeste asiático**.
![Imagem de ajuda multi-Geo 1][1]
3. Selecione a área de trabalho e, em seguida, clique em **Iniciar sessão no ML Studio**.
![Imagem de ajuda multi-Geo 2][2]

4. Tem agora uma área de trabalho na outra região que poderá utilizar tal como faria com qualquer outra área de trabalho. Para alternar entre as áreas de trabalho, procure o canto superior direito do seu ecrã. Clique no menu pendente, selecione a região e, em seguida, selecione a área de trabalho. Tudo o que é o local à região de área de trabalho; Por exemplo, todos os serviços web criados a partir de uma área de trabalho será na mesma região que a área de trabalho está localizada.
![Imagem de ajuda multi-Geo 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir uma experiência a partir da Galeria

Se abrir uma experiência da galeria, também pode selecionar qual região que pretende copiar a experiência para.

![Imagem de ajuda multi-Geo 4][4a]

## <a name="web-service-management"></a>Gestão de serviço Web

Para gerir através de programação serviços web, tal como para a Reciclagem, utilize o endereço de específicos da região:
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Aspetos a nota

1.  Só pode copiar experiências entre as áreas de trabalho que pertencem à mesma região da seguinte forma. Se precisar de copiar experiência através de áreas de trabalho em diferentes regiões, pode utilizar o commandlet [PowerShell](http://aka.ms/amlps) [*AmlExperiment cópia*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) para realizar que. Solução de outra é publicar a experiência para o Gallery no modo não listado, em seguida, abra-o na área de trabalho a partir de outras região.
2.  Selector de região Mostrar apenas as áreas de trabalho para uma região uma vez. No futuro, poderá ver uma lista completa de áreas de trabalho que tiver acesso a através de todas as regiões ao mesmo tempo.  
3.  Uma área de trabalho gratuita ou área de trabalho (anónima) acesso de convidado será criada e alojada no Sul Central dos e.u.a. No futuro, poderá criar áreas de trabalho do Access Free/convidado na região que escolher.  
4.  Serviços Web implementados a partir de uma área de trabalho na Ásia Sudeste também serão alojados no Sudeste asiático. No futuro, poderá ter a flexibilidade de criação de experiências numa região e implementar gerado pontos finais de serviço web para regiões diferentes.  

## <a name="more-information"></a>Obter mais informações

Fazer uma pergunta no [Fórum do Azure máquina aprendizagem](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
