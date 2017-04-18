<properties 
    pageTitle="Nome do emissor e chave emissor nos serviços do BizTalk | Microsoft Azure" 
    description="Saiba como recuperar nome emissor e chave emissor para o serviço Bus ou controlo de acesso (ACS) nos serviços do BizTalk. MAK, MAB, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk serviços: Nome do emissor e chave emissor

Serviços de BizTalk Azure utiliza o nome do serviço Bus emissor e chave emissor e o nome do controlo de acesso emissor e chave emissor. Especificamente:

Tarefa | Que nome emissor e chave emissor
--- | ---
Implementar a aplicação a partir do Visual Studio | Aceder ao nome do controlo emissor e chave emissor
Configurar o Portal de serviços de Azure BizTalk | Aceder ao nome do controlo emissor e chave emissor
Criar LOB estações de retransmissão com os serviços de placa BizTalk no Visual Studio | Nome do serviço Bus emissor e chave emissor

Este tópico indica os passos para obter o nome do emissor e a chave de emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Aceder ao nome do controlo emissor e chave emissor
O nome do controlo de acesso emissor e a chave de emissor são utilizados pelos seguintes passos:

- A aplicação de serviço de BizTalk do Azure criada no Visual Studio: para com êxito implementar a aplicação de serviço de BizTalk no Visual Studio Azure, introduza o nome do controlo de acesso emissor e a chave de emissor. 
- Portal de serviços do Azure BizTalk: Quando criar um serviço de BizTalk e abra o Portal de serviços de BizTalk, o nome do controlo de acesso emissor e chave emissor são registados automaticamente nas suas implementações com os mesmos valores de controlo de acesso.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Para copiar e colar o nome do controlo de acesso emissor e a chave de emissor

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk serviços**.
3. Selecione o seu serviço de BizTalk. 
4. Selecione as **Informações de ligação** na barra de tarefas. O espaço de nomes do controlo de acesso, emissor predefinido (emissor nome) e a chave predefinido (tecla emissor) estão listados e podem ser copiado e colado.  

Resumindo:  
Nome do emissor = emissor predefinido  
Chave emissor = chave predefinida


Também pode selecionar **Abrir Portal de gestão de ACS** obtenha os valores de controlo de acesso:

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk serviços**.
3. Selecione o seu serviço de BizTalk.
4. Selecione o botão de informações de ligação e selecione **Abrir Portal de gestão de ACS**.
5. No Portal em **Definições do serviço**, selecione **Identidades de serviço**. Isto apresenta a sua identidade de serviço, que é o valor de nome de emissor do controlo de acesso. Selecione a sua ligação de identidade de serviço para ver a palavra-passe, que é o valor de chave emissor. Podem ser copiados os respetivos valores.<br/><br/>
Por exemplo, no **Identidades de serviço**, verá "proprietário". "Proprietário" é o seu nome de emissor de controlo de acesso. Quando clicar na ligação "proprietário", verá a **palavra-passe**. Quando clicar na ligação "Palavra-passe", verá o valor. Este valor de palavra-passe é a sua chave de emissor de controlo de acesso.  

Resumindo:  
Nome do emissor = nome de identidade de serviço  
Chave emissor = valor de palavra-passe

No painel de navegação à esquerda, também pode selecionar **Active Directory** para obter os valores de controlo de acesso. 

> [AZURE.IMPORTANT]Quando um espaço de nomes do controlo de acesso é criado utilizando o **Active Directory**, uma identidade de serviço **não** é criada automaticamente. Quando o aprovisionamento de um serviço de BizTalk, um espaço de nomes do controlo de acesso, serviço de identidade com o nome "nome do proprietário" (emissor), palavra-passe (emissor chave), e chave simétrica são criados automaticamente.<br /> 
[Como: utilizar ACS gestão de serviço para configurar o serviço de identidades](http://go.microsoft.com/fwlink/p/?LinkID=303942) fornece mais informações sobre identidades de serviço de controlo de acesso.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome do serviço Bus emissor e chave emissor
Nome do serviço Bus emissor e chave emissor são utilizadas pelo BizTalk placa de serviços. No seu projeto BizTalk serviços no Visual Studio, utilize os serviços de placa BizTalk para ligar a um sistema de linha de negócio (LOB) no local. Para ligar, crie o reencaminhamento LOB e introduza os detalhes do seu sistema LOB. Quando este procedimento, também introduzir o nome do serviço Bus emissor e a chave de emissor.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para obter o nome do serviço Bus emissor e a chave de emissor

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Bus de serviço**.
3. Selecione o seu espaço de nomes. Na barra de tarefas, selecione **Informações de ligação**. Esta ação apresentará o **Emissor predefinido** (emissor nome) e a **Chave predefinido** (tecla emissor). Podem ser copiados os respetivos valores.  

Resumindo:  
Nome do emissor = emissor predefinido  
Chave emissor = chave predefinida

## <a name="next"></a>Seguinte
Tópicos de Azure BizTalk serviços adicionais:

-  [Instalar os serviços de Azure BizTalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Serviços de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Consulte também
-  [Como: Utilize o serviço de gestão de ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços de BizTalk: Gráfico de estado de aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk serviços: Cópia de segurança e restauro](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços de BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
