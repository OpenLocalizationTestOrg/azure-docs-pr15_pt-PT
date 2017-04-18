<properties
   pageTitle="Públicas e privadas CC/SO agente de agrupamentos ACS | Microsoft Azure"
   description="Os conjuntos de agente públicas e privadas funcionam com um cluster de serviço do Azure contentor."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>Conjuntos de dados do Centro de dados/SO agente para o serviço de contentor Azure

O serviço de contentor do Azure CC/SO divide agentes em agrupamentos de públicos ou privados. Uma implementação pode ser feita ao agrupamento de um dos, afetar acessibilidade entre máquinas no seu serviço de contentor. As máquinas podem ser divulgadas na internet (público) ou mantidas interno (privado). Este artigo fornece uma breve descrição geral dos motivos pelos quais existem um conjunto de dados público e privado.

### <a name="private-agents"></a>Agentes privados

Executar nós agente privado através de uma rede não encaminháveis. Esta rede só está acessível a partir da zona de administrador ou através do router de limite de zona público. Por predefinição, Cc/SO inicia aplicações em nós agente privado. Consulte a [documentação do Centro de dados/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre a segurança de rede.

### <a name="public-agents"></a>Agentes públicos

Nós de agente público executar CC/SO aplicações e serviços através de uma rede acessível publicamente. Consulte a [documentação do Centro de dados/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre a segurança de rede.

## <a name="using-agent-pools"></a>Utilizando agente conjuntos de dados

Por predefinição, **Marathon** implementa qualquer nova aplicação para os nós de agente *privado* . Tem de explicitamente implementar a aplicação para o *público* nó durante a criação da aplicação. Selecione o separador **opcional** e introduza **slave_public** para o valor de **Funções de recurso aceites** . Este processo é documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Próximos passos

Leia mais informações sobre como [Gerir os contentores CC/SO](container-service-mesos-marathon-ui.md).

Saiba como [Abrir a firewall](container-service-enable-public-access.md) fornecida pela Azure para permitir o acesso do público para o contentor CC/SO.