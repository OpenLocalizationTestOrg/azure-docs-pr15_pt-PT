<properties
   pageTitle="Atualização da aplicação: serialização dados | Microsoft Azure"
   description="Melhores práticas para serialização de dados e como afeta-ficha técnica em actualizações de aplicação."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>


# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como serialização dados afeta uma atualização de aplicação

Uma [utilização gradual aplicação](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio para a atualização ao mesmo tempo. Durante este processo, alguns domínios atualização será na versão mais recente da aplicação e alguns domínios atualização irão estar ligado a versão mais antiga da sua aplicação. Durante a implementação, a nova versão da sua aplicação tem de conseguir ler a versão antiga dos seus dados e a versão antiga da sua aplicação tem de conseguir ler a nova versão dos seus dados. Se o formato de dados não é compatível com de avançar e retroceder, a atualização pode falhar ou pior, dados podem ser perdidos ou danificados. Este artigo explica o que constituem o formato de dados e oferece as melhores práticas para assegurar que os dados estiverem avançar e retroceder compatíveis.


## <a name="what-makes-up-your-data-format"></a>O que faz com que o seu formato de dados?

No Azure Service ferro, vêm os dados que são mantidos e replicados suas Turmas c#. Para aplicações que utilizam [Fiável coleções de sites](service-fabric-reliable-services-reliable-collections.md), que é os objetos nas dicionários fiáveis e filas. Para aplicações que utilizam [Intervenientes fiável](service-fabric-reliable-actors-introduction.md), que é o estado de segurança para o ator. Estas classes c# têm de ser serializáveis para ser persistentes e replicadas. Por conseguinte, o formato de dados é definido pelos campos e propriedades que estão a serializado, bem como a forma como estes são serializados. Por exemplo, numa `IReliableDictionary<int, MyClass>` os dados são uma série `int` e uma série `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Código de alterações que resultam numa alteração de formato de dados

Uma vez que o formato de dados é determinado pela classes c#, as alterações às classes poderão causar uma alteração de formato de dados. Cuidados devem ser tomados para se certificar de que uma utilização gradual pode processar a alteração de formato de dados. Exemplos que podem fazer com que as alterações de formato de dados:

- Adicionar ou remover campos ou propriedades
- Mudar o nome de campos ou propriedades
- Alterar os tipos de campos ou propriedades
- Alterar o nome de classe ou o espaço de nomes

### <a name="data-contract-as-the-default-serializer"></a>Contrato de dados, tal como o serializador predefinido

O serializador é geralmente responsável por ler os dados e anular a serialização para a versão atual, mesmo se os dados estiverem numa versão *mais recente* ou mais antiga. O serializador predefinido é o [contrato de dados de serializador](https://msdn.microsoft.com/library/ms733127.aspx), que tem as regras de controlo de versões bem definidos. Coleções fiáveis permitem serializador sejam substituídos, mas intervenientes fiável atualmente não o fizer. O serializador de dados, é reproduzido um papel importante na ativação atualiza os ficha técnica em. O serializador contrato de dados é o serializador que recomendamos para as aplicações de serviço ferro.


## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma utilização gradual

Durante uma actualização gradual, existem dois cenários principais, onde o serializador poderá encontrar uma versão *mais recente* ou mais antiga dos seus dados:

1. Depois de um nó é atualizado e inicia a cópia de segurança, o novo serializador irá carregar os dados que foram persistentes no disco pela versão antiga.
2. Durante a atualização gradual, cluster irá conter uma mistura das versões novas e antigas do seu código. Uma vez que réplicas podem ser colocadas em domínios atualização diferentes e réplicas enviem dados entre si, poderá ser encontrada a versão nova e/ou antiga dos seus dados pela versão nova e/ou antiga do seu serializador.

> [AZURE.NOTE] O "nova versão" e "versão antiga" aqui referir-se para a versão do seu código que está a ser executado. O serializador"novo" refere-se para o código de serializador que está a executar a nova versão da sua aplicação. Os "novos dados" refere-se para a classe de c# série a partir da nova versão da sua aplicação.

Duas versões do formato de código e dados têm de ser avançar e retroceder compatíveis. Se não são compatíveis, a atualização de ficha técnica em poderá falhar ou dados poderão ser perdidos. A atualização de ficha técnica em poderá falhar porque o código ou serializador pode gerar a exceções ou uma falha quando encontra a outra versão. Dados poderão ser perdidos se, por exemplo, foi adicionada uma nova propriedade mas o antigo serializador rejeita-lo durante desserialização.

Contrato de dados é a solução recomendada para assegurar que os seus dados são compatíveis. Tiver regras de controlo de versões bem definidos para adicionar, remover e alterar os campos. Também não tem suporte para trabalhar com campos desconhecidos, ao ligar para o processo de serialização e desserialização e trabalhar com herança de classe. Para mais informações, consulte o artigo [Utilizar o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).


## <a name="next-steps"></a>Próximos passos

[Uprading sua aplicação de utilizar o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização de aplicação utilizando o Visual Studio.

[Uprading sua aplicação de utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização de aplicação através do PowerShell.

Controlar como a aplicação actualiza utilizando [Parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a aplicação ao referir [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em actualizações de aplicação ao referir os passos em [Actualizações de resolução de problemas de aplicação ](service-fabric-application-upgrade-troubleshooting.md).
