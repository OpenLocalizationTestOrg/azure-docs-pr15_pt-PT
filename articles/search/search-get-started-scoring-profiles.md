<properties 
    pageTitle="Como utilizar pontuação perfis na pesquisa Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem" 
    description="Ajustar a classificação através de perfis de pontuação na pesquisa Azure, um serviço de pesquisa na nuvem alojado no Microsoft Azure de pesquisa." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Como utilizar perfis de pontuação na pesquisa do Azure

Os perfis de pontuação são uma funcionalidade do Microsoft Azure pesquisa que personalizar os cálculos de resultados de pesquisa, que influenciam como os itens são classificados numa lista de resultados de pesquisa. Poderá pensar pontuação perfis de forma relevância de modelo, ao aumentar a itens que correspondam a critérios predefinidos. Por exemplo, imaginemos que a aplicação é um site de reserva online hotel. Ao aumentar a `location` de campo, pesquisas incluem termos como Seattle irá resultar em pontuações mais elevadas para itens que tenham Seattle na `location` campo. Tenha em atenção que pode ter mais de um perfil de pontuação ou nenhum sequer, se a predefinição de pontuação é suficiente para a sua aplicação.

Para ajudar a experimentar com os perfis de pontuação, pode transferir uma aplicação de exemplo que utiliza os perfis de pontuação para alterar a ordem de classificação dos resultados da pesquisa. A amostra é uma aplicação de consola – talvez não muito real para o desenvolvimento de aplicações do mundo real – mas útil assim como uma ferramenta de aprendizagem. 

A aplicação de exemplo demonstra comportamentos pontuação utilizando os dados fictícios, denominados a `musicstoreindex`. Simplificar da aplicação exemplo torna mais fácil modificar perfis de pontuação e consultas e, em seguida, consulte o artigo os efeitos de imediatos na ordem de classificação quando o programa é executado.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Pré-requisitos

A aplicação de exemplo escrita c# utilizando o Visual Studio 2013. Se ainda não tiver uma cópia do Visual Studio, experimente o [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) gratuita.

Precisar de uma subscrição do Azure e um serviço de pesquisa do Azure para concluir o tutorial. Consulte o artigo [criar um serviço de pesquisa no portal](search-create-service-portal.md) para obter ajuda com a configurar o serviço.

