<properties 
    pageTitle="Tarefas de gestão de serviço de nuvem comuns | Microsoft Azure" 
    description="Saiba como gerir serviços em nuvem no portal do Azure. Estes exemplos utilizam o portal do Azure." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/02/2016"
    ms.author="adegeo"/>


# <a name="how-to-manage-cloud-services"></a>Como gerir serviços em nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-manage-portal.md)
- [Azure portal clássico](cloud-services-how-to-manage.md)

O serviço de nuvem é gerido na área de **Serviços em nuvem (clássico)** do Azure portal. Este artigo descreve algumas ações comuns que iria tomar ao gerir os seus serviços na nuvem. Que inclui a atualizar, eliminar, dimensionamento e promover uma implementação faseada produção.

Mais informações sobre como dimensionar o seu serviço de nuvem estão disponíveis [aqui](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como: atualizar uma função de serviço de nuvem ou implementação

Se precisar de atualizar o código da aplicação do seu serviço de nuvem, utilize a **Atualizar** no pá de serviço de nuvem. Pode atualizar uma única função ou todas as funções. Para atualizar, pode carregar um ficheiro de configuração do serviço ou novo pacote de serviço.

1. No [portal do Azure][], selecione o serviço de nuvem que pretende atualizar. Este passo é aberta a pá de instância do serviço de nuvem.

2. Na pá, clique no botão de **atualização** .

    ![Botão Actualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implementação com um novo ficheiro de pacote de serviço (.cspkg) e o ficheiro de configuração de serviço (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionalmente,** Atualize a etiqueta de implementação e a conta de armazenamento. 

5. Se qualquer funções têm apenas uma instância de função, selecione a **Implementar o mesmo se uma ou mais funções contêm uma única ocorrência** para ativar a atualização continuar. 

    Azure apenas pode garantir a percentagem de 99.95 disponibilidade do serviço durante uma atualização de serviço de nuvem se cada função tem, pelo menos, duas funções instâncias (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processará pedidos de cliente, enquanto a outra é atualizada.

6. Verifique a **começar a implementação** de ter a actualização aplicada depois do carregamento do pacote já terminou.

7. Clique em **OK** para começar a actualizar o serviço.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como: trocar implementações para promover uma implementação faseada produção

Quando decidir implementar uma nova versão do serviço de nuvem, fase e testar a sua nova versão no seu ambiente de teste de serviço de nuvem. Utilize a **trocar** para mudar os URLs pela qual as duas implementações estão endereçadas e promover uma nova versão produção. 

Pode trocar implementações a partir da página de **Serviços em nuvem** ou o dashboard.

1. No [portal do Azure][], selecione o serviço de nuvem que pretende atualizar. Este passo é aberta a pá de instância do serviço de nuvem.

2. Na pá, clique no botão **trocar** .

    ![Trocar de serviços na nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. É apresentada a seguinte mensagem de confirmação.

    ![Trocar de serviços na nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implementação, clique em **OK** para trocar as híbridas.

    Trocar a implementação acontece rapidamente uma vez que os endereços IP virtuais (VIP) de é a única coisa que muda para as implementações.

    Para guardar os custos de cluster, pode eliminar a implementação de transição depois de verificar a sua implementação de produção está a funcionar como esperado.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como: ligar um recurso num serviço na nuvem

Portal do Azure não associá recursos como é que o portal de clássico Azure atual. Em vez disso, implemente recursos adicionais para o mesmo grupo de recursos a ser utilizado pelo serviço na nuvem.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como: eliminar implementações e um serviço na nuvem

Pode eliminar um serviço na nuvem, tem de eliminar cada implementação existente.

Para guardar os custos de cluster, pode eliminar a implementação de transição depois de verificar a sua implementação de produção está a funcionar como esperado. São faturada para cluster custos de instâncias de uma função implementado que estejam parados.

Utilize o seguinte procedimento para eliminar uma implementação ou o seu serviço de nuvem. 

1. No [portal do Azure][], selecione o serviço de nuvem que pretende eliminar. Este passo é aberta a pá de instância do serviço de nuvem.

2. Na pá, clique no botão **Eliminar** .

    ![Trocar de serviços na nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Pode eliminar o serviço de nuvem inteira ao selecionar **serviço na nuvem e respetivas implementações** ou escolha a **implementação de produção** ou de **implementação de teste**.

    ![Trocar de serviços na nuvem](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. Clique no botão **Eliminar** na parte inferior.

5. Para eliminar o serviço de nuvem, clique em **Eliminar serviço em nuvem**. Em seguida, no pedido de confirmação, clique em **Sim**.

> [AZURE.NOTE]
> Quando é eliminado um serviço na nuvem e monitorização verboso está configurado, tem de eliminar os dados manualmente da sua conta de armazenamento. Para obter informações sobre onde localizar as tabelas de métricas, consulte [Este](cloud-services-how-to-monitor.md) artigo.

[Portal do Azure]: https://portal.azure.com

## <a name="next-steps"></a>Próximos passos

* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate-portal.md).