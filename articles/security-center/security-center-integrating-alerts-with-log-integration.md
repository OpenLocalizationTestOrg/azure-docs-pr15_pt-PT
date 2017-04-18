<properties
   pageTitle="Integração de alertas do Centro de segurança do Azure com a integração do registo Azure (pré-visualização) | Microsoft Azure"
   description="Este artigo ajuda-o artigo introdução à integração de alertas do Centro de segurança com a integração do Azure registo."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Integração de alertas do Centro de segurança com a integração do registo Azure (pré-visualização)

Muitas operações de segurança e equipas de resposta incidente dependem uma solução de gestão de evento (SIEM) e informações de segurança como ponto de partida para triaging e alertas de segurança a investigar. Com a integração do registo Azure, clientes podem sincronizar alertas do Centro de segurança do Azure, juntamente com os eventos de segurança de máquina virtual recolhidos pelo diagnósticos do Azure e registos de auditoria Azure, com o respectivo registo analytics ou solução SIEM em tempo real.

Integração do registo Azure funciona com HP ArcSight, Splunk, IBM QRadar e outras pessoas.

## <a name="what-logs-can-i-integrate"></a>O que os registos que pode a integrar

Azure gera registo extenso para cada serviço. Estes registos são categorizados como:

- **Registos de gestão do controlo** que dão visibilidade para as operações criar de Gestor de recursos do Azure, ATUALIZAR e eliminar.
- **Registos de plano de dados** que dão visibilidade para os eventos elevado ao utilizar um recurso Azure. Um exemplo é o registo de eventos do Windows - segurança e registos de aplicações numa máquina virtual.

Integração do registo Azure atualmente suporta a integração do:

- Azure VM registos
- Registos de auditoria Azure
- Alertas do Centro de segurança do Azure

## <a name="install-azure-log-integration"></a>Instalar a integração do registo Azure

Transfira a [integração de iniciar sessão Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração de registo Azure recolhe dos dados de telemetria no computador onde está instalado.  Dados de telemetria recolhidos são:

- Exceções que ocorrer durante a execução de integração de registo Azure
- Métricas sobre o número de consultas e eventos processados
- Estatísticas sobre qual Azlog.exe estão a ser utilizadas opções de linha de comandos

> [AZURE.NOTE] Pode desativar a recolha de dados de telemetria desmarcando esta opção.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar o Centro de segurança e registos de auditoria Azure alertas

1. Abra a linha de comandos e o **cd** para **c:\Program Files\Microsoft Azure registo integração**.

2. Execute o comando **azlog createazureid** para criar um [Principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) no inquilinos Azure Active Directory (AD) que alojam as subscrições Azure.

    Será pedido para o início de sessão Azure.

    > [AZURE.NOTE] Tem de ser a subscrição proprietário ou administrador de cocriação da subscrição.

    Autenticação do Azure é feita através de Azure AD.  Criação de um capital de serviço de integração de registo Azure irá criar a identidade do Azure AD que será concedida acesso de leitura a partir do Azure subscrições.

3. Executar o **azlog autorizar <SubscriptionID> ** comando para atribuir acesso de leitor da subscrição para o capital de serviço que criou no passo 2. Se não especificar um **SubscriptionID**, em seguida, o capital de serviço será atribuído a função de leitor para todas as subscrições para a qual tem acesso.

    > [AZURE.NOTE] Poderá ver avisos se executar o comando **autorizar** imediatamente após o comando **createazureid** porque existe algum latência entre quando a conta do Azure AD é criada e quando a conta está disponível para utilização. Se Aguarde cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **autorizar** , deverá ver não estes avisos.

4. Verificar as seguintes pastas para confirmar se os ficheiros de JSON de registo de auditoria estão lá:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Verificar as seguintes pastas para confirmar que existem de alertas do Centro de segurança no-las:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Aponte o conector de reencaminhador de ficheiro SIEM padrão para a pasta adequada encaminhamento os dados para a instância SIEM. Consulte para as [configurações de SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) na sua configuração SIEM.

Se tiver questões sobre a integração de registo do Azure, envie um e-mail para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre registos de auditoria Azure e definições de propriedades, consulte o artigo:

- [Operações de auditoria com o Gestor de recursos](../resource-group-audit.md)
- [Listar os eventos de gestão de uma subscrição](https://msdn.microsoft.com/library/azure/dn931934.aspx) - para obter eventos de registo de auditoria.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do Azure Security](http://blogs.msdn.com/b/azuresecurity/) — obter as notícias mais recentes do Azure segurança e informações.
