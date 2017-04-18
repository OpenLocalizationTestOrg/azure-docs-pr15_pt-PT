
<properties
    pageTitle="Utilizar o Azure AD ligar-se do Estado de funcionamento com a sincronização | Microsoft Azure"
    description="Esta é a página do Azure AD ligar-se do Estado de funcionamento que vai discutir como monitorizar a ligação do Azure AD sync."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização
A seguinte documentação é específica a ligação do Azure AD (sincronização) com o Azure AD ligar-se do Estado de funcionamento de monitorização.  Para obter informações sobre como monitorizar o AD FS com Azure AD ligar-se do Estado de funcionamento consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md). Para além disso, para obter informações sobre como monitorizar o Active Directory Domain Services com o Azure AD ligar-se do Estado de funcionamento consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md).

![Ligação do Estado de funcionamento do Azure AD para a sincronização](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas para Azure AD ligar-se do Estado de funcionamento para a sincronização
O Azure AD ligar-se do Estado de funcionamento secção alertas para sincronizar fornece-lhe a lista de alertas ativas. Cada alerta inclui informações relevantes, os passos de resolução e ligações para documentação relacionada. Ao selecionar um alerta ativo ou resolvido irá ver um novo pá com informações adicionais, bem como os passos que pode tomar para resolver o alerta e ligações para documentação adicional. Também pode ver dados histórico sobre alertas que foram resolvidos no passado.

Ao selecionar um alerta que serão fornecidos com informações adicionais, bem como os passos que pode tomar para resolver o alerta e as hiperligações para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Limitado avaliação de alertas
Se a ligação do Azure AD não estiver a utilizar a configuração predefinida (por exemplo, se a filtragem de atributo for alterada da configuração predefinida para uma configuração personalizada), em seguida, o agente do Azure AD ligar-se do Estado de funcionamento carregar não relacionados com a ligação do Azure AD os eventos de erro.

Este procedimento limita da avaliação de alertas pelo serviço. Seria verá uma faixa que indica que esta condição no Portal do Azure em seu serviço.

![Ligação do Estado de funcionamento do Azure AD para a sincronização](./media/active-directory-aadconnect-health-sync/banner.png)

Pode alterar ao clicar em "Definições de" e permitindo agente do Azure AD ligar-se do Estado de funcionamento carregar todos os registos de erros.

![Ligação do Estado de funcionamento do Azure AD para a sincronização](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Conhecimentos aprofundados de sincronização
Os administradores frequentemente querer saber sobre o tempo que demora a sincronizar as alterações Azure AD e a quantidade de alterações ocorridas. Esta funcionalidade fornece uma forma fácil de visualizar esta utilizando a abaixo gráficos:   

- Latência de operações de sincronização
- Tendência de alteração de objeto

### <a name="sync-latency"></a>Latência de sincronização
Esta funcionalidade fornece uma tendência de latência das operações de sincronização (importar, exportar, etc.) para os conectores gráfica.  Este procedimento fornece uma maneira rápida e fácil de compreender não só a latência da sua operações (maiores se tiver um conjunto maior de eventuais alterações que ocorram), mas também uma forma de detetar anomalias na latência que pode exigir mais inquérito.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Por predefinição, é apresentada apenas a latência da operação de «Export» para o conector do Azure AD.  Para ver mais operações o Connector ou para ver as operações a partir de outras conexões, botão direito do rato no gráfico, selecione editar gráfico ou clique no botão "Editar Gráfico de latência" e escolha a operação específica e conectores.

### <a name="sync-object-changes"></a>Sincroniza as alterações de objeto
Esta funcionalidade fornece uma tendência de gráfica do número de alterações que estão a ser avaliada e exportado para Azure AD.  Hoje em dia, está a tentar recolher esta informação a partir dos registos de sincronização é difícil.  O gráfico dá-lhe, não apenas uma forma mais simples de monitorização o número de alterações que estão a ocorrer no seu ambiente, mas também uma vista visual das falhas que estão a ocorrer.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Relatório de erros de sincronização de nível do objeto (pré-visualização)
Esta funcionalidade fornece um relatório de erros de sincronização que podem ocorrer quando os dados de identidade são sincronizados entre o Windows Server AD e Azure AD utilizando a ligação do Azure AD.

- O relatório abrange erros registados pelo cliente de sincronização (ligação do Azure AD versão 1.1.281.0 ou superior)
- Inclui os erros que ocorreram na última operação de sincronização do motor de sincronização de dados. ("Exportar" no Azure AD conector.)
- Azure agente de estado de funcionamento do AD ligar-se para a sincronização tem de ter conectividade de saída para os pontos finais necessários para o relatório incluir os dados mais recentes. Consulte a [secção requisitos](active-directory-aadconnect-health-agent-install.md#Requirements) para obter detalhes.
- O relatório é **atualizada após a cada 30 minutos** a utilizar os dados carregados pelo agente do Azure AD ligar-se do Estado de funcionamento para a sincronização.
Fornece as seguintes capacidades chaves

    - Categorização de erros
    - Lista de objetos com o erro por categoria
    - Todos os dados sobre os erros de um só local
    - Lado a lado comparação lado de objetos com o erro devido a um conflito
    - Transferir o relatório de erros como um CVS (brevemente)

### <a name="categorization-of-errors"></a>Categorização de erros
O relatório categoriza erros de sincronização existentes nas seguintes categorias:

| Categoria | Descrição |
| -------------- | ----------- |
| Atributo duplicado | Erros ao tentativas de ligação do Azure AD criar ou actualizar objetos com valores duplicados dos atributos de uma ou mais no Azure AD que têm de ser exclusivo num inquilino, tal como proxyAddresses, UserPrincipalName. |
| Erro de correspondência de dados | Erros à correspondência de contornos falha para que correspondam aos objetos que resultam em erros de sincronização. |
| Falha de validação de dados | Erros devido a dados inválidos, como carateres não suportados em atributos críticos como UserPrincipalName, formatar erros de validação antes de a ser escrito Azure AD falhem.|
| Atributo de grandes dimensões | Erros ao maiores atributos de uma ou mais do que o tamanho permitido, comprimento ou contagem.|
| Outros | Todos os outros erros de que não se ajustam nas categorias acima. Com base nos comentários, esta categoria vai dividir no sub categorias.

![Relatório Resumo de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![sincronizar categorias de relatório de erro](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos com o erro por categoria
Explorar em cada categoria irá fornecer da lista de objetos que está a ter o erro nessa categoria.
![Lista de relatório de erros de sincronização](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalhes do erro
Seguir dados está disponível na vista detalhada para cada erro

- Identificadores para o *Objeto de AD* envolvidos
- Identificadores para o *Objeto do Azure AD* envolvidos (como aplicável)
- Descrição do erro e como corrigir
- Artigos relacionados

![Detalhes do relatório de erros de sincronização](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Transferir o relatório de erros como CSV
Esta funcionalidade é brevemente. Esteja atento aos mais atualizações.



## <a name="related-links"></a>Ligações relacionadas
* [Resolução de problemas de erros durante a sincronização](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Atributo duplicado RDP](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Ligação do Azure AD instalação do agente de estado de funcionamento](active-directory-aadconnect-health-agent-install.md)
* [Operações de estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
