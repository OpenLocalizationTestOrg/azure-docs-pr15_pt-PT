##<a name="highly-available-solutions-with-azure-traffic-manager"></a>Soluções altamente disponíveis com o Gestor de tráfego Azure

Tem de determinar se requisitos de elevada disponibilidade da sua carga de trabalho podem ser correspondidos por utilizando o Gestor de tráfego Azure por si só, ou se precisar de combinar o Gestor de tráfego com outras soluções de DNS ou processos. Consoante as suas necessidades, pode utilizar:

- **Gestor de tráfego sozinho**. Se um % de 99,99 tempo é suficiente para a sua carga de trabalho, pode utilizar o Gestor de tráfego por si. Em caso de falha no serviço de Gestor de tráfego, os utilizadores que não seja possível aceder à sua carga de trabalho até que o serviço de Gestor de tráfego seja restabelecido.

- **Utilizar outra solução de Gestor de tráfego juntamente com o Gestor de tráfego Azure**. Em caso de falha no serviço de Gestor de tráfego, pode alterar o registo CNAME para apontar para outro serviço de Gestor de tráfego. Acesso à sua carga de trabalho ainda está disponível e distribuída para todas as localizações que aloja a sua carga de trabalho. Esta é a solução mais dispendiosa, mas pode ser necessária para das cargas de trabalho que necessitam de um SLA superior.
