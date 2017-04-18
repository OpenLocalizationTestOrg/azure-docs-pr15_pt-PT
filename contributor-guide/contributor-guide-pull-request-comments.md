# <a name="pull-request-comment-automation"></a>Separar pedido comentário automatização

Utilizamos automatização de comentário em comentários de pedido de solicitação para permitir que os contribuintes e os autores para atribuir etiquetas orientar o pedido de solicitação rever o processo.

| Comentário | O que faz | Disponibilidade|
| -------- |-------------|-------------|
|#Desativar início de sessão | Quando o autor de um artigo tipos do comentário **#sign-off** na sequência do comentário, é atribuída a etiqueta **pronto para impressão em série** . | Públicos e privados|
|#Desativar início de sessão | Se um contribuinte que não seja o autor listados tenta iniciar sessão um pedido de solicitação público utilizando o comentário **#sign-off** , uma mensagem é escrita para o pedido de solicitação que indica que a etiqueta pode ser atribuída apenas por autor. | Público |
|#Desativar suspensão | Se escrever **#hold-off** num comentário de pedido de solicitação, remove a etiqueta **pronto para impressão em série** - caso mude de ideias ou cometer um erro. O repo privado, atribui a etiqueta de **intercalação de não fazer** . | Públicos e privados |
| #Fechar, por favor | Autores podem escrever o comentário **#please-fecho** na sequência do comentário de um pedido de solicitação para fechá-lo, se decidir não tem alterações intercaladas. | Público |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Resolução de problemas de início de sessão compromissos no repo público

O sinal de repo público desativar automatização é permite que apenas o autor iniciar sessão. Pode ser necessárias algumas processamento manual exceção:

- **Autores de artigo**: para utilizar a automatização de comentário do repositório público, a conta GitHub real deve corresponder EXATAMENTE na conta de GitHub listada nos metadados do artigo. Questões o uso de maiúsculas/minúsculas da sua conta. Se estiverem bloqueados de iniciar devido a este problema, envie correio para o alias azdocprs.

- **Outros empregados**: se se for um funcionário quem está a iniciar sessão em nome do autor e está bloqueadas pela automatização, contacte azdocprs com a ligação do pedido por solicitação. Indica a quem está – PMs equipa de produto do mesmo, colegas a equipa de escrita e escrever os gestores de equipa são considerados fidedignas origens.



## <a name="related"></a>Relacionados com

- [Separar etiqueta do pedido e as melhores práticas para colaboradores da Microsoft](contributor-guide-pull-request-etiquette.md)

- [Reveja os critérios de qualidade para pedido de solicitação](contributor-guide-pr-criteria.md)