[AZURE. INCLUIR [precisa de uma conta Azure para concluir este tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Aceda a [Demonstração de perfis de pontuação do Azure pesquisa](https://azuresearchscoringprofiles.codeplex.com/) no codeplex para transferir a aplicação de exemplo descrita neste tutorial.

No separador código fonte, clique em **Transferir** para obter um ficheiro zip da solução. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Editar App

1. Depois de extrair os ficheiros, abra a solução no Visual Studio para editar o ficheiro de configuração.
1. No Explorador de solução, faça duplo clique **App**. Este ficheiro Especifica o ponto final de serviço e um `api-key` utilizado para autenticar o pedido. Pode obter estes valores a partir do Portal clássica.
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Vá para o dashboard do serviço de pesquisa Azure.
1. Clique no mosaico de **Propriedades** para copiar o URL do serviço
1. Clique no mosaico de **teclas** para copiar o `api-key`.

Quando tiver terminado de adicionar o URL e `api-key` para App, as definições da aplicação deverão ter este aspeto:

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Explorar a aplicação

Estiver quase pronto para criar e executar a aplicação, mas antes de o fazer, consulte o artigo os ficheiros JSON utilizados para criar e povoar o índice.

**Schema.JSON** define o índice, incluindo os perfis de pontuação que são realçados neste demonstração. Aviso de que o esquema define todos os campos utilizados no índice remissivo, incluindo os campos que não sejam pesquisável, tais como `margin`, que pode utilizar no perfil de pontuação. Sintaxe de perfil de pontuação é documentado em [Adicionar um perfil de pontuação a um índice de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Dados1 3.json** fornece os dados, 246 álbuns através de alguns géneros. Os dados são uma combinação de reais informações do álbum de fotografias e intérprete, com campos fictícios como `price` e `margin` utilizada para ilustrar operações de pesquisa. Os ficheiros de dados está em conformidade com o índice e são carregados no seu serviço de pesquisa do Azure. Depois dos dados são carregados e indexados, pode emitir consultas contra-lo.

**Program.cs** executa as seguintes operações:

- É aberta uma janela de consola.

- Liga-se ao utilizar o URL do serviço de pesquisa de Azure e `api-key`.

- Elimina a `musicstoreindex` se existir.

- Cria um novo `musicstoreindex` utilizando o ficheiro schema.json.

- Preenche o índice que utilizam os ficheiros de dados.

- Consulta o índice através de consultas de quatro. Repare que os perfis de pontuação são especificados como um parâmetro de consulta. Todas as consultas de pesquisa para o mesmo termo, 'melhor'. A primeira consulta demonstra pontuação predefinido. As consultas de três restantes utilizam um perfil de pontuação.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Criar e executar a aplicação

Para excluir a conectividade ou problemas com a referência assemblagem, criar e executar a aplicação para garantir que não existem problemas para trabalhar sessão primeiro. Deverá visualizar uma aplicação de consola abrir em segundo plano. Executar todas as consultas de quatro sequência sem pausa. Em muitos sistemas, o programa completo executa em 15 segundos. Se a aplicação da consola incluir uma mensagem a indicar "completo. Prima a tecla enter para continuar a", o programa foi concluída com êxito. 

Para comparar consulta é executada, pode marcar copiar colar os resultados da consulta a partir da consola e colá-los um ficheiro do Excel. 

A ilustração seguinte mostra os resultados da primeira três consultas lado a lado. Todas as consultas utilizam o mesmo termo de pesquisa, 'melhor,' que é apresentado nos vários títulos de álbum de fotografias.

   ![][10]

A primeira consulta utiliza pontuação predefinido. Uma vez que o termo de pesquisa aparece apenas na títulos de álbum de fotografias e não outros é especificados critérios, tendo 'melhor' no título do álbum de fotografias de itens são devolvidos pela ordem em que o serviço de pesquisa encontra-los. 

A segunda consulta utiliza um perfil de pontuação, mas repare que o perfil não teve efeitos. Os resultados são idênticos da primeira consulta. Isto acontece porque o perfil pontuação aumenta num campo ('género') que não seja germano à consulta. O termo de pesquisa «melhores» não existir em qualquer campo 'género' de qualquer documento. Quando um perfil de pontuação não tem efeito, os resultados são iguais a pontuação predefinido.  

A consulta terceira é a primeira prova da pontuação impacto do perfil. O termo de pesquisa é ainda 'melhor' para que estamos a trabalhar com o mesmo conjunto de álbuns, mas uma vez que o perfil pontuação fornece critérios adicionais que aumenta 'Classificação' e 'última-atualizado', alguns itens são movidos superior na lista.

A ilustração seguinte mostra a consulta quarta e final, aumentada pelo 'Margem'. O campo 'Margem' é que não sejam pesquisável e não pode ser devolvido nos resultados da pesquisa. O valor de 'Margem' foi adicionado manualmente a folha de cálculo para ajudar a ilustrar o ponto de itens com as margens superiores aparecem no topo da lista de resultados de pesquisa. 

   ![][9]

Agora que tem experimented com os perfis de pontuação, experimente alterar o programa para utilizar a sintaxe de consulta diferentes, pontuação perfis ou os dados mais ricas. Ligações na secção seguinte fornecem mais informações.

<a id="next-steps"></a>
## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os perfis de pontuação. Consulte o artigo [Adicionar um perfil de pontuação a um índice de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.

Saiba mais sobre os parâmetros de sintaxe e a consulta de pesquisa. Consulte o artigo [Procurar documentos (Azure pesquisa REST API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

Necessita de passo anterior e saiba mais sobre a criação de índices? [Veja este vídeo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) para compreender as noções básicas.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 