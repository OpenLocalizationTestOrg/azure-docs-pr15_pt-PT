<properties
    pageTitle="Inicie sessão estruturador de vista de análise | Microsoft Azure"
    description="Estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados no repositório de OMS. Este artigo contém uma descrição geral do estruturador de vista e procedimentos para criar e editar vistas personalizadas."
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
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer"></a>Estruturador de vista de análise de registo
O estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados no repositório de OMS. Este artigo contém uma descrição geral do estruturador de vista e procedimentos para criar e editar vistas personalizadas.

Outros artigos disponíveis para o estruturador de vista são:

- [Referência de mosaico](log-analytics-view-designer-tiles.md) - referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas. 
- [Referência de peça de visualização](log-analytics-view-designer-parts.md) - referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas. 


## <a name="concepts"></a>Conceitos
Vistas criadas com o Designer de vista contenham os elementos da tabela seguinte.

| Peça | Descrição |
|:--|:--|
| Mosaico | Apresentado no dashboard de descrição geral do registo Analytics principal.  Inclui uma resumir visual das informações contidas na vista personalizada.  Diferentes tipos de mosaico fornecem diferentes visualizações de registos no repositório de OMS.  Clique no mosaico para abrir a vista personalizada. |
| Vista personalizada | Apresentada quando o utilizador clica no mosaico.  Contém uma ou mais peças de visualização. |
| Peças de visualização | Visualização de dados no repositório OMS com base numa ou mais [pesquisas de registo](log-analytics-log-searches.md).  A maioria das partes irão incluir um cabeçalho que fornece uma visualização de alto nível e uma lista dos resultados superiores.  Tipos de diferentes peças fornecem diferentes visualizações de registos no repositório de OMS.  Clique em elementos na peça para efetuar uma pesquisa de registo fornecer registos detalhados. |

![Descrição geral do estruturador de vista](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Adicionar estruturador de vista para a área de trabalho
Enquanto está a pré-visualização do estruturador de vista, tem de adicioná-la à área de trabalho selecionando **Pré-visualização funcionalidades** na secção **Definições** do OMS portal.

![Ativar a pré-visualização](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Criar e editar vistas

### <a name="create-a-new-view"></a>Criar uma nova vista
Abra uma nova vista no **Estruturador de vista** ao clicar no mosaico do estruturador de vista no dashboard de OMS principal.

![Mosaico do estruturador de vista](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Editar uma vista existente
Para editar numa vista existente no estruturador de vista, abra a vista ao clicar no respetivo mosaico no dashboard de OMS principal.  Em seguida, clique no botão **Editar** para abrir a vista no estruturador de vista.

![Editar uma vista](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonar numa vista existente
Quando clonar uma vista, cria uma nova vista e abre-o no estruturador de vista.  A nova vista terá o mesmo nome como o original com "copiar" anexadas para o fim da mesma.  Para clonar uma vista, abra a vista existente clicando no respetivo mosaico no dashboard de OMS principal.  Em seguida, clique no botão de **clonar** para abrir a vista no estruturador de vista.

![Clonar uma vista](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Eliminar uma vista existente
Para eliminar uma vista existente, abra a vista ao clicar no respetivo mosaico no dashboard de OMS principal.  Em seguida, clique no botão **Editar** para abrir a vista no estruturador de vista e clique em **Eliminar vista**.

![Eliminar uma vista](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportar uma vista existente
Pode exportar uma vista para um ficheiro JSON que pode importar para outra área de trabalho ou utilizar um [modelo de Gestor de recursos do Azure](../resource-group-authoring-templates.md).  Para exportar uma vista existente, abra a vista ao clicar no respetivo mosaico no dashboard de OMS principal.  Em seguida, clique no botão **Exportar** para criar um ficheiro numa pasta de transferências do browser.  O nome do ficheiro será o nome da vista com a extensão *omsview*.

![Exportar uma vista](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importar uma vista existente
Pode importar um ficheiro que exportou a partir de outro grupo de gestão de *omsview* .  Para importar uma vista existente, primeiro crie uma nova vista.  Em seguida, clique no botão **Importar** e selecione o ficheiro de *omsview* .  A configuração no ficheiro será ser copiada para a vista existente.

![Exportar uma vista](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Trabalhar com o estruturador de vista
O estruturador de vistas tem três painéis.  O painel de **estrutura** representa a vista personalizada.  Quando adicionar mosaicos e peças a partir do painel de **controlo** para o painel de **estrutura de** que serem adicionadas à vista.  O painel de **Propriedades** irá apresentar as propriedades para o mosaico ou peça selecionada.

![Estruturador de vista](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurar o mosaico de vista
Uma vista personalizada pode ter um único mosaico.  Selecione o separador **Dispor em mosaico** no painel de **controlo** para ver o mosaico atual ou selecione uma alternativa.  O painel de **Propriedades** irá apresentar as propriedades do mosaico atual.  Configurar as propriedades de mosaico de acordo com as informações detalhadas na [Referência dispor em mosaico](log-analytics-view-designer-tiles.md) e clique em **Aplicar** para guardar as alterações.

### <a name="configure-visualization-parts"></a>Configurar peças de visualização
Uma vista pode incluir qualquer número das partes da visualização.  Selecione o separador de **vista** e, em seguida, uma peça de visualização para adicionar à vista.  O painel de **Propriedades** irá apresentar as propriedades para a parte selecionada.  Configurar as propriedades de vista de acordo com as informações detalhadas na [referência da peça de visualização](log-analytics-view-designer-parts.md) e clique em **Aplicar** para guardar as alterações.

### <a name="delete-a-visualization-part"></a>Eliminar uma peça de visualização
Pode remover uma peça de visualização na vista ao clicar no botão **X** no canto superior direito da peça.

### <a name="rearrange-visualization-parts"></a>Reorganizar partes de visualização
As vistas têm apenas uma linha das partes da visualização.  Reorganize as peças existentes numa vista clicando e arrastando-os para uma nova localização.


## <a name="next-steps"></a>Próximos passos

- Adicione [mosaicos](log-analytics-view-designer-tiles.md) à sua vista personalizada.
- Adicione [Peças de visualização](log-analytics-view-designer-parts.md) para a vista personalizada.
