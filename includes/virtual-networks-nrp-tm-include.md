## <a name="traffic-manager-profile"></a>Perfil do Gestor de tráfego

Gestor de tráfego e o seu recurso de ponto final subordinado activar o encaminhamento de DNS para os pontos finais no Azure e fora da Azure. Distribuição essas tráfego é regida pelos métodos de encaminhamento de política. Gestor de tráfego também permite a Estado de funcionamento do ponto final ser monitorizadas e tráfego desviado corretamente com base no estado de funcionamento de um ponto final. 

| Propriedade | Descrição |
|---|---|
|**trafficRoutingMethod**| os valores possíveis são *Desempenho*, *ponderado*e *prioridade* | 
| **dnsConfig** | FQDN para o perfil | 
| **Protocolo** | protocolo de monitorização, os valores possíveis são *HTTP* e *HTTPS*|
| **Porta** | porta de monitorização |  
| **Caminho** | caminho de monitorização |
| **Pontos finais** |  contentor para recursos de ponto final | 

### <a name="endpoint"></a>Ponto final 

Um ponto final é um recurso de subordinados de um perfil do Gestor de tráfego. Representa um serviço ou ponto final de web à qual o utilizador é distribuído tráfego baseada na política configurada no recurso de perfil do Gestor de tráfego. 

| Propriedade | Descrição | 
|---|---| 
| **Tipo** |  o tipo do ponto final, os valores possíveis são *ponto final do Azure*, *Ponto final externos*e *Aninhados ponto final* | 
| **targetResourceId** |  endereço IP público de um ponto final de serviço ou web. Isto pode ser um extremo Azure ou externo. | 
| **Peso** | peso de ponto final utilizado na gestão de tráfego. | 
| **Prioridade** | prioridade do ponto final, utilizada para definir uma ação de activação pós-falha |

Exemplo do Gestor de tráfego no formato de Json: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Recursos adicionais

Leia [REST API documentação para o Gestor de tráfego](https://msdn.microsoft.com/library/azure/mt163664.aspx) para obter mais informações.
