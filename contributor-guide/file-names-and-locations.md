<properties title="" pageTitle="Nomes de ficheiros e localizações para artigos técnicos Azure" description="Explica a estrutura de ficheiro para artigos e as convenções de nomenclatura que deve seguir quando cria um novo artigo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Nomes de ficheiros e localizações para artigos técnicos Azure

No nosso repositório de conteúdos técnico, utilizamos uma única pasta (a pasta de **artigos** ) para todos os artigos. Não existe nenhuma hierarquia de pastas - todos os artigos live na estrutura de ficheiro simples. Se criar pastas com artigos-los, os seus artigos não não possível publicar.

Em vez de utilizar uma estrutura de ficheiro como um princípio organizar, utilizamos uma convenção de nomenclatura de ficheiro estritamente que identifica claramente tópicos e que contribui para descoberta na web.

Eis o que precisa de saber:

+ [Regras]
+ [Padrão]
+ [Exemplos de padrão]
+ [Conteúdo Marketplace]
+ [Aprovação de nome de ficheiro]

##<a name="rules"></a>Regras

- Nomes de ficheiros podem conter apenas letras em minúsculas, números e hífenes. 
- Sem espaços nem carateres de pontuação. Utilize os hífenes para separar palavras e números no nome do ficheiro.
- Mais do que 80 caracteres - este é um limite de sistema de publicação
- Verbos de acção de utilização que são específicos, tais como desenvolver, comprar, construir, resolver problemas. Sem palavras - ar.
- Sem palavras pequenas - não incluir um e, a, em ou, etc.
- Todos os ficheiros tem de estar no markdown e utilizar a extensão de ficheiro MD.
- Escreva as palavras saída; evitar acrónimos não aprovados ou desnecessários em nomes de ficheiro

Acrónimos e initialisms em nomes de ficheiro - orientações específicas:

- Não abreviar nomes de serviço Azure - as primeiras palavras do nome do ficheiro devem ser o padrão, por extenso nome serviço ou tecnologia Azure. 
-   Não permitem rm ou processador como acrónimos em qualquer parte do nome de um ficheiro
- Outros abreviaturas de norma da indústria são aceitáveis conforme necessário em nomes de ficheiro. 

##<a name="pattern"></a>Padrão

Eis o padrão de geral:

 **Service-Platform-Language-Content-Product-Version.md**

Utilize as partes do padrão que se aplicam e reveja a lista de artigos no repositório para obter uma ideia de nomes existentes. Nomes que não pode começar com uma plataforma Dev Center ou um nome de serviço é provavelmente suspeitos e com lapso através de.

##<a name="standard-examples"></a>Exemplos de padrão

Aqui estão alguns exemplos de nomes válidos que siga o padrão. :

- nuvem-serviços-dotnet-contínua-delivery.md
- Mobile-serviços-ios-get-started.md
- account.md documentdb gerir
- Mobile-Services-DotNet-backend-Get-Started-Settings-Sync.md
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.md
- virtual-Machines-Install-Windows-Server-2008r2.md
- cache-aspnet--Estado-fornecedor de sessão
- Azure-SDK-DotNet-Release-Notes-2-8
- storsimple-Disaster-Recovery-using-Azure-site-Recovery

##<a name="marketplace-content"></a>Conteúdo Marketplace

Para distinguir conteúdo foca-se em contribuições de parceiro para o Azure marketplace, inicie os nomes de ficheiro com "marketplace". Este conteúdo não deve ser demasiado comuns, como a maior parte dos conteúdos para parceiros devem ser criado na sites do web dos parceiros.

- Marketplace-mongodb-virtual-Machines-Install-Windows-Server-2008r2.md

##<a name="file-name-approval"></a>Aprovação de nome de ficheiro

É a tarefa de nosso grupo de revisores de pedido de solicitação para rever os nomes de ficheiro quando um novo ficheiro é submetido para o repositório pela primeira vez. Os revisores de pedido de solicitação devem reveja o nome do ficheiro e fornecer comentários através da sequência de comentário do pedido de solicitação se forem necessárias alterações. O nome do ficheiro tem de ser corrigidos antes de é aceite o pedido de solicitação. Os contribuintes podem facilmente emissão a atualização ao pedido de solicitação pendentes.

##<a name="folder-names-in-the-repo"></a>Nomes das pastas na repo

Pastas devem ser criadas apenas para serviços e o nome de ficheiro deve corresponder a Libra massa de serviço. Utilize apenas letras e hífenes e utilize todas as letras minúsculas. Obter aprovação do administrador do repositório antes de criar uma nova pasta que não seja para um serviço de lançamento.

##<a name="changing-case-in-file-names"></a>Alterar as maiúsculas/minúsculas em nomes de ficheiro

Sistemas operativos do Windows estão maiúsculas e minúsculas, por isso se precisar de alterar um nome de ficheiro para corrigir invólucro, é melhor efetuar uma alteração substancial, a menos que seja possível para que as alterações num Linux ou Mac. Por exemplo:

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Utilize o seguinte comando para mudar o nome de um ficheiro:
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Ligações de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)


<!--Anchors-->
[Regras]: #rules
[Padrão]: #pattern
[Exemplos de padrão]: #standard-examples
[Conteúdo Marketplace]: #marketplace-content
[Aprovação de nome de ficheiro]: #file-name-approval
