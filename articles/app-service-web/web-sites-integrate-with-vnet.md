<properties 
    pageTitle="Integrar uma aplicação uma rede Virtual Azure" 
    description="Mostra-lhe como ligar uma aplicação na aplicação de serviço de Azure a uma rede de virtual Azure nova ou existente" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar a aplicação uma rede Virtual Azure #

Este documento descreve a funcionalidade de integração de rede virtual do serviço de aplicação do Azure e mostra como configurá-lo com aplicações na [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Se não está familiarizado com o Azure redes virtuais (VNETs), esta é uma funcionalidade que lhe permite colocar muitos dos seus recursos Azure numa rede routeable não internet que pode controlar o acesso a.  Estas redes, em seguida, podem ser ligadas às suas redes local ligado através de uma variedade de tecnologias VPN.  Para saber mais sobre redes virtuais Azure começam com as informações aqui: [Descrição geral de rede Virtual Azure][VNETOverview].  

O serviço de aplicação do Azure tem duas formas.  

1. Os sistemas de inquilinos com várias que suportam as diversas preços planos
1. A funcionalidade de premium do ambiente de serviço de aplicação (Auxiliar) que implementa o seu VNET.  

Este documento analise VNET integração e não ambiente de serviço de aplicação.  Se pretender obter mais informações sobre a funcionalidade de Auxiliar, em seguida, comece com as informações aqui: [introdução de ambiente de serviço de aplicação][ASEintro].

Integração de VNET dá o acesso de aplicação web aos recursos na sua rede virtual mas não de conceder acesso privado para a sua aplicação web da rede virtual.  Acesso a sites privada só está disponível com um Auxiliar configurado com uma interno carga balanceador (ILB).  Para obter detalhes sobre como utilizar um Auxiliar ILB, comece com o artigo aqui: [criar e utilizar um Auxiliar ILB][ILBASE]. 

Um cenário comum onde utilizaria VNET integração é ativar o acesso a partir da sua aplicação web a uma base de dados ou serviços web em execução numa máquina virtual na sua rede virtual Azure.  Com a integração de VNET não precisa de expor um ponto final de público para as aplicações no seu VM mas pode como alternativa, utilizam os endereços de encaminháveis não internet privados.  

A funcionalidade de integração de VNET:

- requer um padrão ou Premium preços plano 
- irá funcionar com Classic(V1) ou recurso Manager(V2) VNET 
- suporta TCP e UDP
- funciona com aplicações Web, de API e Mobile
- permite que uma aplicação ligar a apenas 1 VNET cada vez
- permite até 5 VNETs para integrado num plano de serviço de aplicação 
- permite que o mesmo VNET ser utilizado por várias aplicações num plano de serviço de aplicação
- suporta um SLA 99,9% devido a uma dependência de VNET Gateway

Eis algumas coisas que VNET integração não suporta incluindo:

- montagem numa unidade
- Integração do AD 
- NetBios
- acesso a sites privada

### <a name="getting-started"></a>Introdução ###
Eis algumas coisas a ter em conta antes de ligar a sua aplicação web a uma rede virtual:

- Integração de VNET só funciona com aplicações num **padrão** ou **Premium** preços plano.  Se ativar a funcionalidade e, em seguida, dimensionar o seu plano de serviço de aplicação para um plano de preços não suportado as suas aplicações irão perder suas ligações para VNETs que estão a utilizar.  
- Se a sua rede virtual destino já existir, tem de ter VPN ponto-para-site ativada com um gateway encaminhamento dinâmico antes de que podem ser ligado a uma aplicação.  Não é possível ativar o site do ponto de rede privada Virtual (VPN) se o gateway está configurado com encaminhamento estático.
- O VNET tem de ser na mesma subscrição do seu Plan(ASP) de serviço de aplicação.  
- As aplicações que se integram com um VNET irão utilizar o DNS especificada para esse VNET.
- Por predefinição as suas aplicações integração só serão encaminhar o tráfego para o seu VNET com base nas rotas que são definidas em seu VNET.  


## <a name="enabling-vnet-integration"></a>Ativar a integração de VNET ##

Este documento é focaram-se principalmente no utilizando o Portal do Azure para a integração de VNET.  Para ativar a integração de VNET com a sua aplicação através do PowerShell, siga as instruções aqui: [ligar a aplicação à sua rede utilizando o PowerShell virtual][IntPowershell].

Tem a opção para ligar a sua aplicação a uma rede virtual nova ou existente.  Se criar uma nova rede como parte da sua integração, em seguida, para além de criar apenas a VNET, um gateway encaminhamento dinâmico será pré-configurada para si e aponte para o Site VPN irão estar ativada.  

>[AZURE.NOTE] Configurar uma nova integração de rede virtual pode demorar alguns minutos.  

Para ativar a integração de VNET abrir sua aplicação definições e, em seguida, selecione funcionamento em rede.  IU disponibilizado oferece três opções de redes.  Este guia é apenas a ser entregues VNET integração apesar híbrido ligações e ambientes de serviço de aplicação são descritos mais adiante neste documento.  

Se a sua aplicação está não no correcto preços plano IU helpfully permite-lhe dimensionar o seu plano para um plano de preços superior da sua escolha.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Ativar a integração de VNET com um VNET pré-existentes###
IU de integração de VNET permite-lhe selecionar a partir de uma lista dos seus VNETs.  O VNETs clássica irá indicar que estes provêm tal com a palavra "Clássico" Parêntese junto ao nome do VNET.  A lista estiver ordenada assim que o Gestor de recursos VNETs são listados em primeiro lugar.  Na imagem é mostrada abaixo pode ver que apenas uma VNET pode ser selecionado.  Existem vários motivos que um VNET vai ser a cinzento incluindo:

- o VNET está na outra subscrição que a sua conta tem acesso ao
- o VNET não tem o ponto para Site activado
- o VNET não tem um gateway encaminhamento dinâmico


![][2]

Para ativar a integração basta clicar no VNET que pretende integrar com.  Depois de selecionar o VNET, a aplicação vai ser reiniciada automaticamente para que as alterações sejam aplicadas.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Ativar o ponto de Site num VNET clássico #####
Se o seu VNET não tem um gateway nem tem o ponto de Site, em seguida, tem de configurar que o primeiro.  Para fazê-lo para um VNET clássico, aceda ao [Portal do Azure] [ AzurePortal] e abra a lista de Virtual Networks(classic).  A partir do clique aqui na rede que pretende integrar com e clique na caixa de grande em Essentials denominado ligações VPN.  A partir daqui pode criar o seu ponto de VPN do site e até mesmo mantê-la a criar um gateway.  Depois de aceder ao site experiência de criação de gateway através do ponto será cerca de 30 minutos antes de está pronto.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Ativar o ponto de Site no VNET Gestor de recursos #####

Para configurar um Gestor de recursos VNET com um gateway e aponte para o Site tem de utilizar o PowerShell como documentado aqui, [Configurar uma ligação ponto-para-Site a uma rede virtual através do PowerShell][V2VNETP2S].  Ainda na IU para executar esta funcionalidade não está disponível. 

### <a name="creating-a-pre-configured-vnet"></a>Criar uma VNET pré-configurada ###
Se pretender criar um novo VNET que está configurado com um gateway e ponto-para-Site, em seguida, o serviço de aplicação à rede IU tem a funcionalidade para fazê-lo, mas apenas para um Gestor de recursos VNET.  Se pretender criar um VNET clássica com um gateway e um ponto para o Site, em seguida, tem de fazer isto manualmente através da interface de utilizador de funcionamento em rede. 

Para criar um VNET Gestor de recursos através da UI de integração de VNET, basta selecionar **Criar nova rede Virtual** e forneça a:

- Nome de rede virtuais
- Bloco de endereços de rede virtual
- Nome de sub-rede
- Bloco de endereços de sub-rede
- Bloco de endereços do gateway
- Bloco de endereços do ponto-para-Site

Se pretender que este VNET para ligar a qualquer um dos seus outra rede deve evitar escolher espaço de endereços IP sobrepõe-se com essas redes.  

>[AZURE.NOTE] Criação de Gestor de recursos VNET com um gateway demora cerca de 30 minutos e atualmente irá não integrar o VNET com a sua aplicação.  Depois do VNET é criado com o gateway tem de voltar atrás para a sua aplicação VNET integração IU e selecione o seu novo VNET.

![][3]

Azure VNETs normalmente são criados no prazo de endereços de rede privada.  Por predefinição a integração de VNET funcionalidade irá encaminha qualquer tráfego destinado a esses intervalos de endereços IP para o seu VNET.  Os intervalos de endereços IP privados são:

- 10.0.0.0/8 - Este é o mesmo que 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - este é o mesmo que 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - este é o mesmo que 192.168.0.0 - 192.168.255.255
 
O espaço de endereços VNET tem de ser especificado na notação CIDR.  Se não está familiarizado com notação CIDR, é um método para especificar os blocos de endereço utilizando um endereço IP e um número inteiro que representa a máscara de rede. Como uma referência rápida, considere que 10.1.0.0/24 seria 256 endereços e 10.1.0.0/25 seria 128 endereços.  Um endereço de IPv4 com um /32 seria apenas 1 endereço.  

Se definir as informações do servidor DNS aqui, em seguida, que será definido para sua VNET.  Após a criação de VNET pode editar esta informação a partir de experiências do utilizador VNET.

Quando cria uma VNET clássica utilizando a IU de integração de VNET, criará uma VNET no mesmo grupo de recursos, como a sua aplicação. 

## <a name="how-the-system-works"></a>Como funciona o sistema ##
Nas folhas de rosto esta funcionalidade constrói na parte superior de tecnologia de ponto-para-Site VPN para ligar a sua aplicação ao seu VNET.   Aplicações na aplicação de serviço de Azure tem uma arquitetura de sistema do inquilino com várias que exclua aprovisionamento uma aplicação diretamente num VNET como é concluído com máquinas virtuais.  Através da criação de tecnologia de ponto-para-site podemos limitar o acesso de rede à apenas máquina virtual que aloja a aplicação.  Acesso à rede está restringido nesses anfitriões de aplicação para que as suas aplicações só podem aceder às redes que configurar-lhes aceder.  

![][4]
 
Se ainda não configurou um servidor de DNS com a sua rede virtual terá de utilizar endereços IP.  Ao utilizar endereços IP, lembre-se de que o benefício principal esta funcionalidade está que permite-lhe utilizar os endereços privados na rede da sua privado.  Se definir a sua aplicação para que utilize endereços IP público para um dos seus VMs, em seguida, não estiver a utilizar a funcionalidade de integração de VNET e está a comunicar através da internet.


##<a name="managing-the-vnet-integrations"></a>Gerir as integrações VNET##

A capacidade de ligar e desligar para um VNET é um nível de aplicação.  As operações que podem afetar a integração de VNET entre várias aplicações são a um nível ASP.  A partir de IU que é apresentada a nível da aplicação pode obter detalhes no seu VNET.  A maior parte das mesmas informações também é apresentada ao nível do ASP.  

![][5]

A partir da página de estado da funcionalidade de rede pode ver se a sua aplicação está ligada à sua VNET.  Se o seu gateway VNET destina-se para baixo qualquer motivo, em seguida, isto seria Mostrar ligado como não.  

As informações que tem agora disponível para si na aplicação que nível VNET integração IU é igual a informação detalhada que receber do ASP.  Seguem-se esses itens:

- Nome VNET - esta hiperligação abre a rede da IU
- Localização - isto reflete a localização da sua VNET.  É possível integrar com um VNET noutra localização.
- Estado do certificado - existem certificados utilizados para proteger a ligação VPN entre a aplicação e o VNET.  Isto apresenta um teste para se certificar de que estão sincronizados.
- Estado do gateway - seu gateways deverá para baixo por qualquer motivo, em seguida, a aplicação não é possível aceder aos recursos a VNET.  
- Espaço de endereços VNET - este é o espaço de endereços IP para o seu VNET.  
- Aponte para o espaço de endereços do Site - este é o ponto para espaço de endereços IP de site para o seu VNET.  A aplicação irá mostrar comunicação como proveniente de uma das IPs neste espaço de endereços.  
- Site para o espaço de endereços do site - pode utilizar o Site para Site VPNs para ligar o seu VNET para os recursos do local no ou para outros VNETs.  Deve ter que configurado, em seguida, os intervalos IP definidos com maior que ligação VPN mostrará aqui.
- Servidores DNS - se tiver servidores DNS configurados com o seu VNET, em seguida, estão listados aqui.
- IPs encaminhado para VNET - daí são uma lista de endereços IP que o seu VNET tem de encaminhamento definida para.  Esses endereços mostrará aqui.  

É a única operação que pode tomar na vista de aplicação da integração do VNET desligar a sua aplicação de VNET está ligado atualmente para.  Para fazer isto basta clicar desligar na parte superior.  Esta ação não irá alterar a sua VNET.  A configuração VNET e é, incluindo os gateways permanece inalterada.  Se quiser eliminar a sua VNET, em seguida, tem de eliminar pela primeira vez os recursos no mesmo incluindo os gateways.  

A vista de plano de serviço de aplicação tem um número de operações adicionais.  É também acedida forma diferente que a partir da aplicação.  Para chegar a IU de funcionamento em rede ASP basta abrir sua ASP IU e desloque-se para baixo.  Existe um elemento de IU chamado estado da funcionalidade de rede.  Irá dar-alguns detalhes secundárias à volta a integração do VNET.  Se clicar nesta IU abre IU de estado da funcionalidade de rede.  Se, em seguida, clicar em "Clique aqui para gerir" abrirá o IU que lista Os integrações VNET neste ASP.

![][6]

A localização do ficheiro ASP é boa ideia lembrar-se quando as localizações das VNETs são integrar com a opção.  Quando está a VNET noutra localização são muito mais provável que veja os problemas de latência.  

VNETs integrados com o é um lembrete no VNETs quantos as suas aplicações são integradas neste ASP e quantas pode ter.  

Para ver os detalhes adicionados em cada VNET, basta clicar no VNET que lhe interessam.  Para além dos detalhes que foram referir anteriormente que também irá ver uma lista de aplicações neste ASP que está a utilizar esse VNET.  

Relativamente às ações existem duas ações principais.  O primeiro é a capacidade de adicionar rotas unidade tráfego deixar a sua aplicação para o seu VNET.  A segunda ação se a capacidade de sincronizar certificados e informações de rede.

![][7]

**Encaminhamento** Tal como indicado anteriormente as rotas que são definidas em seu VNET são o que é utilizado para direcionar tráfego para o seu VNET da sua aplicação.  Existem algumas utilizações apesar de onde os clientes pretende enviar o tráfego de saída adicional a partir de uma aplicação para o VNET e para os mesmos esta funcionalidade é fornecido.  O que acontece ao tráfego de após que cabe como o cliente configura os respetivos VNET.  

**Certificados** O estado de certificado apresenta uma marca de verificação a ser executada pelo serviço de aplicação para validar que estão ainda boas os certificados que estamos a utilizar para a ligação VPN.  Quando a integração de VNET ativada, em seguida, se for a primeira integração para esse VNET a partir de aplicações neste ASP, existe uma necessária troca de certificados para garantir a segurança da ligação.  Juntamente com os certificados recebemos a configuração de DNS, rotas e outras coisas semelhantes que descrevem a rede.
Se esses certificados ou das informações de rede for alterada, em seguida, terá de clicar em "Rede de sincronização".  **Nota**: quando clica em "Rede de sincronização", em seguida, provocará uma falha de breve na conectividade entre a sua aplicação e o seu VNET.  Enquanto a aplicação não será reiniciada a perda de conectividade poderia fazer com que o site para não funcionar corretamente.  

##<a name="accessing-on-premise-resources"></a>Aceder a recursos de local##

Uma das vantagens da funcionalidade de integração de VNET é que, se o seu VNET estiver ligado à rede no local com uma VPN do Site para Site, em seguida, as suas aplicações podem ter acesso aos recursos no local da sua aplicação.  Para que isto funciona apesar poderá ter de atualizar o gateway da VPN no local com as rotas para o ponto de IP do Site intervalo.  Quando a VPN do Site para Site estiver definida pela primeira vez, em seguida, os scripts utilizados configurá-lo devem configurar rotas, incluindo o seu ponto de VPN do Site.  Se adicionar o ponto para VPN Site depois do criar o seu Site para Site VPN, em seguida, terá de atualizar as rotas manualmente.  Mais informações sobre como fazê-lo serão variam consoante o gateway e não são descritas aqui.  

>[AZURE.NOTE] Enquanto a funcionalidade de integração de VNET funciona com uma VPN do Site para Site para aceder a recursos local atualmente não funcionar com um VPN ExpressRoute para fazer o mesmo.  Este é verdadeiro quando integrar com uma clássico ou o Gestor de recursos do VNET.  Se precisar de acesso aos recursos através de um VPN ExpressRoute, em seguida, pode utilizar um Auxiliar que pode executar no seu VNET. 

##<a name="pricing-details"></a>Detalhes do preços##
Existem algumas das nuances que deve ter em mente quando utilizar a funcionalidade de integração de VNET de preços.  Existem 3 taxas relacionadas para a utilização desta funcionalidade:

- ASP preços requisitos de camadas
- Os custos de transferência de dados
- Custos de VPN Gateway.

Para as aplicações poder utilizar esta funcionalidade, precisam de estar num padrão ou o plano de serviço de aplicação do Premium.  Pode ver mais detalhes sobre esses custos aqui: [Preços de serviço de aplicação][ASPricing]. 

Devido a forma de ponto de Site VPNs são processados, tenha sempre um encargo para os dados de saída através da ligação de integração de VNET mesmo se o VNET está no Centro de dados do mesmo.  Para ver o que essas taxas são explorá aqui: [Transferir preços detalhes dos dados][DataPricing].  

O último item é o custo dos VNET gateways.  Caso não precise os gateways de algo como VPNs de Site para Site, em seguida, estiver a pagar para gateways suportar a funcionalidade de integração de VNET.  Não existem detalhes sobre esses custos aqui: [Preços de Gateway VPN][VNETPricing].  

##<a name="troubleshooting"></a>Resolução de problemas##

Enquanto a funcionalidade é fácil configurar o que não significa que a sua experiência será problema gratuito.  Que encontrar problemas de aceder ao seu pretendido ponto final de não existem são alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicação.  Existem duas experiências de consola que pode utilizar.  Uma é a partir da consola de Kudu e o outro é na consola que pode chegar no Portal do Azure.  Para obter a consola Kudu a partir da sua aplicação para ir para ferramentas -> Kudu.  Este é o mesmo que aceder a [nome do site]. scm.azurewebsites.net.  Uma vez que abre-se simplesmente aceda ao separador da consola de depuração.  Para aceder ao portal alojada consola do Azure, em seguida, da sua aplicação para ir para ferramentas -> consola.  


####<a name="tools"></a>Ferramentas####

Ferramentas ping, nslookup e tracert não irá funcionar através da consola devido a restrições de segurança.  Para preencher a daí void ter sido duas ferramentas separadas adicionadas.  Para testar a funcionalidade DNS adicionámos uma ferramenta denominada nameresolver.exe.  A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode utilizar nameresolver para verificar os nomes de anfitriões depende da sua aplicação.  Desta forma pode testar se tem a ver mal configurados com o seu DNS ou talvez não têm acesso ao seu servidor DNS.

A ferramenta de seguinte permite-lhe testar a conectividade TCP para uma combinação de anfitrião e porta.  Esta ferramenta é denominada tcpping.exe e a sintaxe é:

    tcpping.exe hostname [optional: port]

Esta ferramenta irá indicar que se pode contactar um anfitrião específico e uma porta, mas não irá efetuar a mesma tarefa que obterá com a ICMP com base utilitário ping.  O utilitário de ping ICMP irá indicar se o seu sistema anfitrião é para cima.  Com tcpping descobre se pode aceder a uma porta específica num sistema anfitrião.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Depuração de acesso a VNET alojado recursos####

Existem várias coisas que pode impedir a sua aplicação de que um anfitrião específico e uma porta.  A maioria das vezes é uma das três elementos:

- **Existe uma firewall da forma**  Se tiver uma firewall da forma será clicada o tempo limite do TCP.  Que é neste caso, 21 segundos.  Utilize a ferramenta de tcpping para testar a conectividade.  Tempos limite de TCP pode ser devido ao muitas coisas para além das firewalls mas começar por aí.  
- **O DNS é não está acessível**  O tempo limite DNS dura 3 segundos por servidor DNS.  Se tiver 2 servidores DNS que é 6 segundos.  Utilize nameresolver para ver se o DNS está a funcionar.  Lembre-se de que não é possível utilizar nslookup à medida que não utiliza o seu VNET está configurado com de DNS.
- **Intervalo de IP P2S inválido** Aponte para o intervalo IP do site tem de estar incluída os intervalos IP privados RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) se o intervalo de utiliza IPs fora do que, em seguida, não irá funcionar coisas.  

