<properties
  pageTitle="Utilizar o Azure DNS com outros serviços do Azure | Microsoft Azure"
  description="Noções sobre como utilizar o Azure DNS para resolver o nome para outros serviços do Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Utilizar o Azure DNS com outros serviços do Azure

Azure DNS é um alojado gestão e o nome do serviço de resolução DNS. Esta opção permite-lhe criar públicos DNS nomes para as outras aplicações e serviços que tiver implementado no Azure. Criar um nome para um serviço Azure no seu domínio personalizado é tão simple como adicionar um registo do tipo de correto para o seu serviço.

* Para endereços IP alocados dinamicamente, terá de criar um registo CNAME de DNS que o nome DNS que Azure criado do seu serviço de mapas. Padrões de DNS impedem-lo de utilizar um registo CNAME para o vértice zona.
* Para endereços IP atribuídos de forma estática, pode criar um registo DNS A utilizar qualquer nome, incluindo um nome de _domínio livre_ no vértice do zona.

A tabela seguinte descreve os tipos de registo suportados que podem ser utilizados para vários serviços Azure. Como pode ver a partir desta tabela, Azure DNS suporta apenas os registos DNS para recursos de rede de acesso à Internet. Azure DNS não podem ser utilizados para resolução de nomes de endereços internas e privadas.

| Serviço Azure | Interface da rede | Descrição |
|---------------|-------------------|-------------|
| Gateway de aplicação | Endereço IP público front-end | Pode criar um registo DNS A ou CNAME. |
| Balanceador de carga | Endereço IP público front-end | Pode criar um registo DNS A ou CNAME. Balanceador de carga pode ter um endereço IP público do IPv6 que dinamicamente é atribuído. Por isso, tem de criar um registo CNAME para um endereço IPv6. |
| Gestor de tráfego | Nome público | Só pode criar um CNAME que o nome de trafficmanager.net atribuído ao seu perfil do Gestor de tráfego de mapas. Para obter mais informações, consulte [Gestor de tráfego de como funciona](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Serviço na nuvem | Endereço IP público | Para endereços IP atribuídos de forma estática, pode criar um registo DNS. Para endereços IP alocados dinamicamente, terá de criar um registo CNAME que mapas para o nome de _cloudapp.net_ . Aplica esta regra a VMs criadas no portal do clássico porque estão implementados como um serviço na nuvem. Para mais informações, consulte o artigo [configurar um nome de domínio personalizado no serviços em nuvem](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Aplicação de serviço | IP externo | Para endereços IP externos, pode criar um registo DNS. Caso contrário, terá de criar um registo CNAME que mapas para o nome de azurewebsites.net. Para obter mais informações, consulte o artigo [mapear um nome de domínio personalizado para uma aplicação do Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Gestor de recursos VMs | Endereço IP público | Gestor de recursos VMs pode ter endereços IP público. Também pode ser uma VM com um endereço IP público atrás de um balanceador de carga. Pode criar um registo DNS A ou CNAME para o endereço público. Este nome personalizado pode ser utilizado para ignorar VIP no balanceador de carga. |
| VMs clássicos | Endereço IP público | VMs clássicas criada através do PowerShell ou clip pode ser configurado com um endereço de virtual (reservado) dinâmico ou estático. Pode criar um CNAME de DNS ou um registo, respetivamente. |
