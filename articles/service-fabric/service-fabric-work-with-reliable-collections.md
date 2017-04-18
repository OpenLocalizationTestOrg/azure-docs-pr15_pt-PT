<properties
    pageTitle="Trabalhar com conjuntos fiáveis | Microsoft Azure"
    description="Obter informações sobre as práticas recomendadas para trabalhar com fiável coleções de sites."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Trabalhar com conjuntos fiáveis

Serviço ferro oferece um modelo de programação com estado disponível para programadores do .NET através do fiável coleções de sites. Especificamente, serviço ferro fornece fiável dicionário e classes fiável fila de espera. Quando utiliza estas classes, estado da sua é criado a partições (para escalabilidade), replicado (para disponibilidade) e efectuado dentro de uma partição (para a semântica de ácido). Vamos observar uma utilização típica de um objeto de dicionário fiável e ver que seu realmente efetuar.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Todas as operações de objetos de dicionário fiável (exceto ClearAsync que não está irreversível), necessitam de um objeto de ITransaction. Este objeto tem associada qualquer e todas as alterações que está a tentar efetuar a qualquer dicionário fiável e/ou fiável fila objetos dentro de uma única partição. Adquire um ITransaction objeto ao contactar o suporte a partições do método de CreateTransaction do StateManager.
 
No código, o objeto ITransaction lhe é transmitido método de AddAsync um dicionário fiável. Internamente, os métodos de dicionário que aceita uma chave de tirar um cadeado Leitor/gravador associado à chave. Se o método modifica valor a tecla, o método leva-o até um bloqueio de escrita na chave e se só lê o método de valor a tecla, em seguida, um bloqueio de leitura é dar a chave. Uma vez que AddAsync modifica valor a chave para o novo passou no valor, o bloqueio de escrita a tecla é disponibilizado. Por isso, se tentarem threads 2 (ou mais) para somar valores com a mesma chave ao mesmo tempo, um tópico irá adquirir o bloqueio de escrita e os outros threads irão bloquear. Por predefinição, o bloco de métodos para até 4 segundos adquirir o bloqueio; Após 4 segundos, os métodos de gerar um TimeoutException. Overloads método existem permitindo-lhe transmitir um valor de tempo limite explícitas se preferir.
 
Normalmente, tem de escrita seu código resposta a um TimeoutException por captura-lo e repetir a operação inteira (conforme mostrado no código de acima). No meu código simple, basta posso estou a chamar Task.Delay prisma 100 milissegundos cada vez. No entanto, na realidade, talvez seja melhor desativar utilizar qualquer tipo de atraso de back-off exponencial como alternativa.
 
Depois de adquirir o bloqueio, AddAsync adiciona as referências de objeto chave e valor para um dicionário temporário interno associado ao objecto ITransaction. Isto é feito para fornecer-lhe a semântica de leitura-your-proprietário-escritas. Isto é, após ligar AddAsync, uma chamada posterior para TryGetValueAsync (utilizando o objeto ITransaction mesmo) devolverá o valor, mesmo se ainda não consolidado a transação. Em seguida, AddAsync serializes a sua chave e valor matrizes byte os objetos e acrescenta estas matrizes byte para um ficheiro de registo no nó local. Por fim, AddAsync envia matrizes byte para todas as réplicas secundárias para que tenham as mesmas informações de tecla/valor. Apesar das informações de tecla/valor foi escritas para um ficheiro de registo, as informações não são consideradas parte do dicionário até a transação que estão associadas foi consolidada. 

No código, a chamada para CommitAsync consolida todas as operações a transação. Especificamente, acrescenta consolidar informações para o ficheiro de registo no nó local e envia também o registo de consolidação para todas as réplicas secundárias. Depois de um quórum (maioria) das réplicas respondeu, todas as alterações de dados são consideradas permanentes e quaisquer bloqueios associados teclas que foram manipulação através do objeto ITransaction são lançados para que as outras operações/threads pode manipular as mesmas teclas e os respetivos valores.

Se não for chamado CommitAsync (normalmente devido a uma exceção a ser iniciada), o objeto ITransaction é eliminado. Quando a eliminação de um objeto de ITransaction repetição, serviço ferro acrescenta informações de interrupção ao ficheiro de registo do nó local e nada tem de ser enviados para qualquer uma das réplicas secundárias. E, em seguida, são lançados quaisquer bloqueios associados teclas que foram manipulação através da transação.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Falhas comuns e como evitá-los
Agora que já sabe como as coleções de ficheiros fiáveis internamente funcionam, vamos ver algumas incorrectas comuns dos mesmos. Ver o código abaixo:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Quando trabalha com um dicionário .NET normal, pode adicionar um valor/chave ao dicionário e, em seguida, altere o valor de uma propriedade (tal como LastLogin). No entanto, este código não funcionará corretamente com um dicionário fiável. Lembre-se a partir de um debate anterior, a chamada para AddAsync serializes a objetos de valor/chave à matrizes byte e, em seguida, guarda as matrizes para um ficheiro local e também envia-las para as réplicas secundárias. Se alterar uma propriedade mais tarde, este procedimento altera o valor da propriedade apenas; na memória -não causam impacto na ficheiro local ou os dados enviados para as réplicas. Se o processo de falha, o que é na memória é devolvido como ausente. Quando inicia um novo processo ou outra réplica torna-se principal, o valor da propriedade antigo é o que está disponível. 