Se esses itens não atender o seu problema, veja primeiro para as coisas tão simples:  

- O Gateway mostra como sendo o no Portal do?
- Certificados que apresentam como sendo sincronizar?
- Qualquer pessoa mudou a configuração de rede sem ter de fazer um "rede Sincronizar" a ASP afetado? 

Se for do seu gateway para baixo, em seguida, coloque-cópias de segurança.  Se os seus certificados são está sincronizados, em seguida, ir para a vista ASP da integração do VNET e visitas "Rede de sincronização".  Se suspeitar que tiver havido uma alteração efetuada à sua configuração VNET e não estava selecionado sincronização faria com o ASP, em seguida, vai para a vista ASP da integração do VNET e visitas "Rede de sincronização" apenas como um lembrete, isto irá causar uma falha de breve com a sua ligação de VNET e as suas aplicações.  

Se tudo o que é fino, em seguida, é necessário procurar de forma um pouco mais aprofundada:

- Existem outras aplicações de utilizando a integração do VNET para alcançar recursos no mesmo VNET? 
- Pode ir para a consola de aplicação e utilizar tcpping para alcançar quaisquer outros recursos no seu VNET?  

Se um dos anteriores forem VERDADEIRO, em seguida, a integração do VNET está bem formatada e o problema é noutro.  Este é onde obtém-lo ter mais de um desafio porque não existe nenhuma forma simple para ver porque é que não é possível alcançar uma anfitrião: porta.  Algumas das causas incluem:

