<properties
   pageTitle="Como anotar as origens de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce como anotar elementos de dados no catálogo de dados do Azure, incluindo nomes amigáveis, etiquetas, descrições e especialistas."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Como anotar as origens de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o catálogo de dados é tudo sobre ajudando às pessoas descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir dos seus dados existentes. Quando uma origem de dados está registada com o catálogo de dados, os metadados são copiado e indexado pelo serviço, mas a história não existem termina. Catálogo de dados permite aos utilizadores fornecer os seus próprios metadados descritivo – como descrições e etiquetas – para completar os metadados extraídos da origem de dados e para efetuar a origem de dados mais compreensíveis a mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todas as pessoas tiverem um parecer. E esta é uma boa ideia.
Catálogo de dados reconhece que diferentes utilizadores têm diferentes perspetivas no origens de dados empresariais e que cada um dos seguintes perspetivas pode ser útil. Considere o seguinte cenário:

* O administrador do sistema sabe o contrato de nível de serviço para os servidores ou serviços que alojam a origem de dados.
* O administrador da base de dados sabe a agenda de cópia de segurança para cada base de dados e as janelas de processamento de ETL permitido.
* O proprietário do sistema sabe o processo para os utilizadores pedir acesso à origem de dados.
* O responsável pelos dados sabe como mapeiam os elementos e os atributos na origem de dados ao modelo de dados de empresa.
* O analista sabe como os dados são utilizados no contexto dos processos de negócio que ele suporta.

Cada um dos seguintes perspetivas é útil e o catálogo de dados utiliza uma abordagem crowdsourcing metadados que permite que cada um para ser capturado e utilizado para fornecer uma imagem completa de origens de dados registadas. Utilizar o portal do catálogo de dados, cada utilizador pode adicionar e editar o seus próprio anotações, ser capaz de ver anotações fornecidas por outros utilizadores.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
Catálogo de dados suporta os seguintes tipos de anotações:

| Anotação     | Notas                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome amigável  | Nomes amigáveis podem ser fornecidos ao nível de elementos de dados, para tornar os ativos de dados mais fácil compreensíveis. Nomes amigáveis são mais úteis quando o nome do objeto subjacente for críptica, abreviada ou caso contrário não significativo aos utilizadores.                                                                                                                            |
| Descrição    | Descrições podem ser fornecidas na linha de elementos de dados e o atributo / níveis de coluna. Descrições são anotações texto breve de formato livre que descrevem o utilizador perspectiva no elemento de dados ou a sua utilização.                                                                                                                                                              |
| Etiquetas (etiquetas de utilizador)          | Etiquetas podem ser fornecidas na linha de elementos de dados e o atributo / níveis de coluna. Etiquetas de utilizador são etiquetas definidos pelo utilizador que podem ser utilizadas para categorizar os elementos de dados ou atributos.                                                                                                                                                                                                    |
| Etiquetas (glossário etiquetas)          | Etiquetas podem ser fornecidas na linha de elementos de dados e o atributo / níveis de coluna. Glossário etiquetas são os termos de glossário definidas pelo centralmente que podem ser utilizados para categorizar elementos de dados ou atributos utilizando uma taxonomia de negócio comuns. Para mais informações, consulte [como configurar o glossário de negócio para regida etiquetas de rede](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Especialistas        | Podem ser fornecidas especialistas ao nível de elementos de dados. Especialistas identificar utilizadores ou grupos com perspetivas expert nos dados e podem servir de pontos de contacto para utilizadores que detetar origens de dados registadas e tiver questões não respondidas pelas anotações existentes.  |
| Pedir acesso | Pedir acesso informações podem ser fornecidas ao nível de elementos de dados. Estas informações ficam para utilizadores que descobrir uma origem de dados ainda não tem permissões de acesso. Os utilizadores podem introduzir o endereço de e-mail do utilizador ou grupo quem concede acesso, o URL do processo ou ferramenta que os utilizadores têm de ter acesso, ou podem introduzir o processo propriamente dito como texto. |
| Documentação | Documentação pode ser fornecida ao nível de elementos de dados. Documentação de elementos é informações de texto formatado que pode incluir hiperligações e imagens, e que pode fornecer as informações não encaminhadas através das descrições e etiquetas. |


## <a name="annotating-multiple-assets"></a>Anotar vários elementos
Quando seleciona vários elementos de dados no portal do catálogo de dados, os utilizadores podem anotar todos os elementos selecionados numa única operação. Anotações serão aplicada a todos os elementos selecionados, tornando mais fácil selecionar e fornecer uma descrição consistente e conjuntos de etiquetas e especialistas para elementos de dados relacionados.

> [AZURE.NOTE] Também podem ser fornecidas etiquetas e especialistas quando a ferramenta de registo da origem de registo elementos de dados a utilizar os dados de catálogo de dados.

Quando selecionar várias tabelas e vistas, apenas as colunas que selecionado todos os dados de activos têm em comum será apresentado no portal do catálogo de dados. Isto permite aos utilizadores fornecer etiquetas e descrições de todas as colunas com o mesmo nome para todos os elementos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e deteção
Tal como os metadados extraídos da origem de dados durante o registo são adicionado ao índice de pesquisa do catálogo de dados, metadados fornecidos pelo utilizador também são indexado. Isto significa que não só anotações tornam mais fácil para os utilizadores compreender os dados que são descobrirem, anotações também tornam mais fácil para os utilizadores descobri os ativos anotados de dados através de pesquisa com os termos que fazem sentido aos mesmos.

## <a name="summary"></a>Resumo
Registar uma origem de dados com o catálogo de dados assegura que os dados detetável copiando estrutural e descritivo metadados da origem de dados para o serviço de catálogo. Assim que tiver sido registada uma origem de dados, os utilizadores podem fornecer anotações para facilitar a descobrir e compreender a partir do portal do catálogo de dados.

## <a name="see-also"></a>Consulte também
- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter mais detalhes passo a passo sobre como anotar as origens de dados.
