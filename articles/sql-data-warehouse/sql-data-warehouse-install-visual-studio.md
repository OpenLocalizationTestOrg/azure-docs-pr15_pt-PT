<properties
   pageTitle="Instalar o Visual Studio e SSDT para armazém de dados SQL | Microsoft Azure"
   description="Instalar o Visual Studio e ferramentas de desenvolvimento do SQL Server (SSDT) para armazém de dados do Azure SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio 2015 e SSDT para armazém de dados SQL

Para desenvolver aplicações para armazém de dados SQL, recomendamos que utilize o Visual Studio 2015 com a versão mais recente do SQL Server dados Tools (SSDT).  Visual Studio 2013 Update 5 com SSDT também é suportada para compatibilidade com versões anteriores.  

Utilizar o Visual Studio com SSDT permite-lhe utilizar o Explorador de objeto do SQL Server para visualmente explorar tabelas, vistas, procedimentos armazenados e muito mais objectos no seu armazém de dados SQL, bem como executar consultas.

> [AZURE.NOTE] SQL Data Warehouse não suporta ainda projectos de base de dados do Visual Studio.  Esta funcionalidade será adicionada numa versão futura.

## <a name="step-1-install-visual-studio-2015"></a>Passo 1: Instalar o Visual Studio 2015

Siga estas ligações para transferir e instalar o Visual Studio 2015. Se já tiver Visual Studio 2013 ou 2015 instalado, pode avançar para o passo 2, instalar SSDT.

1. [Transferir o Visual Studio 2015][].
2. Siga o guia de [Instalar o Visual Studio][] no MSDN e escolha as configurações predefinidas.

## <a name="step-2-install-ssdt"></a>Passo 2: Instalar SSDT

Para instalar o SSDT para Visual Studio simplesmente procurar uma atualização SSDT a partir do Visual Studio, seguindo estes passos.

1. No Visual Studio, clique em **Ferramentas** / **Extensions e atualizações …**  /  **Atualizações**
2. Selecione **Atualizações de produto** e, em seguida, procure a **ferramenta do Microsoft SQL Server Update para base de dados**

Se uma atualização não for encontrada, em seguida, deverá ter a versão mais recente instalada.  Para confirmar SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e aspeto para ferramentas de dados do SQL Server na lista.  A versão mais recente do SSDT é 14.0.60525.0.  Em alternativa se a opção de instalação não está disponível a partir do Visual Studio, pode visitar a página [SSDT transferir][] para transferir e instalar SSDT manualmente.

## <a name="next-steps"></a>Próximos passos

Agora que tem a versão mais recente do SSDT, está pronto para [Ligar][] para o seu armazém de dados SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[ligar]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Transferir o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalar o Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Transferir SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
