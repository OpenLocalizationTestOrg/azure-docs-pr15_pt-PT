<properties
   pageTitle="Pretender modelo de Gestor de recursos de configuração de estado | Microsoft Azure"
   description="Definição do modelo de Gestor de recursos para a configuração de estado pretendido no Azure com exemplos e resolução de problemas"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS e pretender configuração de estado com modelos de Gestor de recursos do Azure
Este artigo descreve o modelo de Gestor de recursos para o [processador de extensão de configuração de estado pretendido](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows

O fragmento que se segue flui para a secção de recursos do modelo.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Exemplo de modelo para o Windows VMSS

Um nó VMSS tem uma secção "Propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC é adicionado em "extensões". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="detailed-settings-information"></a>Obter informações detalhadas definições

O esquema que se segue é para a parte de definições da extensão do Azure DSC num modelo de Gestor de recursos do Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detalhes
| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| settings.wmfVersion | cadeia | Especifica a versão do Windows Management Framework que devem ser instaladas no seu VM. Definir esta propriedade para 'mais recente' instala a versão mais actualizada do WMF. Os valores possíveis apenas atuais para esta propriedade são **'4.0', '5.0', ' 5.0PP' e 'mais recente'**. Estes valores possíveis são sujeitas atualizações. O valor predefinido é 'mais recente'.|
| Settings.Configuration.URL | cadeia | Especifica a localização de URL a partir da qual pretende transferir o seu ficheiro de postal de configuração de DSC. Se o URL fornecido requer um token de SA de acesso, terá de definir a propriedade protectedSettings.configurationUrlSasToken para o valor do seu token SA. Esta propriedade é necessária se settings.configuration.script e/ou settings.configuration.function são definidos. |
| Settings.Configuration.script | cadeia | Especifica o nome do ficheiro de script que contém a definição de configuração do seu DSC. Este script tem de ser na pasta raiz do ficheiro zip transferida a partir do URL especificado pela propriedade configuration.url. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.script são definidos. |
| Settings.Configuration.Function | cadeia | Especifica o nome da sua configuração DSC. A configuração denominado tem de estar contida no script definido pelo configuration.script. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.function são definidos. |
| settings.configurationArguments | Coleções de sites | Define quaisquer parâmetros que pretender para passar para a sua configuração DSC. Esta propriedade não está encriptada. |
| settings.configurationData.url | cadeia | Especifica o URL a partir do qual transferir o ficheiro de dados (.pds1) configuração para utilizar como entrada para a sua configuração DSC. Se o URL fornecido requer um token de SA de acesso, terá de definir a propriedade protectedSettings.configurationDataUrlSasToken para o valor do seu token SA.|
| settings.privacy.dataEnabled | cadeia | Ativa ou desativa a coleção de telemetria. Os valores possíveis apenas para esta propriedade são **'Activar', 'Desactivar', ', ou $null**. Deixe esta propriedade em branco ou nulo permite telemetria. O valor predefinido é '. [Obter mais informações](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Coleções de sites | Define as localizações alternativas a partir do qual transferir o WMF. [Obter mais informações](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Coleções de sites | Define quaisquer parâmetros que pretender para passar para a sua configuração DSC. Esta propriedade está encriptada. |
| protectedSettings.configurationUrlSasToken | cadeia | Especifica o token de SA para aceder ao URL definido pelo configuration.url. Esta propriedade está encriptada. |
| protectedSettings.configurationDataUrlSasToken | cadeia | Especifica o token de SA para aceder ao URL definido pelo configurationData.url. Esta propriedade está encriptada. |

## <a name="settings-vs-protectedsettings"></a>Definições de vs. ProtectedSettings
Todas as definições são guardadas num ficheiro de texto de definições na VM.
Propriedades em 'Definições' são as propriedades públicas porque não estiverem encriptados no ficheiro de definições de texto.
Propriedades em 'protectedSettings' são encriptadas com um certificado e não são apresentadas em texto simples deste ficheiro à VM.

Se a configuração de precisar de credenciais, podem ser incluídos no protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Exemplo

O exemplo seguinte deriva da secção da [página Descrição geral de processador de extensão DSC](virtual-machines-windows-extensions-dsc-overview.md)"Introdução".
Este exemplo utiliza modelos de Gestor de recursos em vez de cmdlets para implementar a extensão. Guardar a configuração de "IisInstall.ps1", coloque-o numa. Código postal ficheiro e carregue o ficheiro um URL acessível. Este exemplo utiliza armazenamento de Blobs do Azure, mas é possível transferir. Código postal ficheiros a partir de qualquer localização arbitrário.

No modelo de Gestor de recursos do Azure, o código seguinte instrui VM para transferir o ficheiro correto e executar a função adequada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Atualizar a partir do formato anterior
As definições de formato anterior (que contém as propriedades públicas ModulesUrl, ConfigurationFunction, SasToken ou propriedades) automaticamente adaptarem para o formato atual e execute tal como estes faziam anteriormente.

O esquema que se segue é que o anterior definições esquema velhos:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Eis como o formato anterior adapta-se para o formato atual:

| Nome da propriedade | Anterior equivalente de esquema |
| --- | --- |
| settings.wmfVersion | definições. WMFVersion |
| Settings.Configuration.URL | definições. ModulesUrl |
| Settings.Configuration.script | Primeira parte das definições. ConfigurationFunction (antes de '\\\\') |
| Settings.Configuration.Function | Segunda parte das definições. ConfigurationFunction (depois de '\\\\') |
| settings.configurationArguments | definições. Propriedades |
| settings.configurationData.url | protectedSettings.DataBlobUri (sem token de SA) |
| settings.privacy.dataEnabled | definições. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | definições. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | definições. SasToken |
| protectedSettings.configurationDataUrlSasToken | Token de SA a partir do protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Resolução de problemas - código de erro 1100
Código de erro 1100 indica que não existe um problema com a entrada do utilizador a extensão da DSC.
O texto destes erros é variável e podem ser alteradas.
Eis alguns dos erros que pode ocorrer e como pode corrigi-los.

### <a name="invalid-values"></a>Valores inválidos
"Privacy.dataCollection é '{0}'. Os valores possíveis apenas são ', 'Activar' e 'Desactivar' ""WmfVersion é '{0}'. Apenas os valores possíveis são... e 'mais recente' "

Problema: Não é permitido um valor fornecido.

Solução: Altere o valor inválido para um valor válido. Consulte a tabela na secção de detalhes.

### <a name="invalid-url"></a>URL inválido
"ConfigurationData.url é '{0}'. Não é um URL válido""DataBlobUri é '{0}'. Não é um URL válido""Configuration.url é '{0}'. Não é um URL válido"

Problema: A fornecido que URL não é válido.

Solução: Verifique todos os URLs de fornecido. Certifique-se de todos os URLs resolvem para localizações válidas que pode aceder a extensão no computador remoto.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArgument inválido
"Escrever configurationArguments inválidos {0}"

Problema: A propriedade ConfigurationArguments não consegue resolver para um objeto de Hashtable. 

Solução: Tornar a propriedade de ConfigurationArguments um Hashtable. Siga o formato fornecido no exemplo anterior. Deve prestar propostas, vírgulas e chavetas.

### <a name="duplicate-configurationarguments"></a>Duplicar ConfigurationArguments
"Encontrada argumentos duplicados '{0}' na configurationArguments públicas e protegida"

Problema: ConfigurationArguments nas definições públicas e ConfigurationArguments nas definições protegidas conter propriedades com o mesmo nome.

Solução: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades em falta
"Configuration.function requer que configuration.url ou configuration.module está especificado"

"Configuration.url requer que configuration.script for especificado"

"Configuration.script requer que configuration.url for especificado"

"Configuration.url requer que configuration.function for especificado"

"ConfigurationUrlSasToken requer que configuration.url for especificado"

"ConfigurationDataUrlSasToken requer que configurationData.url for especificado"

Problema: Uma propriedade definida necessita de outra propriedade que está em falta.

Soluções: 
- Forneça a propriedade em falta.
- Remova a propriedade que necessita da propriedade em falta.


## <a name="next-steps"></a>Próximos passos
Saiba mais sobre DSC e conjuntos de escala de máquina virtual no [Utilizar Máquina Virtual escala conjuntos com a extensão de DSC Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Localize mais detalhes sobre a [Gestão de credenciais seguro do DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Para mais informações sobre a alça de extensão Azure DSC, consulte [Introdução ao processador de extensão Azure configuração de estado pretendido](virtual-machines-windows-extensions-dsc-overview.md). 

Para mais informações sobre DSC PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
