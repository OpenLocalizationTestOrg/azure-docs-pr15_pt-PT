Recurso|Limite predefinido|Limite máximo
---|---|---
Azure contas de serviços de multimédia (MGA) numa única subscrição||25
Recursos por conta AMS||1.000.000<sup>1</sup>
Tarefas interligadas por tarefa||30
Recursos por tarefa||50
Recursos por tarefa||100
Tarefas por conta AMS ||50.000<sup>2</sup>
Locator exclusivo associado a um recurso ao mesmo tempo||5<sup>4</sup>
Canais direto por conta AMS </p></td>|5</p></td>|N /<sup>1</sup>
Programas no estado parado por canal </p></td>|50</p></td>|N /<sup>1</sup>
Programas na executar o estado por canal </p></td>|3</p></td>|3
Transmissão pontos finais a executar o estado por conta AMS</p></td>|2</p></td>|N /<sup>1</sup>
Unidades de transmissão por transmissão ponto final </p></td>|10 </p></td>|N /<sup>1</sup>
Unidades de codificação por conta AMS </p></td>|25</p></td>|N /<sup>1</sup>
Contas de armazenamento | |1.000<sup>5</sup>
Políticas || 1.000.000<sup>6</sup>

<sup>1</sup> pode pedir para atualizar os limites para esta quota ao abrir um bilhetes de suporte. Não crie mais contas AMS para aumentar limites, em vez disso, submeter um bilhetes de suporte.

<sup>2</sup> este número inclui tarefas em fila de espera, concluídas, ativas e canceladas. Não inclui tarefas eliminadas. Pode eliminar as tarefas antigas utilizando **IJob.Delete** ou o pedido de HTTP **Eliminar** .

<sup>3</sup> quando alterar um pedido de entidades de tarefa da lista, um máximo de 1.000, será devolvido por pedido. Se precisar de controlar todas as tarefas submetidas, pode utilizar início/ignorar, tal como descrito nas [Opções de consulta do sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Locator não foram concebido para gerir o controlo de acesso por utilizador. Para dar direitos de acesso diferentes para os utilizadores individuais, utilize as soluções de gestão de direitos digitais (DRM).

<sup>5</sup> as contas de armazenamento tem de ser a partir da mesma subscrição do Azure.

<sup>6</sup> existe um limite de 1.000.000 políticas para diferentes AMS as políticas (por exemplo, para a política de Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o ID de política mesmo se estiver a utilizar sempre os mesmo dias / permissões de acesso / etc.
