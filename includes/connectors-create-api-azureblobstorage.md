### <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)
- Uma [conta de armazenamento de Blobs do Azure](../articles/storage/storage-create-storage-account.md) incluindo o nome da conta de armazenamento e a sua chave de acesso. Esta informação está listada nas propriedades da conta de armazenamento no portal do Azure. Leia mais sobre o [Armazenamento do Windows Azure](../articles/storage/storage-introduction.md).

Antes de utilizar a sua conta de armazenamento de Blobs do Azure numa aplicação lógica, ligar à sua conta de armazenamento de Blobs do Azure. Pode fazê-lo facilmente dentro da sua aplicação de lógica no portal do Azure.  

Ligar à sua conta de armazenamento de Blobs do Azure através dos seguintes passos:  

1. Crie uma aplicação de lógica. No estruturador de lógica aplicações, adicione um accionador e, em seguida, adicione uma ação. Selecione **Mostrar Microsoft geridos APIs** na lista pendente e, em seguida, introduza "BLOBs" na caixa de pesquisa. Selecione uma das ações:  

    ![Passo de criação de ligação do Azure armazenamento de BLOBs](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Se ainda não tiver criado quaisquer ligações ao armazenamento Azure, lhe for pedido para os detalhes de ligação:   

    ![Passo de criação de ligação do Azure armazenamento de BLOBs](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Introduza os detalhes de conta de armazenamento. Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
|---|---|
| Nome da ligação * | Introduza um nome para a ligação. |
| Nome da conta de armazenamento Azure * | Introduza o nome de conta de armazenamento. O nome da conta de armazenamento é apresentado nas propriedades de armazenamento no portal do Azure. |
| Tecla de acesso de conta de armazenamento Azure * | Introduza a chave de conta de armazenamento. As teclas de acesso são apresentadas nas propriedades de armazenamento no portal do Azure. |

    Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder aos seus dados. 

4. Selecione **Criar**.

5. Repare que a ligação foi criada. Agora, continue com os outros passos na sua aplicação de lógica: 

    ![Passo de criação de ligação do Azure armazenamento de BLOBs](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
