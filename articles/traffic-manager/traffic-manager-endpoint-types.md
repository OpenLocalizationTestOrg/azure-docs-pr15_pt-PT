<properties
    pageTitle="Tipos de ponto final do Gestor de tráfego | Microsoft Azure"
    description="Este artigo explica tipos diferentes de pontos finais que podem ser utilizados com o Gestor de tráfego Azure"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-endpoints"></a>Pontos finais de Gestor de tráfego

Gestor de tráfego do Microsoft Azure permite-lhe controlar a forma como o tráfego de rede é distribuído para implementações da aplicação a ser executada em centros de dados diferentes. Configurar a implementação da cada aplicação como um extremo no Gestor de tráfego. Quando o Gestor de tráfego recebe um pedido de DNS, opte por um ponto final disponível para devolver na resposta DNS. Gestor de tráfego baseia a escolha sobre o estado atual de ponto final e o método de encaminhamento de tráfego. Para mais informações, consulte o artigo [Como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).

Existem três tipos de ponto final suportados pelo tráfego Manager:

- **Azure pontos finais** são utilizadas para serviços hospedados no Azure.
- **Os pontos finais externos** são utilizadas para serviços alojados fora do Azure, quer no local ou com outro fornecedor de alojamento.
- **Os pontos finais de aninhadas** são utilizadas para combinar perfis do Gestor de tráfego para criar esquemas de encaminhamento de tráfego mais flexíveis para suportar as necessidades de implementações maiores e mais complexas.

Não existe nenhuma restrição de como os pontos finais de diferentes tipos são combinados num único gestor de tráfego de perfil. Cada perfil pode conter qualquer mistura de tipos de ponto final.

As secções seguintes descrevem cada tipo de ponto final profundidade maior.

## <a name="azure-endpoints"></a>Pontos finais Azure

Azure pontos finais são utilizados para serviços baseados na Azure no Gestor de tráfego. São suportados os seguintes tipos de recursos Azure:

- 'Clássico' IaaS VMs e PaaS cloud services.
- Web Apps
- Recursos de PublicIPAddress (que podem ser ligado ao VMs diretamente ou através de um balanceador de carga Azure). O publicIpAddress tem de ter um nome DNS atribuído a ser utilizado num perfil do Gestor de tráfego.

Recursos de PublicIPAddress são recursos de Gestor de recursos do Azure. Não existem no modelo de implementação clássica. Assim são experiências de Gestor de recursos do Azure só são suportadas no tráfego do gestor. Os outros tipos de ponto final são suportados através do Gestor de recursos e o modelo de implementação clássica.

Quando utilizar os pontos finais Azure, Gestor de tráfego Deteta quando uma VM de IaaS 'Clássico', serviço na nuvem ou uma aplicação Web é parada e iniciada. Este estado é refletido no estado de ponto final. Para obter mais detalhes, consulte a [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) . Quando o serviço subjacente está parado, Gestor de tráfego não executar verificações de integridade de ponto final ou direto tráfego para o ponto final. Não existe nenhum gestor de tráfego faturaçãohttps os eventos ocorrem para a instância parada. Quando o serviço é reiniciado, currículos faturaçãohttps e o ponto final é elegível para receber tráfego. Esta detecção não se aplica ao pontos finais de PublicIpAddress.

## <a name="external-endpoints"></a>Pontos finais externos

Os pontos finais externos são utilizados para serviços de fora do Azure. Por exemplo, um serviço alojado no local ou com outro fornecedor. Os pontos finais externos podem ser utilizados individualmente ou combinados com os pontos finais de Azure no perfil do Gestor de tráfego mesmo. Combinar os pontos finais Azure com os pontos finais externos permite vários cenários:

