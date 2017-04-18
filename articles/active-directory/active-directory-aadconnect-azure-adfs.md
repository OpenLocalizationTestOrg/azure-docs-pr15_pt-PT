<properties
    pageTitle="Serviços de Federação do Active Directory no Azure | Microsoft Azure"
    description="Neste documento serão Saiba como implementar o AD FS no Azure para availablity alta."
    keywords="implementar o AD FS no azure, implementar azure adfs, azure adfs, azure ad fs, implementar adfs, implementar o ad fs, adfs no azure, implementar adfs no azure, implementar o AD FS no azure, adfs azure, introdução para o AD FS, Azure, AD FS no Azure, iaas, ADFS, mova o adfs para azure"
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
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Implementação do AD FS no Azure 

O AD FS fornece Federação de identidade simplificado e segura e capacidades de sessão único (SSO) Web. Federação com Azure AD ou Office 365 permite aos utilizadores autenticar com as credenciais no local e aceder a todos os recursos na nuvem. Como resultado, que se torne importante ter uma infraestrutura do AD FS altamente disponível para assegurar o acesso aos recursos de ambas no local e na nuvem. Implementação do AD FS no Azure pode ajudar a alcançar a disponibilidade de alta necessária com os esforços mínimos.
Existem várias vantagens de implementação do AD FS no Azure, alguns dos mesmos estão indicadas abaixo:

* **Elevada disponibilidade** - com o poder dos conjuntos de disponibilidade do Azure, o utilizador garantir uma infraestrutura altamente disponível.
* **Fácil à escala** – precisa de mais de desempenho? Facilmente migrar para o mais eficazes máquinas por apenas alguns cliques no Azure
* **Publicação em Geo redundância** – com o Azure Geo redundância que pode ter a certeza de que a sua infraestrutura de é altamente disponível em todo o mundo
* **Fácil a gerir** – com opções de gestão altamente simplificado no portal do Azure, gerir a sua infraestrutura é muito fácil e preocupações 

## <a name="design-principles"></a>Princípios de design

![Estrutura de implementação](./media/active-directory-aadconnect-azure-adfs/deployment.png)

O diagrama acima mostra a topologia básica recomendada para iniciar a sua infraestrutura de AD FS no Azure a implementar. Os princípios atrás de vários componentes da topologia de estão indicados abaixo:

* **Cc / ADFS servidores**: Se tiver menos de 1.000 utilizadores simplesmente pode instalar função de AD FS no seu controladores de domínio. Se não pretende qualquer impacto no desempenho nos controladores de domínio ou se tiver mais de 1.000 utilizadores, em seguida, implemente o AD FS em servidores separados.
* **Servidor de WAP** – é necessário implementar servidores Proxy de aplicação Web, para que os utilizadores podem contactar o AD FS quando não estiverem na rede da empresa também.
* **DMZ**: servidores Proxy de aplicação Web do irão ser colocados a DMZ e apenas TCP/443 acesso é permitido entre a DMZ e de sub-rede interna.
* **Carregar balanceadores**: para assegurar que elevada disponibilidade dos servidores do AD FS e Proxy de aplicação Web, recomendamos que utilize um balanceador de carga interna para servidores de AD FS e Balanceador de carga Azure para servidores Proxy de aplicação Web.
* **Conjuntos de disponibilidade**: A fornecer redundância a sua implementação do AD FS, é recomendável que agrupam duas ou mais máquinas virtuais um conjunto de disponibilidade das cargas de trabalho semelhantes. Esta configuração garante que durante um evento de manutenção planeada ou não planeado, pelo menos uma máquina de virtual estará disponível
* **Contas de armazenamento**: recomenda-se para ter duas contas de armazenamento. Ter uma conta de armazenamento única pode levar a criação de um ponto de falha único e pode causar a implementação para ficar indisponíveis num cenário de pouco onde a conta de armazenamento vai para baixo. Duas contas de armazenamento irão ajudá-associar uma conta de armazenamento para cada linha de falhas.
* **Separação de rede**: devem ser implementados servidores Proxy de aplicação Web numa rede DMZ em separado. Pode dividir uma rede virtual em duas sub-redes e, em seguida, implementar o servidor (es) Proxy de aplicação Web numa sub-rede isolada. Pode configurar as definições de grupo de segurança de rede para cada sub-rede simplesmente e permitir apenas necessária comunicação entre duas sub-redes. Obter mais detalhes são dadas por baixo de cenário de implementação

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Passos para implementar o AD FS no Azure

