<properties
   pageTitle="Comparação de arquivo do Azure dados Lake com Azure armazenamento Blob | Microsoft Azure"
   description="Comparação de arquivo do Azure dados Lake com Azure armazenamento Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparar o arquivo de Lake de dados do Azure e armazenamento de Blobs do Azure

A tabela neste artigo resume as diferenças entre o arquivo de Lake de dados do Azure e armazenamento de Blobs do Azure ao longo de alguns aspetos chaves grande do processamento de dados. Armazenamento de Blobs do Azure é um objetivo geral, o arquivo de objeto dimensionáveis que foi concebido para uma grande variedade de cenários de armazenamento. Azure dados Lake arquivo é um repositório de hyper escala que está optimizado para dados grande analytics das cargas de trabalho.

|    | Arquivo de Lake de dados do Azure  | Armazenamento de Blobs do Azure  |
|----|-----------------------|--------------------|
| Objetivo  | Armazenamento otimizado para dados grande analytics das cargas de trabalho                                                                          | Objeto de objetivo gerais armazenar para uma grande variedade de cenários de armazenamento                                                                                |
| Casos de utilização                                   | Lote, interativo, transmissão de dados de aprendizagem automática e de análise tal como ficheiros de registo, IoT dados, clique em fluxos de grandes conjuntos de dados | Qualquer tipo de dados de texto ou binário, como a aplicação de trás final, dados de cópia de segurança, armazenamento de multimédia para os dados de objetivo de transmissão e gerais |
| Conceitos chave                                | Conta de arquivo de Lake dados contém pastas, que por sua vez contém dados armazenados como ficheiros | Conta de armazenamento tem contentores, que por sua vez possui dados sob a forma de blobs |
| Estrutura | Sistema de ficheiros hierárquico                                                                                                    | Arquivo de objecto com espaço de nomes simples  |
| API   | API REST por HTTPS | API REST através de HTTP/HTTPS                                                                                                                            |
| API do lado do servidor                             | [Compatível com o WebHDFS REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Armazenamento de Blobs do Azure REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Ficheiro Hadoop cliente do sistema                   | Sim                                                                                                                         | Sim                                                                                                                                                 |
| Operações de dados - autenticação            | Com base em [identidades do Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | Com base no segredos partilhados - [Teclas de acesso de conta](../storage/storage-create-storage-account.md#manage-your-storage-account) e [Chaves de assinatura partilhadas do Access](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Operações de dados - protocolo de autenticação     | OAuth 2.0. Chamadas tem de conter um JWT válido (JSON Web Token) emitido por Azure Active Directory                     | Código de autenticação de mensagem com base em hash (HMAC). Chamadas tem de conter um hash codificado em Base64 SHA-256 sobre uma parte do pedido HTTP. |
| Operações de dados - autorização               | Listas de controlo de acesso POSIX (ACL).  Juntamente com base no Azure Active Directory identidades pode ser definido nível de ficheiros e pastas. | De autorização de nível de conta – utilizar [Teclas de acesso de conta](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Para conta, contentor ou blob autorização - utilizar [Partilhadas as teclas de acesso assinatura](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de dados - auditoria                    | Disponível. Consulte [aqui](data-lake-store-diagnostic-logs.md) para obter informações.                                                                                                                   | Disponível                                                                                                                                           |
| Dados de encriptação at rest                     | Transparente, do lado do servidor (brevemente)<ul><li>Com as teclas gerido de serviço</li><li>Com as teclas gerido de cliente no Azure KeyVault</li></ul>| <ul><li>Transparente, do lado do servidor</li> <ul><li>Com as teclas gerido de serviço</li><li>Com as teclas gerido de cliente no Azure KeyVault (brevemente)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão de (por exemplo, conta criar) | [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) (RBCA) fornecidas pelo Azure para gestão de contas                                                                       | [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) (RBCA) fornecidas pelo Azure para gestão de contas                                                                                               |
| Programador SDK                              | .NET, Java, Node.js                                                                                                         | .NET, Java, Python, Node.js, C++, Rubi                                                                                                              |
| Desempenho de carga de trabalho de análise              | Desempenho otimizado para cargas de trabalho de análise paralelo. Alto débito e IOPS.                                           | Não otimizados para cargas de trabalho de análise                                                                                                               |
| Limites de tamanho                                 | Sem limites de tamanhos de conta, tamanhos de ficheiro ou número de ficheiros                                                                   | Limites específicos documentado [aqui](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Geo redundância                              | Localmente-redundantes (várias cópias dos dados numa região Azure)                                                             | Localmente redundantes (LRS), redundantes globalmente (GRS), acesso de leitura globalmente redundantes (RT-GRS). Consulte [aqui](../storage/storage-redundancy.md) para obter mais informações |
| Estado do serviço                               | Pré-visualização do público                                                                                                              | Ficará disponível                                                                                                                                 |
| Disponibilidade regional  | Consulte o artigo [aqui](https://azure.microsoft.com/regions/#services)| Consulte o artigo [aqui](https://azure.microsoft.com/regions/#services) |
| Preço                                       |     Consulte o artigo [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)| Consulte o artigo [preços](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Próximos passos

- [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)
- [Introdução ao arquivo de dados de Lake](data-lake-store-get-started-portal.md)