- Em qualquer um de um modelo de activação pós-falha activo activo ou activo-passivo, utilize o Azure para fornecer maior redundância para um existente no local aplicação.
- Para reduzir a latência da aplicação para utilizadores em todo o mundo, expandir uma aplicação no local existente para localizações geográficas adicionais no Azure. Para obter mais informações, consulte o artigo [Gestor de tráfego 'Desempenho' Encaminhamento de tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Utilizar Azure para fornecer a capacidade adicional para um existente no local aplicação, continuamente ou como uma solução 'rajada para nuvem' para satisfazer um coletor no pedido.

Em certos casos, é útil para utilizar os pontos finais externos para serviços Azure de referência (para obter exemplos, consulte as [Perguntas mais frequentes](#faq)). Neste caso, são faturadas verificações de integridade, a taxa de pontos finais Azure e não a taxa de pontos finais externos. No entanto, ao contrário os pontos finais Azure, se parar ou eliminar o serviço subjacente, estado de funcionamento do verificar faturação continua até desativar ou eliminar o ponto final no Gestor de tráfego.

## <a name="nested-endpoints"></a>Pontos finais aninhados

Os pontos finais aninhados combinam vários perfis do Gestor de tráfego para criar esquemas de encaminhamento de tráfego flexíveis e suportar as necessidades de implementações complexas, grandes. Com os pontos finais de aninhadas, um perfil de 'criança' é adicionado como um ponto final a um perfil de 'principal'. Perfis do subordinado e o principal podem conter outros pontos finais de qualquer tipo, incluindo outros perfis aninhados. Para mais informações, consulte o artigo [perfis do Gestor de tráfego aninhadas](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps como pontos finais

Algumas considerações adicionais aplicam-se ao configurar as aplicações Web como pontos finais no Gestor de tráfego:

1. Apenas Web Apps no SKU 'Padrão' ou acima são elegíveis para utilização com o Gestor de tráfego. Tentativas para adicionar uma aplicação Web de um SKU inferior falhar. Mudar o SKU da aplicação Web do existente resulta no Gestor de tráfego já não enviar tráfego para esse Web App.

2. Quando um ponto final recebe um pedido HTTP, utiliza o cabeçalho 'host' no pedido de para determinar qual a aplicação Web deve o pedido de serviço. O cabeçalho de anfitrião contém o nome DNS utilizado para iniciar o pedido, por exemplo 'contosoapp.azurewebsites.net'. Para utilizar um nome diferente de DNS com a aplicação Web, o nome de DNS deve estar registado como um nome de domínio personalizado para a aplicação. Quando adicionar um ponto final de aplicação Web como um ponto final Azure, o nome do Gestor de tráfego perfil DNS é registado automaticamente para a aplicação. Este registo é removido automaticamente quando o ponto final é eliminado.

3. Cada perfil do Gestor de tráfego pode conter no máximo uma aplicação Web ponto final de cada região Azure. Para contornar este constrangimento, pode configurar uma aplicação Web como um ponto final externo. Para mais informações, consulte as [FAQ](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Activar e desactivar os pontos finais

Desativar um ponto final no Gestor de tráfego pode ser útil para remover temporariamente o tráfego de um ponto final que está no modo de manutenção ou novamente a ser implementada. Assim que está a ser executado novamente o ponto final, pode ser ativado novamente.

Os pontos finais podem ser ativados ou desativados através do portal do Gestor de tráfego, PowerShell, clip ou REST API, as quais são suportadas no Gestor de recursos e o modelo de implementação clássica.

>[AZURE.NOTE] Desativar um extremo Azure nada de fazer com o seu estado de implementação no Azure. Um serviço Azure (tal como uma VM ou aplicação Web permanece em execução e conseguir receber tráfego mesmo quando está desativada no Gestor de tráfego. Pode ser endereçado tráfego diretamente para a instância do serviço em vez de através do nome do Gestor de tráfego perfil DNS. Para mais informações, consulte o artigo [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).

A elegibilidade atual de cada ponto final para receber tráfego depende os seguintes fatores:

- O estado de perfil (ativado/desactivado)
- O estado de ponto final (ativado/desactivado)
- Os resultados do Estado de funcionamento verifica se ocorre esse ponto final

Para obter detalhes, consulte o artigo [monitorização do Gestor de tráfego de ponto final](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Uma vez que o Gestor de tráfego funciona ao nível de DNS, não consegue influenciar ligações existentes a qualquer ponto final. Quando um ponto final estiver indisponível, Gestor de tráfego direciona os novas ligações para outro ponto final disponível. No entanto, o anfitrião do ponto final desativado ou danificado pode continuar a receber o tráfego através de ligações existentes até nessas sessões são terminadas. Aplicações deverão limitar a duração de sessão para permitir o tráfego esgotar a partir de ligações existentes.

Se todos os pontos finais de um perfil são desativados ou, se o próprio perfil estiver desativado, em seguida, Gestor de tráfego envia uma resposta 'NXDOMAIN' para uma nova consulta DNS.

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Pode utilizar o Gestor de tráfego com os pontos finais a partir de múltiplas subscrições?

Utilizar os pontos finais a partir de múltiplas subscrições não é possível Azure Web Apps. Azure Web Apps requer que qualquer nome de domínio personalizado utilizado com Web Apps só é utilizada dentro de uma subscrição única. Não é possível utilizar Web Apps a partir de múltiplas subscrições com o mesmo nome de domínio.

Para outros tipos de ponto final, é possível utilizar o Gestor de tráfego com os pontos finais de mais do que uma subscrição. Como configurar o Gestor de tráfego depende se estiver a utilizar o modelo de implementação clássico ou a experiência de Gestor de recursos.

- No Gestor de recursos, pontos finais de qualquer subscrição podem de ser adicionados ao Gestor de tráfego, desde que a pessoa a configurar o perfil do Gestor de tráfego tem acesso de leitura para o ponto final. Estas permissões podem ser concedidos utilizar [o controlo de acesso baseado em funções de Gestor de recursos do Azure (RBCA)](../active-directory/role-based-access-control-configure.md).
- Na interface de modelo clássico de implementação, Gestor de tráfego requer que serviços em nuvem ou aplicações Web do configurado como os pontos finais Azure residem na mesma subscrição como o perfil do Gestor de tráfego. Pontos finais de serviço de nuvem nas outras subscrições podem ser adicionados ao Gestor de tráfego como «externos» pontos finais. Estes pontos finais externos são faturadas como os pontos finais Azure, em vez da taxa externa.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Pode utilizar o Gestor de tráfego com faixas de 'Teste' Serviço na nuvem?

Sim. Serviço em nuvem 'teste' faixas pode ser configurado no Gestor de tráfego de como os pontos finais externos. Verificações de integridade ainda estão Serei cobrado à taxa pontos finais de Azure. Porque o tipo de ponto final externo é utilizado, alterações ao serviço subjacente não são recolhidas automaticamente. Com os pontos finais externos, o tráfego Manager não consegue detetar quando o serviço de nuvem está parado ou eliminado. Por conseguinte, o Gestor de tráfego continua faturação verificações de integridade até o ponto final tiver sido desactivado ou eliminado.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestor de tráfego suporta pontos finais IPv6?

Gestor de tráfego não fornece atualmente IPv6 addressible servidores de nomes. No entanto, o tráfego gestor ainda podem ser utilizado pelo clientes IPv6 estabelecer ligação ao pontos finais IPv6. Um cliente não fazer pedidos de DNS diretamente para o tráfego o gestor. Em vez disso, o cliente utiliza um serviço DNS recursiva. Um cliente apenas de IPv6 envia pedidos para o serviço DNS recursiva através do IPv6. Em seguida, o serviço de recursiva deverá conseguir contactar os servidores de nomes do Gestor de tráfego utilizando IPv4.

Gestor de tráfego responde com o nome DNS do ponto final. Para suportar um ponto final IPv6, tem de existir um registo DNS AAAA apontar para o nome de ponto final DNS para o endereço IPv6. Verificações de integridade de Gestor de tráfego apenas suportam endereços IPv4 do. O serviço necessita para expor um ponto final IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Pode utilizar o Gestor de tráfego com mais do que uma aplicação Web na mesma região?

Normalmente, Gestor de tráfego é utilizado para direcionar tráfego para aplicações implementado regiões diferentes. No entanto, também pode ser utilizado em que uma aplicação tem mais do que uma implementação na mesma região. Os pontos finais Azure Gestor de tráfego não permitir mais do que um ponto final de Web App a partir da mesma região Azure a serem adicionados ao perfil do Gestor de tráfego de mesmo.

Os passos seguintes fornecem uma solução para este constrangimento:

1. Verifique se o seu os pontos finais estão em diferentes web app 'unidades da escala'. Um nome de domínio tem de mapear para um site único numa unidade de escala determinado. Por conseguinte, as duas aplicações Web na mesma unidade de escala não pode partilhar um perfil do Gestor de tráfego.
2. Adicione o seu nome de domínio relegado como um nome de anfitrião personalizado para cada Web App. Cada Web App tem de ser uma unidade de escala diferente. Todas as aplicações Web tem de pertencer à mesma subscrição.
3. Adicione uma (e apenas uma) ponto final de Web App para o seu perfil do Gestor de tráfego, como um ponto final Azure.
4. Adicione cada ponto final do Web App adicional ao seu perfil do Gestor de tráfego como um ponto final externo. Os pontos finais externos só podem ser adicionados utilizando o modelo de implementação do Gestor de recursos.
5. Criar um registo CNAME de DNS no seu domínio relegado que aponta para o seu nome DNS de perfil do Gestor de tráfego (<>.... trafficmanager.net).
6. Aceder ao seu site através do nome de domínio relegado, não o nome DNS do Gestor de tráfego perfil.

## <a name="next-steps"></a>Próximos passos

- Saiba [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).
- Saiba mais sobre o Gestor de tráfego [monitorização de ponto final e activação pós-falha automática](traffic-manager-monitoring.md).
- Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).