- tiver uma firewall para cima no seu anfitrião de impedir o acesso a porta de aplicação a partir do seu ponto de intervalo de IP do site.  Passagem sub-redes frequentemente requer o acesso do público.
- for do seu anfitrião de destino para baixo
- a aplicação é para baixo
- tinha hostname ou IP errado
- a aplicação está a escutar uma porta diferente esperado.  Pode verificar isto vai para o anfitrião e utilizando "netstat - aon" a partir da linha de comandos do cmd.  Isto vai mostrar que processo ID está a escutar o que porta.  
- os grupos de segurança de rede estão configurados de maneira a que estes impedem o acesso ao seu anfitrião da aplicação e porta do seu ponto de intervalo de IP do site

Lembre-se de que não sabe que IP no seu ponto de intervalo de IP do Site que a aplicação irá utilizar, pelo que necessita permitir o acesso a partir de todo o intervalo.  

Depuração adicionais passos incluem:

- Inicie sessão no outro VM no seu VNET e tente atingir o seu anfitrião: porta do recurso a partir daí.  Existem alguns utilitários de ping TCP que pode utilizar para o efeito, ou ainda pode utilizar telnet se precisa de ser.  Aqui o objetivo é apenas determinar se conectividade existe a partir de outras VM esta. 
- Ativar uma aplicação no access VM e teste outra para esse anfitrião e porta a partir da consola da sua aplicação  
####<a name="on-premise-resources"></a>Recursos de local####
Se o não é possível alcançar recursos local, em seguida, a primeira coisa que deve verificar é se pode chegar um recurso no seu VNET.  Se de que está a funcionar os passos são muito fácil.  Uma VM no seu VNET precisa de fazer alcançar a aplicação local no.  Pode utilizar telnet ou um utilitário de ping TCP.  Se a VM não é possível alcançar o seu recurso de local no, em seguida, em primeiro lugar, certifique-se a ligação VPN Site para o Site está a trabalhar.  Se estiver a funcionar, em seguida, verifique os aspetos mesmo indicados anteriormente, bem como a configuração de gateway local e o estado no.  

