##<a name="tcp-settings-for-azure-vms"></a>Definições de TCP para Azure VMs

Azure VMs comunicam com a Internet pública utilizando [NAT] [ nat] (tradução de endereços de rede). Dispositivos NAT atribuam um endereço IP público e uma porta para uma VM Azure, permitindo que VM para estabelecer um socket para a comunicação com outros dispositivos. Se pacotes parar a fluir através desse socket após uma hora específica, o dispositivo NAT elimina o mapeamento se e o socket é gratuito ser utilizado por outros VMs.

Este é um comportamento NAT comuns, que pode causar problemas de comunicação em aplicações de TCP com base no que devem esperar um socket destacado, para além de um período de tempo limite. Existem duas definições de tempo limite inactivo a ter em conta para sessões de num estado *estabeleceu ligação* :

- **entrada** através do [Balanceador de carga Azure][azure-lb-timeout]. Este tempo limite predefinições a 4 minutos e pode ser ajustado para cima para 30 minutos.
- **saída** utilizando [SNAT] [ snat] (origem NAT). Este tempo limite está definido para 4 minutos e não pode ser ajustado.

Para assegurar ligações não são perdidas além do limite de tempo de espera, deve certificar-se de que a aplicação mantém a sessão vivo, ou pode configurar o sistema operativo subjacente para fazê-lo. As definições para ser utilizado são diferentes para os sistemas de Linux e Windows, conforme apresentado abaixo.

Para [Linux][linux], deverá alterar variáveis kernel abaixo.
NET.IPv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8
 
Para o [Windows][windows], deverá alterar os valores do registo.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


As definições acima, certifique-se um pacote de activo manter é enviado depois de 2 minutos (120 segundos) de tempo de espera e, em seguida, enviado a cada 30 segundos. E se 8 desses pacotes falhar, a sessão é ignorada.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md