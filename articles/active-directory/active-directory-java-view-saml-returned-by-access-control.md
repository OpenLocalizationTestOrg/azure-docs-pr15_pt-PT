<properties
    pageTitle="Vista SAML devolvida pelo serviço de controlo de acesso (Java)"
    description="Saiba como ver SAML devolvida pelo serviço de controlo de acesso nas aplicações de Java alojadas no Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Como ver SAML devolvido pelo serviço de controlo de acesso do Azure

Este guia mostrar-lhe como ver a subjacente segurança declaração Markup Language (SAML) devolvidos à aplicação pelo serviço de controlo de acesso de Azure (ACS). O guia constrói no tópico [como utilizadores do Web autenticar com Azure acesso controlo serviço utilizando Eclipse][] , fornecendo código que apresenta as informações de SAML. Aplicação concluída terá uma aspeto semelhante ao seguinte.

![Resultado do exemplo SAML][saml_output]

Para mais informações sobre ACS, consulte a secção [os passos seguintes](#next_steps) .

> [AZURE.NOTE]
> O filtro de controlo de serviços do Azure Access é uma pré-visualização de tecnologia de Comunidade. Como o software de pré-lançamento, não é formal suportada pela Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as tarefas neste guia, concluir a amostra como [utilizadores do Web autenticar com Eclipse de utilizar de serviço de controlo de acesso do Azure][] e utilizá-lo como o ponto de partida para este tutorial.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Adicionar a biblioteca de JspWriter à sua assemblagem de compilação caminho e implementação

Adicione a biblioteca que contém a classe de **javax.servlet.jsp.JspWriter** à sua assemblagem de caminho e implementação de compilação. Se estiver a utilizar Tomcat, a biblioteca está **jsp api.jar**, que está localizada na pasta de **biblioteca** Apache.

1. No Explorador de projeto do Eclipse, com o botão direito **MyACSHelloWorld**, clique em **Criar caminho**, clique em **Configurar o caminho de criar**, clique no separador de **bibliotecas** e, em seguida, clique em **Adicionar vasos externos**.
2. Na caixa de diálogo **Seleção JAR** , navegue para a necessária para caixa, selecione-o e, em seguida, clique em **Abrir**.
3. Com a caixa de diálogo de **Propriedades para MyACSHelloWorld** continua aberta, clique em **Assemblagem de implementação**.
4. Na caixa de diálogo **Assemblagem de implementação de Web** , clique em **Adicionar**.
5. Na caixa de diálogo **Nova assemblagem directiva** , clique em **Java construir caminho entradas** e, em seguida, clique em **seguinte**.
6. Selecione a biblioteca adequada e clique em **Concluir**.
7. Clique em **OK** para fechar a caixa de diálogo de **Propriedades para MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modificar o ficheiro JSP para apresentar SAML

Modificar **index.jsp** para utilizar o código seguinte.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Executar a aplicação

1. Executar a sua aplicação no emulador de computador ou implementar Azure, utilizando os passos documentados como [utilizadores do Web autenticar com Azure acesso controlo serviço utilizando Eclipse][].
2. Iniciar um browser e abra a aplicação web. Depois de iniciar sessão para a sua aplicação, verá informações SAML, incluindo a declaração de segurança fornecida pelo fornecedor de identidade.

## <a name="next-steps"></a>Próximos passos

Para mais explorar a funcionalidade do ACS e para experimentar mais sofisticados cenários, consulte o artigo [2.0 de serviço de controlo de acesso][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Serviço de controlo do Access 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Como autenticar utilizadores do Web com o serviço de controlo de acesso Azure utilizando Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 