Agora, se o seu VNET alojado VM pode contactar o seu sistema local no mas a aplicação não é possível, em seguida, a razão é provável que um dos seguintes:
- as rotas não estão configuradas com o seu ponto de intervalos IP do site no seu gateway local no
- os grupos de segurança de rede estão a bloquear o acesso do seu ponto de intervalo de IP do Site
- firewalls local no estão a bloquear o tráfego do seu ponto para o intervalo de IP do Site
- tiver uma Route(UDR) de definidos pelo utilizador na sua VNET que impede que o seu ponto para o tráfego de Site baseado chegar a sua rede local no

## <a name="hybrid-connections-and-app-service-environments"></a>Ambientes de aplicação de serviço e ligações de híbrido##
Existem 3 funcionalidades que permitem aceder aos recursos VNET alojado.  São descritos abaixo:

- Integração de VNET
- Ligações de híbrido
- Ambientes de aplicação de serviço

Ligações de híbrido requer que instalar um agente de reencaminhamento denominado a Manager(HCM) de ligação híbrido na sua rede.  O HCM tem de estar conseguir ligar para Azure e também para a sua aplicação.  Esta solução é especialmente excelente a partir de uma rede remota, tal como a sua rede local no ou na nuvem mesmo noutra alojado rede porque não requer um ponto final acessível de internet.  O HCM apenas é executada no Windows e pode ter até 5 instâncias em execução para fornecer elevada disponibilidade.  Ligações de híbrido suporta apenas TCP apesar e cada ponto final HC tem de corresponder a uma combinação de anfitrião: porta específica.  

