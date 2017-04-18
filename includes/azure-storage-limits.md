Recurso|Limite predefinido
---|---
Número de contas de armazenamento por subscrição|200<sup>1</sup>
TB por conta de armazenamento|500 TB
Número máximo de BLOBs contentores, blobs, partilhas de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento|Apenas o limite é a capacidade de conta de armazenamento 500 TB
Tamanho máximo de um contentor de BLOBs única, tabela ou fila de espera|500 TB
Máximo de número de blocos num blob bloco ou acrescentar blob|50000
Máximo de tamanho de um bloco de um blob bloco ou acrescentar blob|4 MB
Máximo de tamanho de um blob bloco ou acrescentar blob|50000 x 4 MB (cerca de 195 GB) 
Tamanho máximo de um blob de página |1 TB
Tamanho máximo de uma entidade de tabela|1 MB
Número máximo de propriedades de uma entidade de tabela|252
Tamanho máximo de uma mensagem de uma fila|64 KB
Tamanho máximo de uma partilha de ficheiros|5 TB
Tamanho máximo de um ficheiro numa partilha de ficheiros|1 TB
Número máximo de ficheiros numa partilha de ficheiros|Apenas o limite é a capacidade total de 5 TB da partilha de ficheiros
Máximo 8 KB IOPS por partilhar|1000
Número máximo de ficheiros numa partilha de ficheiros|Apenas o limite é a capacidade total de 5 TB da partilha de ficheiros
Número máximo de BLOBs contentores, blobs, partilhas de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento|Apenas o limite é a capacidade de conta de armazenamento 500 TB
Número máximo de políticas de acesso armazenados por contentor, partilha de ficheiros, tabela ou fila de espera|5
Total pedir taxa (partindo do princípio de tamanho do objeto 1KB) por conta de armazenamento|Até 20.000 IOPS, entidades por segundo ou mensagens por segundo
Débito de destino para blob único|Até 60 MB por segundo, ou até 500 pedidos por segundo
Débito de destino para única fila (mensagens 1 KB)|Até 2000 mensagens por segundo
Débito de destino para partição de tabela única (1 KB entidades)|Até 2000 entidades por segundo
Débito de destino para partilha de ficheiro único|Até 60 MB por segundo
Máximo penetração<sup>2</sup> por conta de armazenamento (regiões-nos)|10 Gbps se GRS/ZRS<sup>3</sup> ativada, 20 Gbps para LRS
Máximo saída<sup>2</sup> por conta de armazenamento (regiões-nos)|20 Gbps se RT-GRS/GRS/ZRS<sup>3</sup> ativada, 30 Gbps para LRS
Máximo penetração<sup>2</sup> por conta de armazenamento (Europeia e regiões asiático)|5 Gbps se GRS/ZRS<sup>3</sup> ativada, 10 Gbps para LRS
Máximo saída<sup>2</sup> por conta de armazenamento (Europeia e regiões asiático)|10 Gbps se RT-GRS/GRS/ZRS<sup>3</sup> ativada, 15 Gbps para LRS

<sup>1</sup> Isto inclui Standard e Premium contas de armazenamento. Se necessitar de mais de 200 contas de armazenamento, certifique um pedido de através do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipa de armazenamento do Windows Azure vai revê o seu caso de negócio e pode aprovar até 250 contas de armazenamento. 

<sup>2</sup> *Penetração* refere-se a todos os dados (pedidos) a ser enviados para uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) a ser recebidos de uma conta de armazenamento.  

<sup>3</sup> Opções de replicação do Azure armazenamento incluem:

- **GRS RT**: armazenamento geo redundantes acesso de leitura. Se estiver ativada GRS RT, destinos de saída para a localização secundário são idênticos aos para a localização principal.
- **GRS**: armazenamento Geo redundante. 
- **ZRS**: armazenamento zona redundante. Disponível apenas para blobs bloco. 
- **LRS**: armazenamento localmente redundante. 

