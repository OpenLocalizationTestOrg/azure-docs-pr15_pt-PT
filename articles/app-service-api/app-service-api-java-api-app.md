<properties
    pageTitle="Criar e implementar uma aplicação de Java API na aplicação de serviço do Azure"
    description="Saiba como criar um pacote de aplicação Java API e implementá-lo para a aplicação de serviço de Azure."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Criar e implementar uma aplicação de Java API na aplicação de serviço do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar uma aplicação Java e implementá-lo às aplicações de API do serviço de aplicação do Azure utilizando [Git]. As instruções neste tutorial, podem ser seguidas em qualquer sistema operativo que seja compatível com a execução de Java. O código neste tutorial é construído utilizando [Maven]. [Jax RS] é utilizada para criar o serviço RESTful e é gerado com base na especificação de metadados [Swagger] utilizando o [Editor de Swagger].

## <a name="prerequisites"></a>Pré-requisitos

1. [8 de Kit de Java para programadores] \(ou posterior)
1. [Maven] instalado no seu computador de desenvolvimento
1. [Git] instalado no seu computador de desenvolvimento
1. Uma subscrição paga ou a [versão de avaliação gratuita] do [Microsoft Azure]
1. Uma aplicação de teste HTTP como [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold API utilizando Swagger.IO

Utilizar o editor de online swagger.io, pode introduzir código Swagger JSON ou YAML que representa a estrutura do seu API. Assim que tiver a superfície de API concebida, pode exportar código para uma variedade de quadros e plataformas. Na secção seguinte, o código scaffolded será modificado para incluir a funcionalidade fictício. 

Esta demonstração vai começar com um corpo Swagger JSON que irá colar no editor de swagger.io, em seguida, será utilizado para gerar código efetuar utilizar de JAX-r para aceder a um ponto final de REST API. Em seguida, irá editar o código scaffolded para devolver dados fictício, simulação um REST API criadas por cima de um um mecanismo persistente de dados.  

1. Copie o seguinte código de Swagger JSON para sua área de transferência:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Navegue até ao [Online Swagger Editor]. Assim que estiver lá, clique no item de menu **ficheiro -> colar JSON** .

    ![Item de menu colar JSON][paste-json]

1. Cole a contactos lista API Swagger JSON que copiou anteriormente. 

    ![Colar o código JSON no Swagger][pasted-swagger]

1. Ver as páginas de documentação e resumo API foi processada num editor de. 

    ![Vista Swagger gerado documentos][view-swagger-generated-docs]

1. Selecione a opção de menu **Gerar servidor-> JAX RS** para scaffold o código do lado do servidor irá editar mais tarde para adicionar fictício pós-implementação. 

    ![Gerar código Item de Menu][generate-code-menu-item]

    Assim que o código é gerado, vai ser fornecido um ficheiro ZIP para transferir. Este ficheiro contém o código scaffolded pelo gerador de código Swagger e todas as associadas Crie scripts. Deszipe toda a biblioteca para um diretório na sua estação de trabalho de desenvolvimento. 

## <a name="edit-the-code-to-add-api-implementation"></a>Editar o código para adicionar API implementação

Nesta secção, irá substituir a implementação do lado do servidor do código gerados pelo Swagger com o código personalizado. O novo código irá devolver uma entidades ArrayList de contacto para o cliente de chamada. 

1. Abra o ficheiro de modelo *Contact.java* , que está localizado na pasta *gen/src/java/swagger/io/modelo* , utilizar o [Código do Visual Studio] ou o seu editor de texto favorito. 

    ![Ficheiro de modelo contactos Open][open-contact-model-file]

1. Adicione o construtor de seguinte para a classe de **contacto** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Abra o ficheiro de implementação do *ContactsApiServiceImpl.java* serviço, que está localizado na pasta *src/principais/java/es/swagger/api/impl* , utilizar o [Código do Visual Studio] ou o seu editor de texto favorito.

    ![Ficheiro de código do serviço de contacto aberto][open-contact-service-code-file]

1. Substitui o código no ficheiro com este código novo para adicionar uma implementação fictício para o código de serviço. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Abra uma linha de comandos e altere o diretório para a pasta de raiz da sua aplicação.

1. Execute o seguinte comando de Maven para construir o código e executá-la utilizando o servidor de aplicação do cais localmente. 

        mvn package jetty:run

1. Deverá ver a janela de comando refletir que cais iniciou o código num porta 8080. 

    ![Ficheiro de código do serviço de contacto aberto][run-jetty-war]

1. Utilize [Postman] para fazer um pedido de "obter todos os contactos" método API na api/http://localhost:8080/contactos.

    ![Ligar os contactos API][calling-contacts-api]

1. Utilize [Postman] para tornar um pedido para o método de API "obter o contacto específico" que se encontra no http://localhost:8080/api/contactos/2.

    ![Ligar os contactos API][calling-specific-contact-api]

1. Por fim, a criar o ficheiro Java guerra (arquivo Web) executando o seguinte comando Maven na consola. 

        mvn package war:war

1. Quando o ficheiro de guerra estiver compilado, será colocado para a pasta de **destino** . Navegue para a pasta de **destino** e mude o nome de ficheiro guerra para **ROOT.war**. (Assegurar que o uso de maiúsculas/minúsculas corresponde neste formato).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Por fim, execute os seguintes comandos a partir da pasta raiz da sua aplicação para criar uma pasta **Implementar** utilizar para implementar o ficheiro de guerra Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicar o resultado da aplicação de serviço do Azure

Nesta secção irá aprender a criar uma nova aplicação API utilizando o Portal do Azure, prepare nessa aplicação API para alojar aplicações Java e implementar o ficheiro de guerra recentemente criado Azure Service de aplicação para executar a sua nova aplicação API. 

1. Crie uma nova aplicação de API no [portal do Azure], clicando no item de menu **novo-Web > + Mobile -> API aplicação** , introduzir os detalhes da aplicação e, em seguida, clicando em **Criar**.

    ![Criar uma nova aplicação API][create-api-app]

1. Assim que a sua aplicação API ter sido criada, abra pá de **Definições** da sua aplicação e, em seguida, clique no item de menu de **definições da aplicação** . Selecione as versões mais recentes do Java das opções disponíveis, em seguida, selecione o Tomcat mais recente do menu do **contentor de Web** e, em seguida, clique em **Guardar**.

    ![Configurar o Java no pá a aplicação de API][set-up-java]

1. Clique no item de menu de definições de **credenciais de implementação** e fornecer um nome de utilizador e palavra-passe que pretende utilizar para publicar ficheiros para a sua aplicação API. 

    ![Definir credenciais de implementação][deployment-credentials]

1. Clique no item de menu de definições de **origem da implementação** . Uma vez, clique no botão de **Escolher origem** , selecione a opção de **Repositório de Git Local** e, em seguida, clique em **OK**. Isto irá criar um repositório de Git em execução no Azure, que tenha uma associação com a sua aplicação API. Sempre que consolidar código para o ramo *principal* do seu repositório Git, será publicado seu código para o seu direto instância de aplicação API em execução. 

    ![Configurar um repositório de Git novo local][select-git-repo]

1. Copie o URL do repositório Git nova para a sua área de transferência. Guarde-o como será importante para um pouco a incorporá. 

    ![Configurar um repositório de Git novo para a sua aplicação][copy-git-repo-url]

1. Git emissão o ficheiro de guerra para o repositório online. Para fazer isto, navegue para a pasta **Implementar** que criou anteriormente, para que facilmente possível consolidar o código por excesso para o repositório em execução no seu serviço de aplicação. Assim que estiver na janela da consola e compreendê-las para a pasta onde se encontra a pasta webapps, emita os seguintes comandos Git para iniciar o processo e fire de desativar uma implementação. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Assim que emitir o pedido de **envio** , é-lhe perguntado a palavra-passe que criou anteriormente para a credencial de implementação. Depois de introduzir as suas credenciais, deverá ver o seu portal apresentar que a atualização foi implementada.

1. Se mais uma vez utilizar Postman para acertar a aplicação de API recentemente implementado a ser executada em aplicação de serviço de Azure, verá que o comportamento é consistente e que agora está a devolver dados de contacto como esperado e utilizar as alterações de código simples para o Swagger.io scaffolded código Java. 

    ![Utilizar o seu Java contactos REST API live no Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Próximos passos

Neste artigo, foram conseguir iniciar com um ficheiro de Swagger JSON e algumas scaffolded código Java obtido a partir do editor de Swagger.io. A partir daí, as suas alterações simples e um Git implementar processo resultou num ter uma aplicação de API funcional escrita em Java. Mostra o próximo tutorial como [consumir API aplicações a partir dos clientes JavaScript, utilizando CORS][App Service API CORS]. Tutoriais posteriores na série mostram como implementar a autenticação e autorização.

Para criar este amostra, pode saber mais sobre o [Armazenamento SDK para Java] para continuarem as blobs JSON. Em alternativa, pode utilizar o [Documento DB Java SDK] para guardar os seus dados de contacto para Azure documento DB. 

Para mais informações sobre como utilizar Java no Azure, consulte o [Centro de programadores do Java].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portal do Azure]: https://portal.azure.com/
[Documento DB Java SDK]: ../documentdb/documentdb-java-application.md
[versão de avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Centro de programadores do Java]: /develop/java/
[8 de Kit de Java para programadores]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX-r]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Editor de online Swagger]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Armazenamento SDK para Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor de swagger]: http://editor.swagger.io/
[Código do Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