Posso não é possível limite suficiente como é fácil tornar o tipo de erro apresentado acima. E, irá apenas Saiba mais sobre o erro se/quando que acede o processo para baixo. A forma correta para escrever o código é simplesmente inverter as duas linhas:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Eis outra exemplo que mostra um erro comum:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Novamente, com dicionários .NET regulares, o código acima funciona e é um padrão comuns: o programador utiliza uma chave para procurar um valor. Se o valor não existir, o programador altera valor uma propriedade. No entanto, com colecções fiáveis, este código exibe o mesmo problema, tal como já abordado: __não tem de modificar um objeto depois de ter dado-o para uma coleção de fiável.__
 
É a forma correta para atualizar um valor numa coleção de fiável obter uma referência ao valor existente e ter em consideração o objeto referido por esta referência imutáveis. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, pode modificar o estado deste novo objeto e escrever o novo objeto para a coleção de modo a que-obtém serializado para matrizes byte, acrescentado ao ficheiro local e enviadas para as réplicas. Depois de fazer as alterações efectuadas, os objetos na memória, o ficheiro local e todas as réplicas tenham o mesmo Estado exato. Todos os é boa ideia!

O código abaixo mostra a forma correta para atualizar um valor numa coleção de fiável:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para impedir que o erro de programador

Idealmente, gostaríamos compilador a relatar erros quando acidentalmente produtos agrícolas código mutates estado de um objeto que é suposto existirem considere imutáveis. No entanto, o compilador c# não tiver a capacidade de faça o seguinte. Sim, para evitar potenciais erros de programador, recomendamos vivamente que definir os tipos de utilizar com colecções fiáveis para serem tipos imutáveis. Especificamente, isto significa que for implementada tipos de valor core (tais como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e outro). E, claro, também pode utilizar cadeia. É melhor evitar propriedades da coleção de como serializar e anular a serialização-los pode frequentemente pode afetar o desempenho. No entanto, se pretender utilizar as propriedades de coleções de sites, recomendamos vivamente a utilização de. Biblioteca de colecções imutáveis do líquido (System.Collections.Immutable). Esta biblioteca está disponível para transferência a partir do http://nuget.org. Recomendamos também fecho suas Turmas e efetuar campos só de leitura sempre que possível.

O tipo de informações do utilizador abaixo demonstra como define um tipo de imutáveis tirar partido das recomendações acima referidos.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

O tipo de ItemId também é um tipo de imutáveis, conforme mostrado aqui:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Controlo de versões do esquema (atualiza os)

Internamente, coleções fiável serializar os objetos utilizar. DataContractSerializer do líquido. Os objetos série são permanentes para disco local a réplica principal e também são transmitidos para as réplicas secundárias. Tal como o seu serviço de casos, é provável que pretende alterar o tipo de dados (esquema) requer o seu serviço. Tem de abordar o controlo de versões dos seus dados com cuidado. Em primeiro lugar e mais, terá sempre poderá serialização dados antigos. Especificamente, isto significa que o código de desserialização tem de ser infinitamente compatível: 333 de versão do código serviço tem de conseguir trabalhar com dados colocado numa coleção de fiável pela versão 1 do seu código do serviço 5 anos atrás.

Além disso, o código do serviço é atualizado um domínio de atualização cada vez. Por isso, durante uma atualização, tem duas versões diferentes do seu código de serviço a ser executado em simultâneo. Tem evitar ter a nova versão do seu código do serviço como versões anteriores do seu código do serviço poderão não conseguir processar o novo esquema, utilize o novo esquema. Sempre que possível, deve conceber cada versão do seu serviço de serem compatíveis reencaminhar pela 1 versão. Especificamente, isto significa que deverá conseguir simplesmente ignorar qualquer elementos de esquema não explicitamente processar V1 do código serviço. No entanto, tem de ser capaz de guardar quaisquer dados-explicitamente não sabe sobre e simplesmente escrita-o novamente saída ao atualizar uma chave de dicionário ou valor. 

>[AZURE.WARNING] Enquanto pode modificar o esquema de uma chave, certifique-se de que o código de hash da sua chave e algoritmos é igual a estão estáveis. Se alterar a forma como quer destes algoritmos operam, não conseguir procurar a chave de dicionário fiável alguma vez novamente.
  
Em alternativa, pode executar o que é normalmente referido como uma atualização de fase 2. Com uma atualização de fase 2, atualizar o serviço a partir de V1 para V2: V2 contém o código que sabe como lidar com a nova alteração de esquema, mas não executa este código. Quando o código de V2 lê V1 dados, funciona no mesmo e escreve V1 dados. Em seguida, após a atualização está concluída através de atualização de todos os domínios, pode ficou sinal para as instâncias de V2 em execução que a atualização está concluída. (Uma forma de sinal isto é para implementar o uma atualização configuração; este é o que faz isto uma atualização de fase 2). Agora, as instâncias de V2 podem ler os dados de V1, convertê-la para dados V2, processá-lo e escrevê-la como V2 dados. Quando outras instâncias ler V2 dados, não precisam de convertê-la, apenas processá-lo e escrever dados V2.

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre a criação de contratos de dados compatíveis reencaminhar, consulte o artigo [Antecipada contratos de dados](https://msdn.microsoft.com/library/ms731083.aspx).

Para aprender as melhores práticas na contratos de dados de controlo de versões, consulte o [Controlo de versões de contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx). 

Para saber como implementar o contratos de tolerância a falhas de dados de versão, consulte o artigo [Tolerância a falhas versão serialização as chamadas de retorno](https://msdn.microsoft.com/library/ms733734.aspx). 

Para saber como fornecer uma estrutura de dados que pode interagir em múltiplas versões, consulte o artigo [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
