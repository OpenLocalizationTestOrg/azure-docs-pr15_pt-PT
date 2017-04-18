<properties 
    pageTitle="Configurar o SSL para um serviço na nuvem | Microsoft Azure" 
    description="Saiba como especificar um ponto final HTTPS para uma função de web e como carregar um certificado SSL para proteger a sua aplicação. Estes exemplos utilizam o portal do Azure." 
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
    ms.date="10/04/2016"
    ms.author="adegeo"/>




# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurar SSL para uma aplicação no Azure

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-configure-ssl-certificate-portal.md)
- [Azure portal clássico](cloud-services-configure-ssl-certificate.md)

A encriptação Secure Socket Layer (SSL) é o método mais frequentemente utilizado de proteger dados enviados através da internet. Esta tarefa comum aborda como especificar um ponto final HTTPS para uma função de web e como carregar um certificado SSL para proteger a sua aplicação.

> [AZURE.NOTE] Os procedimentos nesta tarefa aplicam-se aos serviços em nuvem Azure; para os serviços de aplicação, consulte o artigo [Esta](../app-service-web/web-sites-configure-ssl-certificate.md).

Esta tarefa utiliza uma implementação de produção; informações sobre como utilizar uma implementação transição são fornecidas no final deste tópico.

Leia [Este](cloud-services-how-to-create-deploy-portal.md) primeiro se não tiver ainda criado um serviço na nuvem.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## <a name="step-1-get-an-ssl-certificate"></a>Passo 1: Obter um certificado SSL

Para configurar SSL para uma aplicação, primeiro precisa de obter um certificado SSL assinado por um certificado de certificação (AC), terceiros fidedigna que emite certificados para esse efeito. Se não já possui um, terá de obter um partir de uma empresa que vende os certificados SSL.

O certificado tem de cumprir os seguintes requisitos para certificados SSL no Azure:

-   O certificado tem de conter uma chave privada.
-   O certificado deve ser criado para troca de chaves, exportável para um ficheiro de intercâmbio de informações pessoais (. pfx).
-   Nome de assunto o certificado tem de corresponder o domínio utilizado para aceder ao serviço de nuvem. Não consegue obter um certificado SSL de uma autoridade de certificação (AC) do domínio cloudapp.net. É necessário adquirir um nome de domínio personalizado para utilizar quando aceder ao serviço. Quando pedir um certificado de uma AC nome de assunto o certificado tem de corresponder o nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, se o seu nome de domínio personalizado é **contoso.com** seria pedir um certificado a partir do seu AC para * **. contoso.com** ou * *www.contoso.com**.
-   O certificado tem de utilizar um mínimo de encriptação de 2048-bit.

Para fins de teste, pode [Criar](cloud-services-certs-create.md) e utilizar um certificado autoassinado. Um certificado autoassinado não é autenticado através de uma AC e pode utilizar o domínio cloudapp.net como o URL do Web site. Por exemplo, a tarefa abaixo utiliza um certificado autoassinado na qual o nome comum (NC) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, tem de incluir informações sobre o certificado na sua definição de serviço e os ficheiros de configuração de serviço.

<a name="modify"> </a>
## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar os ficheiros de definição e configuração do serviço

A aplicação tem de ser configurada para utilizar o certificado e um ponto final HTTPS têm de ser adicionado. Como resultado, a definição de serviço e os ficheiros de configuração do serviço tem de ser atualizados.

