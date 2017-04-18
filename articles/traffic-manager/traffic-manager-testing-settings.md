<properties
    pageTitle="Teste as definições do Gestor de tráfego | Microsoft Azure"
    description="Este artigo irá ajudá-lo testar definições do Gestor de tráfego de"
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

# <a name="test-your-traffic-manager-settings"></a>Teste as suas configurações de Gestor de tráfego

Para testar as suas definições de Gestor de tráfego, tem de ter vários clientes, em várias localizações, a partir do qual pode executar os testes. Em seguida, coloque os pontos finais no seu perfil do Gestor de tráfego para baixo uma de cada vez.

* Defina o valor DNS TTL baixa para que as alterações se propaguem rapidamente (por exemplo, 30 segundos).
* Saiba quais os endereços IP dos seus serviços em nuvem Azure e sites públicos no perfil que estiver a testar.
* Utilize ferramentas que lhe permitem resolver um nome DNS para um endereço IP e apresentar esse endereço.

Está a dar para ver que os nomes de DNS resolver para endereços IP os pontos finais no seu perfil. Deverão resolver os nomes de uma forma consistente com o método de encaminhamento de tráfego definido no perfil do Gestor de tráfego. Pode utilizar as ferramentas de como o **nslookup** ou **aprofundar** para resolver nomes DNS.

Os exemplos seguintes ajudá-lo a testar o seu perfil do Gestor de tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o Gestor de tráfego perfil utilizando nslookup e ipconfig no Windows

1. Abra um comando ou o Windows PowerShell linha de comandos como administrador.
2. Tipo de `ipconfig /flushdns` a cache de resolução DNS.
3. Tipo de `nslookup <your Traffic Manager domain name>`. Por exemplo, o seguinte comando verifica o nome de domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Um resultado típico mostra as seguintes informações:

    * O nome DNS e o endereço IP do servidor DNS a ser acedido para resolver este nome de domínio do Gestor de tráfego.
    * O nome de domínio do Gestor de tráfego que escreveu na linha de comandos depois de "nslookup" e o endereço IP para o qual o domínio de Gestor de tráfego resolve. O endereço IP segundo é aquele importante para verificar. -Deve corresponder um endereço de IP (VIP) virtual público para um dos serviços em nuvem ou sites públicos no perfil do Gestor de tráfego de que estiver a testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de activação pós-falha

1. Deixe todos os pontos finais para cima.
2. Utilizar um único cliente, pedir resolução DNS para o seu nome de domínio de empresa utilizando o nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde ao ponto final principal.
4. Trazer para baixo do seu ponto final principal ou remover o ficheiro de monitorização para que o Gestor de tráfego pensa que a aplicação é premida.
5. Aguarde que o DNS Time-para-Live (TTL) de perfil do Gestor de tráfego de plus adicional de dois minutos. Por exemplo, se o TTL DNS for 300 segundos (5 minutos), tem de aguardar sete minutos.
6. Esvaziar o seu DNS cliente cache e um pedido de resolução DNS utilizando o nslookup. No Windows, pode limpar a cache DNS com o comando ipconfig/flushdns.
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto final secundário.
8. Repita o processo, trazer para baixo de cada ponto final sucessivamente. Verifique se o DNS devolve o endereço IP do ponto final seguinte na lista. Quando todos os pontos finais encontram-se para baixo, deve obter o endereço IP do ponto final principal novamente.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego ponderada

1. Deixe todos os pontos finais para cima.
2. Utilizar um único cliente, pedir resolução DNS para o seu nome de domínio de empresa utilizando o nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde a um dos seus os pontos finais.
4. A cache DNS cliente e repita os passos 2 e 3 para cada ponto final. Deverá ver diferentes endereços IP devolvidos para cada um dos seus os pontos finais.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de desempenho

Para testar a um método de encaminhamento de tráfego de desempenho de forma eficaz, tem de ter clientes localizados em diferentes partes do mundo. Pode criar os clientes de diferentes regiões Azure que podem ser utilizados para testar os seus serviços. Se tiver uma rede global, remotamente pode iniciar sessão no clientes em outras partes do mundo e executar a sua testes a partir daí.

Em alternativa, existem livre baseada na web pesquisa de DNS e aprofundar os serviços disponíveis. Algumas destas ferramentas dar-lhe a capacidade de verificar a resolução de nomes DNS a partir de várias localizações em todo o mundo. Efetue uma pesquisa em "Pesquisa de DNS" para obter exemplos. Serviços de terceiros, como Gomez ou da apresentação podem ser utilizados para confirmar que os perfis de estiver a distribuir o tráfego, tal como é esperado.

## <a name="next-steps"></a>Próximos passos

* [Acerca do Gestor de tráfego de tráfego de métodos de encaminhamento](traffic-manager-routing-methods.md)
* [Considerações sobre o desempenho tráfego Gestor](traffic-manager-performance-considerations.md)
* [Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)




