## <a name="dynamic-service-plan"></a>Plano de serviço dinâmicos

No dinâmico serviço plano, as suas aplicações de função serão atribuídas a uma instância da aplicação de função. Se for necessário mais ocorrências serão adicionadas dinamicamente.
Podem expandir essas instâncias através de vários recursos informáticos, tornar o máximo partido da infraestrutura do Azure disponível. Além disso, as funções serão executadas em paralelo minimizar o tempo total necessário para processar pedidos. Tempo de execução para cada função é adicionado para cima, em segundos e agregado pela aplicação do função que contenham. Com o custo de esforço pelo número de instâncias, respectivos tamanho da memória e o tempo de execução total medido em Gigabyte segundos. Isto é uma excelente opção se as suas necessidades cluster são intermitentes ou as horas de trabalho tendem para ser muito curto como permite-lhe apenas pagar para recursos de cluster quando estes forem realmente em utilização.   

### <a name="memory-tier"></a>Camadas de memória

Dependendo do seu função precisa pode selecionar a quantidade de memória necessária para executá-las na aplicação de função (container das funções).
As opções de tamanho de memória variam do **128 MB para 1536 MB**. O tamanho da memória selecionado corresponde ao conjunto de trabalho necessário por todas as funções que fazem parte da sua aplicação de função. Se o seu código exigir mais memória do que o tamanho selecionado, a instância da aplicação de função irá encerrar devido a falta de memória disponível.

### <a name="scaling"></a>Dimensionamento

A plataforma de funções Azure irá avaliar as tráfego necessidades, com base em accionadores configurados, para decidir quando dimensionar para cima ou para baixo. A granularidade do dimensionamento da é a aplicação de função. Neste caso escalar para cima significa adicionar mais ocorrências de uma aplicação de função. Inversamente como tráfego vai para baixo, instâncias da aplicação de função são desactivado-eventualmente dimensionamento para baixo para zero quando nenhuma a ser executado.  

### <a name="resource-consumption-and-billing"></a>Consumo de recursos e faturação

No dinâmica alocação de recursos do modo concluir o processo diferente, o plano de serviço de aplicação padrão, por conseguinte, o modelo de consumo também é diferente, tem a permissão para um modelo de "de pagamento por utilização". Consumo será comunicado por aplicação de função, apenas para o tempo quando está a ser executado código.  
É calculada ao multiplicar: o tamanho da memória (em GB) pela quantidade total de tempo de execução (em segundos) para todas as funções em execução no interior da aplicação essa função. A unidade de consumo serão **GB-s (Gigabyte segundos)**.