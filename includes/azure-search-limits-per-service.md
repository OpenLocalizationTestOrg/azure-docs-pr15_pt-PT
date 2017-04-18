Armazenamento está restringido por espaço em disco ou por um limite de disco rígido o *número máximo* de índices ou documentos, que vier primeiro. 

Recurso|Gratuito|Básicas|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Sem <sup>1</sup> |Sim |Sim  |Sim |Sim |Sim
Armazenamento por partição|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
Partições por serviço|N/D|1|12|12|12|3
Tamanho da partição|N/D|2 GB|25 GB|100 GB|200 GB |200 GB
Réplicas|N/D|1|12|12|12|12
Índices máximos|3|5|50|200|200|1000 por partição ou 3000 por serviço
Máximo de documentos|10.000|1 milhão|15 milhões por partição ou 180 milhão de serviço |milhões de 60 por partição ou 720 milhão de serviço |120 milhões de por partição ou mil milhões de 1.4 por serviço|1 milhão de índice remissivo ou milhões de 200 por partição |
Consultas estimadas por segundo (QPS)|N/D|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|~ 60 por réplica|> 60 por réplica

<sup>1</sup> gratuitos e pré-visualização SKUs não são fornecidos com acordos de nível de serviço (SLA). SLA é impostas assim que um SKU torna-se ficará disponível.