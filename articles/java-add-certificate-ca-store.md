<properties 
    pageTitle="Adicionar um certificado ao arquivo de Java AC | Microsoft Azure" 
    description="Saiba como adicionar um certificado de certificação (AC) para o arquivo de certificados (cacerts) Java AC para Twilio serviço ou Azure Service Bus." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Adicionar um certificado para o arquivo de certificados de AC Java
Os passos seguintes mostram-lhe como adicionar um certificado de certificação (AC) para o arquivo de certificados (cacerts) Java AC. O exemplo utilizado é para o certificado de AC necessário pelo serviço Twilio. Informação fornecida mais tarde no tópico descreve como instalar o certificado AC para o Bus de serviço do Azure. 

Pode utilizar keytool para adicionar o certificado de AC antes de comprimir seu JDK e adicioná-lo para a pasta do seu projeto Azure **approot** ou que podem ocorrer uma tarefa de arranque Azure utiliza keytool para adicionar o certificado. Este exemplo assume que irá adicionar um certificado de AC antes de JDK a ser compactado. Além disso, um certificado específico de AC será utilizado no exemplo, mas os passos de obter um certificado AC diferente e importá-lo para o arquivo cacerts seria semelhantes.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para adicionar um certificado para o arquivo de cacerts

1. Numa linha de comandos que está definido para **jdk\jre\lib\security** pasta sua JDK, execute o seguinte procedimento para ver quais os certificados instalados:

    `keytool -list -keystore cacerts`

    Será solicitado para a palavra-passe de arquivo. A palavra-passe predefinida é **changeit**. (Se pretender alterar a palavra-passe, consulte a documentação keytool em <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Este exemplo assume que o certificado com MD5 impressões digitais 67:CB:9 D: não estiver listado C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 e, ao qual pretende importá-lo (este certificado específico é necessário pelo serviço Twilio API).
2. Obtenha o certificado a partir da lista de certificados listados no [GeoTrust certificados de raiz](http://www.geotrust.com/resources/root-certificates/). Botão direito do rato na ligação para o certificado com o número de série 35:DE:F4:CF e guarde-o para a pasta **jdk\jre\lib\security** . Para fins neste exemplo, foi guardado num ficheiro denominado **Equifax\_seguro\_certificado\_Authority.cer**.
3. Importe o certificado através do seguinte comando:

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Quando lhe for pedido para confiar neste certificado, se o certificado tem MD5 identificação 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responder ao escrever **y**.
4. Execute o seguinte comando para se certificar de que foram importado com êxito o certificado de AC:

    `keytool -list -keystore cacerts`

5. Código postal a JDK e adicioná-lo para a pasta do seu projeto Azure **approot** .

Para obter informações sobre keytool, consulte o artigo <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados de raiz Azure

As suas aplicações que utilizam Azure serviços (como Azure Service Bus) necessitarem confiar no certificado de Baltimore CyberTrust Root. (15 de Abril de 2013, do início Azure começou migrar a partir do GTE CyberTrust Global Root na raiz de CyberTrust Baltimore. Esta migração demorou vários meses para ser concluída.)

Baltimore certificado poderá já estar instalado no seu arquivo cacerts, por isso, se lembrar de executar o **keytool-lista** comando pela primeira vez para ver se já existe.

Se precisar de adicionar a raiz de CyberTrust Baltimore, tem 02:00:00:b9 de número de série e SHA1 identificação d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Podem ser transferido a partir do <https://cacert.omniroot.com/bc2025.crt>, guardadas num ficheiro local com a extensão **. cer**e, em seguida, importados utilizando **keytool** , conforme mostrado acima.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre os certificados de raiz utilizado pelo Azure, consulte o artigo [Migração de certificado de raiz do Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para mais informações sobre Java, consulte o [Centro de programadores do Java](/develop/java/).
