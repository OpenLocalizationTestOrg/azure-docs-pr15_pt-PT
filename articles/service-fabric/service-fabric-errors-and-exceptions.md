<properties
   pageTitle="Comuns FabricClient exceções iniciadas | Microsoft Azure"
   description="Descreve as exceções e erros que podem ser iniciados pela API FabricClient enquanto executa a aplicação e operações de gestão de cluster comuns."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros ao trabalhar com as APIs FabricClient comuns
API do [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permitem aos administradores de cluster e de aplicações desempenhar tarefas administrativas numa aplicação de serviço ferro, serviço ou cluster. Por exemplo, a implementação da aplicação, atualização e remoção, verificar o estado de funcionamento um cluster, testes ou um serviço. Os programadores de aplicações e administradores de cluster podem utilizar as APIs FabricClient para desenvolver ferramentas para gerir o cluster de hardware de serviço e as aplicações.

Existem vários tipos de operações que podem ser executadas utilizando FabricClient diferentes.  Cada método pode gerar exceções para erros devido a entrada incorreto, runtime erros ou problemas de infraestrutura breves.  Consulte a documentação de referência de API para localizar as exceções são iniciadas por um método específico. Existem algumas exceções, no entanto, que podem ser iniciadas por muitas APIs [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diferentes. A tabela seguinte apresenta as exceções que são as APIs FabricClient comuns.

|Exceção| Iniciadas quando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|O objeto de [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) é num Estado de fechada. Dispor do objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está a utilizar e criar uma instância de um novo objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|A operação excedida. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) é devolvido quando a operação de demora mais do que MaxOperationTimeout para concluir.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|A verificação de acesso a operação de falha. É devolvido E_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Ocorreu um erro de runtime ao executar a operação. Qualquer um dos métodos FabricClient potencialmente podem gerar [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), a propriedade de [código de erro](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica a causa exata da excepção. Códigos de erro são definidos na enumeração [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|A operação falhou devido a uma condição de erro breves de qualquer tipo. Por exemplo, uma operação poderá falhar porque o quórum réplicas temporariamente não está acessível. Exceções breves correspondem à falhadas operações que podem ser tentada novamente.|

Alguns erros [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) comuns que podem ser devolvidos num [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Erro| Condição|
|---------|:-----------|
|CommunicationError|Um erro de comunicação causado a operação falhar, repita a operação.|
|InvalidCredentialType|O tipo de credencial é inválido.|
|InvalidX509FindType|O X509FindType é inválido.|
|InvalidX509StoreLocation|O X509 localização do arquivo é inválida.|
|InvalidX509StoreName|O X509 revendedores parceiro são inválido.|
|InvalidX509Thumbprint|O X509 cadeia do certificado impressão digital é inválida.|
|InvalidProtectionLevel|Nível de proteção de é inválido.|
|InvalidX509Store|X509 arquivo de certificados não pode ser aberto.|
|InvalidSubjectName|O nome de assunto é inválido.|
|InvalidAllowedCommonNameList|O formato da cadeia de lista comuns do nome é inválido. Deverá ser uma lista separados por vírgulas.|
