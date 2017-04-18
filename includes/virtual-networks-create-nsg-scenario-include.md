## <a name="scenario"></a>Cenário

Para ilustrar melhor como criar NSGs, este documento irá utilizar o cenário abaixo.

![Cenário de VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Neste cenário criará uma NSG para cada sub-rede na rede virtual **TestVNet** , conforme descrito abaixo: 

- **NSG FrontEnd**. O front-end NSG será aplicado à sub-rede *front-end* e conter duas regras:  
    - **rdp regra**. Esta regra irá permitir o tráfego RDP sub-rede *front-end* .
    - **regra de web**. Esta regra irá permitir o tráfego HTTP à sub-rede *front-end* .
- **NSG-back-end**. O back-end NSG será aplicado à sub-rede *back-end* e conter duas regras: 
    - **regra de sql**. Esta regra permite o tráfego SQL apenas a partir da sub-rede *front-end* .
    - **regra de web**. Esta regra impede a todos os internet vinculado tráfego da sub-rede *back-end* .

A combinação destas regras crie um cenário de DMZ semelhantes, onde a sub-rede back-end só pode receber o tráfego de entrada para SQL da sub-rede front-end e não tem acesso à Internet, enquanto a sub-rede front-end pode comunicar com a Internet e recebe pedidos HTTP recebidos apenas.
 
