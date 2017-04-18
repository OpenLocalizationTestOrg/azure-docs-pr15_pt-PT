## <a name="download-and-understand-the-arm-template"></a>Transferir e compreender o modelo de processador

Pode transferir o modelo de processador existente para criar um VNet e duas sub-redes de github, faça as alterações que poderá pretende e reutilizá-la. Para fazê-lo, siga os passos abaixo.

1. Navegue para [a página de modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Clique em **azuredeploy.json**e, em seguida, clique em **matéria-PRIMA**.
3. Guarde o ficheiro para um uma pasta local no seu computador.
4. Se estiver familiarizado com os modelos de processador, avance para o passo 7.
5. Abra o ficheiro que acabou de guardar e veja o conteúdo em **parâmetros** na linha 5. Parâmetros de modelo de processador fornecem um marcador de posição para valores que podem ser preenchidos durante a implementação.

    | Parâmetro | Descrição |
    |---|---|
    | **localização** | Azure região onde o VNet será criado |
    | **vnetName** | Nome para o novo VNet |
    | **addressPrefix** | Espaço de endereços para VNet, no formato CIDR |
    | **subnet1Name** | Nome para a primeira VNet |
    | **subnet1Prefix** | Bloco de CIDR para a primeira sub-rede |
    | **subnet2Name** | Nome para a segunda VNet |
    | **subnet2Prefix** | Bloco de CIDR para a segunda sub-rede |

    >[AZURE.IMPORTANT] Podem alterar os modelos de processador mantidos nas github ao longo do tempo. Certifique-se de que verificar o modelo antes de o utilizar.
    
6. Verifique o conteúdo em **recursos** e tenha em atenção o seguinte procedimento:

    - **tipo**. Tipo de recurso a ser criado pelo modelo. Neste caso, **Microsoft.Network/virtualNetworks**, que representam um VNet.
    - **nome**. Nome do recurso. Repare que a utilização de **[parameters('vnetName')]**, que significa o nome serão fornecidas como entrada pelo utilizador ou um ficheiro de parâmetro durante a implementação.
    - **Propriedades**. Lista de propriedades para o recurso. Este modelo utiliza das propriedades de sub-rede e espaço de endereços durante a criação de VNet.

7. Navegar para [a página de modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Clique **azuredeploy paremeters.json**e, em seguida, clique em **matéria-PRIMA**.
9. Guarde o ficheiro para um uma pasta local no seu computador.
10. Abra o ficheiro que acabou de guardar e edite os valores para os parâmetros. Utilize os valores abaixo para implementar VNet descrito no nosso cenário.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Guarde o ficheiro.
  