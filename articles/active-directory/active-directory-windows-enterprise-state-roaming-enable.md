<properties
    pageTitle="Activar Enterprise Estado Roaming no Azure Active Directory | Microsoft Azure"
    description="Perguntas mais frequentes sobre as definições de Roaming do Estado de empresa em dispositivos com Windows. Enterprise Estado Roaming fornece aos utilizadores uma experiência unificada em todos os seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
    services="active-directory"
    keywords="Estado do Enterprise roaming, nuvem do windows, como ativar o enterprise Estado roaming"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>



# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Activar Enterprise Estado Roaming no Azure Active Directory

Enterprise Estado Roaming está disponível para qualquer organização com uma subscrição Premium Azure Active Directory (Azure AD). Para obter mais detalhes sobre como obter uma subscrição do Azure AD, consulte a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory).

Quando ativa Roaming de estado de empresa, sua organização serão automaticamente concedida licenças para uma subscrição gratuita e utilização limitada a Azure Rights Management. Esta subscrição gratuita está limitada à encriptar e desencriptar definições da empresa e dados da aplicação sincronizados pelo serviço de Roaming do Enterprise Estado; tem de ter uma subscrição paga para usar as capacidades da gestão de direitos do Azure completos.

Depois de obter uma subscrição Premium Azure AD, siga estes passos para ativar Roaming de estado do Enterprise:

1. Inicie sessão no portal clássico Azure.
2. À esquerda, selecione **Do ACTIVE DIRECTORY**e, em seguida, selecione a pasta para a qual pretende ativar Roaming de estado do Enterprise.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Aceda ao separador **Configurar** no canto superior.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.  Desloque-se para baixo na página e selecione **os utilizadores podem SINCRONIZAR definições e dados de aplicação empresarial**e, em seguida, clique em **Guardar**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Para um dispositivo Windows 10 alternar definições com o serviço de Roaming do Estado de empresa, o dispositivo tem autenticar utilizando uma identidade do Azure AD. Para dispositivos que estão associados a Azure AD, início de sessão do principal do utilizador é a identidade do Azure AD, pelo que não é necessária nenhuma configuração adicional. Para dispositivos que utilizam um tradicional no Active Directory local, o administrador de TI tem [ligue os dispositivos de domínio para Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Armazenamento de dados de sincronização
Dados de Roaming do Estado de empresa estão alojados num ou mais [Azure regiões](https://azure.microsoft.com/regions/ ) que melhor alinha com o valor de país/região configurado na instância do Azure Active Directory. Dados de Roaming do Enterprise estado são a partições com base em três regiões geográficas principais: América do Norte, EMEA e APAC. Enterprise Estado Roaming dados para o inquilino se encontra localmente à região geográfica e não são replicados entre as regiões.  Por exemplo, clientes que têm o valor de país/região definido para um dos países EMEA por "França" ou "Zâmbia" terá os respetivos dados alojados em uma ou das regiões Azure na Europa.  Clientes que defina o valor de país/região em Azure AD para um dos países da América do Norte como "Dos Estados Unidos" ou "Canadá" terá os respetivos dados alojados numa ou mais das regiões Azure dentro dos Estados Unidos.  Clientes que defina o valor de país/região em Azure AD para um dos países APAC como "Austrália" ou "Nova Zelândia" terá os respetivos dados alojados numa ou mais das regiões Azure dentro da Ásia.  Países da América do Sul e dados Antárctica serão alojados numa ou mais regiões Azure dentro dos Estados Unidos.  O valor de país/região está definido como parte do processo de criação de diretório do Azure AD e não pode ser modificado posteriormente. 

Se precisar de mais detalhes sobre a localização de armazenamento de dados, submeta um bilhetes com o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Gerir o estado de empresa Roaming
Administradores globais do Azure AD podem ativar e desativar o Enterprise Estado Roaming no portal do Azure clássico.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Os administradores globais podem limitar a sincronização de definições para grupos de segurança específicos.

Os administradores globais também podem ver um relatório de estado de sincronização de dispositivo por utilizador selecionando um determinado utilizador na lista de **utilizadores** instância do Active Directory e clicando no separador **dispositivos** e selecionando vista **dispositivos sincronizar definições e dados de aplicação empresarial**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

##<a name="data-retention"></a>Retenção de dados
Dados sincronizados com o Azure através do Enterprise Estado Roaming serão retidos indefinidamente, a menos que é executada uma operação de eliminação manual ou os dados em questão são determinados para ser obsoletos. 

**Eliminação explícita:** Os dados são eliminados quando um administrador do Azure elimina um utilizador ou um diretório ou um administrador os pedidos de explicitamente que os dados estão a ser eliminada.

- **Eliminação do utilizador**: quando um utilizador é eliminado no Azure AD, a conta de utilizador roaming dados será marcada para eliminação e será eliminada entre 90 para 180 dias. 
- **Eliminação de diretório**: eliminar uma pasta inteira no Azure AD é uma operação de imediata. Todos os dados de definições associados com que o diretório será marcado para eliminação e será eliminado entre 90 para 180 dias. 
- **No eliminação pedido**: Se pretender que o administrador do Azure AD eliminar manualmente dados ou dados de definições de um utilizador específico, o administrador pode arquivar um bilhetes com [Azure de suporte](https://azure.microsoft.com/support/). 

**Eliminação de dados obsoletos**: dados que não foi acedidos para um ano ("o período de retenção") será tratado como obsoletas e podem ser eliminado do Azure. O período de retenção está sujeito a alterações, mas não serão menos de 90 dias. Os dados obsoletos poderão ser um conjunto específico de definições de aplicação do Windows ou todas as definições para um utilizador. Por exemplo:
 
- Se nenhum dos dispositivos aceder a uma colecção de definições específico (por exemplo, uma aplicação é removida do dispositivo, ou um grupo de definições, tais como "Tema" está desativado para todos os dispositivos de um utilizador), em seguida, essa coleção irão tornar-se obsoletos após o período de retenção e podem ser eliminada. 
- Se um utilizador desativou sincronizar definições em todos os seus dispositivos, em seguida, nenhum dos dados definições irão ser acedidas e todos os dados de definições para esse utilizador irão tornar-se obsoletos e podem ser eliminados depois do período de retenção. 
- Se o administrador de diretório do Azure AD desativa Enterprise Estado Roaming para todo o diretório, em seguida, todos os utilizadores nesse diretório deixarão de sincronizar definições, todos os dados de definições para todos os utilizadores irão tornar-se obsoletos e podem ser eliminados depois do período de retenção. 

**Recuperação de dados eliminados**: A política de retenção de dados não é configurável. Assim que tem sido eliminados permanentemente os dados, não será recuperável. No entanto, é importante ter em atenção que os dados de definições só serão eliminados do Azure, não o dispositivo do utilizador final. Se qualquer dispositivo mais tarde reestabelece ligação ao serviço de Roaming do Estado de empresa, as definições de novamente serão sincronizadas e armazenadas no Azure.


## <a name="related-topics"></a>Tópicos relacionados
- [Descrição geral de Roaming do Estado de empresa](active-directory-windows-enterprise-state-roaming-overview.md)
- [Definições e dados roaming perguntas mais frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Definições de política e a MDM para a sincronização de definições de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referência de definições guardado no servidor do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
