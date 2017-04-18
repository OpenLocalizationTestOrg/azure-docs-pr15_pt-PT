<properties
   pageTitle="Migrar os seus dados para armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para migrar os seus dados para o armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Migrar os seus dados
Dados podem ser movidos de diferentes origens para o seu armazém de dados SQL com ferramentas de variedade.  Copiar ADF, SSIS e bcp podem ser utilizadas para atingir este objectivo. No entanto, como a quantidade de dados aumenta deve pensar sobre força para baixo o processo de migração de dados para os passos. Isso concede a oportunidade de otimizar cada passo para obter um desempenho e para uma rápida recuperação para se certificar de uma migração de dados suaves.

Este artigo aborda pela primeira vez os cenários de migração simples de cópia de ADF, SSIS e bcp. -Em seguida, analisar um pouco mais aprofundada como pode ser otimizada a migração.

## <a name="azure-data-factory-adf-copy"></a>Azure cópia de dados fábrica (ADF)
[Copiar ADF][] faz parte de [Fábrica do Azure dados][]. Pode utilizar ADF cópia para exportar os seus dados para ficheiros simples que reside no armazenamento local, a ficheiros simples remotos mantidas em armazenamento de Blobs do Azure ou diretamente no armazém de dados SQL.

Se os seus dados é iniciado nos ficheiros simples, em seguida, primeiro terá de transferi-lo para BLOBs do Azure armazenamento antes de iniciar uma carga-lo para armazém de dados SQL. Assim que os dados são transferidos para o armazenamento de Blobs do Azure pode optar por utilizar [ADF cópia][] novamente para os dados de emissão para armazém de dados SQL.

PolyBase também fornece uma opção de alto desempenho para carregar os dados. No entanto, significa que se utilizar duas ferramentas em vez de um. Se o precisa de obter o melhor desempenho, em seguida, utiliza PolyBase. Se quiser que uma experiência de ferramenta única (e os dados não são grandes) ADF é uma resposta.

> [AZURE.NOTE] PolyBase requer os ficheiros de dados para ser em UTF-8. Esta é predefinição da cópia de ADF codificação pelo que não existe nada para alterar. Este é apenas um lembrete para não alterar o comportamento predefinido da cópia de ADF.

HEAD sobre o seguinte artigo para algumas excelente [amostras ADF][].

## <a name="integration-services"></a>Serviços de integração ##
Integration Services (SSIS) é uma poderosa e flexível extrair transformar e carga (ETL) ferramenta que suporte fluxos de trabalho complexos, da transformação de dados e várias opções de carregamento de dados. Utilize SSIS para transferir dados simplesmente para Azure ou como parte de uma migração mais amplo.

> [AZURE.NOTE] SSIS pode exportar para UTF-8 sem que a marca da ordem de byte no ficheiro. Para configurar esta tem primeiro de utilizar o componente de coluna derivada para converter os dados de caráter no fluxo de dados para utilizar a página de códigos UTF-8 65001. Depois de terem sido convertidas as colunas, escreva os dados ao adaptador de destino de ficheiro simples, garantindo que 65001 também foi seleccionada como a página de código para o ficheiro.

SSIS liga ao armazém de dados SQL, tal como faria ligar-se para uma implementação do SQL Server. No entanto, as ligações terá de estar a utilizar o Gestor de ligações de ADO.NET. Também deverá tomar cuidado para configurar o "utilizar em volume inserir quando se encontra disponível" definição para maximizar o débito. Consulte o artigo [ADO.NET placa de destino][] para saber mais sobre esta propriedade

> [AZURE.NOTE] Ligar a armazém de dados do SQL Azure com OLEDB não é suportada.

Além disso, há sempre a possibilidade de um pacote a poderá falhar devido problemas limitação ou de rede. Pacotes de estrutura, de modo que podem ser retomadas no ponto de falha, sem a Refazer trabalhar que completada antes da falha.

Para obter mais informações consulte a [documentação SSIS][].

## <a name="bcp"></a>BCP
BCP é um utilitário da linha de comandos que foi concebido para o ficheiro simples dados importação e exportação. Algumas transformação pode ocorrer durante a exportação de dados. Para executar transformações simples utilizam uma consulta para selecionar e transformar os dados. Depois de exportados, os ficheiros simples, em seguida, é possível carregar diretamente para o destino a base de dados do armazém de dados do SQL.

> [AZURE.NOTE] Muitas vezes é uma boa ideia para incorporar as transformações utilizadas durante a exportação de dados numa vista no sistema de origem. Isto garante que é mantida a lógica e o processo for possa ser repetido.

Vantagens da bcp são:

- Simplificar. comandos de BCP estão simples criar e executar
- Processo de carregamento novamente arrancar. Uma vez exportado a carga pode ser executado qualquer número de vezes

Limitações da bcp são:

