<properties
    pageTitle="Considerações sobre o desempenho para o Gestor de tráfego Azure | Microsoft Azure"
    description="Compreender o desempenho no Gestor de tráfego e como testar o desempenho do seu Web site quando utilizar o Gestor de tráfego"
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

# <a name="performance-considerations-for-traffic-manager"></a>Considerações sobre o desempenho para o Gestor de tráfego

Esta página explica considerações sobre o desempenho utilizando o Gestor de tráfego. Considere o seguinte cenário:

Tem de instâncias do seu Web site nas regiões WestUS e EastAsia. Uma das instâncias está a falhar a verificação de integridade a sonda de Gestor de tráfego. Tráfego de aplicação é direcionado para a região saudável. Este activação pós-falha é esperado mas desempenho pode ser um problema com base na latência do tráfego agora viajar para uma região distante.

## <a name="how-traffic-manager-works"></a>Como funciona o Gestor de tráfego

O impacto no desempenho apenas que pode ter o Gestor de tráfego no seu Web site é a pesquisa de DNS inicial. Um pedido DNS para o nome do seu perfil do Gestor de tráfego é processado pelo servidor DNS da Microsoft raiz que aloja a zona de trafficmanager.net. Gestor de tráfego preenche e actualiza regularmente, servidores de raiz de DNS da Microsoft com base na política de tráfego gestor e os resultados de sonda. Por isso, mesmo durante a pesquisa de DNS inicial, sem consultas de DNS são enviadas para o tráfego Manager.

Gestor de tráfego é constituído por vários componentes: nome DNS servers, um serviço API, a camada de armazenamento e um ponto final de serviço de monitorização. Se um componente de serviço do Gestor de tráfego falhar, não tem qualquer efeito no nome de DNS associado com o seu perfil do Gestor de tráfego. Os registos em servidores DNS da Microsoft que permaneça inalterados. No entanto, monitorização de ponto final e atualização de DNS não ocorrer. Por conseguinte, Gestor de tráfego não é possível atualizar o DNS para apontarem para o seu site de activação pós-falha quando acede do seu site principal para baixo.

Resolução de nomes DNS é rápida e os resultados são armazenadas em cache. A velocidade de pesquisa DNS inicial depende servidores DNS do que cliente utiliza para resolução de nomes. Normalmente, um cliente pode concluir uma pesquisa DNS dentro ms ~ 50. Os resultados de pesquisa são armazenadas em cache para a duração do DNS Time-para-live (TTL). O TTL para o Gestor de tráfego predefinido é de 300 segundos.

Tráfego não fluxo através do Gestor de tráfego. Assim que a pesquisa de DNS for concluída, o cliente tem um endereço de IP para uma instância do seu web site. O cliente liga-se diretamente para esse endereço e não passa através do Gestor de tráfego. A política de Gestor de tráfego que escolher não têm influência sobre o desempenho do DNS. No entanto, um desempenho encaminhamento-método negativa pode afetar a experiência de aplicação. Por exemplo, se a política de redireciona o tráfego da América do Norte para uma instância alojado na Ásia, a latência da rede para esses sessões poderá um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medir o Gestor de tráfego de desempenho

Existem várias Web sites que pode utilizar para compreender o desempenho e comportamento de um perfil do Gestor de tráfego. Muitos destes sites são gratuitos, mas poderão ter limitações. Alguns sites oferecem melhorado monitorização e relatórios para uma taxa.

As ferramentas de sobre estes sites medida DNS latências e apresentar os resolvido endereços IP para localizações de cliente em todo o mundo. A maior parte destas ferramentas não em cache os resultados DNS. Por conseguinte, as ferramentas de mostram a pesquisa de DNS completa sempre que um teste é executado. Quando testar a partir do seu próprio cliente, que se deparar apenas o desempenho de pesquisa DNS completo uma vez durante a duração TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de exemplo para medir o desempenho do DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS oferece muitos ferramentas de desempenho. A ferramenta de comparação de DNS pode mostrar-lhe quanto tempo demora para resolver o seu nome DNS e como que se compara com outros fornecedores de serviços DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Uma das ferramentas de mais simples é WebSitePulse. Introduza o URL para ver o tempo de resolução de DNS, o primeiro Byte, o último Byte e outras estatísticas de desempenho. Pode escolher a partir de três localizações de teste diferente. Neste exemplo, verá que a primeira execução mostra que pesquisa DNS leva-o até 0.204 seg.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Uma vez que os resultados são armazenadas em cache, o segundo teste para o mesmo ponto final do Gestor de tráfego a pesquisa de DNS leva 0.002 seg.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [AC aplicação síntese Monitor](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta de verificação Watchmouse Web site, este site mostrar-lhe o tempo de resolução DNS de regiões geográficas múltiplos em simultâneo. Introduza o URL para ver o tempo de resolução de DNS, momento da ligação e velocidade de várias localizações geográficas. Utilize este teste para ver qual o serviço alojado é devolvido para diferentes localizações no mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Esta ferramenta fornece estatísticas de desempenho para cada elemento de uma página web. O separador de página Análise mostra a percentagem de tempo gasto na pesquisa de DNS.

- [O que é o meu DNS?](http://www.whatsmydns.net/)

    Este site faz uma pesquisa de DNS a partir de 20 localizações diferentes e apresenta os resultados num mapa.

- [Aprofundar a Interface de Web](http://www.digwebinterface.com)

    Este site mostra que informações de DNS, incluindo CNAMEs e registos de mais detalhadas. Certifique-se de que verifique o 'Colorize saída' e 'Estatística' em Opções e selecione 'Todos' em servidores de nomes.

## <a name="next-steps"></a>Próximos passos

[Acerca do Gestor de tráfego de tráfego de métodos de encaminhamento](traffic-manager-routing-methods.md)

[Teste as suas configurações de Gestor de tráfego](traffic-manager-testing-settings.md)

[Operações no Gestor de tráfego (REST API referência)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets de Gestor de tráfego Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