1.  No seu ambiente de desenvolvimento, abra o ficheiro de definição de serviço (CSDEF), adicione uma secção de **certificados** na secção **WebRole** e incluir as seguintes informações sobre o certificado (intermédios certificados):

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="My"
                             permissionLevel="limitedOrElevated" />
                <!-- IMPORTANT! Unless your certificate is either
                self-signed or signed directly by the CA root, you
                must include all the intermediate certificates
                here. You must list them here, even if they are
                not bound to any endpoints. Failing to list any of
                the intermediate certificates may cause hard-to-reproduce
                interoperability problems on some clients.-->
                <Certificate name="CAForSampleCertificate"
                             storeLocation="LocalMachine"
                             storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    A secção de **certificados** define o nome do nosso certificado, a localização e o nome do arquivo de onde está localizado.
    
    Permissões (`permisionLevel` atributo) podem ser definidas para um dos seguintes procedimentos:

  	| Valor de permissão  | Descrição |
  	| ----------------  | ----------- |
  	| limitedOrElevated | **(Predefinição)** Todos os processos de função podem aceder a chave privada. |
  	| elevados          | Apenas os processos elevados podem aceder a chave privada.|

2.  No seu ficheiro de definição de serviço, adicione um elemento **InputEndpoint** na secção **pontos finais** para ativar HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  No seu ficheiro de definição de serviço, adicione um elemento **encadernação** dentro da secção de **Sites** . Esta opção adiciona uma ligação HTTPS para mapear o ponto final para o seu site:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Todas as alterações necessárias para o ficheiro de definição de serviço tem sido concluídas, mas ainda precisa de adicionar as informações do certificado para o ficheiro de configuração do serviço.

4.  No serviço ficheiro de configuração (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione uma secção de **certificados** dentro da secção de **função** , substitua o valor de impressão digital de exemplo apresentado abaixo com que o seu certificado:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
                <Certificate name="CAForSampleCertificate"
                    thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(O exemplo acima utiliza **sha1** para o algoritmo de impressão digital. Especifique o valor apropriado para algoritmo de impressão digital do certificado.)

Agora que foram actualizados a definição de serviço e os ficheiros de configuração do serviço, o pacote sua implementação para carregar para Azure. Se estiver a utilizar **cspack**, não utilize o sinalizador **/generateConfigurationFile** , à medida que irá substituir as informações do certificado que acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado

Ligar ao portal e...

1. Selecionar o seu serviço de nuvem no Portal do, selecione o seu **Serviço na nuvem**. (Que é na secção de **todos os recursos** .) 
    
    ![Publicar o seu serviço de nuvem](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **certificados**.

    ![Clique no ícone de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Fornecer o **ficheiro**, **palavra-passe**, em seguida, clique em **carregar**.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Ligue à instância de função utilizando HTTPS

Agora que a sua implementação está a trabalhar no Azure, pode ligar ao mesmo através de HTTPS.
    
1.  Clique no **URL do Site** para abrir o browser.

    ![Clique no URL do Site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2.  No seu browser, modificar a ligação utilizar **https** em vez de **http**e, em seguida, visite a página.

    >[AZURE.NOTE] Se estiver a utilizar um certificado autoassinado, quando navega para um ponto final HTTPS que está associado o certificado autoassinado poderá ver um erro de certificado no browser. Utilizar um certificado assinado por uma autoridade de certificação fidedigna elimina este problema; Entretanto, pode ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao arquivo de certificados de autoridade de certificação fidedigna do utilizador).

    ![Pré-visualização do site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

    >[AZURE.TIP] Se pretender utilizar SSL para uma implementação transição em vez de uma implementação de produção, primeiro precisa de determinar o URL utilizado para a implementação de transição. Depois de ter sido implementado o seu serviço de nuvem, o URL para o ambiente de teste é determinado pelo **ID da implementação** GUID neste formato:`https://deployment-id.cloudapp.net/`  
      
    >Crie um certificado com o nome comum (NC) igual ao URL com base em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Utilize o portal para adicionar o certificado no seu serviço de nuvem faseada. Em seguida, adicione as informações do certificado aos seus ficheiros CSDEF e CSCFG, voltar a compactar a aplicação e atualizar a sua implementação faseada para utilizar o novo pacote.

## <a name="next-steps"></a>Próximos passos

* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o serviço na nuvem](cloud-services-how-to-manage-portal.md).
