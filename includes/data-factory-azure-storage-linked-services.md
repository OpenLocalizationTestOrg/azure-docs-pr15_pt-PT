## <a name="azure-storage-linked-service"></a>Armazenamento Azure ligadas serviço

O **armazenamento do Windows Azure ligadas serviço** permite-lhe associar uma conta de armazenamento Azure a uma fábrica Azure dados utilizando a **chave da conta**. Isto fornece a fábrica de dados com o global access para o armazenamento do Windows Azure. A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de armazenamento do Windows Azure ligadas.

| Propriedade | Descrição | Obrigatório |
| :-------- | :----------- | :-------- |
| tipo | A propriedade de tipo tem de estar definida: **AzureStorage** | Sim |
| connectionString | Especificar informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. | Sim |

Consulte o seguinte artigo para obter os passos para ver/cópia a chave de conta para um armazenamento do Windows Azure: [vista, cópia e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Exemplo:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Armazenamento Azure SA ligadas serviço  
Uma assinatura de acesso partilhado (SA) oferece um acesso delegado para recursos na sua conta de armazenamento. Isto significa que o utilizador pode conceder que um cliente limitadas permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto específico de permissões, sem ter de partilhar o seu teclas de acesso de conta. As associações de segurança são um URI que englobe nos respetivos parâmetros de consulta autenticados todas as informações necessárias para o acesso a um recurso de armazenamento. Para aceder a recursos de armazenamento com as associações de segurança, o cliente precisa apenas transmitir nas associações de segurança para o método ou construtor adequado. Para obter informações detalhadas sobre SA, consulte o artigo [partilhado assinaturas do Access: Noções sobre o modelo de SA](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
O serviço do Azure armazenamento SA ligadas permite-lhe associar uma conta de armazenamento do Azure a uma fábrica Azure dados utilizando uma assinatura de acesso de partilhados (SA). Este procedimento fornece a fábrica de dados com acesso restrito/tempo-vínculo para recursos de todos os/específicos (blob/container) no armazenamento. A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço do Azure armazenamento SA ligadas. 

| Propriedade | Descrição | Obrigatório |
| :-------- | :----------- | :-------- |
| tipo | A propriedade de tipo tem de estar definida: **AzureStorageSas**  | Sim |
| sasUri | Especifique partilhado URI de assinatura do Access para os recursos de armazenamento do Windows Azure como blob, contentor ou tabela. Veja as notas abaixo para obter detalhes. | Sim | 


**Exemplo:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Ao criar um **URI de associações de segurança**, considerar o seguinte procedimento:  

- Azure dados fábrica suporta apenas **SA de serviço**, não conta SA. Consulte o artigo [Tipos de partilhado acesso as assinaturas](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre estes dois tipos.
- Leitura/escrita adequado **permissões** tem de ser definidas num objetos com base em como o serviço ligado (ler, escrever, leitura/escrita) será utilizado na sua fábrica de dados.
- **Tempo de expiração** tem de ser definida corretamente. Certifique-se de que o acesso a objetos de armazenamento do Windows Azure não expira dentro do período do pipeline de ativo.
- URI deve ser criada ao nível de tabela com base na necessidade de ou contentor/blob à direita. Um Uri SA para um BLOBs do Azure permite que o serviço de dados fábrica aceder a esse blob específico. Um Uri SA a um contentor BLOBs do Azure permite que o serviço de dados fábrica iteração blobs desse contentor. Se precisar de fornecer acesso mais/inferior objetos mais tarde ou atualizar o URI SA, não se esqueça de atualizar o serviço ligado com o novo URI.   
