# <a name="compute"></a>Cluster

Azure permite-lhe implementar e monitorizar o seu código da aplicação em execução no interior de um centro de dados da Microsoft. Quando criar uma aplicação e executá-la no Azure, o código e a configuração em conjunto chama-se uma Azure alojado serviço. Serviços alojados são mais fácil gerir, dimensionar para cima e para baixo, reconfigurar e actualizar com novas versões do código da sua aplicação. Este artigo foca Azure alojado modelo de aplicação de serviço.<a id="compare" name="compare"></a>

## Índice<a id="_GoBack" name="_GoBack"></a>

-   [Vantagens de modelo de aplicação Azure][]
-   [Serviço alojado Core conceitos][]
-   [Considerações de estrutura de serviço alojado][]
-   [Estruturar a sua aplicação de escala][]
-   [Definição de serviço alojado e configuração][]
-   [O ficheiro de definição de serviço][]
-   [O ficheiro de configuração do serviço][]
-   [Criar e implementar um serviço alojado][]
-   [Referências][]

## <a id="benefits"> </a>Benefícios do modelo de aplicação azure

Quando implementar a aplicação como um serviço alojado, Azure cria uma ou mais máquinas de virtuais (VMs) que contêm o código da sua aplicação e inicia as VMs no máquinas físicas que residem dos centros de dados Azure. Como os pedidos de cliente à alojado aplicação introduzir o Centro de dados, um balanceador de carga distribuir estes pedidos uniforme para os VMs. Enquanto a aplicação está alojada no Azure, obtém a três vantagens chave:

-   **Disponibilidade de alta.** Disponibilidade elevada significa que Azure garante que a aplicação está a ser executado quanto possível e consiga responder a pedidos de cliente. Se termina a aplicação (devido a uma exceção não processada, por exemplo), em seguida, Azure irá detectar isto e, é provável automaticamente novamente iniciar a aplicação. Se a máquina a aplicação estiver em execução no experiências qualquer tipo de falha de hardware, em seguida, Azure será também detetar isto e automaticamente criar uma nova VM noutro computador físico de trabalho e execute o código a partir daí. Nota: Ordem para a sua aplicação obter o contrato de nível de serviço da Microsoft de 99.95% disponíveis, tem de ter, pelo menos, duas VMs a executar o código da aplicação. Esta opção permite-um VM para processar pedidos de cliente, enquanto Azure move o cursor seu código a partir de uma falha VM para uma nova, bom VM.

-   **Escalabilidade.** Azure permite-lhe facilmente e dinamicamente alterar o número de VMs a executar o código da aplicação para processar a carga real que está a ser colocada na sua aplicação. Esta opção permite-lhe ajustar a sua aplicação para a carga de trabalho que os seus clientes estiver a colocar no mesmo enquanto paga apenas por VMs precisa quando deles necessita. Quando pretender alterar o número de VMs, Azure responde dentro de minutos, tornando possível dinamicamente alterar o número de VMs em execução como frequentemente conforme pretender.

-   **Capacidade de gestão.** Uma vez que o Azure é uma plataforma como um serviço (PaaS) a perguntar se, gere a infraestrutura do (próprio hardware, eléctrica e redes) necessária para manter estas máquinas em execução. Azure também gere a plataforma, garantindo que num sistema operativo atualizado com todos os a patches corretos e atualizações de segurança, bem como actualizações de componentes como o .NET Framework e servidor de informações da Internet. Uma vez que todas as VMs estiver a executar o Windows Server 2008, Azure disponibiliza funcionalidades adicionais, tal como monitorização de diagnóstico, suporte de ambiente de trabalho remoto, firewalls e configuração do arquivo de certificados. Todas as seguintes funcionalidades são fornecidas no não extra custo. Na verdade, quando executar a aplicação no Azure, a licença de sistema operativo (OS) do Windows Server 2008 é incluída. Uma vez que todas as VMs estiver a executar o Windows Server 2008, qualquer código que é executada no Windows Server 2008 funciona perfeitamente quando a ser executado no Azure.

## <a id="concepts"> </a>Alojado conceitos essenciais de serviço

Quando a aplicação é implementada como um serviço alojado no Azure, executa como uma ou mais *funções.* Uma *função* que se refere simplesmente ficheiros da aplicação e a configuração. Pode definir uma ou mais funções para a sua aplicação, cada uma com o seu próprio conjunto de ficheiros da aplicação e a configuração. Para cada função na sua aplicação, pode especificar o número de VMs ou *instâncias de função*, para executar. A figura seguinte mostrar dois exemplos simples de uma aplicação como um serviço alojado utilizar funções e instâncias de função.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figura 1: Uma única função com três instâncias (VMs) em execução no Centro de dados Azure

