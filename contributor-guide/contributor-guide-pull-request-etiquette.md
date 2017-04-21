# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Separar etiqueta do pedido e as melhores práticas para os colaboradores da Microsoft a documentação do Azure

Para publicar alterações a documentação, submeter solicitação pedidos a partir do seu bifurcação. Todos os pedidos de solicitação tem de ser revisto antes de a ser intercalados. Leia este artigo para compreender como deve trabalhar com os revisores de pedido de solicitação e como pode criar pedidos de solicitação que são mais fácil e rápido para rever, de modo a fila de pedido de solicitação funciona melhor para todas as pessoas.

## <a name="working-with-pull-request-reviewers"></a>Trabalhar com os revisores de pedido de solicitação

Eis as noções básicas que precisa de saber sobre como trabalhar com os revisores de pedido de solicitação. 

- <b>Compreenda a função do revisor solicitação pedido. Revisor:</b>
  - Garante a qualidade básica do conteúdo
  - Impede regressões no repositório
  - Fornece os comentários antes de intercalar

  Solicitação pedido revisores estão numa função de governação conteúdo. A intenção principal não é simplesmente intercalar tudo o que é apresentado mais rapidamente possível. Espera comentários que necessitam de fazer atualizações, especialmente para artigos novos e fortemente revistos.

- <b>Planear com antecedência com o seu revisor de pedido de solicitação:</b>
  - Para pedidos de alta prioridade solicitação de
  - Solicitação pedidos para temporizado/datada de versões
  - Pedidos de solicitação que altere ou adicione grandes quantidades de ficheiros

- <b>SLA para solicitação solicitar a revisão</b>

  No repositório de privado, sempre que o seu pedido de solicitação introduz fila de pedido de solicitação com a etiqueta pronto para impressão em série, a equipa tentará rever o pedido de solicitação dentro de 12 horas de negócio (M-F, 8: 00 para 5 PM) e fornecer comentários ou intercalar não se for necessário nenhuma comentários. Este SLA aplica-se para a legislação sobre de revisão de PR, não a intercalar. PRs serão intercaladas quando satisfizer [os critérios para intercalação](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Tornar a fila de pedido de solicitação funcionam melhor para todos os participantes

Existem duas realidades básicas na fila de espera PR:

- Solicitação pedidos que são pequenos no âmbito e que contenham alterações muito semelhantes demoram menos tempo para rever. 
- Solicitação pedidos que se encontram grandes no âmbito ou que contenham mistos, diferentes tipos de alterações demorar mais tempo para rever.

Pode ajudar a tornar a fila de pedido de solicitação funcionam melhor ao seguir estas práticas recomendadas:

- Secundárias actualizações separadas para artigos existentes a partir de novos artigos ou gravações principais. Trabalhar nestas alterações no separado ramificações de trabalho. 

- Quando elimina artigos ou imagens, não misture as eliminações com novas adições conteúdas ou em atualizações. Processe o conteúdo alterações novo um ramo de trabalhar em separado.

- Para versões ou refactorização de conteúdo, planeie com antecedência com o seu revisor PR. Poderá precisar de ajuda para criar um ramo de lançamento ou para coordenar os tempos de impressão em série com os tempos de publicação para que o conteúdo é publicado na altura certa.

- Se estiver a tentar coordenar atualizações efetuadas na repo ACOM (ie, muda para navegação do lado esquerdo, fiquem páginas, redirecionamentos ou mapas de aprendizagem) com as alterações que estiver a fazer no repositório de conteúdo de azure-pr, terá de coordenar esse trabalho com antecedência com o seu revisor PR. Caso contrário, corre o risco está a ter muitas ligações quebradas.

## <a name="criteria-for-expedited-pull-requests"></a>Critérios para pedidos de solicitação emitidos

- Contacte o azdocprs para acelerar PRs apenas quando realmente necessário. Pode pedir expedited PR tratamento de zona de vermelho, privacidade, legal e problemas de segurança para o cliente verdadeiramente quebrada experiências; e para Escalamentos executivos. 
- Conteúdo para versões de funcionalidade não se qualifique para processamento emitido - conteúdo de lançamento de funcionalidade requer um planeamento prévia ou tem de ser tratada através de fila de espera prioridade padrão.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>Pressa? Submeter PRs que podem ser aceites automaticamente

Utilize as regras de automatização PRMerger para obter mais dos seus PRs no dia a dia intercaladas automaticamente.

PRMerger podem aceitar o PR automaticamente, se:
* -Afeta 10 ficheiros ou menos.
* Contém 15 consolidadas ou menos.
* Menos de 20% das alterações do texto.
* Seletores/switchers não são atualizadas.
* Não são eliminados ou ficheiros adicionados.
* Sem imagens são novas, alteradas ou eliminadas.

Se o seu pedido de solicitação não cumprir estes critérios, a etiqueta "Não é possível intercalar PRmerger" é atribuída automaticamente para que saiba, é necessário rever por um revisor PR humano.

### <a name="need-to-make-a-lot-of-little-changes"></a>Precisa de efetuar muitas alterações pequeno?

Levar a pilha das regras de automatização PRMerger acima e efetue o seguinte procedimento:
* Submeta artigos com simplificada alterações em conjunto num PR com os ficheiros de 10 ou menos.
* Crie um PR separada para artigos que alterar imagens ou seletores. Isto requer humano rever.
* Crie um PR separada para artigos novos ou eliminados. Isto requer humano rever.

## <a name="related"></a>Relacionados com

- [Reveja os critérios de qualidade para pedido de solicitação](contributor-guide-pr-criteria.md)

- [Separar pedido comentário automatização](contributor-guide-pull-request-comments.md)