- BCP funciona com tabelas simples apenas os ficheiros. Não funciona com os ficheiros como xml ou JSON
- BCP não suporta a exportar para UTF-8. Isto pode impedir utilizando PolyBase bcp exportado dados
- Capacidades de transformação de dados estão limitadas a fase de exportar apenas e são simples de natureza
- BCP não foi adaptado para ser robusto ao carregar os dados através da internet. Qualquer instabilidade de rede pode causar um erro ao carregar.
- BCP baseia-se no esquema que está a ser presentes na base de dados de destino antes do carregamento

Para mais informações, consulte o artigo [utilizar bcp para carregar os dados armazém de dados SQL][].

## <a name="optimizing-data-migration"></a>Optimizar a migração de dados
Um processo de migração de dados SQLDW pode ser dividido eficazmente em três passos descontínuos:

1. Exportação dos dados de origem
2. Transferência de dados para Azure
3. Carregar para a base de dados SQLDW de destino

Cada passo pode ser otimizado individualmente para criar um processo de migração robusta, novamente arrancar e flexível que maximize desempenho em cada passo.

## <a name="optimizing-data-load"></a>Optimizar carregamento dos dados
Consultar estes pela ordem inversa para um pouco a incorporá; a maneira mais para carregar os dados é através de PolyBase. Otimizar para um processo de carga PolyBase irá colocar pré-requisitos os passos anteriores para que fique melhor compreender este processo decorra. São descritos abaixo:

1. Codificação de ficheiros de dados
2. Formato de ficheiros de dados
3. Localização dos ficheiros de dados

### <a name="encoding"></a>Codificação
PolyBase requer ficheiros de dados para ser codificado UTF-8. Isto significa que quando exporta dados-está em conformidade com este requisito. Se os seus dados contêm apenas básicas caracteres ASCII (não expandidos ASCII), em seguida, estes mapa diretamente para o padrão de UTF-8 e não tem de se preocupar demasiada a codificação. No entanto, se os seus dados contêm carateres especiais, tais como caracteres especiais, acentos ou símbolos ou os seus dados suporta idiomas que não sejam Latim, em seguida, terá que assegurar que os seus ficheiros de exportação corretamente são codificado UTF-8.

> [AZURE.NOTE] BCP não suporta a exportação de dados para UTF-8. Por conseguinte, a melhor opção é utilizar serviços de integração de ou ADF copiar para a exportação de dados. Vale destacam que a marca de encomenda de byte UTF-8 (BOM) não é necessária no ficheiro de dados.

Quaisquer ficheiros codificados utilizando UTF-16 terá de ser novamente escrita ***prévia*** a transferência de dados.

### <a name="format-of-data-files"></a>Formato de ficheiros de dados
PolyBase impõe terminador uma linha fixa de \n ou nova linha. Os ficheiros de dados tem de estar em conformidade com este padrão. Não existem não estão quaisquer restrições à terminadores de cadeia ou coluna.

Terá de definir cada coluna no ficheiro como parte da sua tabela externa no PolyBase. Certifique-se de que todas as colunas exportadas são necessárias e os tipos de conformidade com as normas exigidas.

Fórum volte a consultar a [migrar o seu esquema] artigo para obter informações detalhadas sobre tipos de dados suportados.

### <a name="location-of-data-files"></a>Localização dos ficheiros de dados
Armazém de dados SQL utiliza PolyBase para carregar os dados a partir do armazenamento de Blobs do Azure exclusivamente. Por conseguinte, os dados tem foram pela primeira vez transferidos para o armazenamento de Blobs.

## <a name="optimizing-data-transfer"></a>Optimizar a transferência de dados
Uma das partes mais lentas da migração de dados é a transferência dos dados para Azure. Não só pode ser de largura de banda de rede um problema, mas também fiabilidade da rede pode dificultar seriamente o progresso. Por predefinição migrar dados para o Azure é através da internet para razoável provavelmente as hipóteses de ocorrência de erros de transferência. No entanto, estes erros precisem de dados sejam enviadas novamente na totalidade ou em parte.

Felizmente tem várias opções para melhorar a velocidade e resistência deste processo:

### <a name="expressroute"></a>[ExpressRoute][]
Pretende considere utilizar [ExpressRoute][] para acelerar a transferência. [ExpressRoute][] fornece uma ligação privada estabelecida para Azure, de modo a ligação não aceda através da internet público. Por não significa este é um passo obrigatório. No entanto, irão melhorar débito quando a instalação push dados Azure a partir de no local ou facilidade de localização cocriação.

Os benefícios da utilização [ExpressRoute][] são:

1. Maior fiabilidade
2. Mais rápida velocidade da rede
3. Latência da rede inferior
4. uma maior segurança de rede

[ExpressRoute][] é vantajoso para um número de cenários; não apenas a migração.

Interessado? Para obter mais informações e preços fórum visite a [documentação de ExpressRoute][].

