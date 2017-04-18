Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada parâmetros que contém todos os valores de parâmetro.
Deve definir um parâmetro para esses valores serão variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que serão sempre manter-se da mesma. Cada valor do parâmetro é utilizado no modelo para definir os recursos que são implementar. 

Quando definir parâmetros, utilize o campo **allowedValues** para especificar quais os valores de um utilizador pode fornecer durante a implementação. Utilize o campo de **ValorPredefinido** para atribuir um valor para o parâmetro, não se for fornecido nenhum valor durante a implementação.

Irá descrevemos cada parâmetro no modelo.

### <a name="logicappname"></a>logicAppName

O nome da aplicação lógica para criar.

    "logicAppName": {
        "type": "string"
    }