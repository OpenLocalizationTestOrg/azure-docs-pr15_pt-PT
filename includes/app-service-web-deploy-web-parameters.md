Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada parâmetros que contém todos os valores de parâmetro.
Deve definir um parâmetro para esses valores serão variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que serão sempre manter-se da mesma. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo. 

Quando definir parâmetros, utilize o campo **allowedValues** para especificar quais os valores de um utilizador pode fornecer durante a implementação. Utilize o campo de **ValorPredefinido** para atribuir um valor para o parâmetro, não se for fornecido nenhum valor durante a implementação.

Irá descrevemos cada parâmetro no modelo.

### <a name="sitename"></a>nome do site

O nome da aplicação web que pretende criar.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

O nome do plano de serviço de aplicação para utilizar para o alojamento do web app.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU

A camada comparar para o plano de alojamento.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

O modelo define os valores que são permitidos para este parâmetro e atribui um valor predefinido (S1) se não for especificado nenhum valor.

### <a name="workersize"></a>workerSize

O tamanho de uma instância do plano de alojamento (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
O modelo define os valores que são permitidos para este parâmetro (0, 1 ou 2) e atribui um valor predefinido (0) se não for especificado nenhum valor. Os valores correspondem às pequenas médios e grandes.
