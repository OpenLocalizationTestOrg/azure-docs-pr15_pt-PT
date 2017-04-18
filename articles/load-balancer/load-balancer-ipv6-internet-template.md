<properties
    pageTitle="Implementar um acesso à Internet com balanceamento de carga solução com o IPv6 utilizando um modelo de | Microsoft Azure"
    description="Como implementar o suporte do IPv6 do Balanceador de carga Azure e VMs balanceamento de carga."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, Balanceador de carga azure, pilha dupla, endereço ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementar um acesso à Internet Balanceador de carga solução com o IPv6 através de um modelo

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Clip Azure](./load-balancer-ipv6-internet-cli.md)
- [Modelo](./load-balancer-ipv6-internet-template.md)

Um balanceador de carga Azure é um balanceador de carga camada-4 (TCP, UDP). O Balanceador de carga fornece elevada disponibilidade através da distribuição de tráfego de entrada entre instâncias do serviço Saudável nos serviços em nuvem ou máquinas virtuais num conjunto de Balanceador de carga. Azure Balanceador de carga também pode apresentar desses serviços no múltiplas portas, vários endereços IP ou ambas.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra a solução de balanceamento de carga em que está a ser implementada utilizando o modelo de exemplo é descrito neste artigo.

![Cenário de Balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Neste cenário irá criar os seguintes recursos Azure:

- uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
- um balanceador de carga de acesso à Internet com um IPv4 e um endereço IP público IPv6
- dois carregar balanceamento regras para mapear VIP público para os pontos finais privados
- um conjunto de disponibilidade que contém as duas VMs
- duas máquinas virtuais (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementar o modelo através do portal Azure

Este artigo faz referência a um modelo que é publicado na Galeria de [Modelos de guia de introdução do Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Pode transferir o modelo a partir da galeria ou iniciação de implementação no Azure diretamente a partir da galeria. Este artigo assume que transferiu o modelo para o seu computador local.

1. Abra o portal do Azure e inicie sessão com uma conta que possua permissões para criar VMs e recursos de redes dentro de uma subscrição do Azure. Além disso, a menos que estiver a utilizar recursos existentes, a conta tem permissão para criar um grupo de recursos e uma conta de armazenamento.

2. Clique em "+ novo" a partir do menu, em seguida, escreva "modelo" na caixa de pesquisa. Selecione "Implementação do modelo" nos resultados da pesquisa.

    ![LB-ipv6-portal-passo2 para](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. No tudo pá, clique em "Modelo implementação."

    ![LB-ipv6-portal-passo 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![LB ipv6-portal step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo." Eliminar o conteúdo existente e copiar/colar todo o conteúdo do ficheiro de modelo (para incluir iniciar e terminar {}), em seguida, clique em "Guardar".

    > [AZURE.NOTE] Se estiver a utilizar o Microsoft Internet Explorer, quando cola receber uma caixa de diálogo a pedir-lhe para permitir o acesso à área de transferência do Windows. Clique em "Permitir o acesso".

    ![LB ipv6-portal step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". No pá parâmetros, especifique os valores pelas orientações na secção de parâmetros de modelo, em seguida, clique em "Guardar" para fechar a pá parâmetros. No pá implementação personalizadas, selecione a sua subscrição, um grupo de recursos existente ou crie um. Se estiver a criar um grupo de recursos, em seguida, selecione uma localização para o grupo de recursos. Em seguida, clique em **termos Legal**, em seguida, clique em **comprar** para os termos legais. Azure começa implementar os recursos. Bastam alguns minutos para implementar todos os recursos.

    ![LB ipv6-portal step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para mais informações sobre estes parâmetros, consulte a secção de [parâmetros de modelo e variáveis](#template-parameters-and-variables) neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Procurar, desloque-se para baixo na lista até ver "Grupos de recursos", em seguida, clique na mesma.

    ![LB ipv6-portal step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Na pá de grupos recurso, clique no nome do grupo de recursos que especificou no passo 6. Verá uma lista de todos os recursos que foram implementadas. Se todas as correu bem, deverá aparecer "Com êxito" em "Última implementação." Caso não esteja, certifique-se de que a conta que está a utilizar tem permissões para criar os recursos necessários.

    ![LB ipv6-portal step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [AZURE.NOTE] Se procurar grupos de recursos imediatamente depois de concluir o passo 6, "Última implementação" irá apresentar o estado de "Implementar" enquanto os recursos estão a ser implementados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Verá que tem um endereço IPv6 em endereço IP e que o seu nome de DNS é o valor que especificou para o parâmetro dnsNameforIPv6LbIP no passo 6. Este recurso é o público IPv6 endereço e nome de anfitrião que esteja acessível para os clientes da Internet.

    ![LB ipv6-portal step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Valide a conectividade

Quando o modelo tem implementado com êxito, pode validar conectividade preenchendo as seguintes tarefas:

1. Inicie sessão portal do Azure e ligue a cada um dos VMs criados por de implementação do modelo. Se tiver implementado um VM de servidor do Windows, executar o ipconfig/tudo a partir de uma linha de comandos. Verá que a VMs têm endereços IPv4 e IPv6. Se implementado Linux VMs, terá de configurar o SO Linux para receber endereços IPv6 dinâmicos utilizando as instruções fornecidas para a distribuição do Linux.
2. A partir de um cliente ligado à IPv6 Internet, inicie uma ligação para o endereço IPv6 público do Balanceador de carga. Para confirmar que é balanceamento Balanceador de carga entre as duas VMs, é possível instalar um servidor web como o Microsoft Internet Information Services (IIS) em cada uma das VMs. A página de web predefinida em cada servidor poderiam conter o texto "Server0" ou "Servidor1" para identificar exclusivamente. Em seguida, abra um browser da Internet num cliente ligado à IPv6 Internet e navegue para o nome do anfitrião especificado para o parâmetro dnsNameforIPv6LbIP do Balanceador de carga para confirmar a conectividade de IPv6 do fim para fim para cada VM. Se vir apenas a página web a partir de apenas um servidor, poderá ter limpar a cache do browser. Abra várias sessões de navegação privadas. Deverá visualizar uma resposta a partir de cada servidor.
3. A partir de um cliente ligado à IPv4 Internet, inicie uma ligação para o endereço de IPv4 público do Balanceador de carga. Para confirmar que o Balanceador de carga é de dois VMs de balanceamento de carga, poderia testá-lo a utilizar o IIS, tal como descrito no passo 2.
4. A partir de cada VM, inicie uma ligação de saída para um dispositivo IPv6 ou IPv4 ligado à Internet. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço de IPv4 ou IPv6 público do Balanceador de carga.

>[AZURE.NOTE]
ICMP para IPv4 e IPv6 está bloqueado na rede Azure. Como resultado, ferramentas ICMP como sempre o ping falharem. Para testar a conectividade, utilize uma alternativa TCP como TCPing ou o cmdlet do PowerShell teste-NetConnection. Note que os endereços IP mostrados no diagrama são exemplos de valores que poderá ver. Uma vez que os endereços do IPv6 são atribuídos dinamicamente, os endereços que recebe serão diferentes e podem variar por região. Além disso, é comum para o endereço IPv6 público no balanceador de carga para iniciar com um prefixo diferentes que os endereços do IPv6 privados no conjunto de back-end.

## <a name="template-parameters-and-variables"></a>Parâmetros de modelo e variáveis

Contém um modelo de Gestor de recursos do Azure múltiplos variáveis e parâmetros que pode personalizar para as suas necessidades. Variáveis são utilizadas para valores fixos que não pretende que um utilizador para alterar. Parâmetros são utilizados para valores que pretende que um utilizador para fornecer ao implementar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Pode personalizar esta às necessidades do seu ambiente.

O modelo de exemplo utilizado neste artigo inclui as seguintes variáveis e parâmetros:

| Parâmetro / variável | Notas |
|-----------|-------|
| adminUsername | Especifique o nome da conta de administrador utilizada para iniciar sessão em máquinas virtuais com. |
| adminPassword | Especifique a palavra-passe da conta de administrador utilizada para iniciar sessão em máquinas virtuais com. |
| dnsNameforIPv4LbIP | Especifique o nome de anfitrião DNS que pretende atribuir como o nome do Balanceador de carga público. Este nome é resolvido para o endereço IPv4 público o Balanceador de carga. O nome tem de estar em minúscula e corresponder o regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP | Especifique o nome de anfitrião DNS que pretende atribuir como o nome do Balanceador de carga público. Este nome é resolvido para o endereço do IPv6 público o Balanceador de carga. O nome tem de estar em minúscula e corresponder o regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Isto pode ser o mesmo nome que o endereço IPv4. Quando um cliente envia uma consulta de DNS para este nome irá devolver Azure A tanto AAAA registos quando o nome é partilhado. |
| vmNamePrefix | Especifique o prefixo do nome VM. O modelo acrescenta um número (0, 1, etc.) para o nome quando são criadas as VMs. |
| nicNamePrefix | Especifique o prefixo de nome de interface de rede. O modelo acrescenta um número (0, 1, etc.) para o nome quando são criadas as interfaces de rede. |
| storageAccountName | Introduza o nome de uma conta existente do armazenamento ou especificar o nome de um novo criada pelo modelo. |
| availabilitySetName | Em seguida, introduza o nome da disponibilidade definida para ser utilizado com os VMs |
| addressPrefix | O prefixo do endereço utilizado para definir o intervalo de endereços da rede Virtual |
| subnetName | O nome da sub-rede no criada para o VNet |
| subnetPrefix | O prefixo do endereço utilizado para definir o intervalo de endereços da sub-rede |
| vnetName | Especifique o nome para o VNet utilizado pelas VMs. |
| ipv4PrivateIPAddressType | O método de alocação utilizado para o endereço IP privado (estática ou dinâmica) |
| ipv6PrivateIPAddressType | O método de alocação utilizado para o endereço IP privado (dinâmico). Apenas o IPv6 suporta atribuição dinâmica. |
| numberOfInstances | O número de ocorrências de balanceamento de carga implementado pelo modelo |
| ipv4PublicIPAddressName | Especifique o nome DNS que pretende utilizar para comunicar com o endereço de IPv4 público do Balanceador de carga. |
| ipv4PublicIPAddressType | O método de alocação utilizado para o endereço IP público (estática ou dinâmica) |
| Ipv6PublicIPAddressName | Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv6 público do Balanceador de carga. |
| ipv6PublicIPAddressType | O método de alocação utilizado para o endereço IP público (dinâmico). Apenas o IPv6 suporta atribuição dinâmica. |
| lbName | Especifique o nome do Balanceador de carga. Este nome é apresentado no portal do ou utilizado quando se referir à mesma com um clip ou PowerShell comando. |

As variáveis restantes no modelo de contenham valores derivadas que são atribuídas quando Azure cria os recursos. Não são alterados essas variáveis.