Os passos mencionados nesta secção descrevem o guia para implementar o abaixo abuso infraestrutura do AD FS no Azure.

### <a name="1-deploying-the-network"></a>1. a implementar o da rede

Conforme descrito acima, pode quer criar duas sub-redes numa única rede virtual ou, caso contrário, criar duas redes virtuais completamente diferentes (VNet). Este artigo irá focar-se em implementar uma única rede virtual e dividi-lo em duas sub-redes. Este é atualmente uma abordagem mais fácil como duas VNets separadas seria exigir uma VNet ao VNet gateway para comunicações.

**1.1 criar rede virtual**

![Criar rede virtual](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
No portal do Azure, selecione rede virtual e pode implementar a rede virtual e uma sub-rede imediatamente com apenas um clique. INT sub-rede também é definida e está agora pronto para ser VMs seja adicionada.
O passo seguinte consiste em adicionar outra sub-rede para a rede, ou seja, sub-rede DMZ. Para criar sub-rede DMZ, simplesmente

* Selecione a rede recentemente criada
* Nas propriedades selecione sub-rede
* Na sub-rede painel, clique no botão Adicionar
* Forneça as informações de espaço de sub-rede nome e endereço, para criar a sub-rede

![Sub-rede](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Sub-rede DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. criar a rede grupos de segurança**

Um grupo de segurança de rede (NSG) contém uma lista de regras de lista de controlo de acesso (ACL) que permitir ou negar o tráfego de rede para o seu instâncias VM numa rede Virtual. NSGs podem ser associados com sub-redes ou instâncias VM individuais dentro desse sub-rede. Quando uma NSG está associada uma sub-rede, as regras ACL aplicam a todas as instâncias VM nessa sub-rede.
Para este orientações, criamos duas NSGs: uma para uma rede interna e uma DMZ. Estes serão identificados NSG_INT e NSG_DMZ respetivamente.

![Criar NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Depois do NSG é criado, irá ser 0 entradas e 0 as regras de saída. Depois das funções nos respetivos servidores são instaladas e funcional, em seguida, as regras de entrada e saídas podem ser efetuadas de acordo com o nível de segurança pretendido.

![Iniciar NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Após a criação de NSGs, associar NSG_INT sub-rede INT e NSG_DMZ com sub-rede DMZ. Uma captura de ecrã do exemplo é dado abaixo:

![Configurar o NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Clique em sub-redes para abrir o painel para sub-redes
* Selecione a sub-rede para associar a NSG 

Depois de configuração, o painel para sub-redes deve aspeto abaixo:

![Sub-redes após NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Criar ligação no local**

Vamos terá uma ligação para o local para implementar o controlador de domínio (CC) no azure. Azure oferece várias opções de conectividade para ligar a sua infraestrutura no local à sua infraestrutura Azure.

* Site de ponto
* Site para site da rede virtual
* ExpressRoute

Recomenda-se para utilizar ExpressRoute. ExpressRoute permite-lhe criar ligações privada entre o Azure centros de dados e infraestrutura que esteja no seu local ou num ambiente localização cocriação. ExpressRoute ligações não aceda através da Internet pública. Estas funcionalidades oferecem mais fiabilidade, velocidades mais rápidas, latências inferiores e superior segurança que as ligações típicas através da Internet.
Enquanto recomenda-se para utilizar ExpressRoute, pode escolher qualquer método de ligação adequado para a sua organização. Para saber mais informações sobre ExpressRoute e as várias opções de conectividade utilizando ExpressRoute, consulte [Descrição geral técnica de ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Crie contas de armazenamento

Para manter a disponibilidade de alta e evitar dependência de uma conta de armazenamento única, pode criar duas contas de armazenamento. Divida máquinas em cada conjunto de disponibilidade em dois grupos e, em seguida, atribua cada grupo de uma conta de armazenamento em separado.

![Criar contas de armazenamento](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. criar conjuntos de disponibilidade

Para cada função (CC/AD FS e WAP), crie conjuntos de disponibilidade que irão conter 2 máquinas no mínimo. Isto irá ajudar atingir uma maior disponibilidade para cada função. Apesar de conjuntos de criar a disponibilidade, é essencial decida no seguinte:
* **Falhas domínios**: máquinas virtuais no mesmo domínio falhas partilhar a mesma origem power e mudar de rede física. Um mínimo de 2 domínios de falhas são recomendadas. O valor predefinido é 3 e pode deixá-la como está para esta implementação
* **Atualizar domínios**: máquinas pertencentes ao mesmo domínio de atualização são reiniciadas em conjunto durante uma atualização. Pretende que o mínimo de 2 actualização de domínios. O valor predefinido é de 5 e pode deixá-la como está para esta implementação

![Conjuntos de disponibilidade](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Criar os seguintes conjuntos de disponibilidade

| Conjunto de disponibilidade | Função | Domínios de falhas | Atualizar domínios |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | CC/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. implementar máquinas virtuais
O passo seguinte é implementar máquinas virtuais que irá alojar diferentes funções na sua infraestrutura. Um mínimo de duas máquinas recomenda-se em cada conjunto de disponibilidade. Crie seis máquinas virtuais para a implementação básica.

| Máquina | Função | Sub-rede | Conjunto de disponibilidade | Conta de armazenamento | Endereço IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|CC/ADFS|INT|contosodcset|contososac1|Estático|
|contosodc2|CC/ADFS|INT|contosodcset|contososac2|Estática|
|contosowap1|WAP|DMZ|contosowapset|contososac1|Estático|
|contosowap2|WAP|DMZ|contosowapset|contososac2|Estático|

À medida que poderá provavelmente, reparou, não foi especificado nenhum NSG. Isto acontece porque azure permite-lhe utilizar NSG ao nível da sub-rede. Em seguida, pode controlar o tráfego de rede de computador utilizando o NSG individual associada quer sub-rede ou, caso contrário, o objeto NIC. Leia sobre do mais [o que é um grupo de segurança de rede (NSG)](https://aka.ms/Azure/NSG).
Endereço IP estático é recomendado se está a gerir o DNS. Pode utilizar o DNS do Azure e em vez disso, nos registos de DNS para o seu domínio, referir-se para as novas máquinas pelo respetivo FQDNs Azure.
O painel de máquina virtual deve aspeto abaixo depois de concluída a implementação:

![Máquinas virtuais implementadas](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. configurar o controlador de domínio / servidores AD FS
 Para autenticar qualquer pedido de receção, AD FS será necessário contactar o controlador de domínio. Para guardar a viagem dispendiosa a partir do Azure Centro de dados no local para autenticação, recomenda-se para implementar uma réplica do controlador de domínio no Azure. Para poder atingir elevada disponibilidade, recomenda-se para criar um conjunto de disponibilidade a ' pelo menos 2 de controladores de domínio.

|Controlador de domínio|Função|Conta de armazenamento|
|:-----:|:-----:|:-----:|
|contosodc1|Réplica|contososac1|
|contosodc2|Réplica|contososac2|

* Promover os dois servidores como controladores de domínio réplica com o DNS
* Configure os servidores de AD FS instalando a função de AD FS utilizando o Gestor de servidor.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. implementar Balanceador de carga interna (ILB)

**6.1. Crie a ILB**

Para implementar um ILB, selecione balanceadores de carga no portal do Azure e clique em Adicionar (+).
>[AZURE.NOTE] Se não vir **Balanceadores de carga** no menu, clique em **Procurar** no canto inferior esquerdo do portal e desloque-se até ver **Balanceadores de carga**.  Em seguida, clique na estrela amarela para adicioná-lo ao menu. Agora, selecione o novo ícone de Balanceador de carga para abrir o painel para começar a configuração do Balanceador de carga.

![Navegue até Balanceador de carga](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nome**: dar um nome adequado ao balanceador de carga
* **Esquema de cores**: uma vez que este Balanceador de carga será colocada à frente os servidores de AD FS e destina-se para ligações de rede interna apenas, selecione "Interno"
* **Rede virtual**: escolher a rede virtual onde está a implementar o AD FS
* **Sub-rede**: escolher a sub-rede interna aqui
* **Atribuição de endereço IP**: dinâmicos

![Balanceador de carga interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Depois de clicar em criar e a ILB é implementada, deverá vê-lo na lista de balanceadores de carga:

![Carregar balanceadores depois ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Passo seguinte é configurar o conjunto de dados back-end e a sonda de back-end.

**6.2. Configurar agrupamento de back-end ILB**

Selecione o ILB recentemente criado no painel de balanceadores de carga. Será aberto o painel de definições. 
1.  Selecionar conjuntos de dados back-end a partir do painel de definições
2.  No painel de conjunto de dados back-end de adicionar, clique em adicionar máquina de virtual
3.  Será apresentada com um painel onde pode escolher o conjunto de disponibilidade
4.  Escolha o conjunto de disponibilidade do AD FS

![Configurar ILB back-end agrupamento](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. Configurar sonda**

No painel de definições de ILB, selecione sondas.
1.  Clique em Adicionar
2.  Fornecer detalhes de sonda um. **Nome**: sonda nome b. **Protocolo**: TCP c. **Porta**: 443 (HTTPS) d. **Intervalo**: 5 (valor predefinido) – este é o intervalo no qual ILB irá sonda máquinas no conjunto de back-end "e". **Limiar danificado limite**: 2 (predefinição val ue) – este é o limiar de falhas consecutivas sonda após o qual ILB irá declarar uma máquina no conjunto de back-end deixasse de responder e pare a enviar tráfego para o mesmo.

![Configurar sonda ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. Crie regras de balanceamento de carga**

Para o tráfego de forma eficaz saldo, o ILB deverá ser configurada com regras de balanceamento de carga. Para criar uma regra balanceamento de carga, 
1.  Selecione a regra a partir do painel de definições do ILB balanceamento de carga
2.  Clique em Adicionar no painel de regra de balanceamento de carga
3.  No painel de regra de balanceamento de carga de adicionar um. **Nome**: forneça um nome para a regra b. **Protocolo**: selecione TCP c. **Porta**: 443 d. **Porta de back-end**: 443 "e". **Agrupamento de back-end**: selecione o conjunto de que criou para o AD FS cluster f anterior. **Sonda**: selecione a sonda criada para os servidores de AD FS anterior

![Configurar ILB balanceamento de regras](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Actualizar DNS com ILB**

Aceda ao seu servidor DNS e criar um CNAME para o ILB. O CNAME deve ser para o serviço de federação com o endereço IP apontar para o endereço IP da ILB. Por exemplo, se o endereço de ILB DIP for 10.3.0.8 e o serviço de Federação instalado está fs.contoso.com, em seguida, crie um CNAME para apontar para 10.3.0.8 de fs.contoso.com.
Isto irá garantir que todas as comunicações relativamente ao fim fs.contoso.com para cima na ILB e são encaminhadas corretamente.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. configurar o servidor de Proxy de aplicação Web

**7.1. configurar os servidores Proxy de aplicação Web para aceder aos servidores do AD FS**

Para garantir que os servidores Proxy de aplicação Web são conseguir contactar os servidores de AD FS atrás de ILB, crie um registo na %systemroot%\system32\drivers\etc\hosts para o ILB. Repare que o nome distinto (DN) deve ser Federação nome do serviço, por exemplo fs.contoso.com. E a entrada IP deve ser que o endereço IP do ILB (10.3.0.8 tal como no exemplo).

**7.2. instalar a função de Proxy de aplicação Web**

Depois de garantir que os servidores Proxy de aplicação Web são capazes de atingir os servidores de AD FS atrás ILB, pode instalar os servidores Proxy de aplicação Web seguinte. Os servidores Proxy de aplicação Web não ser associados ao domínio. Instale as funções de Proxy de aplicação Web nos servidores Proxy de aplicação Web duas ao selecionar a função de acesso remoto. O Gestor de servidor irá guiá-lo para concluir a instalação WAP.
Para mais informações sobre como implementar WAP, leia [instalar e configurar o servidor de Proxy de aplicação Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. implementar Internet opostas Balanceador de carga (pública)

**8.1. Crie Internet opostas Balanceador de carga (pública)**
 
No portal do Azure, selecione balanceadores de carga e, em seguida, clique em Adicionar. No painel de Balanceador de carga de criar, introduza as seguintes informações
1. **Nome**: nome para o Balanceador de carga
2. **Esquema de cores**: público – esta opção indica Azure que este Balanceador de carga, será necessário um endereço público.
3. **IP Address**: criar um novo endereço IP (dinâmico)

![Balanceador de carga oposta Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Após a implementação, Balanceador de carga serão apresentados na lista de balanceadores de carregamento.

![Lista de Balanceador de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. atribuir um rótulo DNS para o endereço IP público**

Clique na entrada do Balanceador de carga recentemente criado no painel de balanceadores de carga para ativar o painel de para a configuração. Siga abaixo passos para configurar a etiqueta DNS para o endereço IP público:
1.  Clique no endereço IP público. Isto irá abrir o painel para o endereço IP público e as suas definições
2.  Clique em configuração
3.  Fornece uma etiqueta DNS. Irão tornar-se a etiqueta DNS pública que pode aceder a partir de qualquer lugar, por exemplo contosofs.westus.cloudapp.azure.com. Pode adicionar uma entrada no DNS externo do serviço de Federação (como fs.contoso.com) que é resolvida como a etiqueta DNS do Balanceador de carga externos (contosofs.westus.cloudapp.azure.com).

![Configurar opostas Balanceador de carga de internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurar internet opostas Balanceador de carga (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. configurar o conjunto de back-end para Balanceador de carga de Internet opostas (público)** 

Siga os mesmos passos vistos criar Balanceador de carga interno, para configurar o conjunto de back-end para Internet opostas Balanceador de carga (pública) como a disponibilidade configurar para os servidores WAP. Por exemplo, contosowapset.

![Configurar o conjunto de back-end de Internet Balanceador de carga opostas](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. Configurar sonda**

Siga os mesmos passos tal como configurar o Balanceador de carga interna para configurar a sonda para o conjunto de dados back-end dos servidores WAP.

![Configurar a sonda de Internet Balanceador de carga opostas](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. Criar regra (s) de balanceamento de carga**

Siga os mesmos passos vistos ILB para configurar a regra balanceamento de carga para TCP 443.

![Configurar regras de balanceamento de Internet Balanceador de carga opostas](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. proteger a rede

**9.1. proteger a sub-rede interna**

Resumindo, terá das seguintes regras para eficazmente seguro sua sub-rede interna (por ordem conforme indicado abaixo)

|Regra|Descrição|Fluxo|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Permitir a comunicação HTTPS de DMZ | Entrada |
|DenyInternetOutbound| Sem acesso à internet | Saída |

![Regras de acesso INT (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comentário]: <> (![regras de acesso INT (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comentário]: <> (![INT as regras de acesso (saídas)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. proteger sub-rede DMZ**

|Regra|Descrição|Fluxo|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Permitir HTTPS de internet para o DMZ | Entrada|
|DenyInternetOutbound|  Tudo exceto HTTPS Internet está bloqueado | Saída |

![Regras de acesso Exp (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comentário]: <> (![regras de acesso Exp (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comentário]: <> (![EXP as regras de acesso (saídas)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Se a autenticação de certificado de utilizador do cliente (autenticação de clientTLS utilizando X509 certificados de utilizador) é necessário, em seguida, o AD FS requer TCP 49443 de porta de estar ativada para acesso de entrada.

###<a name="10--test-the-ad-fs-sign-in"></a>10. início de sessão no teste o AD FS

A forma mais fácil é testar o AD FS utilizando a página IdpInitiatedSignon.aspx. Para poder fazer que, é necessário para activar IdpInitiatedSignOn nas propriedades do AD FS. Siga os passos abaixo para verificar o seu programa de configuração do AD FS
1.  Executar a abaixo cmdlet no servidor do AD FS, através do PowerShell, defini-lo ativado.
    Definir AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  A partir de qualquer máquina externos acesso https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  Deverá visualizar a página de AD FS como abaixo:

![Página de início de sessão de teste](./media/active-directory-aadconnect-azure-adfs/test1.png)

No início de sessão bem sucedida,-lo irá fornecer-lhe uma mensagem de sucesso conforme apresentado abaixo:

![Teste sucesso](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modelo para implementar o AD FS no Azure

O modelo implementa uma configuração de 6 máquina, 2 para controladores de domínio, o AD FS e WAP.

[AD FS no modelo de implementação do Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Pode utilizar uma rede virtual existente ou criar um novo VNET enquanto implementar este modelo. Os vários parâmetros disponíveis para personalizar a implementação estão indicados abaixo, com a descrição da utilização do parâmetro no processo de implementação. 

| Parâmetro | Descrição |
|:--------|:-----|
|Localização| Região para implementar os recursos para, por exemplo: Oriente-nos. |
|StorageAccountType| O tipo de conta de armazenamento criada|
|VirtualNetworkUsage| Indica se uma nova rede virtual será criado ou utilize um já existente|
|VirtualNetworkName| O nome da rede Virtual para criar, obrigatória na utilização de rede virtual existentes ou novas|
|VirtualNetworkResourceGroupName| Especifica o nome do grupo de recursos onde reside a rede virtual existente. Ao utilizar uma rede virtual existente, este torna-se um parâmetro obrigatório para a implementação possa localizar o ID da rede virtual existente|
|VirtualNetworkAddressRange| O intervalo de endereço do novo VNET, é obrigatório se criar uma nova rede virtual|
|InternalSubnetName| O nome do sub-rede interna obrigatória em ambas as opções da utilização da rede virtual (novas ou existentes)|
|InternalSubnetAddressRange| O intervalo de endereços da sub-rede interno, que contém os servidores controladores de domínio e ADFS, obrigatórios se criar uma nova rede virtual.|
|DMZSubnetAddressRange| O intervalo de endereços da sub-rede dmz, que contém os Windows aplicação servidores proxy, obrigatórios se criar uma nova rede virtual.|
|DMZSubnetName| O nome da sub-rede interna obrigatória em ambas as opções da utilização da rede virtual (novas ou existentes). |
|ADDC01NICIPAddress| O endereço IP interno do primeiro controlador de domínio, este endereço IP forem estática atribuído para o Centro de dados e tem de ser um endereço de ip válido dentro da sub-rede interno|
|ADDC02NICIPAddress| O endereço IP interno do segundo controlador de domínio, este endereço IP será atribuído estática para o Centro de dados e tem de ser um endereço de ip válido dentro da sub-rede interno|
|ADFS01NICIPAddress| O endereço IP interno do primeiro servidor ADFS, este endereço IP será atribuído estática no servidor ADFS e tem de ser um endereço de ip válido dentro da sub-rede interno|
|ADFS02NICIPAddress| O endereço IP interno do segundo servidor ADFS, este endereço IP será atribuído estática no servidor ADFS e tem de ser um endereço de ip válido dentro da sub-rede interno|
|WAP01NICIPAddress| O endereço IP interno do primeiro servidor WAP, este endereço IP forem estática atribuído ao servidor WAP e tem de ser um endereço de ip válido dentro da sub-rede DMZ|
|WAP02NICIPAddress| O endereço IP interno do segundo servidor de WAP, este endereço IP forem estática atribuído ao servidor WAP e tem de ser um endereço de ip válido dentro da sub-rede DMZ|
|ADFSLoadBalancerPrivateIPAddress| O endereço IP interno do Balanceador de carga ADFS, este endereço IP forem estática atribuído ao balanceador de carga e tem de ser um endereço de ip válido dentro da sub-rede interno|
|ADDCVMNamePrefix| Prefixo de nome de máquina virtual para controladores de domínio|
|ADFSVMNamePrefix| Prefixo de nome de máquina virtual para os servidores de ADFS|
|WAPVMNamePrefix| Prefixo de nome de máquina virtual para os servidores de WAP|
|ADDCVMSize| O tamanho da memória virtual os controladores de domínio|
|ADFSVMSize| O tamanho da memória virtual dos servidores ADFS|
|WAPVMSize| O tamanho da memória virtual dos servidores WAP|
|AdminUserName| O nome do administrador local das máquinas virtuais|
|AdminPassword| A palavra-passe para a conta de administrador local das máquinas virtuais|

## <a name="additional-resources"></a>Recursos adicionais
* [Conjuntos de disponibilidade](https://aka.ms/Azure/Availability ) 
* [Balanceador de carga Azure](https://aka.ms/Azure/ILB)
* [Balanceador de carga interno](https://aka.ms/Azure/ILB/Internal)
* [Balanceador de carga de expostos à Internet](https://aka.ms/Azure/ILB/Internet)
* [Contas de armazenamento](https://aka.ms/Azure/Storage )
* [Redes virtuais Azure](https://aka.ms/Azure/VNet)
* [O AD FS e hiperligações de Proxy da aplicação Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Próximos passos

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
* [Configurar e gerir o seu AD FS utilizando a ligação do Azure AD](active-directory-aadconnectfed-whatis.md)
* [Implementação de FS elevada disponibilidade AD cruz geográficos no Azure com o Gestor de tráfego Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




