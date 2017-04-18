<properties
   pageTitle="Como guardar um endereço IP virtual constante para um serviço na nuvem | Microsoft Azure"
   description="Saiba como para se certificar de que não altera o endereço IP virtual (VIP) do seu serviço de nuvem Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Como guardar um endereço IP virtual constante para um serviço na nuvem

Quando atualiza um serviço na nuvem que está alojado no Azure, poderá ter de se certificar de que não altera o endereço IP virtual (VIP) do serviço. Vários serviços de gestão de domínio utilizam o sistema de nomes de domínio (DNS) para o registo de nomes de domínio. DNS só funciona se a VIP se mantém o mesmo. Pode utilizar o **Assistente de publicação** no Azure ferramentas para se certificar de que o VIP do serviço na nuvem não é alterado quando atualiza-lo. Para mais informações sobre como utilizar a gestão de domínios DNS para serviços em nuvem, consulte o artigo [configurar um nome de domínio personalizado para um serviço em nuvem Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicação de um serviço de nuvem sem alterar o respetivo VIP

VIP de um serviço na nuvem é atribuída ao primeiro implemente-o Azure num ambiente específico, tal como o ambiente de produção. Não altera o VIP, a menos que elimina a implementação explicitamente ou implicitamente ser eliminada pelo processo de atualização de implementação. Para manter o VIP, não tem de eliminar sua implementação e também deve certificar-se de que Visual Studio, não elimina sua implementação automaticamente. Pode controlar o comportamento ao especificar as definições de implementação do **Assistente de publicação**, que suporta várias opções de implementação. Pode especificar uma implementação fresca ou uma implementação de atualização, que pode ser utilizarão ou simultânea, e ambos os tipos de actualização de implementações reter o VIP. Para definições de um destes tipos diferentes de implementação, consulte o artigo [Aplicação Assistente de publicação do Azure](vs-azure-tools-publish-azure-application-wizard.md).  Além disso, pode controlar se a implementação anterior de um serviço na nuvem é eliminada caso ocorra um erro. O VIP inesperadamente poderá mudar se não definir essa opção corretamente.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Para atualizar um serviço na nuvem sem alterar o respetivo VIP

1. Depois de pelo menos uma vez a implementar o seu serviço de nuvem, abrir o menu de atalho para o nó para o seu projeto Azure e, em seguida, selecione **Publicar**. Aparece o Assistente da **Aplicação de Azure publicar** .

1. Na lista de subscrições, escolha aquele ao qual pretende implementar e, em seguida, selecione o botão **seguinte** . É apresentada a página de **Definições** do assistente.

1. No separador **Definições comuns** , verifique se o nome do serviço em nuvem para o qual está a implementar, o **ambiente**, a **Configuração de criar**e a **Configuração do serviço** estão todas as corretas.

1. No separador **Definições avançadas** , certifique-se de que a conta de armazenamento e a etiqueta de implementação estão corretos, que a caixa de verificação **Eliminar implementação em caso de falha** está desmarcada e que a caixa de verificação de atualização de **implementação** está selecionada. Ao selecionar a caixa de verificação de atualização de **implementação** , garantir que não será eliminada a sua implementação e seu VIP não serão perdida quando voltar a publicar a aplicação. Desmarcando a janela de confirmação **Eliminar implementação na caixa de verificação falha**, é Certifique-se de que seu VIP não serão perdida caso ocorra um erro durante a implementação.

1. Para especificar ainda mais como pretende que as funções a ser atualizados, escolha a ligação de **Definições** ao lado da caixa de **implementação atualizar** e, em seguida, selecione a atualização utilizarão ou actualização simultânea opção na caixa de diálogo de definições **implementação atualizar** . Se optar por atualizar elementar, cada instância é atualizada um após o outro, para que a aplicação está sempre disponível. Se optar por actualização simultânea, todas as instâncias são atualizadas ao mesmo tempo. Atualizar simultânea é mais rápido, mas o seu serviço poderá não estar disponível durante o processo de atualização.

1. Quando estiver satisfeito com as suas definições, selecione o botão **seguinte** .

1. Na página de **Resumo** do assistente, verificar as suas definições e, em seguida, selecione o botão **Publicar** .

  >[AZURE.WARNING] Se a implementação falhar, deve por que razão-Ocorreu uma falha de endereços e implementar rapidamente, para evitar a sair do seu serviço de nuvem num estado danificado.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a publicação Azure a partir do Visual Studio, consulte [Publicar Azure Assistente da aplicação](vs-azure-tools-publish-azure-application-wizard.md).