![imagem][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figura 2: Duas funções, cada uma com duas instâncias (VMs), em execução no Centro de dados Azure

![imagem][1]

Instâncias de função normalmente processam pedidos de cliente de Internet introduzir o Centro de dados através do que é designado por um *ponto final de entrada*. Uma única função pode ter os pontos finais de 0 ou mais entrados. Ponto final de cada indica um protocolo (HTTP, HTTPS ou TCP) e uma porta. É comum para configurar uma função de dois pontos finais de entrada: HTTP escutar portas 80 e HTTPS escutar na porta 443. A figura seguinte mostra um exemplo de duas diferentes funções com pontos finais de entrada diferentes que encaminha os pedidos de cliente às mesmas.

![imagem][2]

Quando cria um serviço alojado no Azure, são atribuída um endereço IP publicamente endereçável que os clientes podem utilizar para aceder à mesma. Após criar o serviço alojado também tem de selecionar um prefixo de URL é mapeado para esse endereço IP. Este prefixo tem de ser exclusivo à medida que é reserva, essencialmente, o *prefixo*. cloudapp.net URL para que ninguém tenha. Os clientes comunicar com o seu instâncias de função utilizando o URL. Normalmente, não distribuir ou publicar o Azure *prefixo*. cloudapp.net URL. Em vez disso, irá comprar um nome de DNS da sua entidade de registo DNS à escolha e configurar o seu nome DNS para redirecionar solicitações de cliente para o URL do Azure. Para obter mais detalhes, consulte o artigo [configurar um nome de domínio personalizado no Azure][].

## <a id="considerations"> </a>Alojado considerações de estrutura de serviço

Quando a estruturar uma aplicação para executar num ambiente na nuvem, existem várias considerações a ter em conta sobre como latência, como sendo de alta disponibilidade e escalabilidade.

Decidir onde localizar o código da aplicação é uma consideração importante quando a ser executado um serviço alojado no Azure. É comuns para implementar a aplicação aos centros de dados que são mais próximo para os seus clientes para reduzir a latência e obter o melhor desempenho possíveis.
No entanto, poderá escolher um centro de dados mais perto da sua empresa ou mais próximo para os seus dados se tiver alguns preocupações jurisdição ou legais sobre os seus dados e que está inserido. Existem seis centros de dados em todo o mundo capaz de alojar o seu código da aplicação. A tabela abaixo mostra as localizações disponíveis:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País/região**

</td>
<td style="width: 200px;">
**Regiões subpastas**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Sul Central e Central da América do Norte

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
América do Norte e oeste

</td>
</tr>
<tr>
<td>
Países da Ásia

</td>
<td>
Sudeste & leste

</td>
</tr>
</tbody>
</table>
Ao criar um serviço alojado, seleccionar uma sub-região indicando a localização na qual pretende que o seu código executar.

Para alcançar elevada disponibilidade e escalabilidade, é muito importante que os dados da sua aplicação ser mantidos num repositório central acessível a várias instâncias de função. Para ajudar com o seguinte, o Azure oferece várias opções de armazenamento como blobs, tabelas e base de dados SQL. Consulte o artigo [Ofertas de armazenamento de dados no Azure][] para obter mais informações sobre estas tecnologias de armazenamento. A figura seguinte mostra como o Balanceador de carga dentro do Centro de dados Azure distribuir pedidos de clientes para as quais têm acesso ao mesmo armazenamento de dados de instâncias de função diferente.

![imagem][3]

Normalmente, que pretende localizar o código da aplicação e os seus dados no Centro de dados do mesmo como isto permite uma latência baixa (um melhor desempenho) quando o código da aplicação acede aos dados. Além disso, não será cobrado para a largura de banda quando dados são movidos à volta do Centro de dados do mesmo.

## <a id="scale"> </a>Estruturar a sua aplicação de escala

Por vezes, poderá querer tirar uma única aplicação (como um web site simples) e a tenha alojado no Azure. Mas frequentemente, a aplicação pode consistir várias funções todos trabalham em conjunto. Por exemplo, na figura abaixo, existem duas instâncias da função de Web site, três instâncias da função ordem de processamento e uma instância da função gerador de relatórios. Estas funções estão todos a funcionar em conjunto e o código para todos os pode ser agrupado e implementado como uma única unidade até Azure.

![imagem][4]

O motivo principal para dividir uma aplicação para diferentes funções cada em execução no seu próprio conjunto de instâncias de função (ou seja, VMs) é dimensionar as funções de forma independente. Por exemplo, durante as férias, muitos clientes podem ser comprar os produtos da sua empresa, para que poderá pretender aumentar o número de ocorrências de função com o seu perfil do Web site, bem como o número de ocorrências de função executar a sua função ordem de processamento. Depois das férias, poderá receber muitas produtos devolvido, para que poderá necessitar de muitas instâncias de Web site, mas menos instâncias ordem de processamento. Durante o resto do ano, poderá ser necessário apenas alguns Web site e ordem de processamento de instâncias. Ao longo de tudo isto, poderá ter apenas uma instância de gerador de relatório. A flexibilidade de implementações baseado em funções no Azure permite-lhe adaptar facilmente a sua aplicação às suas necessidades de negócio.

É comum para que a função de instâncias do seu serviço alojado comunicarem com os outros. Por exemplo, a função de Web site aceita a ordem de um cliente mas, em seguida, offloads-lo a ordem de processamento para as instâncias de função ordem de processamento. A melhor forma de passar o formulário de trabalho um conjunto de instâncias de função para outro conjunto de instâncias está a utilizar a tecnologia de colocação fornecida pela Azure, o serviço de fila ou serviço Bus filas. A utilização de uma fila é uma parte do bloco aqui crítica. Fila de espera permite que o serviço alojado dimensionar o respetivas funções de forma independente permitindo-lhe equilibrar a carga de trabalho contra custo. Se o número de mensagens na fila de espera aumenta ao longo do tempo, pode dimensionar o o número de ocorrências de função ordem de processamento. Se o número de mensagens na fila de espera diminui ao longo do tempo, em seguida, é possível dimensionar para baixo o número de ocorrências de função ordem de processamento. Desta forma, apenas são paga por instâncias necessárias para processar a carga de trabalho real.

Fila de espera também fornece fiabilidade. Quando o dimensionamento para baixo o número de ocorrências de função ordem de processamento, Azure decide quais instâncias para terminar. Pode decidir terminar uma instância que esteja no meio de processamento de uma mensagem de fila de espera. No entanto, uma vez que o processamento de mensagem não for concluído com êxito, a mensagem fica visível novamente para outra ordem de processamento função instância que escolhe-o para cima e processa-lo. Devido a visibilidade de mensagem de fila de espera, mensagens garante eventualmente obter processadas. Fila de espera também funciona como um balanceador de carga por eficazmente distribuir a suas mensagens instâncias de todas as funções que pedem as mensagens a partir dos mesmos.

Para as instâncias de função de Web site, pode monitorizar o tráfego entram-los e decidir dimensionar o número de-los para cima ou para baixo, assim. Fila de espera permite-lhe dimensionar o número de ocorrências de função de Web site independentemente dos existentes das instâncias de função ordem de processamento. Este é muito poderosa e fornece-lhe muitas flexibilidade. Obviamente, se a aplicação é constituída por funções adicionais, é possível adicionar filas adicionais como a ligação para comunicar entre eles para poder aproveitar o mesmo dimensionamento e benefícios de custos.

## <a id="defandcfg"> </a>Alojado definição de serviço e de configuração

Implementar um serviço alojado para Azure requer que também tem um ficheiro de definição de serviço e um ficheiro de configuração do serviço. Ambos os ficheiros são ficheiros XML e permitem-lhe para forma declarativa especificar as opções de implementação do seu serviço alojado. Ficheiro de definição do serviço descreve todas as funções que compõem o serviço alojado e como comunicar. O ficheiro de configuração do serviço descreve o número de instâncias para cada função e definições utilizadas para configurar cada instância de função.

## <a id="def"> </a>o ficheiro de definição de serviço

Como posso mencionado anteriormente, a definição do serviço (CSDEF) o ficheiro é um ficheiro XML que descreve as várias funções que compõem a sua aplicação completa. Esquema de concluída para o ficheiro XML que pode ser encontrado aqui: [[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx]].
O ficheiro CSDEF contém um elemento WebRole ou WorkerRole para cada função que pretende na sua aplicação. Implementar uma função como uma função da web (utilizando o elemento WebRole), significa que o código será executado numa instância de função que contém o Windows Server 2008 e servidor de informação Internet (IIS).
Implementar uma função como uma função de trabalho (utilizando o elemento WorkerRole), significa que a instância de função terão Windows Server 2008 no mesmo (IIS não será instalado).

Pode criar e implementar uma função de trabalho que utiliza algumas outras mecanismo para ouvir para pedidos de web recebidos certamente (por exemplo, o código poderia criar e utilizar um HttpListener .NET). Uma vez que todas as instâncias de função são executar Windows Server 2008, o seu código pode executar qualquer operações que normalmente estão disponíveis para uma aplicação em execução no Windows Server
2008.

Para cada função, indica o tamanho pretendido da memória virtual que devem utilizar instâncias dessa função. A tabela abaixo mostra os vários tamanhos VM disponíveis hoje e os atributos de cada:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamanho da memória virtual**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**Pico de rede e/s**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequenas**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequenas**

</td>
<td>
1 x 1.6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Médio**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Muito grandes**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Qual lhe é cobrada à hora para cada VM utiliza como uma instância de função e é também cobrado por quaisquer dados que o seu instâncias de função enviam fora do Centro de dados. Não são cobrados para os dados, introduzir o Centro de dados. Para mais informações, consulte o artigo [Azure preços] []. Em geral, é aconselhável utilizar várias instâncias de função pequenas por oposição a algumas instâncias grandes para que seja mais flexível para a falha da aplicação. Depois de todos os, as instâncias de função menos tiver, é mais desastrosa uma falha numa delas para a sua aplicação geral. Além disso, tal como mencionado antes, tem de implementar, pelo menos, duas instâncias para cada função para obter o contrato de nível de serviço 99.95% a Microsoft fornece.

Ficheiro de definição (CSDEF) do serviço também é onde pode especificar atributos muitas informações sobre cada função na sua aplicação. Eis alguns dos itens mais útil disponíveis para si:

-   **Certificados**. Utilizar certificados de encriptação de dados ou se o seu serviço web suporta SSL. Qualquer certificados têm de ser carregado para Azure. Para mais informações, consulte o artigo [Gerir certificados no Azure][]. Esta definição de XML instala carregados previamente certificados para o arquivo de certificados a instância de função, para que fiquem utilizáveis por código da aplicação.

-   **Nomes de definição de configuração**. Para valores que pretende que as suas aplicações a leitura ao executar o uma instância de função. O valor real das definições de configuração está definido no ficheiro de configuração (CSCFG) de serviço que pode ser atualizado em qualquer altura, sem que seja necessário voltar a implementar o seu código. Na verdade, pode código suas aplicações de forma para detetar os valores de configuração alteradas sem sempre qualquer tempo de inatividade.

-   **Os pontos finais de entrada**. Aqui pode especificar qualquer HTTP, HTTPS ou TCP pontos finais (com portas) que pretende expor para o mundo exterior através do seu *prefixo*. cloadapp.net URL. Quando Azure implementa ao seu cargo,-lo irá configurar a firewall na instância de função automaticamente.

-   **Os pontos finais internos**. Aqui pode especificar quaisquer HTTP ou TCP pontos finais que pretende expostos para outras instâncias de funções que são implementadas como parte da sua aplicação. Os pontos finais internos permitem todas as instâncias de funções dentro da sua aplicação para falar entre si mas não são acessíveis para as ocorrências de funções que estão fora da sua aplicação.

-   **Importar módulos**. Estes opcionalmente instalação-útil no seu instâncias de função. Componentes de existir diagnóstico Monitoring, ambiente de trabalho remoto e Azure ligue (que permite a instância de função aceder aos recursos no local através de um canal seguro).

-   **Armazenamento local**. Isto atribui um subdirectório na instância de função para a sua aplicação utilizar. É descrito detalhadamente mais no artigo [Ofertas de armazenamento de dados no Azure][] .

-   **Tarefas de arranque**. Tarefas de arranque dar-lhe uma forma de instalar componentes de pré-requisito numa instância de função como-arranca. As tarefas podem executar elevadas como administrador, se necessário.

## <a id="cfg"> </a>o ficheiro de configuração do serviço

O ficheiro de configuração (CSCFG) do serviço é um ficheiro XML que descreve as definições que podem ser alteradas sem Reimplementar a sua aplicação. Esquema de concluída para o ficheiro XML que pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
O ficheiro CSCFG contém um elemento de função para cada função na sua aplicação. Aqui estão alguns dos itens que pode especificar no ficheiro CSCFG:

-   **Versão do SO**. Este atributo permite-lhe selecionar a versão do sistema operativo (OS) que pretende utilizada para todas as instâncias de função a executar o código da aplicação. Este SO é conhecido como *Convidado SO*e cada nova versão inclui os últimos patches de segurança e atualizações disponíveis ao tempo que o convidado SO é disponibilizado. Se definir o valor do atributo osVersion para "\*", em seguida, Azure atualiza automaticamente o convidado SO em cada um dos seus instâncias de função à medida que ficam disponíveis novas versões de SO convidado. No entanto, pode optar terminar as atualizações automáticas ao selecionar a versão do SO convidado específico. Por exemplo, definir o atributo osVersion para um valor de "WA convidado-SO 2,8\_201109 01" faz com que todas as instâncias sua função obter o que é descrito nesta página web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Para mais informações sobre as versões do sistema operativo de convidado, consulte o artigo [Gerir actualizações do SO do Azure convidados].

-   **Instâncias**. Valor deste elemento indica o número de ocorrências de função que pretende executar o código para uma determinada função aprovisionada. Uma vez que pode carregar um novo ficheiro CSCFG para o Azure (sem Reimplementar a sua aplicação), é trivially simples alterar o valor para este elemento e carregue um novo ficheiro CSCFG para dinamicamente aumentar ou diminuir o número de ocorrências de função a executar o código da aplicação. Esta opção permite-lhe dimensionar facilmente a sua aplicação para cima ou para baixo para pedidos de carga de trabalho real encontro enquanto também controlar quanto lhe vai ser cobrada para executar as instâncias de função.

-   **Valores de definição de configuração**. Este elemento indica valores definições (tal como foi definido no ficheiro CSDEF). Ao seu cargo pode ler estes valores enquanto estiver em execução. Estes valores de definições de configuração são normalmente utilizadas para as cadeias de ligação à base de dados SQL ou para o armazenamento do Windows Azure, mas podem ser utilizadas para qualquer efeito que pretende.

## <a id="hostedservices"> </a>Criar e implementar um serviço alojado

Criação de um serviço alojado requer que primeiro aceda ao [Portal de gestão do Azure] e aprovisionar um serviço alojado ao especificar um prefixo DNS e o Centro de dados, finalmente, pretende que o código a ser executada em. Em seguida, no seu ambiente de desenvolvimento, crie o seu ficheiro de definição (CSDEF) do serviço, criar o seu código da aplicação e pacote (zip) todos estes ficheiros para um ficheiro de pacote (CSPKG) de serviço. Também tem de preparar o ficheiro de configuração (CSCFG) de serviço. Para implementar ao seu cargo, carregue os ficheiros CSPKG e CSCFG com a API de gestão de serviço do Azure. Depois de implementado, Azure, irá aprovisionar função instâncias no Centro de dados (baseadas em dados de configuração), extrair código da aplicação do pacote, copiá-lo para as instâncias de função e as instâncias de arranque. Agora, o seu código está a trabalhar.

A figura seguinte mostra os ficheiros CSPKG e CSCFG que criar no seu computador de desenvolvimento. O ficheiro CSPKG contém o ficheiro CSDEF e o código de duas funções. Após carregar os ficheiros CSPKG e CSCFG com a API de gestão de serviço do Azure, Azure cria as instâncias de funções no Centro de dados. Neste exemplo, o ficheiro CSCFG indicado que Azure deverá criar três instâncias da função \#1 e duas instâncias da função \#2.

![imagem][5]

Para mais informações sobre como implementar, atualizar e reconfigurar as funções, consulte o artigo [Implementar e atualizar as aplicações do Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referências

-   [Criação de um serviço alojado do Azure][]

-   [Gerir serviços alojados no Azure][]

-   [Migrar aplicações para o Azure][]

-   [Configurar uma aplicação do Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escritos por Jeffrey Richter (Wintellect)</p>

</div>

  [Vantagens de modelo de aplicação Azure]: #benefits
  [Serviço alojado Core conceitos]: #concepts
  [Considerações de estrutura de serviço alojado]: #considerations
  [Estruturar a sua aplicação de escala]: #scale
  [Definição de serviço alojado e configuração]: #defandcfg
  [O ficheiro de definição de serviço]: #def
  [O ficheiro de configuração do serviço]: #cfg
  [Criar e implementar um serviço alojado]: #hostedservices
  [Referências]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configurar um nome de domínio personalizado no Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Ofertas de armazenamento de dados no Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Gestão de certificados no Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gerir actualizações para convidados Azure SO]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portal de gestão do Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implementar e atualizar as aplicações do Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Criação de um serviço alojado do Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gerir serviços alojados no Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrar aplicações para o Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurar uma aplicação do Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