A funcionalidade de ambiente de serviço de aplicação permite-lhe executar uma instância do serviço de aplicação do Azure no seu VNET.  Isto permite que os recursos de acesso de aplicações no seu VNET sem qualquer passos adicionais.  Alguns dos outros benefícios uma aplicação de ambiente de serviço é que pode utilizar os trabalhadores de core dedicado 8 com 14 GB de RAM.  Outra vantagem é que pode dimensionar o sistema para corresponder às suas necessidades.  Ao contrário os ambientes de inquilinos com várias onde o ASP é limitado em tamanho, num Auxiliar pode controlar a quantidade de recursos que pretende atribuir ao sistema.  Relativamente ao foco de rede deste documento no entanto, uma das funcionalidades que obterá com um Auxiliar que não com a integração de VNET é que pode trabalhar com uma VPN ExpressRoute.  

Enquanto existir que alguns utilizam a sobreposição de maiúsculas/minúsculas, nenhum destas funcionalidade pode substituir qualquer um dos outros.  Saber o que funcionalidade para utilizar está associada às suas necessidades e como irá pretende utilizá-la.  Por exemplo:

- Se for um programador e pretender simplesmente executar um site no Azure e tenha acesso a base de dados na estação de trabalho em seu telefone de secretária, a coisa mais fácil utilizar é híbrido ligações.  
- Se estiver numa grande organização que o pretende colocar um grande número de propriedades da web no público na nuvem e geri-los na sua própria rede, em seguida, o qual pretende aceder com o ambiente do serviço de aplicação.  
- Se tiver um número de aplicação de serviço alojado aplicações e simplesmente pretende aceder a recursos no seu VNET, em seguida, VNET integração é a forma de ir.  

Para além dos casos de utilização, existem algumas simplificar relacionados com aspetos.  Se o seu VNET já está ligado à rede no local, em seguida, utilizando a integração do VNET ou um ambiente do serviço de aplicação é uma forma fácil de consumir recursos local.  Por outro lado, se o VNET não está ligado à rede no local, em seguida, é muitas mais gerais para configurar uma VPN do site para site com o seu VNET comparados com a instalação a HCM.  

Para além das diferenças funcionais existem também preços diferenças.  A funcionalidade de ambiente de serviço de aplicação é um prémio oferta de serviço, mas oferece-rede mais possibilidades de configuração para além de outras funcionalidades fantásticas.  Integração de VNET podem ser utilizada com padrão ou ASP Premium e é perfeita para segura consumir recursos no seu VNET com várias do inquilino de aplicação de serviço.  Ligações de híbrido depende atualmente um BizTalk conta que possui preços níveis que comece gratuito e, em seguida, obtêm gradualmente mais dispendioso baseada no valor precisar.  Quando chegar a trabalhar em muitas redes embora, não existe nenhuma outras funcionalidade como híbrida ligações que pode permitem-lhe acesso aos recursos no bem mais de 100 redes separadas.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
