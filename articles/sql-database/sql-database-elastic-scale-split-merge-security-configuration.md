<properties 
    pageTitle="Configuração de segurança de intercalação de divisão | Microsoft Azure" 
    description="Configurar o x409 certificados para encriptação" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configuração de segurança de intercalação de divisão  

Utilizar o serviço de intercalar/dividir, tem de configurar corretamente segurança. O serviço está parte da funcionalidade flexível escala da base de dados do Microsoft Azure SQL. Para mais informações, consulte o artigo [flexível dividir de escala e Tutorial do serviço de impressão em série](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurar certificados

Os certificados são configurados de duas maneiras. 

1. [Para configurar o certificado SSL](#To-Configure-the-SSL#Certificate)
2. [Para configurar os certificados de cliente](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Podem ser obtidos certificados de público autoridades de certificação (AC) ou do [Serviço de certificados do Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Estes são os métodos preferidos para obter certificados.

Se estas opções não estão disponíveis, pode gerar **autoassinados certificados**.
 
## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [MakeCert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* A partir de um programador comandos para Visual Studios, consulte o artigo [Visual Studio linha de comandos](http://msdn.microsoft.com/library/ms229859.aspx) 

    Se instalou, vá para:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obter o WDK a partir do [Windows 8.1: Transferir kits e ferramentas](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Para configurar o certificado SSL
É necessário um certificado SSL para encriptar a comunicação e autenticar o servidor. Selecione mais aplicável dos três cenários e executar todos os seus passos:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado

1.    [Criar um certificado Autoassinado](#Create-a-Self-Signed-Certificate)
2.    [Criar o ficheiro PFX para um certificado SSL auto-assinado](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Carregar um certificado SSL ao serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Atualizar um certificado SSL no ficheiro de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importar autoridade de certificação de SSL](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Para utilizar um certificado existente a partir da loja de certificado
1. [Exportar um certificado SSL do arquivo de certificados](#Export-SSL-Certificate-From-Certificate-Store)
2. [Carregar um certificado SSL ao serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Atualizar um certificado SSL no ficheiro de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para utilizar um certificado existente num ficheiro PFX

1. [Carregar um certificado SSL ao serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Atualizar um certificado SSL no ficheiro de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Para configurar os certificados de cliente
Os certificados de cliente são necessários para autenticar pedidos de serviço. Selecione mais aplicável dos três cenários e executar todos os seus passos:

### <a name="turn-off-client-certificates"></a>Desativar os certificados de cliente
1.    [Desativar a autenticação baseada em certificados do cliente](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Novos certificados de cliente autoassinado
1.    [Criar uma autoridade de certificação Autoassinado](#Create-a-Self-Signed-Certification-Authority)
2.    [Carregar certificado AC para serviço em nuvem](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Actualizar o certificado de AC no ficheiro de configuração do serviço](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Emitir certificados de cliente](#Issue-Client-Certificates)
5.    [Criar ficheiros PFX para certificados de cliente](#Create-PFX-files-for-Client-Certificates)
6.    [Certificado de cliente de importação](#Import-Client-Certificate)
7.    [Copiar miniaturas de certificado de cliente](#Copy-Client-Certificate-Thumbprints)
8.    [Configurar clientes permitidos no ficheiro de configuração de serviço](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Utilizar certificados de cliente existente
1.    [Localizar a chave pública AC](#Find-CA-Public Key)
2.    [Carregar certificado AC para serviço em nuvem](#Upload-CA-certificate-to-cloud-service)
3.    [Actualizar o certificado de AC no ficheiro de configuração do serviço](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copiar miniaturas de certificado de cliente](#Copy-Client-Certificate-Thumbprints)
5.    [Configurar clientes permitidos no ficheiro de configuração de serviço](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurar a verificação de revogação do certificado de cliente](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos

Acesso aos pontos finais de serviço pode ser restrito para intervalos de endereços IP específicos.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar a encriptação para o arquivo

É necessário um certificado para encriptar as credenciais que estão armazenadas no arquivo de metadados. Selecione mais aplicável dos três cenários e executar todos os seus passos:

### <a name="use-a-new-self-signed-certificate"></a>Utilize um certificado autoassinado novo

1.     [Criar um certificado Autoassinado](#Create-a-Self-Signed-Certificate)
2.     [Criar o ficheiro PFX para certificado de encriptação auto-assinado](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Carregar certificado de encriptação aos serviços em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilizar um certificado existente a partir da loja de certificado

1.     [Exportar o certificado de encriptação do arquivo de certificados](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Carregar certificado de encriptação aos serviços em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utilizar um certificado existente num ficheiro PFX

1.     [Carregar certificado de encriptação aos serviços em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Certificado de encriptação de atualização no ficheiro de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>A configuração predefinida

A configuração predefinida impede a todo o acesso o ponto final de HTTP. Esta é a definição recomendada, uma vez que os pedidos para estes pontos finais podem conter informações confidenciais, como as credenciais de base de dados.
A configuração predefinida permite o acesso de todos os para o ponto final HTTPS. Esta definição pode ser restrita ainda mais.

### <a name="changing-the-configuration"></a>Alterar a configuração

O grupo de regras de controlo de acesso que se aplicam a e ponto final estão configurados na **<EndpointAcls>** secção no **ficheiro de configuração de serviço**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

As regras de um grupo de controlo de acesso estão configuradas num <AccessControl name=""> secção do ficheiro de configuração de serviço. 

O formato é explicado na documentação listas de controlo de acesso de rede.
Por exemplo, para permitir que apenas IPs no intervalo 100.100.0.0 para 100.100.255.255 para aceder ao ponto final HTTPS, as regras teria o seguinte aspeto:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Negação de prevenção de serviço

Existem dois mecanismos diferentes suportados para detetar e impedir ataques de negação de serviço:

*    Restringir o número de pedidos em simultâneo por anfitrião remoto (desativado por predefinição)
*    Restringir a taxa de acesso por anfitrião remoto (por predefinição)

Estes são baseados nas funcionalidades ainda mais documentadas na segurança do IP dinâmico no IIS. Quando alterar esta configuração tenha em atenção os seguintes elementos:

* O comportamento da proxies e dispositivos de rede endereço tradução sobre as informações de sistema anfitrião remoto
* Cada pedido a qualquer outro recurso na função web é considerado (por exemplo, a ser carregados scripts, imagens, etc)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringir o número de acessos em simultâneo

As definições que configurar este comportamento são:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Alterar DynamicIpRestrictionDenyByConcurrentRequests para true para ativar esta protecção.

## <a name="restricting-rate-of-access"></a>Restringir taxa do access

As definições que configurar este comportamento são:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configurar a resposta a um pedido de negado

A seguinte definição configura a resposta a um pedido de negadas:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte a documentação para dinâmicas de IP de segurança no IIS para outros valores suportados.

## <a name="operations-for-configuring-service-certificates"></a>Operações para configurar os certificados de serviço
Este tópico destina-se apenas a referência. Siga os passos de configuração descritos no:

* Configurar o certificado SSL
* Configurar os certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Execute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

*    -n com o URL do serviço. Carateres universais ("CN = * .cloudapp .net") e nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") são suportados.
*    -e com a data de validade do certificado crie uma palavra-passe segura e especificá-lo quando lhe for pedido.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Criar um ficheiro PFX para um certificado SSL autoassinado

Execute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Introduza a palavra-passe e, em seguida, exporte o certificado com estas opções:
* Sim, exporte a chave privada
* Exportar todas as propriedades expandidas

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportar um certificado SSL do arquivo de certificados

* Localizar o certificado
* Clique em ações -> todas as tarefas -> Exportar...
* Exportar o certificado para um. Ficheiro PFX com estas opções:
    * Sim, exporte a chave privada
    * Incluir todos os certificados no caminho de certificação se possível * exportar todas as propriedades expandidas

## <a name="upload-ssl-certificate-to-cloud-service"></a>Carregar um certificado SSL para o serviço na nuvem

Carregar certificado com os existentes ou gerado. Ficheiro PFX com o par de chaves de SSL:

* Introduza a palavra-passe proteger as informações da chave privadas

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Atualizar um certificado SSL no ficheiro de configuração do serviço

Atualize o valor da seguinte definição no ficheiro de configuração de serviço de impressão digital com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importar autoridade de certificação de SSL

Siga estes passos em todos os conta/máquina que irá comunicar com o serviço:

* Faça duplo clique sobre a. Ficheiro CER no Explorador do Windows
* Na caixa de diálogo certificado, clique em instalar certificado...
* Importar o certificado para o arquivo de autoridades de certificação de raiz fidedigna

## <a name="turn-off-client-certificate-based-authentication"></a>Desativar a autenticação baseada em certificados do cliente

Apenas cliente autenticação baseada certificados é suportada e desativar-permitirá acesso do público para os pontos finais de serviço, a menos que outros mecanismos estão no local (por exemplo, Microsoft Azure Virtual Network).

Altere estas definições para FALSO no ficheiro de configuração de serviço para desativar a funcionalidade:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Em seguida, copie a impressão digital do mesmo como um certificado SSL na definição certificado de AC:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação autoassinado
Execute os seguintes passos para criar um certificado autoassinado para funcionar como uma autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Personalizá-lo

*    -e com a data de expiração de certificação


## <a name="find-ca-public-key"></a>Localizar a chave pública AC

Todos os certificados de cliente tem de ter sido emitidos por uma autoridade de certificação fidedigna pelo serviço. Localize a chave pública à autoridade de certificação que emitiu o cliente de certificados que irão ser utilizadas para autenticação para carregue-o para o serviço de nuvem.

Se o ficheiro com a chave pública não estiver disponível, exportá-los a partir da loja de certificado:

* Localizar o certificado
    * Procurar um certificado de cliente emitido por na mesma autoridade de certificação
* Faça duplo clique no certificado.
* Selecione o separador de caminho de certificação na caixa de diálogo certificado.
* Faça duplo clique na entrada AC no caminho.
* Tome notas das propriedades do certificado.
* Feche a caixa de diálogo do **certificado** .
* Localizar o certificado
    * Procurar a AC indicada acima.
* Clique em ações -> todas as tarefas -> Exportar...
* Exportar o certificado para um. CER com estas opções:
    * **Não, não exportar a chave privada**
    * Se possível inclua todos os certificados no caminho de certificação.
    * Exporte todas as propriedades expandidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carregar certificado AC serviço na nuvem

Carregar certificado com os existentes ou gerado. Ficheiro CER com a chave pública AC.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certificado de AC de atualização no ficheiro de configuração do serviço

Atualize o valor da seguinte definição no ficheiro de configuração de serviço de impressão digital com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Atualize o valor da seguinte definição com a impressão digital do mesmo:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Emitir certificados de cliente

Cada indivíduo autorizado para aceder ao serviço deverá ter um certificado de cliente emitido para his/hers exclusivo utilizar e deverá escolher que HIS/hers própria palavra-passe segura para proteger a sua chave privada. 

Os passos seguintes tem de ser executados no mesmo computador onde o certificado autoassinado da AC foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizar:

* -n com um ID de cliente do que vai ser autenticada com este certificado
* -e com a data de validade do certificado
* MyID.pvk e MyID.cer com nomes de ficheiro único para este certificado de cliente

Este comando irá pedir uma palavra-passe ser criados e, em seguida, utilizado uma vez. Utilize uma palavra-passe segura.

## <a name="create-pfx-files-for-client-certificates"></a>Criar ficheiros PFX para o cliente certificados

Cada gerado certificado de cliente, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizar:

    MyID.pvk and MyID.cer with the filename for the client certificate

Introduza a palavra-passe e, em seguida, exporte o certificado com estas opções:

* Sim, exporte a chave privada
* Exportar todas as propriedades expandidas
* A pessoa a quem é emitido este certificado deverá escolher a palavra-passe de exportação

## <a name="import-client-certificate"></a>Certificado de cliente de importação

Cada pessoa para quem tenha sido emitido um certificado de cliente deve importar o par de chave em máquinas por si irá utilizar para comunicar com o serviço:

* Faça duplo clique sobre a. Ficheiro PFX no Explorador do Windows
* Importar para o pessoal arquivo de certificados com, pelo menos, esta opção:
    * Incluir todas as propriedades expandidas selecionadas

## <a name="copy-client-certificate-thumbprints"></a>Copiar miniaturas de certificado de cliente
Cada pessoa para quem tenha sido emitido um certificado de cliente tem de seguir estes passos para poder obter a impressão digital de his/hers certificado que será adicionado ao ficheiro de configuração de serviço:
* Executar certmgr.exe
* Selecione o separador pessoal
* Faça duplo clique sobre o certificado de cliente para ser utilizado para autenticação
* Na caixa de diálogo certificado que é aberta, selecione o separador de detalhes
* Certifique-se de que mostrar está a apresentar todas as
* Selecione o campo denominado impressão digital na lista
* Copie o valor da impressão digital **Eliminar carateres Unicode que não sejam visíveis à frente do primeiro algarismo** eliminar todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar clientes de permitidos no ficheiro de configuração de serviço

Atualize o valor da seguinte definição no ficheiro de configuração de serviço com uma lista separados por vírgulas de miniaturas os certificados de cliente permitidos aceder ao serviço:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurar a verificação de revogação do certificado de cliente

Com a autoridade de certificação de estado de revogação do certificado de cliente não verifica a predefinição. Para ativar os controlos, se a autoridade de certificação que emitiu o cliente de certificados suporta esses controlos, altere a seguinte definição com um dos valores definidos na enumeração X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar um ficheiro PFX para os certificados de encriptação autoassinado

Para um certificado de encriptação, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizar:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Introduza a palavra-passe e, em seguida, exporte o certificado com estas opções:
*    Sim, exporte a chave privada
*    Exportar todas as propriedades expandidas
*    Terá a palavra-passe quando carregar o certificado para o serviço de nuvem.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar o certificado de encriptação do arquivo de certificados

*    Localizar o certificado
*    Clique em ações -> todas as tarefas -> Exportar...
*    Exportar o certificado para um. Ficheiro PFX com estas opções: 
  *    Sim, exporte a chave privada
  *    Incluir todos os certificados no caminho de certificação se possível 
*    Exportar todas as propriedades expandidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Carregar certificado de encriptação do serviço na nuvem

Carregar certificado com os existentes ou gerado. Ficheiro PFX com o par de chave de encriptação:

* Introduza a palavra-passe proteger as informações da chave privadas

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Certificado de encriptação de atualização no ficheiro de configuração do serviço

Atualize o valor das seguintes definições no ficheiro de configuração de serviço de impressão digital com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Operações de certificado comuns

* Configurar o certificado SSL
* Configurar os certificados de cliente

## <a name="find-certificate"></a>Localizar o certificado

Siga estes passos:

1. Execute mmc.exe.
2. Ficheiro -> Adicionar/Remover Snap-in...
3. Selecione **certificados**.
4. Clique em **Adicionar**.
5. Selecione a localização do arquivo de certificados.
6. Clique em **Concluir**.
7. Clique em **OK**.
8. Expanda **certificados**.
9. Expanda o nó do arquivo de certificados.
10. Expanda o nó subordinado do certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Certificado de exportação
No **Assistente de exportação do certificado**:

1. Clique em **seguinte**.
2. Selecione **Sim**, em seguida, **exporte a chave privada**.
3. Clique em **seguinte**.
4. Selecione o formato de ficheiro de exportação pretendido.
5. Selecione as opções pretendidas.
6. Verifique a **palavra-passe**.
7. Introduza uma palavra-passe segura e confirmá-la.
8. Clique em **seguinte**.
9. Escreva ou procure um nome de ficheiro onde pretende guardar o certificado (utilizar um. Extensão PFX).
10. Clique em **seguinte**.
11. Clique em **Concluir**.
12. Clique em **OK**.

## <a name="import-certificate"></a>Certificado de importação

No Assistente de importação de certificado:

1. Selecione a localização do arquivo.

    * Selecione o **Utilizador atual** se apenas processos em execução em utilizador atual irão aceder ao serviço
    * Selecione o **Computador Local** se outros processos neste computador irão aceder ao serviço
2. Clique em **seguinte**.
3. Se importar a partir de um ficheiro, confirme o caminho do ficheiro.
4. Se importar um. Ficheiro PFX:
    1.     Introduza a palavra-passe proteger a chave privada
    2.     Selecione as opções de importação
5.     Selecione os certificados de "Local" no seguinte arquivo
6.     Clique em **Procurar**.
7.     Selecione o arquivo pretendido.
8.     Clique em **Concluir**.
       
    * Se o arquivo de autoridade de certificação de raiz fidedignos foi escolhido, clique em **Sim**.
9.     Clique em **OK** em todas as janelas de caixa de diálogo.

## <a name="upload-certificate"></a>Carregar certificado

No [Portal do Azure](https://portal.azure.com/)

1. Selecione **serviços em nuvem**.
2. Selecione o serviço de nuvem.
3. No menu superior, clique em **certificados**.
4. Na barra inferior, clique em **carregar**.
5. Selecione o ficheiro de certificado.
6. Se é um. PFX ficheiro, introduza a palavra-passe para a chave privada.
7. Uma vez concluída, copie a impressão digital do certificado da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
 
As definições de SSL descritas neste documento encriptar comunicações entre o serviço e os seus clientes quando é utilizado o ponto final HTTPS. Esta informação é importante desde credenciais de acesso de base de dados e potencialmente outras informações confidenciais estão contidas na comunicação. No entanto, tenha em atenção que o serviço persistir estado interno, incluindo as credenciais, nas suas tabelas internas da base de dados do Microsoft Azure SQL que forneceu para o armazenamento de metadados na sua subscrição do Microsoft Azure. Nessa base de dados foi definida como parte da seguinte definição no ficheiro de configuração de serviço (. Ficheiro CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Credenciais armazenadas nesta base de dados são encriptadas. No entanto, como prática recomendada, certifique-se de que as funções web e trabalhador da sua implementações do serviço são mantidas atualizadas e seguro como são ambos têm acesso para a base de dados de metadados e o certificado utilizado para encriptação e desencriptar de credenciais armazenadas. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
