<properties
    pageTitle="Como utilizar o controlo de acesso (Java) | Microsoft Azure"
    description="Saiba como desenvolver e utilizar o controlo de acesso com Java no Azure."
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

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Como autenticar utilizadores do Web com o serviço de controlo de acesso Azure utilizando Eclipse

Este guia mostrar-lhe como utilizar o serviço de controlo de acesso (ACS) o Azure dentro o Toolkit de Azure para Eclipse. Para mais informações sobre ACS, consulte a secção [os passos seguintes](#next_steps) .

> [AZURE.NOTE]
> O filtro de controlo de serviços do Azure Access é uma pré-visualização de tecnologia de Comunidade. Como o software de pré-lançamento, não é formal suportada pela Microsoft.

## <a name="what-is-acs"></a>O que é ACS?

A maior parte dos programadores não são especialistas de identidade e geralmente não quiser perder mecanismos de autenticação e de autorização de desenvolvimento no tempo para os respetivos aplicações e serviços. ACS é um serviço Azure que fornece uma forma fácil de autenticação de utilizadores que precisam de aceder ao seu aplicações e serviços web sem ter de lógica de autenticação complexa fator para o seu código.

As seguintes funcionalidades estão disponíveis no ACS:

-   Integração com o Windows identidade Foundation (WIF).
-   Suporte para fornecedores de identidade de web populares (IPs) incluindo Windows Live ID, Google, Yahoo! e Facebook.
-   Suporte para serviços de Federação do Active Directory (AD FS) 2.0.
-   Um Open Data Protocol (OData)-com base no serviço de gestão que fornece o acesso de programação para definições de ACS.
-   Um Portal de gestão que permita acesso administrativo às definições de ACS.

Para mais informações sobre ACS, consulte o artigo [2.0 de serviço de controlo de acesso][].

## <a name="concepts"></a>Conceitos

Azure ACS baseia-se em principais de identidade baseada em afirmações - uma abordagem coerente para a criação de mecanismos de autenticação para aplicações em execução no local ou na nuvem. Baseada em afirmações identidade fornece uma forma comum para aplicações e serviços adquirir as informações que necessitam de identidade utilizadores dentro da sua organização, de outras organizações e na Internet.

Para concluir as tarefas neste guia, deve compreender os conceitos seguintes:

**Cliente** - no contexto deste guia de procedimentos, este é um browser que está a tentar aceder à sua aplicação web.

**Aplicação de terceiros (RP) Relying** - aplicação An RP é um Web site ou serviço que outsources autenticação de uma autoridade de externa. Jargão identidade, podemos diga o que o RP confia essa autoridade. Este guia explica como configurar a aplicação para fidedignidade ACS.

**Token** - um token é uma coleção de dados de segurança que normalmente são emitidos mediante a autenticação de um utilizador com êxito. Contém um conjunto de *afirmações*, atributos do utilizador autenticado. Uma afirmação pode representar um nome de utilizador, um identificador para uma função de um utilizador pertence, idade de um utilizador e assim sucessivamente. Um token normalmente ter sido assinado digitalmente, que significa que pode sempre ser origem para o respectivo emissor e não pode ser adulterado respetivo conteúdo. Um utilizador obtém acesso a uma aplicação de RP apresentando um token válido emitido por uma autoridade de que a aplicação RP confia.

O **Fornecedor de identidade (IP)** - An IP é uma autoridade de que autentica identidades de utilizador e emite tokens de segurança. O trabalho real emitir tokens é implementado apesar de um serviço especial denominado serviço Token de segurança (STS). Típica IPs exemplos de Windows Live ID, Facebook, repositórios de utilizador de negócio (por exemplo, do Active Directory) e assim sucessivamente.
Quando ACS está configurado para confiar um IP, o sistema irá aceitar e validar os tokens emitidos por esse IP. ACS pode confiar em vários IPs ao mesmo tempo, o que significa que quando a aplicação confia ACS, pode instantaneamente oferecer a aplicação a todos os utilizadores autenticados a partir de todos os a IPs que confia ACS em seu nome.

**Fornecedor de Federação (FP)** - IPs possuir um conhecimento direto de utilizadores e autenticá-las utilizando as respetivas credenciais e emitir em afirmações sobre que eles sabem sobre os mesmos. Um fornecedor de Federação (FP) é um tipo de certificação diferente: em vez de autenticação de utilizadores diretamente, age como uma autenticação intermédio e de corretor entre um RP e uma ou mais IPs. IPs e FPs emitem tokens de segurança, por conseguinte ambas utilizam Services Token de segurança (STS). ACS é uma FP.

**Motor de regra ACS** - a lógica utilizada para transformar recebidas tokens a partir de IPs de confiança para tokens que se destina a ser consumida pela RP é codificação no formulário de regras de transformação de afirmações simples. Funcionalidades de ACS um motor de regra tratará de aplicar qualquer lógica de transformação que especificou para o seu RP.

**Espaço de nomes de ACS** - um espaço de nomes é uma partição de nível superior da ACS que utiliza para organizar as suas definições. Um espaço de nomes mantém uma lista de IPs confiar, as aplicações de RP pretendidas para servir, motor para processar tokens de entrada com as regras que espera que a regra e assim sucessivamente. Um espaço de nomes expõe vários pontos finais que serão utilizados pela aplicação e o programador para obter ACS para efetuar a sua função.

A figura seguinte mostra como autenticação ACS funciona com uma aplicação web:

![Diagrama de fluxo de ACS][acs_flow]

1.  O cliente (neste caso um browser) os pedidos de uma página a partir do RP.
2.  Uma vez que o pedido não é ainda autenticado, o RP redireciona o utilizador para a autoridade que confia, ou seja, ACS. ACS apresenta o utilizador com a escolha das IPs especificada para este RP. O utilizador seleciona o IP adequado.
3.  O cliente navega para a página de autenticação de IP e pede ao utilizador para iniciar sessão.
4.  Depois do cliente é autenticado (por exemplo, a identidade credenciais são introduzidas), o período de inquérito emite um token de segurança.
5.  Depois de emitir o token de segurança, o período de inquérito redireciona o cliente para ACS e o cliente envia o token de segurança emitido por parte do IP para ACS.
6.  ACS validar o token de segurança emitido por PI, entradas a identidade afirmações neste token para o motor de regras de ACS, calcula as afirmações de identidade resultado e problemas de um novo token de segurança que contém estas afirmações de saída.
7.  ACS redireciona o cliente para o RP. O cliente envia o novo token de segurança emitido por ACS para o RP. O RP valida a assinatura no token de segurança emitido por ACS, valida afirmações neste token e devolve a página que foi originalmente pedida.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as tarefas neste guia, terá o seguinte procedimento:

- Um Java programador Kit (JDK), v 1.6 ou posterior.
- -Eclipse IDE para os programadores de EE Java, Indigo ou posterior. Isto pode ser transferido das <http://www.eclipse.org/downloads/>. 
- Uma distribuição de um servidor web baseadas em Java ou servidor de aplicações, tal como Apache Tomcat, GlassFish, JBoss Application Server ou cais.
- uma subscrição Azure, que pode ser adquirida a partir do <http://www.microsoft.com/windowsazure/offers/>.
- O Toolkit de Azure para Eclipse, de Abril de 2014 solte ou posterior. Para mais informações, consulte o artigo [instalar o Toolkit de Azure para Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Um certificado x. 509 para utilizar com a aplicação. Terá este certificado no certificado pública (. cer) e intercâmbio de informações pessoais (. Formato de PFX). (Opções para criar este certificado serão descritas mais tarde neste tutorial).
- Familiaridade com o Azure calcular emulador e técnicas de implementação abordadas na [criação de uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Criar um espaço de nomes de ACS

Para começar a utilizar o serviço de controlo de acesso (ACS) no Azure, tem de criar um espaço de nomes de ACS. O espaço de nomes fornece um âmbito exclusivo para endereçar recursos ACS a partir da aplicação.

1. Iniciar sessão no [Portal de gestão Azure][].
2. Clique em **do Active Directory**. 
3. Para criar um novo espaço de nomes do controlo de acesso, clique em **Novo**, clique em **Serviços de aplicação**, clique em **Controlo de acesso**e, em seguida, clique em **Criar rápida**. 
4. Introduza um nome para o espaço de nomes. Azure verifica se o nome é exclusivo.
5. Selecione a região em que é utilizado o espaço de nomes. Para um melhor desempenho, utilize a região em que está a implementar a aplicação.
6. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar para o espaço de nomes de ACS.
7. Clique em **Criar**.

Azure cria e ativa o espaço de nomes. Aguarde até que o estado de novo espaço de nomes está **ativa** antes de continuar. 

## <a name="add-identity-providers"></a>Adicionar fornecedores de identidade

Nesta tarefa, adicione IPs para utilizar com a aplicação RP para autenticação. Para fins de demonstração, esta tarefa mostra como adicionar o Windows Live como um IP, mas pode utilizar qualquer um dos IPs listado no Portal de gestão de ACS.


1.  No [Portal de gestão do Azure][], clique em **Active Directory**, selecione um espaço de nomes do controlo de acesso e, em seguida, clique em **Gerir**. O Portal de gestão de ACS é aberta.
2.  No painel de navegação do lado esquerdo do Portal de gestão de ACS, clique em **fornecedores de identidade**.
3.  Windows Live ID está ativada por predefinição e não podem ser eliminada. Para efeitos de neste tutorial, é utilizado apenas o Windows Live ID. Este ecrã, no entanto, é onde pode adicionar outros IPs, clicando no botão **Adicionar** .

Windows Live ID agora é activado como um IP para o seu espaço de nomes de ACS. Em seguida, especifique a aplicação web do Java (para ser criados mais tarde) como um RP.

## <a name="add-a-relying-party-application"></a>Adicionar uma aplicação de terceiros dependente

Nesta tarefa, configure ACS reconhecer a sua aplicação web do Java como uma aplicação RP válida.

1.  No Portal de gestão de ACS, clique em **aplicações de terceiros Relying**.
2.  Na página **Aplicações de terceiros confiar** , clique em **Adicionar**.
3.  Na página **Adicionar aplicação depender de festa** , faça o seguinte:
    1.  Em **nome**, escreva o nome da RP. Para fins neste tutorial, escreva **Azure Web App**.
    2.  No **modo de**, selecione **introduzir definições de manualmente**.
    3.  No **Realm**, escreva o URI aos quais se aplica o token de segurança emitido por ACS. Para esta tarefa, escreva **http://localhost:8080 /**.
        ![Confiar realm de terceiros para utilização em cluster emulador][relying_party_realm_emulator]
    4.  **Devolver** URL, escreva o URL para o qual ACS devolve o token de segurança. Para esta tarefa, escreva **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying festa devolver o URL para utilização em cluster emulador][relying_party_return_url_emulator]
    5.  Aceite os valores predefinidos no resto dos campos.

4.  Clique em **Guardar**.

Agora configurou com êxito a aplicação web do Java quando esta é executada no emulador de cluster Azure (na http://localhost:8080 /) para ser um RP no seu espaço de nomes de ACS. Em seguida, crie as regras que ACS utiliza para processar reclamações pela RP.

## <a name="create-rules"></a>Criar regras

Nesta tarefa, pode definir as regras que unidade como em afirmações são transmitidas a partir do IPs para o seu RP. Para este guia, podemos simplesmente irá configurar ACS para copiar os valores e os tipos de afirmações entrada diretamente no token de saída, sem filtragem ou modificá-las.

1.  Na página principal do Portal de gestão de ACS, clique em **grupos de regras**.
2.  Na página **Grupos de regras** , clique em **Grupo de regra predefinida para o Azure Web App**.
3.  Na página **Editar regra de grupo** , clique em **Gerar**.
4.  No **gerar regras: grupo predefinido de regra para o Azure Web App** da página, certifique-se o Windows Live ID está selecionada e, em seguida, clique em **Gerar**.    
5.  Na página **Editar regra de grupo** , clique em **Guardar**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Carregar um certificado para o seu espaço de nomes de ACS

Nesta tarefa, carregar uma. Certificado PFX que será utilizado para assinar pedidos tokens criados pelo seu espaço de nomes de ACS.

1.  Na página principal do Portal de gestão de ACS, clique em **certificados e chaves**.
2.  Na página de **certificados e chaves** , clique em **Adicionar** acima **Assinatura de tokens**.
3.  Na página **Adicionar a assinatura de tokens certificado ou chave** :
    1. Na secção **utilizados para** , clique em **Confiar aplicação de terceiros** e selecione **Azure Web App** (que definiu anteriormente como o nome da sua aplicação de terceiros dependente).
    2. Na secção **tipo** , selecione o **Certificado x. 509**.
    3. Na secção de **certificado** , clique no botão Procurar e navegue para o ficheiro de certificado x. 509 que pretende utilizar. Esta será uma. Ficheiro PFX. Selecione o ficheiro, clique em **Abrir**e, em seguida, introduza a palavra-passe de certificado na caixa de texto **palavra-passe** . Tenha em atenção que, para fins de teste, pode utilizar um automática-signed-certificado. Para criar um certificado autoassinado, utilize o botão de **Novo** na caixa de diálogo **Biblioteca de filtro ACS** (descrita posteriormente) ou utilize o utilitário de **encutil.exe** partir do [Web site de projeto][] do Kit de Starter Azure para Java.
    4. Certifique-se de que está selecionada **Tornar principal** . A página de **certificado de adicionar a assinatura de tokens ou chave** deverá ter um aspeto semelhante ao seguinte.
        ![Adicionar o certificado de assinatura de tokens][add_token_signing_cert]
    5. Clique em **Guardar** para guardar as suas definições e fechar a página de **certificado de adicionar a assinatura de tokens ou chave** .

Em seguida, reveja as informações na página de integração de aplicações e copie o URI que terá de configurar a aplicação web do Java para utilizar ACS.

## <a name="review-the-application-integration-page"></a>Reveja a página de integração de aplicações

Pode encontrar que todas as informações e o código de são necessárias para configurar a sua aplicação da web Java (a aplicação RP) para trabalhar com ACS na página do Portal de gestão de ACS integração de aplicações. Terão estas informações ao configurar a sua aplicação de web Java de autenticação federada.

1.  No Portal de gestão de ACS, clique em **integração de aplicações**.  
2.  Na página de **Integração de aplicações** , clique em **Páginas de início de sessão**.
3.  Na página de **Integração de página de início de sessão** , clique em **Azure Web App**.

No **integração de página de início de sessão: Azure Web App** página do URL listado na **opção 1: hiperligação para uma página de início de sessão alojado ACS** será utilizada na sua aplicação web do Java. Terá este valor ao adicionar a biblioteca de filtro de serviços de controlo do Azure acesso à aplicação Java.

## <a name="create-a-java-web-application"></a>Criar uma aplicação web do Java
1. Dentro de Eclipse, no menu de clique em **ficheiro**, clique em **Novo**e, em seguida, clique em **Projeto de Web dinâmicos**. (Se não vir **Dinâmico Web Project** listado como um projeto disponível depois de clicar em **ficheiro**, a **Nova**, em seguida, faça o seguinte: clique em **ficheiro**, clique em **Novo**, clique em **Project**, expanda **Web**, clique em **Projeto de Web dinâmicos**e clique em **seguinte**.) Para fins neste tutorial, dê um nome do projeto **MyACSHelloWorld**. (Certifique-se de utiliza este nome, passos subsequentes neste tutorial esperar o ficheiro de guerra para ter o nome MyACSHelloWorld). Ecrã irão aparecer semelhante ao seguinte:

    ![Criar um projecto Olá mundo para ACS exampple][create_acs_hello_world]

    Clique em **Concluir**.
2. Vista de Project Explorer de Eclipse, expanda **MyACSHelloWorld**. Com o botão direito **ConteúdoWeb**, clique em **Novo**e, em seguida, clique em **Ficheiro de JSP**.
3. Na caixa de diálogo **Novo ficheiro JSP** , dê um nome de ficheiro **index.jsp**. Manter a pasta principal como MyACSHelloWorld/ConteúdoWeb, conforme mostrado no seguinte:

    ![Adicionar um ficheiro JSP por exemplo ACS][add_jsp_file_acs]

    Clique em **seguinte**.

4. Na caixa de diálogo **Seleccionar JSP modelo** , selecione **Novo ficheiro JSP (html)** e clique em **Concluir**.
5. Quando abre o ficheiro index.jsp no Eclipse, adicionar texto a apresentar **Olá ACS mundo!** dentro dos existentes `<body>` elemento. O atualizada `<body>` conteúdo deverá aparecer como o seguinte:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Guarde index.jsp.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Adicionar a biblioteca de filtro de ACS à aplicação

1. No Explorador de projeto do Eclipse, **MyACSHelloWorld**com o botão direito, clique em **Criar caminho**e, em seguida, clique em **Configurar o caminho de criar**.
2. Na caixa de diálogo **Java construir caminho** , clique no separador de **bibliotecas** .
3. Clique em **Adicionar a biblioteca**.
4. Clique em **Filtrar serviços de controlo de acesso do Azure (por Tech abrir MS)** e, em seguida, clique em **seguinte**. É apresentada a caixa de diálogo **Filtro de serviços de controlo de acesso de Azure** .  (No campo **localização** poderá ter um caminho diferente, dependendo de onde instalou Eclipse, e o número da versão poderão ser diferente, dependendo das atualizações de software.)

    ![Adicionar filtro de ACS biblioteca][add_acs_filter_lib]

5. Utilizando um browser aberto para a página de **Integração de página de início de sessão** do Portal de gestão, copie o URL listado na **opção 1: ligar a uma página de início de sessão alojado ACS** de campo e colá-lo para o campo de **Ponto final de autenticação ACS** da caixa de diálogo Eclipse.
6. Utilizar um browser aberto para a página **Editar aplicação festa depender** do Portal de gestão, copie o URL listado no campo **Realm** e cole-a para o campo **Confiar Realm parte** da caixa de diálogo Eclipse.
7. Na secção **segurança** da caixa de diálogo Eclipse, se pretender utilizar um certificado existente, clique em **Procurar**, navegue para o certificado que pretende utilizar, selecione-o e clique em **Abrir**. Em alternativa, se pretender criar um novo certificado, clique em **Novo** para apresentar a caixa de diálogo **Novo certificado** , em seguida, especifique a palavra-passe, o nome do ficheiro. cer e o nome do ficheiro. pfx para o novo certificado.
8. Selecione **incorporar o certificado no ficheiro guerra**. Incorporar o certificado desta forma inclui-lo na sua implementação sem que seja necessário adicioná-lo manualmente como um componente. (Se em vez disso, tem de guardar o seu certificado externamente do seu ficheiro de guerra, pode adicionar o certificado como um componente de função e desmarque **incorporar o certificado no ficheiro guerra**.)
9. [Opcional] Manter as **ligações exigir HTTPS** selecionada. Se configurar esta opção, terá de aceder à sua aplicação utilizando o protocolo HTTPS. Se não quiser necessitam de ligações HTTPS, desmarque esta opção.
10. Para uma implementação ao emulador de cluster, as definições de **Filtro de ACS Azure** terá uma aspeto semelhantes ao seguinte.

    ![Definições do filtro de ACS Azure para uma implementação ao emulador de cluster][add_acs_filter_lib_emulator]

11. Clique em **Concluir**.
12. Clique em **Sim** quando apresentado com uma caixa de diálogo a informar que será criado um ficheiro de web.xml.
13. Clique em **OK** para fechar a caixa de diálogo **Java construir caminho** .

## <a name="deploy-to-the-compute-emulator"></a>Implementar o emulador cluster

1. No Explorador de projeto do Eclipse, com o botão direito **MyACSHelloWorld**, clique em **Azure**e, em seguida, clique em **Compactar para Azure**.
2. **Nome do projeto**, escreva **MyAzureACSProject** e clique em **seguinte**.
3. Selecione um JDK e servidor de aplicações. (Estes abrange os passos em detalhe no tutorial [criar uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Clique em **Concluir**.
5. Clique no botão **executar no Azure emulador** .
6. Depois de inicia a aplicação web do Java em emulador de cluster, feche todas as instâncias do seu browser (para que quaisquer sessões atual do browser não interfira com o teste de início de sessão ACS).
7. Execute a sua aplicação abrindo <> http://localhost:8080/MyACSHelloWorld/no seu browser ( <> ou/https://localhost:8080/MyACSHelloWorld/se tiver marcado **ligações exigir HTTPS**). Deverá ser-lhe pedido para um início de sessão do Windows Live ID, em seguida, que deverá ser reencaminhado para o URL de retorno especificado para a sua aplicação de terceiros dependente.
99.  Quando tiver concluído a sua aplicação de visualização, clique no botão **Repor emulador Azure** .

## <a name="deploy-to-azure"></a>Implementar Azure

Para implementar Azure, terá de alterar o realm festa dependente e devolver o URL para o seu espaço de nomes de ACS.

1. No Portal de gestão do Azure, na página **Editar aplicação depender de festa** , modifique **Realm** para ser o URL do seu site implementada. Substitua o **exemplo** com o nome DNS que especificou para a sua implementação.

    ![Confiar realm de terceiros para utilização em produção][relying_party_realm_production]

2. Modificar o **URL de retorno** para o URL da sua aplicação. Substitua o **exemplo** com o nome DNS que especificou para a sua implementação.

    ![Colocar o URL de retorno festa para utilização em produção][relying_party_return_url_production]

3. Clique em **Guardar** para guardar o seu realm de festa replying atualizados e voltar alterações de URL.
4. Mantenha a página de **Integração de página de início de sessão** aberta no seu browser, terá de copiar a partir dos mesmos em breve.
5. No Explorador de projeto do Eclipse, **MyACSHelloWorld**com o botão direito, clique em **Criar caminho**e, em seguida, clique em **Configurar o caminho de criar**.
6. Clique no separador de **bibliotecas** , clique em **Filtro de serviços de controlo de acesso de Azure**e, em seguida, clique em **Editar**.
7. Utilizando um browser aberto para a página de **Integração de página de início de sessão** do Portal de gestão, copie o URL listado na **opção 1: ligar a uma página de início de sessão alojado ACS** de campo e colá-lo para o campo de **Ponto final de autenticação ACS** da caixa de diálogo Eclipse.
8. Utilizar um browser aberto para a página **Editar aplicação festa depender** do Portal de gestão, copie o URL listado no campo **Realm** e cole-a para o campo **Confiar Realm parte** da caixa de diálogo Eclipse.
9. Na secção **segurança** da caixa de diálogo Eclipse, se pretender utilizar um certificado existente, clique em **Procurar**, navegue para o certificado que pretende utilizar, selecione-o e clique em **Abrir**. Em alternativa, se pretender criar um novo certificado, clique em **Novo** para apresentar a caixa de diálogo **Novo certificado** , em seguida, especifique a palavra-passe, o nome do ficheiro. cer e o nome do ficheiro. pfx para o novo certificado.
10. Manter **incorporar o certificado no ficheiro guerra** selecionada, partindo do princípio de que pretende incorporar o certificado no ficheiro guerra.
11. [Opcional] Manter as **ligações exigir HTTPS** selecionada. Se configurar esta opção, terá de aceder à sua aplicação utilizando o protocolo HTTPS. Se não quiser necessitam de ligações HTTPS, desmarque esta opção.
12. Para uma implementação do Azure, as definições de filtro de ACS Azure terá uma aspeto semelhantes ao seguinte.

    ![Definições do filtro de ACS Azure para uma implementação de produção][add_acs_filter_lib_production]

13. Clique em **Concluir** para fechar a caixa de diálogo **Editar Biblioteca** .
14. Clique em **OK** para fechar a caixa de diálogo de **Propriedades para MyACSHelloWorld** .
15. No Eclipse, clique no botão **Publicar Azure nuvem** . Responda às mensagens, semelhantes como concluídos na secção **para implementar a aplicação para Azure** do tópico [criar uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Depois de ter sido implementada a sua aplicação web, feche quaisquer sessões aberta do browser, executar a sua aplicação web e deverá ser-lhe pedido para iniciar sessão com as credenciais do Windows Live ID, seguidas a ser enviada para o URL da sua aplicação de terceiros dependente de retorno.

Quando terminar através da aplicação de ACS Olá mundo, lembre-se eliminar a implementação (pode Saiba como eliminar uma implementação no tópico de [criar uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Próximos passos

Para uma análise da segurança afirmação marcação idioma (SAML) devolvido pela ACS à aplicação, consulte o artigo [como ver SAML devolvida pelo serviço de controlo de acesso de Azure][]. Para mais explorar a funcionalidade do ACS e para experimentar mais sofisticados cenários, consulte o artigo [2.0 de serviço de controlo de acesso][].

Além disso, este exemplo utilizado a opção **incorporar o certificado no ficheiro guerra** . Esta opção torna simples implementar o certificado. Se em vez disso pretender manter o seu certificado de assinatura separado do seu ficheiro de guerra, pode utilizar a técnica seguinte:

1. Na secção de **segurança** da caixa de diálogo **Filtro de serviços de controlo de acesso de Azure** , escreva $ **{Envelope. JAVA_HOME}/MyCert.cer** e desmarque **incorporar o certificado no ficheiro guerra**. (Ajuste MyCert. cer se o nome do ficheiro de certificado for diferente.) Clique em **Concluir** para fechar a caixa de diálogo.
2. Copiar o certificado como um componente na sua implementação: Project Explorer da Eclipse com sessão no, expanda **MyAzureACSProject**, **WorkerRole1**com o botão direito, clique em **Propriedades**, expanda **Função Azure**e clique em **componentes**.
3. Clique em **Adicionar**.
4. Dentro da caixa de diálogo **Adicionar um componente** :
    1. Na secção **Importar** :
        1. Utilize o botão de **ficheiro** para navegar para o certificado que pretende utilizar. 
        2. Para o **método**, selecione **uma cópia**.
    2. **Como nome**, clique na caixa de texto e aceitar o nome predefinido.
    3. Na secção **Implementar** :
        1. Para o **método**, selecione **uma cópia**.
        2. **Ao directório**, escreva **% JAVA_HOME %**.
    4. A caixa de diálogo **Adicionar um componente** deverá ter um aspeto semelhante ao seguinte.

        ![Adicionar o componente de certificados][add_cert_component]

    5. Clique em **OK**.

Neste momento, o seu certificado seria incluído na sua implementação. Tenha em atenção que, independentemente de se pretende incorporar o certificado no ficheiro guerra ou adicioná-la como um componente a sua implementação, é necessário carregar o certificado para o seu espaço de nomes, tal como descrito na secção de [carregar um certificado ao ACS espaço de nomes][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Carregar um certificado para o seu espaço de nomes de ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[Web site de projeto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Como ver SAML devolvido pelo serviço de controlo de acesso do Azure]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Serviço de controlo do Access 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portal de gestão do Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
