## <a name="scenario"></a>Cenário

Para ilustrar melhor como criar UDRs, este documento irá utilizar o cenário abaixo.

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Este cenário irá criar um UDR para a *sub-rede de fim de frente* e outra UDR para *trás sub-rede de fim* , conforme descrito abaixo: 

- **UDR FrontEnd**. O front-end UDR será aplicado à sub-rede *front-end* e contêm uma rota:  
    - **RouteToBackend**. Esta rota irá enviar todo o tráfego para a sub-rede back-end à máquina **FW1** virtual.
- **UDR-back-end**. O back-end UDR será aplicado à sub-rede *back-end* e contêm uma rota: 
    - **RouteToFrontend**. Esta rota irá enviar todo o tráfego para a sub-rede front-end à máquina **FW1** virtual.

A combinação destas rotas irá garantir que todo o tráfego destinado a partir de uma sub-rede para outro será encaminhado para o computador de virtual **FW1** , que está a ser utilizado como uma aplicação virtual. Também tem de ativar o reencaminhamento de IP para esse VM, para se certificar de que possa receber tráfego destinado a outros VMs.
