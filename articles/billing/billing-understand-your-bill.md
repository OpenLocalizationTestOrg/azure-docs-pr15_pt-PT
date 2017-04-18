<properties
   pageTitle="Noções sobre a sua fatura | Microsoft Azure"
   description="Saiba como ler e compreender a utilização e a fatura para a sua subscrição do Azure"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/31/2016"
   ms.author="erihur;genli"/>


# <a name="understand-your-bill-for-microsoft-azure"></a>Compreender a sua fatura do Microsoft Azure

> [AZURE.NOTE] Se precisar de mais ajuda em qualquer ponto neste artigo, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

Os encargos para subscrições do Microsoft Azure variam consoante o plano de taxa. Alguns planos de taxa, tais como os subscritores do Visual Studio Enterprise (MPN), incluem créditos mensais que pode utilizar qualquer serviço Azure com base nas suas necessidades.

Tenha em atenção que configurar a 24 horas de utilização latente a partir do seu período de faturação prévia pode ser comunicado no período de faturação atual.

Para mais informações sobre o consumo e planos taxa, consulte a [página de opções de compra do Microsoft Azure](https://azure.microsoft.com/pricing/purchase-options/).

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>Ver ou transferir um título para o Microsoft Azure:

1. Inicie sessão no [Centro de conta](https://account.windowsazure.com/subscriptions) através do Microsoft Account ou ID organizacional.

2. Clique na subscrição na qual pretende ver os detalhes e a utilização.

3. Clique no **Histórico de faturação**

    ![Resumo - o -1 do histórico de faturação](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)

4. Secção **Histórico de faturação** de lista os extratos para períodos de faturaçãohttps anteriores, bem como o período das atual. A instrução para o período atual é uma estimativa dos seus encargos partir a hora que a estimativa foi gerada. Estas informações apenas são atualizadas diariamente e não podem incluir a utilização de todos os suportados até à data. A fatura mensal pode ser diferentes a partir desta estimativa.  

    ![Histórico de faturação de resumo da 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Clique em **Ver a declaração de atual** para ver uma estimativa dos seus encargos partir a hora que a estimativa foi gerada. Estas informações apenas são atualizadas diariamente e não podem incluir a utilização de todos os suportados até à data. A fatura mensal pode ser diferentes a partir desta estimativa.

    ![Histórico de faturação de resumo da 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Histórico de faturação de resumo da 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Clique em **Transferir fatura** para ver uma cópia da sua fatura anterior.

    ![Histórico de faturação de resumo da 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


> [AZURE.NOTE] Encargos listados na extratos de faturação para os clientes internacionais são para fins de estimativa apenas como bancos têm diferentes custos para as taxas de conversão.

Seguem-se algumas declarações de exemplo de duas ofertas diferentes disponíveis no Microsoft Azure.

 Tipo de oferta | Descrição | Transferir |
 :--------- |:-------- | :-------|
Repartição | Pagar mensalmente em atraso | [Ficheiro de exemplo](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Oferta de consolidação | Passam deduzida a partir do seu compromisso pré-paga | [Ficheiro de exemplo](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)

## <a name="account-information"></a>Informações da conta

Secção de informações de conta identifica informações pertinentes sobre a utilização do perfil.

![cabeçalho](./media/billing-understand-your-bill/Header.png)

| Termos                | Descrição                                                                                         |
|---------------------|-----------------------------------------------------------------------------------------------------|
| N. º de fatura         | Um identificador exclusivo de fatura para fins de controlo                                                   |
| Ciclo de faturação       | O intervalo de tempo em que a utilização teve lugar                                                       |
| Data da fatura        | Data em que foi criada a fatura                                                                 |
| Método de pagamento      | Tipo de pagamento utilizado na conta (fatura ou cartão de crédito)                                   |
| Para faturação             | Endereço de pagamentos do Microsoft Azure                                                                    |
| Oferta de subscrição  | Tipo de oferta de subscrição foi comprada (repartição, BizSpark sinal de adição, Azure fase, etc.) |
| E-mail de proprietário da conta | O endereço de e-mail da conta que está registada na conta do Microsoft Azure em                      |

## <a name="understand-the-invoice-summary"></a>Compreender o resumo de fatura

Secção de **Resumo de fatura** da fatura resume as transações desde a última fatura e os custos de utilização atual.

![Resumo da fatura](./media/billing-understand-your-bill/InvoiceSummary.png)

O total anterior, pagamentos e saldo secção da fatura resume as transações desde a última fatura.

| Termos                                              | Descrição                                                                              |
|---------------------------------------------------|------------------------------------------------------------------------------------------|
| Total anterior                                  | Montante total em dívida da sua fatura última                                                 |
| Pagamentos                                          | Total dos pagamentos aplicados a sua última fatura                                                 |
| Saldo (a partir do ciclo de faturação anterior) | Quaisquer ajustes de fatura (créditos ou saldos) aplicados à sua conta desde a última fatura  |

## <a name="understand-the-current-charges"></a>Compreender encargos atuais
Secção de encargos atuais da fatura contém detalhes sobre as cobranças mensais. As ligações estão organizadas nas seguintes subsecções.

| Termos          | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Custos de utilização | Custos de utilização são total dos encargos mensais numa subscrição. São faturada com atraso de utilização do seu mês passado.                                                                                                                                                                                                                                                                                                                                       |
| Descontos     | Descontos Serviço aplicados a sua fatura atual seriam reflectidos neste item de linha.                                                                                                                                                                                                                                                                                                                                              |
| Ajustes   | Diversas ajustes são créditos diversos ou taxas pendentes aplicadas a sua fatura atual. Por exemplo, se tiver o Visual Studio Enterprise com oferta MSDN, verá um crédito mensal este item de linha. Se cancelar a sua subscrição, deverá ver as cobranças para uma utilização mensal que excedam o crédito mensal incluído na sua oferta desde o início do período de faturação atual para a data de cancelamento de subscrição.|

## <a name="footer-information"></a>Informações de rodapé
![rodapé](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>Compreender as informações adicionais
A página de informações adicionais dá-lhe referências a outros recursos para compreender a sua fatura e ligações para ver a sua utilização e outras informações relevantes para a sua fatura.

![informações adicionais](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>Utilização detalhado
Uma ligação na descrição em **Utilização detalhadas** indica o Centro de conta, onde pode ver a utilização do detalhadas para esta subscrição.  Agora existem duas versões disponíveis para transferência: **versão de ficheiro. csv 1** contém os campos de utilização e Convenção de nomenclatura antiga e **a versão de ficheiro. csv 2** contém nomes amigáveis do cliente para cada uma das categorias plus campos adicionais que irão ajudá-lo a compreender o que serviços que estão a utilizar no Microsoft Azure. Tenha em atenção que, na versão de ficheiro. csv 1 que não existem detalhes Azure o Gestor de recursos. Pode encontrar informações de Gestor de recursos Azure na versão de ficheiro. csv 2.

### <a name="additional-information-and-useful-resources"></a>Informações adicionais e recursos úteis
Esta secção tem ligações para simples questões relacionadas com tamanhos de instância cluster, SQL DB taxas e hiperligações úteis para o ajudar a responder a perguntas.

| Termos                 | Descrição                                                                                                                            |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Vendidos a              | Isto é pré-preenchida com o endereço de perfil da conta                                                                           |
| Instruções de pagamento | Esta secção não se as instruções de pagamento do local onde enviar verificações, fio transferências ou noite verifica se o seu método de pagamento for fatura |

## <a name="understand-detailed-usage-charges"></a>Compreender os custos de utilização detalhadas

Como parte do nosso compromisso para ajudar os clientes gerir facilmente a sua utilização Azure em curso, vamos tenha as funcionalidades do ficheiro de utilização de transferir relatórios sobre a utilização dos serviços de Azure e os custos.  A ligação de transferência contém duas versões do ficheiro a utilização:

- **Versão 1** utiliza o formato do pré-existentes

- **Versão 2** inclui informações adicionais e atualizadas nomes das colunas na secção a utilização diárias.  

Custos de utilização são total **mensal** encargos numa subscrição menos qualquer crédito ou desconto. São faturada com atraso de utilização do seu mês passado.  A secção superior do ficheiro apresenta os detalhes sobre os serviços que está a ser faturada para durante o ciclo de faturação do mês anterior.  A tabela anterior lista os nomes das colunas para cada um dos ficheiros. csv versão.

Versão 1 |  Versão 2  |  Descrição|
:---------------| :---------------- | --------|
Período de faturação | Período de faturação | O período de faturação quando o recurso foi consumido.
Nome | Categoria de indicador | Identifica o serviço de nível superior para a qual pertence este utilização.
Tipo | Medidor subcategoria | Serviço Azure pode ser definido ainda mais por tipo desta coluna, que podem afetar a taxa.
Recurso | Nome de um indicador de apresentar | Identifica a unidade de medida para o recurso a ser consumida.
Região | Região de indicador | Identifica a localização do Centro de dados para certos serviços que estão preços com base na localização do Centro de dados.
SKU | SKU | Identifica o identificador exclusivo do sistema para cada recurso Azure.
Unidade | Unidade | Identifica a unidade que o serviço é cobrado no. Por exemplo, GB, horas, 10,000s.
Consumidas | Quantidade consumida | Contém o valor do recurso que tenha sido consumido durante o período de faturação.
Incluído | Quantidade incluída | Contém o valor do recurso que está incluído sem encargos no período de faturação atual.
Cobrar | Quantidade ultrapassada | Se o valor de Consumed excede o montante incluído, esta coluna mostra a diferença. São faturada durante este período. Para ofertas repartição com nenhum valor incluída com a oferta, este total será igual a quantidade de Consumed.
Dentro de consolidação | Dentro de consolidação | Contém as taxas de recurso que estão reduzidas da sua quantidade de consolidação associada a sua oferta de mês 6 ou 12. Os custos de recursos são reduzidos a quantidade de consolidação por ordem cronológica.
Moeda | Moeda | Identifica a moeda refletida no período de faturação atual.
Hiperdosagem em termos | Hiperdosagem em termos | Contém as taxas de recurso ultrapassar a sua quantidade de consolidação associada a sua oferta de mês 6 ou 12.
Taxa de consolidação | Taxa de consolidação | Contém a taxa de consolidação com base na sua quantidade de consolidação total associada a sua oferta de mês 6 ou 12.
Taxa | Taxa | Taxa apresenta a taxa que lhe vai ser cobrada por unidade cobrar.
Valor | Valor | Apresenta o resultado da multiplicação a coluna cobrar pela coluna taxa. Se a quantidade de Consumed não exceda a quantidade de incluído, não será sem encargos nesta coluna.

## <a name="analyze-daily-usage-data"></a>Analisar dados de utilização diária
Dependendo da sua utilização, pode ser milhares de linhas de dados de utilização diária. Se pretender analisar estes dados, clique em **Transferir utilização** e selecione uma versão de ficheiro variável separados por vírgulas (. csv) para ver os dados de utilização diária durante o período de faturação adequado.  Para sua referência, pode transferir um ficheiro. csv de exemplo para cada versão abaixo.

 Nome | Transferir |
 :----------:| :-------: |
  Utilização detalhada. csv versão 1|  [Ficheiro de exemplo](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
  Utilização detalhada. csv versão 2 | [Ficheiro de exemplo](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



No ficheiro. csv, os itens são divididos para apresentar uma lista de quantidade de cada recurso foi consumida dentro do período de faturação atual.

![instantâneo de CSV](./media/billing-understand-your-bill/csvsnapshotportal.png)

As seguintes colunas apresentam detalhes que afetam as taxas no início do período de faturação:

Versão 1 |   Versão 2   |  Descrição |
:---------------| :----------------| -----|
Data de utilização | Data de utilização | A data quando o recurso foi emitido.
Nome | Categoria de indicador | Identifica o serviço de nível superior para a qual pertence este utilização.
GUID do recurso | ID de indicador | O identificador medidor faturado.  Isto é utilizado como o identificador utilizado para a utilização de faturação do preço.
Tipo | Medidor subcategoria | Serviço Azure pode ser definido ainda mais por tipo desta coluna, que podem afetar a taxa.
Recurso | Nome de um indicador de apresentar | Identifica a unidade de medida para o recurso a ser consumida.
Região | Região de indicador | Identifica a localização do Centro de dados para certos serviços que estão preços com base na localização do Centro de dados.
Unidade | Unidade | Identifica a unidade que o serviço é cobrado no. Por exemplo, GB, horas, 10,000s.
Consumidas | Quantidade consumida | Contém o valor do recurso que tenha sido consumido para esse dia.
Sub-região | Localização do recurso | Identifica o Centro de dados onde o recurso está em execução.
Serviço | Serviço consumo | Esta coluna é utilizada para controlar o serviço de plataforma Azure individuais que possam não ser especificamente identificado na coluna nome. Este serviço coluna indica a utilização do serviço específicas do qual pertence.
N/D | Grupo de recursos | _**Nova adição de coluna.**_ O grupo de recursos na qual o recurso implementado está em execução no. Referir-se para a [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)
Componente | ID da instância | O identificador para o recurso em execução. O identificador contém o nome que especificar para o recurso quando foi criada.
N/D | Etiquetas | _**Nova adição de coluna.**_ Novos tipos de recursos no Azure permitem-lhe para recursos de etiqueta. Referir-se para [organizar os recursos do Azure com tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)
Informações adicionais | Informações adicionais | Metadados adicionais relacionados com o serviço.
Informações de serviço 1 | Informações de serviço 1 | Esta coluna fornece o nome do projeto que pertence o serviço na sua subscrição.
Informações de serviço 2 | Informações de serviço 2 | Este é um campo de legado que opcional específicas do serviço de metadados para capturar.

Para além de algumas novos campos e as alterações de nome CSV versão 2, existem vai ser padronizados formatação para os dados na abaixo campos:

- **ID da instância**: O ID da instância campo que representa o utilizador especificado identificador para o serviço aprovisionado. Atualmente, existem dois formatos nos quais o ID da instância é representado: é o nome do recurso ou completamente qualificado ID do recurso. Serviços do Microsoft Azure são na transição para representar o ID da instância num formato de ID do recurso completamente qualificado padronizado _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Como dos serviços de transição para o novo formato irá ver o campo de dados de ID da instância alterar de apenas o nome do recurso para ID do recurso. O ID do recurso é o formato utilizado pelo [API do Gestor de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx) para identificar recursos numa subscrição.

![InstanceId](./media/billing-understand-your-bill/instanceid.png)

- **Informações adicionais**: coluna de informações adicionais. csv a utilização especifica específico do serviço de metadados. Por exemplo, um tipo de imagem para uma VM. Atualmente, um serviço emite específico do serviço de metadados em várias colunas: campos informações adicionais, Info1 de serviço e Service informações 2. Serviços do Microsoft Azure irão normalização emissão específico do serviço de metadados na coluna informações adicionais apenas.  Consulte o artigo o abaixo instantâneo do formato padronizado:

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Etiquetas**: Esta coluna contenha o utilizador especificado etiquetas de recursos. As etiquetas podem ser utilizadas para agrupar registos faturaçãohttps. Por exemplo, pode utilizar etiquetas para distribuir custos por departamento utilizando o serviço. Saiba mais sobre [utilizar etiquetas para organizar Azure recursos](../resource-group-using-tags.md). Serviços que suportam etiquetas emissão são:  

    - Máquinas virtuais

    - Armazenamento e

    - Serviços de funcionamento em rede aprovisionados utilizar a [API do Gestor de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![etiquetas](./media/billing-understand-your-bill/tags.png)


## <a name="next-steps"></a>Próximos passos

- [Configurar alertas de faturação](../billing-set-up-alerts.md)

- [Gerir os seus métodos de pagamento](../billing-how-to-change-credit-card.md)

- [Compreender as cobranças Azure Marketplace](../billing-understand-your-azure-marketplace-charges.md)

- [Perguntas mais frequentes do Azure faturação e subscrição](../billing-subscription-faq.md)

> [AZURE.NOTE] Se ainda tiver ainda mais perguntas, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->
