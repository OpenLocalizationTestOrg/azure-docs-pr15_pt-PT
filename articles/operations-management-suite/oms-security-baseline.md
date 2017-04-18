<properties
   pageTitle="Segurança de conjunto de aplicações de gestão de operações e plano base solução de auditoria | Microsoft Azure"
   description="Este documento explica como utilizar OMS segurança e de auditoria solução para executar uma avaliação do plano base de todos os computadores monitorizadas para fins de segurança e conformidade."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Do plano base avaliação na segurança de conjunto de aplicações de gestão de operações e solução de auditoria

Este documento ajuda-o para utilizar [segurança de conjunto de aplicações (OMS) de gestão de operações e solução de auditoria](operations-management-suite-overview.md) capacidades de avaliação do plano base para aceder o estado dos seus recursos monitorizados seguro.

## <a name="what-is-baseline-assessment"></a>O que é do plano base avaliação?

Microsoft, juntamente com o setor e administração pública organizações em todo o mundo, define uma configuração do Windows que representa implementações do servidor altamente seguro. Esta configuração é um conjunto de chaves de registo, as definições de política de auditoria e definições de política de segurança juntamente com valores de recomendadas da Microsoft para estas definições. Este conjunto de regras é conhecido como do plano base de segurança. Segurança OMS e auditoria a capacidade de avaliação do plano base forma totalmente integrada pode pesquisar todos os computadores para conformidade. 

Existem três tipos de regras:

- **Regras de registo**: verificar a que chaves de registo estão definidas corretamente.
- **Regras de política de auditoria**: regras de política de auditoria.
- **Regras de política de segurança**: regras das permissões do utilizador no computador.

> [AZURE.NOTE] Leia [Utilização OMS segurança para avaliar a linha de base de configuração de segurança](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) para uma breve descrição geral desta funcionalidade.

## <a name="security-baseline-assessment"></a>Segurança do plano base avaliação

Pode rever a sua avaliação do plano base de segurança atual para todos os computadores que são monitorizadas pelo OMS segurança e de auditoria com o dashboard.  Execute os seguintes passos para aceder ao dashboard de avaliação de segurança do plano base:

1. No dashboard principal do **Conjunto de aplicações do Microsoft operações gestão** , clique em **segurança e auditoria** mosaico.
2. No dashboard de **segurança e de auditoria** , clique em **Avaliação do plano base** , em **Domínios de segurança**. Dashboard de **Avaliação do plano base de segurança** é apresentado como é mostrado na seguinte imagem:
    
    ![Segurança OMS e avaliação de linha de base de auditoria](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Este dashboard é dividido em três áreas principais:

- **Computadores em comparação com do plano base**: Esta secção fornece um resumo do número de computadores que foram acedidos e a percentagem de computadores que passou a avaliação. Também dá-10 computadores superiores e o resultado de percentagem para a avaliação.
- **Estado de regras obrigatório**: Esta secção tem a intenção para trazer detecção das regras falhadas ao gravidade e falhou regras por tipo. Procurando ao grafo do primeiro possam identificar rapidamente se a maioria das regras falhadas são críticas, ou não. Também dá-uma lista das regras de falhadas 10 superiores e os respetivos gravidade. O segundo gráfico mostra o tipo de regra que falhou durante a avaliação. 
- **Computadores em falta avaliação do plano base**: Esta secção listar os computadores que não foram acedidos devido a falhas ou incompatibilidade do sistema operativo. 

### <a name="accessing-computers-compared-to-baseline"></a>Aceder a computadores em comparação comparados do plano base

Idealmente todos os computadores são estar em conformidade com a avaliação de linha de base de segurança. No entanto não ocorre é esperado que em algumas circunstâncias isto. Como parte do processo de gestão de segurança, é importante incluir a rever os computadores em que ocorreu uma falha ao passar todos os testes de avaliação de segurança. Uma forma rápida de visualizar que é ao selecionar a opção **computadores acedido** localizada na secção **computadores em comparação com do plano base** . Deverá ver o resultado de pesquisa de registo com a lista de computadores, tal como ilustrado no ecrã seguinte:

![Resultados de computador acedido](./media/oms-security-baseline/oms-security-baseline-fig2.png)

O resultado de pesquisa é apresentado num formato de tabela, onde a primeira coluna tem o nome do computador e a segunda cor tem o número de regras que falhou. Para obter informações relativas ao tipo de regra que não foi possível, clique no número de falhadas regras para além do nome do computador. Deverá visualizar um resultado semelhante ao mostrado na seguinte imagem:

![Detalhes de resultados do computador, pode ser consultada](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Este resultado de pesquisa, tem do total das regras acedidas, o número de críticos regras que falhou, as regras de aviso e as regras de informações falhou.

### <a name="accessing-required-rules-status"></a>Aceder ao estado de regras necessários

Depois de obter as informações relativas ao número de percentagem de computadores que passou a avaliação, poderá querer obter mais informações sobre o qual as regras são a falhar de acordo com a criticalidade. Esta visualização ajuda-o a atribuir prioridades a quais os computadores que devem ser dirigidos pela primeira vez, para garantir que sejam compatíveis com a avaliação seguinte. Paire o cursor sobre a peça crítica do gráfico, localizado no mosaico **falhou regras pelos gravidade** e em **obrigatório Estado regras** e clique nele. Deverá visualizar um resultado semelhante ao ecrã seguinte:

![Ocorreu uma falha regras por detalhes gravidade](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Neste resultado registo verá o tipo de regra do plano base que falhou, a descrição desta regra e o ID de comuns configuração enumeração (CCE) desta regra de segurança. Estes atributos devem ser suficiente para executar uma ação lentes para corrigir este problema no computador de destino.

> [AZURE.NOTE] Para mais informações sobre CCE, aceder a [Base de dados de vulnerabilidade nacionais](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>Aceder a computadores em falta avaliação do plano base

OMS suporta o perfil de linha de base de membro de domínio no Windows Server 2008 R2 por excesso para o Windows Server 2012 R2. Linha de base do Windows Server 2016 funcionalidade ainda não está final e será adicionada assim que é publicado. Todos os outros sistemas operativos digitalizados através de segurança OMS e auditoria de avaliação do plano base é apresentada na secção de **computadores em falta avaliação do plano base** .

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu sobre segurança OMS e auditoria de avaliação do plano base. Para saber mais sobre a segurança OMS, consulte os artigos seguintes:

- [Descrição geral de gestão conjunto de aplicações (OMS) de operações](operations-management-suite-overview.md)
- [Monitorização e a responder a alertas de segurança na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-responding-alerts.md)
- [Monitorizar recursos na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-monitoring-resources.md)

