<properties
    pageTitle="Implementação de FS elevada disponibilidade AD cruz geográficos no Azure com o Gestor de tráfego Azure | Microsoft Azure"
    description="Neste documento serão Saiba como implementar o AD FS no Azure para availablity alta."
    keywords="O AD fs com o Gestor de tráfego Azure, adfs com Azure tráfego Gestor, geográficos, multi Centro de dados, centros de dados geográficos, multi centros de dados geográficos, implementar o AD FS no azure, implementar azure adfs, azure adfs, azure ad fs, implementar adfs, implementar o ad fs, adfs no azure, implementar o adfs no azure, implementar AD FS no azure, adfs azure, introdução para o AD FS, Azure, AD FS no Azure, iaas , ADFS, mover adfs para azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implementação de FS elevada disponibilidade AD cruz geográficos no Azure com o Gestor de tráfego Azure

[Implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) fornece orientações passo a passo Control como pode implementar uma infraestrutura do AD FS simple para a sua organização no Azure. Este artigo fornece os passos para criar uma publicação em geográficos implementação do AD FS no Azure utilizando o [Gestor de tráfego Azure](../traffic-manager/traffic-manager-overview.md). Gestor de tráfego Azure ajuda-o a criar uma geograficamente propagação elevada disponibilidade e a infraestrutura do AD FS de alto desempenho para a sua organização ao efetuar utilização do intervalo de métodos de encaminhamento disponíveis para se adequar às necessidades diferentes a partir de infraestrutura do.

Permite uma infraestrutura do AD FS cruzada geográficos altamente disponível:

* **Eliminação do ponto de falha único:** Com as capacidades de activação pós-falha do Azure tráfego Gestor, é possível alcançar uma infraestrutura do AD FS altamente disponível mesmo quando uma dos centros de dados numa peça do globo vai para baixo
* **Melhorada desempenho:** Pode utilizar a implementação sugerida neste artigo para fornecer uma infraestrutura de AD FS de alto desempenho pode ajudar os utilizadores autenticar mais rápido. 

##<a name="design-principles"></a>Princípios de design

