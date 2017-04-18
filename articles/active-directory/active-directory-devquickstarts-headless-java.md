<properties
    pageTitle="Azure AD Java introdução | Microsoft Azure"
    description="Como criar uma aplicação de linha de comandos Java que os utilizadores a iniciar sessão para aceder a uma API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Utilizar a aplicação de linha de comandos de Java para aceder a uma API com Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna simples e de fácil utilizam recursos externos de gestão de identidades do web app, fornecendo única sessão e terminar sessão com apenas algumas linhas de código.  No Java web apps, que pode efetuar este utilizando implementação da ADAL4J orientadas na Comunidade do Microsoft.

  Aqui vamos utilizar ADAL4J para:
- Inicie sessão do utilizador para a aplicação a utilizar o Azure AD como o fornecedor de identidade.
- Apresenta algumas informações sobre o utilizador.
- Inicie sessão do utilizador terminar a aplicação.

Para poder fazer isto, terá:

1. Registar uma aplicação do Azure AD
2. Configure a sua aplicação para utilizar a biblioteca de ADAL4J.
3. Utilize a biblioteca de ADAL4J para emitir pedidos de iniciar sessão e terminar sessão para Azure AD.
4. Imprima dados sobre o utilizador.

Para começar a, [Transferir a estrutura da aplicação](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  Também terá de um inquilino do Azure AD na qual registar a aplicação.  Se ainda não tiver uma, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. registar uma aplicação do Azure AD
Para ativar a sua aplicação autenticar os utilizadores, primeiro precisa registar uma nova aplicação no seu inquilino.

- Inicie sessão no Portal de gestão Azure.
- No painel de navegação esquerdo, clique no **Active Directory**.
- Selecione o inquilino onde pretender registar a aplicação.
- Clique no separador **aplicações** e clique em Adicionar nível a parte inferior.
- Siga os pedidos e crie uma nova **aplicação Web e/ou WebAPI**.
    - O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    - O **URL de início de sessão** é o URL de base da sua aplicação.  Predefinição a estrutura é `http://localhost:8080/adal4jsample/`.
    - O **ID de aplicação URI** é um identificador exclusivo para a sua aplicação.  A Convenção é utilizar `https://<tenant-domain>/<app-name>`, por exemplo,`http://localhost:8080/adal4jsample/`
- Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de configurar.

Uma vez no portal para a sua aplicação crie uma **Aplicação secreta** para a sua aplicação e copie-o para baixo.  Terá-brevemente.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurar a aplicação para utilizar a biblioteca de ADAL4J pré-requisitos utilizando Maven
Aqui, podemos irá configurar ADAL4J para utilizar o protocolo de autenticação OpenID ligar.  ADAL4J será utilizada para iniciar sessão e terminar sessão pedidos de problema, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

-   No diretório de raiz do seu projeto, abrir/criar `pom.xml` e localize o `// TODO: provide dependencies for Maven` e substituir com o seguinte:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. criar o ficheiro de PublicClient java

Como indicado acima, vamos utilizar a API do gráfico para obter dados sobre o utilizador com sessão iniciada. Para que esta seja mais fácil-nos Recomendamos criar um ficheiro para representar um **Objeto de diretório** e ficheiros individuais para representar o **utilizador** para que possa ser utilizado o padrão OO de Java.

1. Criar um ficheiro denominado `DirectoryObject.java` que utilizamos para armazenar dados básicos sobre qualquer DirectoryObject (que pode à vontade utilizar este mais tarde para quaisquer consultas de gráfico que poderá fazer). Pode cortar/colar este abaixo:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compilar e executar o exemplo

Alterar novamente a saída para o seu diretório de raiz e execute o seguinte comando para criar a amostra apenas a colocar em conjunto com o `maven`. Isto irá utilizar o `pom.xml` escreveu para dependências de ficheiro.

`$ mvn package`

Agora deverá ter um `adal4jsample.war` de ficheiros no seu `/targets` diretório. Pode implementar que na sua contentor Tomcat e visite o URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
É muito fácil implementar uma guerra com os servidores de Tomcat mais recentes. Navegue simplesmente para `http://localhost:8080/manager/` e siga as instruções sobre como transferir o ' adal4jsample.war' ficheiro. Irá autodeploy por si com o ponto final correto.

##<a name="next-steps"></a>Próximos passos

Parabéns! Tem agora um trabalho aplicação Java que tem a capacidade de autenticar utilizadores, de forma segura APIs Web utilizando OAuth 2.0 de chamadas e obter informação básica sobre o utilizador.  Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

