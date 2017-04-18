<properties
   pageTitle="Ativar o acesso do público para uma aplicação de ACS | Microsoft Azure"
   description="Como ativar o acesso do público num serviço de contentor Azure."
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
   ms.date="08/26/2016"
   ms.author="timlt"/>

# <a name="enable-public-access-to-an-azure-container-service-application"></a>Ativar o acesso do público para uma aplicação de serviço de contentor Azure

Qualquer contentor CC/SO no ACS [agrupamento de agente público](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automaticamente está divulgada na internet. Por predefinição, as portas **80**, **443**, **8080** são abertos e qualquer contentor (pública) escutar essas portas são acessíveis. Este artigo mostra como abrir mais portas para as suas aplicações no Azure o serviço de contentor.

## <a name="open-a-port-portal"></a>Abrir uma porta (portal) 

Em primeiro lugar, precisamos de abrir a porta que queremos.

1. Inicie sessão no portal.
2. Localize o grupo de recursos implementado a Azure contentor do serviço.
3. Selecione o Balanceador de carga de agente (que é o nome semelhante ao **XXXX-agent lb xxx**).

    ![Balanceador de carga de serviço do contentor Azure](media/container-service-dcos-agents/agent-load-balancer.png)

4. Clique em **sondas** e, em seguida, **Adicionar**.

    ![Sondas Balanceador de carga de serviço do contentor Azure](media/container-service-dcos-agents/add-probe.png)

5. Preencha o formulário de sonda e clique em **OK**.

  	| Campo | Descrição |
  	| ----- | ----------- |
  	| Nome  | Um nome descritivo da sonda. |
  	| Porta  | A porta do contentor para testar. |
  	| Caminho  | (Quando no modo de HTTP) O caminho de Web site relativa para sonda. HTTPS não suportadas. |
  	| Intervalo | A quantidade de tempo entre sonda tentativas em segundos. |
  	| Limiar danificado | Número de sonda consecutiva tentativas antes de considerar o contentor danificado. | 
    

6. Novamente nas propriedades do Balanceador de carga de agente, clique em **regras de balanceamento de carga** e, em seguida, **Adicionar**.

    ![Regras do Balanceador de carga de serviço do contentor Azure](media/container-service-dcos-agents/add-balancer-rule.png)

7. Preencha o formulário de Balanceador de carga e clique em **OK**.

  	| Campo | Descrição |
  	| ----- | ----------- |
  	| Nome  | Um nome descritivo do Balanceador de carga. |
  	| Porta  | A porta recebida pública. |
  	| Porta de back-end | A porta interno público do contentor para encaminhar o tráfego para. |
  	| Agrupamento de back-end | Os contentores neste conjunto de dados será o destino para este Balanceador de carga. |
  	| Sonda | A sonda utilizada para determinar se um destino no **conjunto de back-end** está em bom estada. |
  	| Persistente de sessão | Determina a forma como o tráfego de um cliente deve ser processado para a duração da sessão.<br><br>**Nenhum**: sucessivos pedidos a partir do cliente do mesmo, poderá ser resolvidos por qualquer contentor.<br>**IP de cliente**: sucessivos pedidos de IP do cliente mesmo são processados pelo mesmo contentor.<br>**IP de cliente e o protocolo**: sucessivos pedidos da mesma combinação de IP e o protocolo de cliente são processados pelo mesmo contentor. |
  	| Tempo limite inactivo | (Apenas TCP) Em minutos, o tempo para manter um cliente TCP/HTTP abrir sem depender de *persistentes* mensagens. |

## <a name="add-a-security-rule-portal"></a>Adicionar uma regra de segurança (portal)

Em seguida, precisamos de adicionar uma regra de segurança que encaminha o tráfego da nossa porta aberta através da firewall.

1. Inicie sessão no portal.
2. Localize o grupo de recursos implementado a Azure contentor do serviço.
3. Selecione o **público** agente rede grupo de segurança (que é o nome semelhante ao **XXXX-agente de público-nsg-xxx**).

    ![Grupo de segurança de rede de serviço de contentor Azure](media/container-service-dcos-agents/agent-nsg.png)

4. Selecione **segurança regras de entrada** e, em seguida, **Adicionar**.

    ![Regras de grupo de segurança de rede de serviço de contentor Azure](media/container-service-dcos-agents/add-firewall-rule.png)

5. Preencha a regra de firewall para permitir que o seu porta pública e clique em **OK**.

  	| Campo | Descrição |
  	| ----- | ----------- |
  	| Nome  | Um nome descritivo da regra de firewall. |
  	| Prioridade | Classificação de prioridade para a regra. Quanto mais baixa o número maior é a prioridade. |
  	| Origem | Restringir o intervalo de endereços IP recebido para ser permitido ou negado por esta regra. Utilize **qualquer** para não especificar uma restrição. |
  	| Serviço | Selecione um conjunto de serviços predefinidos que esta regra de segurança destina-se. Caso contrário, utilize **personalizada** para criar a sua própria. |
  	| Protocolo | Restringir o tráfego com base em **TCP** ou **UDP**. Utilize **qualquer** para não especificar uma restrição. |
  	| Intervalo de porta | Quando o **serviço** é **personalizado**, especifica o intervalo de portas afeta esta regra. Pode utilizar uma única porta, tal como **80**ou um intervalo como **1024 1500**. |
  	| Ação | Permitir ou negar tráfego que cumpre os critérios. |

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a diferença entre [agentes de CC/SO públicos e privados](container-service-dcos-agents.md).

Leia mais informações sobre como [Gerir os contentores CC/SO](container-service-mesos-marathon-ui.md).