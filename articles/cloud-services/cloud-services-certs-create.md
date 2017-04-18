<properties 
    pageTitle="Serviços e certificados de gestão de nuvem | Microsoft Azure" 
    description="Saiba como criar e utilizar certificados com o Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Descrição geral de certificados para serviços em nuvem Azure
Certificados são utilizados no Azure para serviços em nuvem ([certificados do serviço](#what-are-service-certificates)) e para autenticar com a gestão, de API ([certificados de gestão](#what-are-management-certificates) ao utilizar o portal de clássica Azure e não processador). Este tópico fornece uma descrição geral de ambos os tipos de certificado, como [Criar](#create) e [implementá](#deploy) -los para Azure.

Certificados utilizados no Azure são x. 509 v3 certificados e podem ser assinados por outro certificado fidedigno ou pode ser personalizada com a sessão iniciadas. Um certificado autoassinado está assinado pelo seu próprio criador e por esta razão, não são considerados fidedignos por predefinição. A maioria dos browsers pode ignorar este. Certificados autoassinados só devem ser utilizados por si mesmo quando desenvolver e testar o seu serviços em nuvem. 

Certificados utilizados pelo Azure podem conter um privado ou uma chave pública. Os certificados têm uma impressão digital que fornece um meio identificá-las de uma forma não ambígua. Este impressão digital é utilizado no Azure [ficheiro de configuração](cloud-services-configure-ssl-certificate.md) para identificar qual deve utilizar um serviço na nuvem o certificado. 

## <a name="what-are-service-certificates"></a>O que são certificados do serviço?
Certificados do serviço de estão anexados a serviços em nuvem e activar a comunicação segura para e de serviço. Por exemplo, se implementado uma função da web, que seria pretende fornecer um certificado que pode autenticar um extremo HTTPS exposto. Certificados do serviço, definidos na sua definição de serviço, são automaticamente implementados a máquina virtual que está a ser executada uma instância da sua função. 

Pode carregar certificados do serviço de portal clássica Azure quer através do portal clássico Azure ou ao utilizar a API de gestão do serviço. Certificados do serviço de estão associados a um serviço na nuvem específicos e atribuídos a uma implementação no ficheiro de definição de serviço.

Certificados do serviço de podem ser geridos separadamente a partir do seu services e podem ser geridos por pessoas diferentes. Por exemplo, um programador pode carregar um pacote de serviço que se refere a um certificado que um Gestor de TI tem carregados previamente para Azure. Um Gestor de TI pode gerir e renovar esse certificado alterar a configuração do serviço de sem ser necessário especificá-las carregar um novo pacote de serviço. O que é possível porque o nome lógico para o certificado e o nome do arquivo e a localização estão especificados no ficheiro de definição de serviço, durante a impressão digital do certificado é especificada no ficheiro de configuração de serviço. Para atualizar o certificado, só é necessário carregar um novo certificado e altere o valor de impressão digital no ficheiro de configuração de serviço.

## <a name="what-are-management-certificates"></a>O que são certificados de gestão da?
Certificados de gestão da permitem-lhe autenticar com a API de gestão do serviço fornecido pelo Azure clássico. Vários programas e ferramentas (como Visual Studio ou o SDK do Azure) irão utilizar estes certificados para automatizar a configuração e implementação de vários serviços Azure. Estas não são realmente relacionados com a serviços em nuvem. 

>[AZURE.WARNING] Tenha o cuidado de! Estes tipos de certificados permitir que qualquer pessoa que autentica com os mesmos para gerir a subscrição que estão associadas. 

### <a name="limitations"></a>Limitações
Existe um limite de certificados de gestão de 100 por subscrição. Também existe um limite de 100 certificados de gestão para todas as subscrições em ID de utilizador. do administrador de um serviço específico Se o ID de utilizador para o administrador de conta já foi utilizado para adicionar 100 certificados de gestão e existir uma necessidade de certificados mais, pode adicionar um administrador de cocriação para adicionar os certificados adicionais. 

Antes de adicionar mais de 100 certificados, consulte o artigo se pode reutilizar um certificado existente. Utilizar coadministradores adiciona complexidade potencialmente desnecessárias para o processo de gestão de certificado.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado
Pode utilizar qualquer ferramenta disponível para criar um certificado autoassinado, desde que estas adiram a estas definições:

* Um certificado x. 509.
* Contém uma chave privada.
* Criada para troca de chaves (ficheiro. pfx).
* Nome de assunto tem de corresponder o domínio utilizado para aceder ao serviço de nuvem. 
    > Não é possível adquirir um domínio de certificado para o cloudapp.net (ou para qualquer Azure relacionados com) SSL; nome de assunto o certificado tem de corresponder o nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, **contoso.net**, **contoso.cloudapp.net**.
* Mínimo de encriptação de 2048-bit.
* **Certificado de serviço apenas**: certificado do lado do cliente tem armazenadas no arquivo de certificados *pessoais* .

Existem duas formas de fácil para criar um certificado no Windows, com a `makecert.exe` utilitário ou IIS.

### <a name="makecertexe"></a>Makecert.exe

Este utilitário foi preterido e já não é documentado aqui. Consulte [Este artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) para obter mais informações.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Se pretender utilizar o certificado com um endereço IP em vez de um domínio, utilize o endereço IP no parâmetro - NomeDNS.


Se quiser utilizar este [certificado com o portal de gestão](../azure-api-management-certs.md), exportá-lo para um ficheiro **. cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de informação Internet (IIS)

Existem muitas páginas na internet, como o pode fazer com o IIS de folha de rosto. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é uma excelente posso encontrar posso pensar explica-lhe bem. 

### <a name="java"></a>Java
Pode utilizar Java para [criar um certificado](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[Este](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Próximos passos

[Carregue o seu certificado de serviço Portal clássica do Azure](cloud-services-configure-ssl-certificate.md) (ou o [Azure portal](cloud-services-configure-ssl-certificate-portal.md)).

Carregar um [certificado de gestão da API](../azure-api-management-certs.md) para o portal do Azure clássico.

>[AZURE.NOTE] Portal do Azure não utiliza o certificados de gestão para aceder à API mas em vez disso utiliza contas de utilizador.
