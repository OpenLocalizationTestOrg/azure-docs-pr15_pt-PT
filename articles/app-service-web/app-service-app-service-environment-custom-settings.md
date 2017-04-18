<properties
    pageTitle="Definições personalizadas para ambientes de serviço de aplicação"
    description="Definições de configuração personalizadas para ambientes de serviço de aplicação"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Definições de configuração personalizadas para ambientes de serviço de aplicação

## <a name="overview"></a>Descrição geral ##
Porque são isoladas para um único cliente ambientes de serviço de aplicação, existem algumas definições de configuração de que podem ser aplicadas exclusivamente a ambientes de serviço de aplicação. Este artigo documentos as vários personalizações específicas que estão disponíveis para ambientes de serviço de aplicação.

Se não tiver um ambiente do serviço de aplicação, consulte o artigo [como criar um ambiente do serviço de aplicação](app-service-web-how-to-create-an-app-service-environment.md).

Pode armazenar personalizações de ambiente de serviço de aplicação, utilizando uma matriz no atributo **clusterSettings** novo. Este atributo está localizado no dicionário "Propriedades" *hostingEnvironments* Gestor de recursos do Azure entidade.

O fragmento de modelo de Gestor de recursos abreviada seguinte mostra o atributo **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

O atributo **clusterSettings** pode ser incluído num modelo de Gestor de recursos para atualizar o ambiente do serviço de aplicação.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilizar o Explorador de recursos do Azure para atualizar um ambiente do serviço de aplicação
Em alternativa, pode atualizar o ambiente do serviço de aplicação através do [Explorador de recursos do Azure](https://resources.azure.com).  

1. No Explorador do recurso, vá para o nó para o ambiente do serviço de aplicação (**subscrições** > **resourceGroups** > **fornecedores** > **Micrososft.Web** > **hostingEnvironments**). Em seguida, clique em ambiente de serviço de aplicação específico ao qual pretende atualizar.

2. No painel direito, clique em **Leitura/escrita** na barra de ferramentas superior para permitir que interativos de edição no Explorador do recurso.  

3. Clique em azul **Editar** botão para tornar o modelo de Gestor de recursos editável.

4. Desloque-se para a parte inferior do painel da direita. O atributo **clusterSettings** está na parte inferior, onde pode introduzir ou atualizar o seu valor.

5. Escreva (ou copie e cole) a matriz de valores de configuração que pretende no atributo **clusterSettings** .  

6. Clique na verde **colocar** botão que tem localizada na parte superior do painel da direita para consolidar a alteração para o ambiente do serviço de aplicação.

No entanto, submeter a alteração, demora cerca de 30 minutos multiplicados pelo número de extremidades frente no ambiente de serviço de aplicação para que a alteração entre em vigor.
Por exemplo, se um ambiente do serviço de aplicação tem quatro extremidades frente, irá demorar cerca de duas horas para a atualização configuração concluir. Enquanto está a ser lançada a alteração de configuração, sem outras operações dimensionamento ou operações de alteração de configuração, podem ter lugar no ambiente de serviço de aplicação.

## <a name="disable-tls-10"></a>Desativar TLS 1.0 ##
Uma pergunta periódica de clientes, especialmente os clientes que estão a lidar com conformidade PCI auditorias, é como desativar explicitamente TLS 1.0 para as suas aplicações.

Pode ser desativada TLS 1.0 através de entrada **clusterSettings** seguintes:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Alterar a ordem de conjunto de aplicações de encriptação TLS ##
Outra pergunta de clientes é se podem modificar a lista de codificações negociada pelo seu servidor de Isto pode ser realizado pelos modificar o **clusterSettings** conforme apresentado abaixo. A lista de conjuntos de codificação disponíveis pode ser obtida [Este artigo do MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Se valores incorretos estão definidos para o conjunto de codificação que não é possível compreender SChannel, todas as comunicações TLS ao seu servidor poderão deixar de funcionar. Neste caso, terá de remover a entrada de *FrontEndSSLCipherSuiteOrder* da **clusterSettings** e submeter o modelo de Gestor de recursos atualizado para reverter para as predefinições de conjunto de aplicações de codificação.  Utilize esta funcionalidade com cuidado.

## <a name="get-started"></a>Introdução
O site de modelo de Gestor de recursos de guia de introdução do Azure inclui um modelo com a definição de base para a [criação de um ambiente do serviço de aplicação](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
