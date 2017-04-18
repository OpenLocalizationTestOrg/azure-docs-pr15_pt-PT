## <a name="load-balancer-differences"></a>Diferenças do Balanceador de carga

Existem diferentes opções para distribuir o tráfego de rede com o Microsoft Azure. Estas opções têm um funcionamento diferente a partir de outro, tendo uma funcionalidade diferentes definir e suporte cenários diferentes. Cada podem ser utilizados em isolamento ou combiná-los.

- **Azure Balanceador de carga** funciona na camada de transporte (4 camada na pilha de referência de rede de OSI). Fornece distribuição de nível de rede de tráfego em várias instâncias de uma aplicação em execução no Centro de dados Azure do mesmo.

- **Aplicação Gateway** funciona na camada de aplicação (camada 7 na pilha de referência de rede de OSI). Funciona como um serviço de proxy inverso, terminar a ligação do cliente e reencaminhar pedidos para os pontos finais de back-end.

- **Gestor de tráfego** funciona ao nível de DNS.  Utiliza as respostas DNS para direcionar tráfego de utilizador final para os pontos finais globalmente distribuídos. Clientes, em seguida, ligar a esses pontos finais diretamente.

A tabela seguinte resume as funcionalidades oferecidas pelo cada serviço:

| Serviço | Balanceador de carga Azure | Gateway de aplicação | Gestor de tráfego |
|---|---|---|---|
|Tecnologia de apoio| Nível de transporte (camada 4) | Nível de aplicação (camada 7) | Nível DNS |
| Protocolos de aplicação suportados | Qualquer | HTTP e HTTPS |  Qualquer (um ponto final HTTP é necessário para a monitorização de ponto final) |
| Pontos finais | Instâncias de função Azure VMs e serviços em nuvem | Qualquer endereço IP interno Azure ou internet pública endereço IP | Azure VMs, serviços em nuvem, aplicações Web do Azure e os pontos finais externos |
| Suporte de Vnet | Pode ser utilizada para ambas as aplicações de (Vnet) Internet internas e opostas | Pode ser utilizada para ambas as aplicações de (Vnet) Internet internas e opostas |    Suporta apenas os pedidos de acesso à Internet |
Monitorização de ponto final | Suportadas através de sondas | Suportadas através de sondas | Suportadas através de HTTP/HTTPS obter | 

Azure Balanceador de carga e de rede do Gateway aplicação encaminhar tráfego para os pontos finais mas possuírem cenários de utilização diferente para a qual o tráfego para processar. A tabela seguinte ajuda a compreender a diferença entre os balanceadores de dois carga:

| Tipo | Balanceador de carga Azure | Gateway de aplicação |
|---|---|---|
| Protocolos | TCP/UDP RECOMENDADAS | HTTP / HTTPS |
| Reserva de IP | Suportada | Não é suportado | 
| Modo de balanceamento de carga | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Round Robin<br>Encaminhamento com base no URL | 
| Modo de balanceamento de carga (IP de origem / autocolantes sessões) |  2-cadeia de identificação (IP de origem e destino IP), 3-cadeia de identificação (IP de origem, IP de destino e porta). Pode dimensionar para cima ou para baixo com base no número de máquinas virtuais | Com base em cookie afinidade<br>Encaminhamento com base no URL |
| Estado de funcionamento sondas | Predefinido: intervalo de sonda - 15 segundos. Tomadas terminar rotação: 2 falhas contínuas. Suporta definidos pelo utilizador sondas | Intervalo de sonda Idle 30 segundos. Subscritas depois de 5 falhas de tráfego direto consecutivos ou uma falha de sonda única no modo inactivo. Suporta definidos pelo utilizador sondas | 
| Descarregar SSL | Não é suportado | Suportada | 
  