![Estrutura global](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Os princípios básicos da estrutura serão mesmos, tal como indicado na princípios de Design no artigo implementação do AD FS no Azure. O diagrama acima mostra uma extensão simple da implementação básica para outra à região geográfica errada. Abaixo estão alguns pontos a considerar quando expande a sua implementação para nova à região geográfica errada

* **Da rede Virtual:** Deve criar uma nova rede virtual na região geográfica que pretende implementar a infraestrutura adicional do AD FS. No diagrama indicados acima, consulte o artigo Geo1 VNET e Geo2 VNET como as duas redes virtuais em cada região geográfica errada.

* **Controladores de domínio e servidores do AD FS no novo VNET geográfica:** Recomenda-se para implementar o domínio controladores na região geográfica novo para que os servidores de AD FS na nova região não ter de contactar um controlador de domínio noutro longe de rede para concluir uma autenticação e portanto melhorar o desempenho.

* **Contas de armazenamento:** Contas do armazenamento estão associadas uma região. Uma vez que implementar máquinas numa região geográfica nova, terá de criar novas contas de armazenamento para ser utilizado na região.  

* **Grupos de segurança de rede:** Como contas de armazenamento, grupos de segurança de rede criado numa região não podem ser utilizadas no outra à região geográfica errada. Por conseguinte, terá de criar nova rede grupos de segurança semelhantes à região geográfica primeira para sub-rede INT e DMZ na região geográfica novo.

* **Etiquetas de DNS para endereços IP públicos:** Gestor de tráfego Azure pode referir-se para os pontos finais apenas através de etiquetas DNS. Por conseguinte, são necessários para criar etiquetas DNS para endereços IP públicos balanceadores da carga externos.

* **Azure Gestor de tráfego:** Gestor de tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego de utilizador para o seu pontos finais de serviço a ser executada em centros de dados diferentes em todo o mundo. Gestor de tráfego Azure funciona ao nível de DNS. Utiliza as respostas DNS para direcionar tráfego de utilizador final para os pontos finais globalmente distribuído. Clientes, em seguida, ligar a esses pontos finais diretamente. Com as diferentes opções de encaminhamento de desempenho, ponderado e prioridade, pode facilmente escolher a opção de encaminhamento adequada para necessidades da sua organização. 

* **V líquido para V líquido conectividade entre duas regiões:** Não tem de ter conectividade entre as redes virtuais própria. Uma vez que cada rede virtual tem acesso ao controladores de domínio e tem de servidor do AD FS e WAP por si só, podem trabalhar sem qualquer ligação entre as redes virtuais em diferentes regiões. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Passos para integrar o Gestor de tráfego do Azure

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implementar o AD FS na região geográfica novo
Siga os passos e diretrizes na [implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) para implementar a topologia da mesma na nova região geográfica errada.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etiquetas DNS para endereços IP públicos de balanceadores de carga Internet opostas (público)
Tal como mencionado acima, o Gestor de tráfego Azure pode apenas referir-se para os pontos finais das etiquetas de DNS e, por isso, é importante criar etiquetas DNS para endereços IP públicos balanceadores da carga externos. Por baixo de captura de ecrã mostra como pode configurar a etiqueta DNS para o endereço IP público. 

![Etiqueta de DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Implementar o Gestor de tráfego Azure

Siga os passos abaixo para criar um perfil do Gestor de tráfego. Para mais informações, também pode consultar a [Gerir um perfil do Gestor de tráfego Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Criar um perfil do Gestor de tráfego:** Dê um nome exclusivo ao seu perfil do Gestor de tráfego. Este nome do perfil faz parte do nome de DNS e age como um prefixo para a etiqueta de nome de domínio do Gestor de tráfego. O nome / prefixo é adicionado à. trafficmanager.net para criar uma etiqueta DNS para o tráfego do seu gestor. A captura de ecrã abaixo mostra o Gestor de tráfego prefixo de DNS a ser definidos como mysts e resultante etiqueta DNS será mysts.trafficmanager.net. 

    ![Gestor de tráfego a criação de perfil](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Método de encaminhamento de tráfego:** Existem três encaminhamento opções disponíveis no Gestor de tráfego:

    * Prioridade 
    * Desempenho
    * Ponderada
    
    O **Desempenho** está a opção recomendada para alcançar altamente da infraestrutura do AD FS. No entanto, pode escolher qualquer método de encaminhamento adequado para as suas necessidades de implementação. A funcionalidade de AD FS não é afetada pela opção de encaminhamento selecionada. Para mais informações, consulte [Gestor de tráfego de métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md) . No exemplo captura de ecrã indicados acima, pode ver o método de **Desempenho** seleccionado.
   
3.  **Configurar os pontos finais:** Na página de Gestor de tráfego, clique em pontos finais e selecione Adicionar. Será aberta uma página de ponto final de adicionar semelhante a captura de ecrã abaixo
 
    ![Configurar os pontos finais](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Para as entradas diferentes, siga a orientação abaixo:

    **Tipo:** Selecione o ponto final de Azure como podemos vai ser apontando para um endereço IP público Azure.

    **Nome:** Crie um nome que pretende associar o ponto final. Isto não é o nome de DNS e tem sem influência nos registos de DNS.

    **Tipo de recurso de destino:** Selecione o endereço IP público como o valor para esta propriedade. 

    **Recurso de destino:** Isto irá dar-lhe uma opção para escolher a partir de etiquetas DNS diferentes que tem disponíveis com a sua subscrição. Selecione a etiqueta DNS para.

    Adicione o ponto final de cada que pretende que o Gestor de tráfego Azure para encaminhar tráfego para à região geográfica errada.
    Para obter mais informações e instruções detalhadas sobre como adicionar / configurar os pontos finais no Gestor de tráfego, consulte [Adicionar, desativar, ativar ou eliminar pontos finais](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Configurar sonda:** Na página de Gestor de tráfego, clique na configuração. Na página de configuração, tem de alterar as definições de monitor para sonda na porta HTTP 80 e /adfs/probe caminho relativa

    ![Configurar sonda](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Certifique-se de que o estado dos pontos finais está ONLINE assim que a configuração está concluída**. Se todos os pontos finais estejam num Estado 'degradado', Gestor de tráfego Azure executará uma tentativa de utilizações para encaminhar o tráfego partindo do princípio de que estão incorreto diagnósticos e todos os pontos finais estejam acessíveis.

5. **Registos de DNS modificar para o Gestor de tráfego Azure:** Serviço de Federação deve ser um CNAME para o nome do Azure Gestor de tráfego de DNS. Crie um CNAME nos registos de DNS públicos para quem está a tentar entrar o serviço de Federação atinge realmente o Gestor de tráfego Azure.

    Por exemplo, para apontar para o Gestor de tráfego de fs.fabidentity.com de serviços de Federação, que precisa para atualizar o registo de recursos DNS para ser o seguinte:

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Testar o encaminhamento e início de sessão no AD FS   

###<a name="routing-test"></a>Encaminhamento de teste

Um teste muito básico para o encaminhamento seria experimentar o ping o nome DNS do serviço de Federação a partir de uma máquina de cada região geográfica. Dependendo do método de encaminhamento escolhido, o ponto final-realmente o ping será refletido na visualização ping. Por exemplo, se tiver seleccionado o encaminhamento de desempenho, o ponto final mais próxima da região do cliente será atingido. Abaixo encontra o instantâneo dos dois pings a partir de dois computadores de cliente de região diferente, uma numa região EastAsia e outra em EUA Ocidental. 

![Encaminhamento de teste](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS início de sessão no teste

A forma mais fácil para testar o AD FS é ao utilizar a página IdpInitiatedSignon.aspx. Para poder fazer que, é necessário para activar IdpInitiatedSignOn nas propriedades do AD FS. Siga os passos abaixo para verificar o seu programa de configuração do AD FS
 
1. Executar a abaixo cmdlet no servidor do AD FS, através do PowerShell, defini-lo ativado. Definir AdfsProperties - EnableIdPInitiatedSignonPage $true
2. A partir de qualquer máquina externos acesso https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Deverá visualizar a página de AD FS como abaixo:

    ![Teste ADFS - contestação de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    e no bem sucedida iniciar sessão,-lo irá fornecer-lhe uma mensagem de sucesso conforme apresentado abaixo:

    ![Teste ADFS - sucesso de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Ligações relacionadas
* [Implementação do AD FS básica no Azure](active-directory-aadconnect-azure-adfs.md)
* [Gestor de tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Métodos de encaminhamento de tráfego de Gestor de tráfego](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Próximos passos
* [Gerir um perfil do Gestor de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Adicionar, desativar, ativar ou eliminar pontos finais](../traffic-manager/traffic-manager-endpoints.md) 

