<properties
   pageTitle="Segurança de nível de linha com o Power BI incorporado"
   description="Obter mais detalhes sobre a segurança de nível de linha com o Power BI incorporado"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Segurança de nível de linha com o Power BI incorporado

Segurança de nível de linha (RLS) pode ser utilizada para restringir o acesso de utilizador a dados em particular dentro de um relatório ou um conjunto de dados, tem a permissão para vários utilizadores diferentes utilizar o mesmo relatório enquanto está a ver todos os de dados diferentes. Power BI incorporado suporta agora configurados com RLS de conjuntos de dados.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Forma a tirar partido de RLS, é importante que compreende os conceitos principais três; Os utilizadores, funções e regras. Vamos mais detalhada cada:

**Os utilizadores** – estes são os utilizadores finais reais visualizar relatórios. No Power BI incorporado, os utilizadores são identificados pela propriedade do nome de utilizador num Token de aplicação.

**Funções** – utilizadores pertencem a funções. Uma função é um contentor para regras e pode ser denominado por algo como "Gestor de vendas" ou "Representante de vendas". No Power BI incorporado, os utilizadores são identificados pela propriedade de funções num Token de aplicação.

**Regras** – funções têm regras e as regras são os filtros reais que irão ser aplicada aos dados. Isto pode ser tão simple como "país = EUA" ou algo dinâmico muito mais.

### <a name="example"></a>Exemplo

Para o resto neste artigo, irá fornecemos um exemplo de criação de conteúdos RLS e, em seguida, utilizado por outros que dentro de uma aplicação incorporada. Nosso exemplo utiliza o ficheiro PBIX de [Exemplo de análise de retalho](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Nosso exemplo de análise de revenda mostra as vendas de todos os arquivos de uma cadeia de revenda específico. Sem RLS, independentemente do qual administrativo gestor de sinais de em vistas e o relatório, verá os mesmos dados. Gestão de curricula tem determinada o Gestor de cada distrito só deverá ver as vendas de stores que gerir e, para fazer isto, pode utilizamos RLS.

RLS é criado no ambiente de trabalho do Power BI. Quando o conjunto de dados e o relatório são abertos, podemos pode mudar para vista de diagrama para ver o esquema:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Eis alguns aspetos a Repare com este esquema:

-   Todas as medidas, como o **Total de vendas**, são armazenadas na tabela de factos de **vendas** .
-   Existem quatro tabelas de dimensão relacionada adicionais: **Item**, **hora**, **arquivo**e **administrativo**.
-   As setas nas linhas de relação indicam qual a forma mais filtros podem um fluxo de uma tabela para outra. Por exemplo, se for colocado um filtro tempo **[data]**, no esquema de atual-seria filtrar apenas para baixo de valores existentes na tabela de **vendas** . Não existem outras tabelas seriam afectadas por este filtro uma vez que todas as setas nas linhas de relação aponte para a tabela de vendas e não como ausente.
-   A tabela **administrativo** indica quem é o gestor para cada distrito:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Com base neste esquema, se podemos aplicar um filtro para a coluna de **Gestor de distrito** na tabela distrito e se que filtrar correspondências o utilizador a visualização do relatório, esse filtro também irá filtrar para baixo as tabelas de **arquivo** e **de vendas** para mostrar apenas dados para esse administrativo determinado gestor.

Eis como:

1.  No separador modelação, clique em **Gerir funções**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Crie uma nova função chamada **Gestor**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Na tabela **administrativo** introduza a seguinte expressão do DAX: **[Manager Distrito] = username ()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Para se certificar de que as regras estiver a trabalhar, no separador **Modelação** , clique em **Ver como as funções**e, em seguida, introduza o seguinte:  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Os relatórios agora vai mostrar dados como se foram a sessão iniciada como **O André do Friso do Excel**.

Aplicar o filtro, a forma como o fizemos aqui, irá filtrar para baixo de todos os registos das tabelas **Distrito**, **armazenar**e **vendas** . No entanto, devido a direção de filtro nas relações entre **vendas** e a **hora**, tabelas de **vendas** e **Item**e **Item** e **tempo** serão não filtradas para baixo.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Que pode ser ok para este requisito, no entanto, se não queremos gestores para ver os itens para as quais não têm qualquer vendas, podemos poderia ativar bidireccional filtragem por comparação para a relação e um fluxo de filtro de segurança em ambos os sentidos. Isto pode ser feito ao editar a relação entre as **vendas** e **Item**, da seguinte forma:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Agora, filtros também podem fluxo da tabela de vendas para a tabela **produto** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Nota** Se estiver a utilizar o modo DirectQuery para os seus dados, terá de activar a filtragem cruzada bidireccional ao selecionar estas duas opções:

1.  **Ficheiro** -> **Opções e definições** -> **Pré-visualização funcionalidades** -> **Ativar cruzada filtragem em ambos os sentidos para DirectQuery**.
2.  **Ficheiro** -> **Opções e definições** -> **DirectQuery** -> **Permitir medida ilimitada no modo DirectQuery**.


Para saber mais sobre filtragem por comparação bidirecional, transfira o documento técnico [bidireccional filtragem por comparação no SQL Server Analysis Services 2016 e no ambiente de trabalho do Power BI](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Isto é moldado o todo o trabalho que necessita de ser executadas no ambiente de trabalho do Power BI, mas existe uma mais parte de um trabalho que necessita de ser executadas para tornar o RLS regras de que trabalho definido no Power BI incorporado. Os utilizadores sejam autenticados e autorizados pela aplicação e tokens de aplicação são utilizadas para conceder esse utilizador acesso a um relatório de Power BI incorporado específico. Power BI incorporado não tem qualquer informações específicas sobre quem é o utilizador. Para RLS trabalhar, terá de ser efetuada com algumas contexto adicional como parte do seu token de aplicação:
-   **nome de utilizador** (opcional) – utilizada com RLS esta é uma cadeia que pode ser utilizada para ajudar a identificar o utilizador quando aplicar RLS regras. Consulte o artigo utilizar linha incorporada a segurança de nível com o Power BI
-   **funções** – uma cadeia que contém as funções para selecionar quando aplicar regras de segurança de nível de linha. Se passando mais do que uma função, devem ser passados como uma matriz de cadeia.

Se existir a propriedade do nome de utilizador, também tem de passar pelo menos um valor em funções.

O token de aplicação completo será o aspeto algo parecido com:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Agora, com todas as peças em conjunto, quando alguém iniciar sessão na nossa aplicação para ver este relatório, irá apenas poderão ver os dados que são permitidos para ver, conforme definido pelo nossa segurança de nível de linha.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Consulte também
[Segurança de nível de linha (RLS) com Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
