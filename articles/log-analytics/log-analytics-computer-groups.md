<properties
    pageTitle="Grupos do computador no registo de análise sessão pesquisas | Microsoft Azure"
    description="Grupos do computador no registo de análise permitem-lhe as pesquisas de registo do âmbito a um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que pode utilizar para criar grupos de computador e como utilizá-las numa pesquisa registo."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>

# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos do computador no registo de análise sessão pesquisas
Grupos do computador no registo de análise permitem-lhe a âmbito [sessão pesquisas de](log-analytics-log-searches.md) um determinado conjunto de computadores.  Cada grupo é povoado com computadores que quer utilizar uma consulta definidos por si ou através da importação de grupos de origens diferentes.  Quando o grupo for incluído numa pesquisa de registo, os resultados são limitados ao registos que correspondem aos computadores no grupo.

## <a name="creating-a-computer-group"></a>Criar um grupo de computador
Pode criar um grupo do computador no registo de análise utilizar qualquer um dos métodos na tabela seguinte.  Para obter detalhes sobre cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:---|:---|
| Pesquisa de registo       | Crie uma pesquisa de registo que devolve uma lista de computadores e guarde os resultados como um grupo de computador. |
| Registo de pesquisa API   | Utilize a API do registo de pesquisa para criar um grupo de computador com base nos resultados de uma pesquisa de registo através de programação. |
| O Active Directory | Analisar automaticamente a associação a grupos de todos os computadores agente que são membros de um domínio do Active Directory e criar um grupo no registo Analytics para cada grupo de segurança.
| WSUS              | Digitalizar servidores WSUS ou clientes para filtragem de grupos e criar um grupo no registo Analytics para cada automaticamente. |


### <a name="log-search"></a>Pesquisa de registo

Grupos de computador criados a partir de um registo de pesquisa conterá todos os computadores devolvidos por uma consulta de pesquisa definidos por si.  Esta consulta é executada sempre que o grupo de computador é utilizado para que as alterações, desde que foi criado o grupo serão refletidas.

Utilize o seguinte procedimento para criar um grupo de computador a partir de uma pesquisa de registo.

1. [Criar uma pesquisa de registo](log-analytics-log-searches.md) que devolve uma lista de computadores.  A pesquisa tem de devolver um conjunto de computadores distinto utilizando algo como **Computador distintos** ou **medida count() pelo computador** na consulta.  
2. Clique no botão **Guardar** na parte superior do ecrã.
3. Selecione **Sim** para **Guardar esta consulta como um grupo de computador:**.
4. Escreva um **nome** e uma **categoria** para o grupo.  Se já existir uma pesquisa com o mesmo nome e categoria, em seguida, vai ser-lhe para substituí-lo.  Pode ter vários pesquisas com o mesmo nome diferentes categorias. 

Seguem-se as pesquisas de exemplo que pode guardar como um grupo de computador.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>Inicie sessão API de procura

Grupos de computador criados com a API do registo de pesquisa são os mesmos como pesquisas criadas com um registo de pesquisa.

Para obter detalhes sobre como criar um grupo de computador com a API do registo de pesquisa vejam [grupos do computador no registo de análise sessão pesquisa REST API](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>O Active Directory

Quando configurar a análise de registo para importar os membros do grupo do Active Directory, irá analisar a da associação a grupos de computadores qualquer domínio associado com o agente de OMS.  Um grupo de computador é criado no registo Analytics para cada grupo de segurança no Active Directory e cada computador é adicionado aos grupos de computador correspondente aos grupos de segurança que aos quais pertencem.  Este associação é constantemente atualizada cada 4 horas.  

Configurar a análise de registo para importar os grupos de segurança do Active Directory no menu de **Grupos do computador** no registo Analytics **Definições**.  Selecione **Automatização** e, em seguida, **os membros do grupo do Active Directory importação de computadores**.  Não existe nenhuma configuração mais obrigatória.

![Grupos de computador do Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Quando tiverem sido importados grupos, no menu irá listar o número de computadores com membros do grupo detetado e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos de **ComputerGroup** estas informações.

### <a name="windows-server-update-service"></a>Serviço de atualização do Windows Server

Quando configurar a análise de registo para importar os membros do grupo WSUS, irá analisar a associação a grupos-alvo de todos os computadores com o agente OMS.  Se estiver a utilizar o lado do cliente filtragem, qualquer computador que está ligado à OMS e faz parte de quaisquer WSUS filtragem de grupos tem os membros do grupo importados para o registo de análise. Se estiver a utilizar o lado do servidor filtragem, o OMS agente deve ser instalada no servidor WSUS para que a informação de membro do grupo para serem importados para OMS.  Este associação é constantemente atualizada cada 4 horas. 

Configurar a análise de registo para importar os grupos de segurança do Active Directory no menu de **Grupos do computador** no registo Analytics **Definições**.  Selecione **Do Active Directory** e, em seguida, **os membros do grupo do Active Directory importação de computadores**.  Não existe nenhuma configuração mais obrigatória.

![Grupos de computador do Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Quando tiverem sido importados grupos, no menu irá listar o número de computadores com membros do grupo detetado e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos de **ComputerGroup** estas informações.

## <a name="managing-computer-groups"></a>Gerir grupos de computador

Pode ver os grupos de computador que foram criados a partir de uma pesquisa de registo ou API de procura o registo a partir do menu **Grupos de computador** de análise de registo **Definições**.  Clique no **x** na coluna **Remover** para eliminar o grupo do computador.  Clique no ícone **Ver membros** de um grupo executar a pesquisa de registo do grupo que devolve os seus membros. 

![Grupos de computador guardados](media/log-analytics-computer-groups/configure-saved.png)

Para modificar o grupo, crie um novo grupo com o mesmo **categoria** e o **nome** para substituir o grupo original.

## <a name="using-a-computer-group-in-a-log-search"></a>Utilizar um grupo de computador na pesquisa registo
Utilize a seguinte sintaxe para fazer referência a um grupo de computador numa pesquisa de registo.  Especificar que a **categoria** é opcional e apenas necessário se tiver grupos de computador com o mesmo nome no diferentes categorias. 

    $ComputerGroups[Category: Name]

Quando uma pesquisa é executada, os membros dos grupos qualquer computador incluídos na procura primeiro são resolvidos.  Se o grupo for baseado uma pesquisa de registo, é executada que a pesquisa para devolver os membros do grupo antes de executar a pesquisa de registo de nível superior.

Grupos de computador são normalmente utilizados com **a cláusula in na pesquisa registo tal como no exemplo seguinte** .

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Agrupar registos de computador

Um registo é criado no repositório OMS para cada associação a grupos de computador criado a partir do Active Directory ou WSUS.  Estes registos tenham um tipo de **ComputerGroup** e tem as propriedades da tabela seguinte.  Registos não são criados para grupos de computador com base em pesquisas de registo.

| Propriedade | Descrição |
|:--|:--|
| Tipo                | *ComputerGroup* |
| SourceSystem        | *SourceSystem*  |
| Computador            | Nome do computador no membro. |
| Grupo               | Nome do grupo. |
| GroupFullName       | Caminho completo para o grupo incluindo de origem e o nome da origem.
| GroupSource         | Grupo de origem foi recolhidas a partir de. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName     | Nome da origem de que os grupos foi recolhidas a partir do.  Para o Active Directory, este é o nome de domínio. |
| ManagementGroupName | Nome do grupo de gestão de agentes SCOM.  Para os outros agentes, esta é AOI -\<ID da área de trabalho\> |
| TimeGenerated       | Data e hora, o grupo de computadores foi criado ou atualizado. |



## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções.  