### <a name="azure-import-and-export-service"></a>Azure importação e exportação serviço
O serviço de exportar e importar Azure é um processo de transferência de dados concebido para grande (GB + +) para grandes (TB + Resp +) as transferências de dados para Azure. Envolva escrever os dados discos e envio-los para um centro de dados Azure. O conteúdo do disco, em seguida, será carregado na Azure armazenamento de Blobs em seu nome.

Uma vista de alto nível do processo de exportação de importação é da seguinte forma:

1. Configurar um contentor de armazenamento de Blobs do Azure para receber os dados
2. Exportar dados para o armazenamento local
2. Copie os dados para polegada 3,5 SATA II/III rígido unidades de disco utilizando a [Azure importar/exportar ferramenta]
3. Criar uma tarefa de importação com o Azure importar e exportar serviço fornecer os ficheiros de diário produzidos pela [Azure importar/exportar ferramenta]
4. Enviar o seu centro de dados Azure nomeados de discos
5. Os seus dados são transferidos para o contentor de armazenamento de Blobs do Azure
6. Carregue os dados no SQLDW utilizando PolyBase

### <a name="azcopy-utility"></a>[AZCopy][] utility
O utilitário [AZCopy][] é uma ótima ferramenta para obter os seus dados transferidos para o Azure armazenamento de Blobs. Concebido para pequenas (MB + Resp +) para muito grandes (GB + Resp +) as transferências de dados. [AZCopy] também foi concebido para fornecer boa e são débito quando transferir dados para o Azure, pelo que é uma excelente escolha para o passo de transferência de dados. Uma vez transferido, pode carregar os dados utilizando PolyBase para armazém de dados SQL. Também pode incorporar AZCopy na sua pacotes SSIS utilizando uma tarefa de "Executar o processo".

Para utilizar AZCopy primeiro terá de transferir e instalá-lo. Existe uma [versão de produção][] e uma [versão de pré-visualização][] disponíveis.

Para carregar um ficheiro a partir do seu sistema de ficheiros terá um comando, como o apresentado abaixo:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Pode ser um resumo de alto nível processo:

1. Configurar um contentor de Blobs do Azure armazenamento para receber os dados
2. Exportar dados para o armazenamento local
3. AZCopy os dados no contentor de armazenamento de Blobs do Azure
4. Carregue os dados no armazém de dados SQL utilizando PolyBase

Total documentação à disposição: [AZCopy][].

## <a name="optimizing-data-export"></a>Optimizar exportação de dados
Para além de garantir que a exportação está em conformidade com os requisitos de apresentadas por PolyBase também pode atingir otimizar a exportação de dados para melhorar o processo ainda mais.

> [AZURE.NOTE] Como PolyBase requer que os dados para estar no formato de UTF-8 que não é provável que irá utilizar bcp para executar a exportação de dados. BCP não suporta a saída de ficheiros de dados para UTF-8. SSIS ou ADF cópia são muito mais adequados para efetuar este tipo de exportação de dados.

### <a name="data-compression"></a>Compressão de dados
PolyBase pode ler os dados de gzip comprimido. Se for possível comprimir os seus dados para ficheiros de gzip irá minimizar a quantidade de dados a ser enviados através da rede.

### <a name="multiple-files"></a>Vários ficheiros
Não só força de tabelas grandes para vários ficheiros de ajuda a melhorar a velocidade de exportação, também ajuda a com re-startability de transferência e a capacidade de gestão global dos dados uma vez no armazenamento de Blobs do Azure. Uma das várias funcionalidades totalmente de PolyBase é serão ler todos os ficheiros dentro de uma pasta e processado como uma tabela. Por conseguinte, é uma boa ideia para identificar os ficheiros para cada tabela para a sua própria pasta.

PolyBase também suporta uma funcionalidade conhecida como "traversal de pasta recursiva". Pode utilizar esta funcionalidade para melhorar ainda mais a organização do seu dados exportados para melhorar a gestão de dados.

Para saber mais sobre o carregamento de dados com PolyBase, consulte o artigo [Utilizar PolyBase para carregar os dados armazém de dados SQL][].


## <a name="next-steps"></a>Próximos passos
Para mais informações sobre a migração, consulte o artigo [migrar a solução para armazém de dados do SQL][].
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copiar ADF]: ../data-factory/data-factory-data-movement-activities.md 
[Exemplos ADF]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md
[Migrar a sua solução para armazém de dados SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Utilizar bcp para carregar os dados para armazém de dados SQL]: sql-data-warehouse-load-with-bcp.md
[Utilizar PolyBase para carregar os dados para armazém de dados SQL]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Dados Azure fábrica]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentação]: http://azure.microsoft.com/documentation/services/expressroute/

[versão de produção]: http://aka.ms/downloadazcopy/
[versão de pré-visualização]: http://aka.ms/downloadazcopypr/
[ADO.NET placa de destino]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentação SSIS]: https://msdn.microsoft.com/library/ms141026.aspx
