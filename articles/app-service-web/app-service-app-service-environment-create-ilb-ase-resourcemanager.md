<properties 
    pageTitle="Como criar um Auxiliar ILB utilizar modelos de Gestor de recursos do Azure | Microsoft Azure" 
    description="Saiba como criar um Auxiliar do Balanceador de carga interno utilizar modelos de Gestor de recursos do Azure." 
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
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como criar um Auxiliar ILB utilizar modelos de Gestor de recursos do Azure

## <a name="overview"></a>Descrição geral ##
Ambientes de aplicação de serviço podem ser criados com um endereço interno da rede virtual em vez de um VIP público.  Este endereço interno é fornecido por um componente Azure denominado Balanceador de carga interna (ILB).  Um Auxiliar ILB podem ser criado através do portal Azure.  Pode também ser criado utilizando automatização em modelos de Gestor de recursos do Azure.  Este artigo explica os passos e sintaxe necessárias para criar um Auxiliar ILB com modelos de Gestor de recursos do Azure.

Existem três passos envolvidos na automatizar a criação de um Auxiliar ILB:
1. É criada pela primeira vez a Auxiliar base numa rede virtual utilizando um endereço de Balanceador de carga interno em vez de um VIP público.  Como parte deste passo, um nome de domínio de raiz está atribuído a Auxiliar o ILB.
2. Quando o Auxiliar ILB estiver criado, é carregado um certificado SSL.  
3. O certificado SSL carregado explicitamente está atribuído a Auxiliar o ILB como o certificado SSL de "predefinido".  Este certificado SSL será utilizado para o tráfego SSL para aplicações na Auxiliar ILB quando as aplicações estão endereçadas utilizando o domínio de raiz comuns atribuído a Auxiliar (por exemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criar a Base ILB Auxiliar ##
Um modelo de Gestor de recursos do Azure de exemplo e o seu ficheiro de parâmetros associados, estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros no ficheiro *azuredeploy.parameters.json* é comuns para a criação de tanto ILB ASEs, bem como ASEs vinculados a um público VIP.  Na lista abaixo chamadas parâmetros de nota especial de saída ou que são exclusivas, quando criar uma Auxiliar ILB:


- *interalLoadBalancingMode*: na maioria dos casos conjunto esta opção para 3, que significa que o tráfego HTTP/HTTPS nas portas 80/443 e controlo/dados portas de canal de ouvidos pelo serviço FTP no Auxiliar, irá estar vinculado a um ILB atribuído endereço interno da rede virtual.  Se esta propriedade em vez disso estiver definida para 2, em seguida, apenas o serviço FTP relacionado com portas (controlo e dados canais) serão vinculadas para um endereço de ILB, enquanto o tráfego HTTP/HTTPS irá permanecer na VIP público.
-  *dnsSuffix*: este parâmetro define o domínio de raiz predefinidos que será atribuído à Auxiliar.  Na variação da aplicação de serviço de Azure pública, o domínio de raiz predefinido para todas as aplicações web é *azurewebsites.net*.  No entanto uma vez que um Auxiliar ILB interno a rede virtual de um cliente, não faz sentido para utilizar o domínio de raiz o serviço de público predefinido.  Em vez disso, uma Auxiliar ILB deve ter um domínio de raiz predefinido que faça sentido para utilização na rede da virtuais interna numa empresa.  Por exemplo, um hipotética Contoso Corporation poderá utilizar um domínio de raiz predefinido de *interno contoso.com* para as aplicações que sejam destinam a apenas ser resolvido e acessível na rede da Contoso virtual. 
-  *ipSslAddressCount*: este parâmetro é automaticamente predefinir para um valor de 0 no ficheiro *azuredeploy.json* porque ILB ASEs conter apenas um único endereço ILB.  Não existem explícitos endereços IP SSL para um Auxiliar ILB e, consequentemente, o conjunto de endereços IP SSL para um Auxiliar ILB tem de estar definido como zero, caso contrário, irá ocorrer um erro de aprovisionamento. 

Assim que o ficheiro *azuredeploy.parameters.json* foi preenchido para um Auxiliar ILB, o Auxiliar ILB pode, em seguida, ser criada utilizando o fragmento de código do Powershell seguinte.  Altere o ficheiro caminhos para corresponder ao onde os ficheiros de modelo de Gestor de recursos do Azure estão localizados no seu computador.  Também não se esqueça de fornecer os seus próprios valores para o nome de implementação do Gestor de recursos do Azure e o nome de grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do Gestor de recursos do Azure modelo submeter irá demorar algumas horas a Auxiliar ILB seja criada.  Assim que a criação for concluída, o Auxiliar ILB irá aparecer no portal do UX na lista de ambientes de serviço de aplicação para a subscrição que o acionou de implementação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregar e configurar o certificado SSL "Predefinido" ##

Quando o Auxiliar ILB estiver criado, um certificado SSL deve ser associado com o Auxiliar à medida que utiliza "predefinido" certificado SSL para estabelecer ligações de SSL às aplicações.  Continuar com o exemplo Contoso Corporation hipotético, se o Auxiliar predefinido DNS sufixo é *interna contoso.com*, em seguida, uma ligação para *https://some-random-app.internal-contoso.com* requer um certificado SSL que seja válido para **.internal contoso.com*. 

Existem várias formas de obter um certificado SSL válido, incluindo AC interno, adquirir um certificado de um emissor externo e utilizar um certificado autoassinado.  Independentemente de origem do certificado SSL, os seguintes atributos de certificado tem de ser configurado corretamente:

- *Assunto*: este atributo tem de estar definido **.your-raiz domínio here.com*
- *Nome do assunto alternativo*: este atributo tem de incluir ambos * *.your-raiz domínio here.com*, e * *.scm.your-raiz-domínio-here.com*.  O motivo para a segunda entrada é que as ligações de SSL para o site de SMS/Kudu associados a cada aplicação serão feitas utilizando o endereço da de formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido mão, dois passos de preparação adicionais são necessários.  O certificado SSL tem de estar convertido/guardado como um ficheiro. pfx.  Lembre-se de que o ficheiro. pfx tem de incluir todos os intermédio e certificados de raiz e também tem de ser protegido com uma palavra-passe.

Em seguida, o ficheiro. pfx resultante tem de ser convertido numa cadeia base64 porque o certificado SSL será possível carregá-las utilizando um modelo de Gestor de recursos do Azure.  Uma vez que o Gestor de recursos do Azure modelos são ficheiros de texto, o ficheiro. pfx tem de ser convertidas numa cadeia base64, por isso, pode ser incluído como um parâmetro do modelo.

O fragmento de código do Powershell abaixo mostra um exemplo de gerar um certificado autoassinado, exportar o certificado como um ficheiro. pfx, converter o ficheiro. pfx com base64 cadeia codificada e, em seguida, guardando o base64 codificado cadeia para um ficheiro separado.  O código do Powershell para codificação base64 foi adaptado do [Blogue de Scripts de Powershell]do[examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Assim que o certificado SSL foi com êxito gerado e convertido com base64 codificado cadeia, o modelo de Gestor de recursos do Azure de exemplo no GitHub para [configurar o certificado SSL de predefinido] [ configuringDefaultSSLCertificate] pode ser utilizado.

Os parâmetros no ficheiro *azuredeploy.parameters.json* estão indicados abaixo:

- *appServiceEnvironmentName*: O nome da Auxiliar ILB a ser configuradas.
- *existingAseLocation*: cadeia de texto que contém o Azure região onde o Auxiliar ILB foi implementada.  Por exemplo: "Sul Central-nos".
- *pfxBlobString*: O based64 codificado representação de cadeia do ficheiro. pfx.  Utilizar o fragmento de código mostrado anteriormente, teria copie a cadeia "exportedcert.pfx.b64" contida e colá-lo no como o valor do atributo *pfxBlobString* .
- *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro. pfx.
- *certificateThumbprint*: impressão digital do certificado.  Se obter este valor a partir do Powershell (por exemplo, *$certificate. Impressão digital* a partir do fragmento de código anterior), pode utilizar o valor que-é.  No entanto se copiar o valor a partir da caixa de diálogo do certificado do Windows, lembre-se excluir os espaços estranhos.  O *certificateThumbprint* deverá ter um aspeto semelhante: AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *nome do certificado*: um identificador de cadeia amigável da sua preferência utilizado para identidade o certificado.  Como parte do identificador exclusivo do Gestor de recursos do Azure, é utilizado o nome para a entidade de *Microsoft.Web/certificates* que representa o certificado SSL.  A **tem** termina com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE.  Este sufixo é utilizado pelo portal como um indicador de que o certificado é utilizado para proteger um Auxiliar com capacidade de ILB.


Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado abaixo:


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Depois de preencher o ficheiro *azuredeploy.parameters.json* , o certificado SSL de predefinido pode ser configurado o fragmento de código do Powershell seguinte.  Altere o ficheiro caminhos para corresponder ao onde os ficheiros de modelo de Gestor de recursos do Azure estão localizados no seu computador.  Também não se esqueça de fornecer os seus próprios valores para o nome de implementação do Gestor de recursos do Azure e o nome de grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do Gestor de recursos do Azure modelo submeter irá demorar cerca minutos 40 minutos por Auxiliar front-end para aplicar a alteração.  Por exemplo, com um Auxiliar predefinido dimensionado utilizando duas frente-extremidades, o modelo irá demorar cerca uma hora e vinte minutos para concluir.  Enquanto o modelo estiver em execução a Auxiliar não conseguir dimensionada.  

Assim que o modelo for concluída, aplicações na Auxiliar ILB podem ser acedidas por HTTPS e as ligações serão protegidas utilizando o certificado SSL de predefinido.  O certificado SSL de predefinido será utilizado quando aplicações na Auxiliar ILB estão endereçadas utilizando uma combinação do nome da aplicação assim como o nome do anfitrião predefinido.  Por exemplo, *https://mycustomapp.internal-contoso.com* utilizaria o certificado SSL de predefinido de **.internal contoso.com*.

No entanto, tal como as aplicações a executar o serviço de inquilino com várias público, os programadores podem também de configurar nomes do anfitrião personalizados para as aplicações individuais e, em seguida, configurar enlaces de certificado SNI SSL exclusivos para as aplicações individuais.  


## <a name="getting-started"></a>Introdução

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de ambiente de serviço](app-service-app-service-environment-intro.md)

Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
