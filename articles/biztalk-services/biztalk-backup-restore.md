<properties 
    pageTitle="Criar e restaurar uma cópia de segurança nos serviços do BizTalk | Microsoft Azure" 
    description="Serviços de BizTalk inclui cópia de segurança e restauro. Saiba como criar e restaurar uma cópia de segurança e determinar o que obtém cópias de segurança. MAK, MAB, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-backup-and-restore"></a>BizTalk serviços: Cópia de segurança e restauro

Azure BizTalk serviços inclui capacidades de cópia de segurança e restauro. Este tópico descreve como fazer cópia de segurança e restaurar BizTalk serviços através do portal clássico Azure.

Também pode agregar BizTalk Services utilizando a [BizTalk serviços REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Híbrido ligações não são cópia de segurança, independentemente da edição. Tem de recriar suas ligações híbrido.

## <a name="before-you-begin"></a>Antes de começar

- Cópia de segurança e restauro poderão não estar disponível para todas as edições. Consulte o artigo [BizTalk serviços: edições gráfico](biztalk-editions-feature-chart.md).

- Utilizando o portal clássico Azure, pode criar uma cópia de segurança no pedido ou criar uma cópia de segurança agendada. 

- Conteúdo de cópia de segurança pode ser restaurado para o mesmo serviço BizTalk ou para um novo serviço BizTalk. Para restaurar o serviço de BizTalk utilizando o mesmo nome, o serviço de BizTalk existente têm de ser eliminado e o nome tem de estar disponível. Depois de eliminar um serviço de BizTalk, pode demorar mais tempo do que pretendia para o mesmo nome esteja disponível. Se não puder aguardar que o mesmo nome esteja disponível, em seguida, restaure a um novo serviço BizTalk.

- Serviços de BizTalk podem ser restaurados mesmo de edição ou uma edição superior. Restaurar BizTalk serviços para uma edição inferior, a partir do quando a cópia de segurança foi redirecionada, não é suportada.

    Por exemplo, uma cópia de segurança com a edição básica pode ser restaurada para a edição de Premium. Não pode ser restaurada uma cópia de segurança com a edição de Premium Standard Edition.

- Os números de controlo de editar cópia de segurança abrange para manter a continuidade dos números de controlo. Se as mensagens são processadas após a última cópia de segurança, restaurar este conteúdo de cópia de segurança pode causar números de controlo duplicados.

- Se um lote de tem mensagens ativas, processa o lote de **antes de** executar uma cópia de segurança. Ao criar uma cópia de segurança (conforme necessário ou agendada), mensagens em lotes nunca são armazenadas. 

    **Se uma cópia de segurança é redirecionada com as mensagens ativas num lote, estas mensagens não são cópia de segurança e, por conseguinte, são perdidas.**

- Opcional: No Portal de serviços BizTalk, pare qualquer operações de gestão.


## <a name="create-a-backup"></a>Criar uma cópia de segurança

Uma cópia de segurança pode ser tidos em qualquer altura e completamente é controlada pelo utilizador. Esta secção apresenta os passos para criar cópias de segurança através do portal clássico Azure, incluindo:

[Na cópia de segurança a pedido](#backupnow)

[Agendar uma cópia de segurança](#backupschedule)

#### <a name="backupnow"></a>Na cópia de segurança a pedido
1. No portal do Azure clássico, selecione **BizTalk serviços**e, em seguida, selecione o serviço de BizTalk que pretende fazer cópia de segurança.
2. No separador **Dashboard** , selecione **fazer cópia de segurança** na parte inferior da página.
3. Introduza um nome de cópia de segurança. Por exemplo, introduza *myBizTalkService*BU*data*.
4. Selecione uma conta de armazenamento de BLOBs e selecione a marca de verificação para iniciar a cópia de segurança.

Assim que a cópia de segurança for concluída, um contentor com o nome da cópia de segurança que introduzir é criado na conta de armazenamento. Neste contentor contém a configuração de cópia de segurança do serviço de BizTalk.

#### <a name="backupschedule"></a>Agendar uma cópia de segurança

1. No portal do Azure clássico, selecione **BizTalk serviços**, selecione o nome do serviço de BizTalk que pretende agendar a cópia de segurança e, em seguida, selecione o separador de **Configurar** .
2. Defina o **Estado de cópia de segurança** para **Automático**. 
3. Selecione a **Conta de armazenamento** para armazenar a cópia de segurança, introduza a **frequência** para criar cópias de segurança e durante quanto tempo para manter as cópias de segurança (**Retenção dias**):

    ![][AutomaticBU]

    **Notas**   
    - Em **Dias de retenção**, o período de retenção tem de ser maior do que a frequência de cópia de segurança.
    - Selecione **manter sempre pelo menos uma cópia de segurança**, mesmo se for passado o período de retenção.
    

4. Selecione **Guardar**.


Quando executa uma tarefa de cópia de segurança agendada, cria um contentor (para armazenar dados de cópia de segurança) na conta de armazenamento que introduziu. O nome do contentor é denominado *BizTalk serviço nome-data-hora*. 

Se o dashboard do serviço de BizTalk mostra um estado **falhou** :

![Estado da cópia de segurança agendado última][BackupStatus] 

A ligação abre-se os registos de operação de serviços de gestão para ajudar a resolver. Consulte o artigo [BizTalk serviços: Resolução de problemas com os registos de operação](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restaurar

Pode restaurar as cópias de segurança a partir do portal do Azure clássico ou de [Restaurar BizTalk serviço REST API](http://go.microsoft.com/fwlink/p/?LinkID=325582). Esta secção apresenta os passos para restaurar através do portal clássico.

#### <a name="before-restoring-a-backup"></a>Antes de restaurar uma cópia de segurança

- Novo rastreio, arquivar e monitorização armazena podem ser introduzidos enquanto restaurar um serviço de BizTalk.

- Os mesmos dados de tempo de execução de editar são restaurados. A cópia de segurança de editar Runtime armazena os números de controlo. Os números de controlo restaurado são sequência da hora da cópia de segurança de. Se as mensagens são processadas após a última cópia de segurança, restaurar este conteúdo de cópia de segurança pode causar os números de controlo duplicados.

#### <a name="restore-a-backup"></a>Restaurar uma cópia de segurança

1. No portal do Azure clássico, selecione **Novo** > **Serviços de aplicação** > **BizTalk serviço** > **Restaurar**:

    ![Restaurar uma cópia de segurança][Restore]

2. Na **Cópia de segurança URL**, selecione o ícone de pasta e expandir a conta de armazenamento Azure que armazena a cópia de segurança de configuração do serviço de BizTalk. Expanda o contentor e no painel direito, selecione o correspondente para subir. txt. 
<br/><br/>
Selecione **Abrir**.

3. Na página do **Serviço de BizTalk restaurar** , introduza um **Nome de serviço BizTalk** e verifique se o **URL do domínio**, **edição**e **região** para o serviço de BizTalk restaurados. **Criar uma nova instância de base de dados do SQL** para a base de dados de controlo:

    ![][RestoreBizTalkService]

    Selecione a seta seguinte.

4.  Verificar o nome da base de dados SQL, introduza o servidor físico onde a base de dados do SQL será criado e uma nome de utilizador/palavra-passe para que o servidor.


    Se pretende configurar a edição de base de dados SQL, tamanho e outras propriedades, selecione **Configurar definições avançadas de base de dados**. 

    Selecione a seta seguinte.

5. Criar uma nova conta de armazenamento ou introduza uma conta de armazenamento existente para o serviço de BizTalk.

7. Selecione a marca de verificação para iniciar o restauro.

Assim que o restauro for concluída com êxito, um novo serviço BizTalk está listado num estado suspenso na página BizTalk Services no portal do Azure clássica.



### <a name="postrestore"></a>Depois de restaurar uma cópia de segurança

O serviço de BizTalk sempre é restaurado em estado **suspenso** . Neste estado, pode efetuar alterações à configuração antes do novo ambiente está funcional, incluindo:

- Se tiver criado utilizando o SDK do Azure BizTalk serviços de aplicações de serviço de BizTalk, poderá ter para atualizar as credenciais de controlo de acesso (ACS) essas aplicações para trabalhar com o ambiente restaurado.

- Restaurar um serviço de BizTalk para criar uma réplica um ambiente existente do serviço de BizTalk. Esta situação, se existirem acordos configurados no portal do BizTalk serviços original que utilizam uma pasta FTP de origem, poderá ter de atualizar os acordos no ambiente recentemente restaurado para utilizar uma pasta FTP origem diferentes. Caso contrário, poderão existir dois acordos diferentes tentar separar a mesma mensagem.

- Se restaurado ter vários ambientes de serviço de BizTalk, certifique-se de que direcionar o ambiente correto nas aplicações do Visual Studio, os cmdlets do PowerShell, REST APIs ou negociação APIs de OM de gestão de parceiro.

- É aconselhável para configurar as cópias de segurança automatizadas no ambiente de serviço de BizTalk recentemente restaurado.

Para iniciar o serviço de BizTalk no portal do Azure clássico, selecione o serviço de BizTalk restaurado e selecione **currículo** na barra de tarefas. 



## <a name="what-gets-backed-up"></a>O que obtém cópias de segurança

Quando é criada uma cópia de segurança, os itens seguintes estão cópia de segurança:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Itens de cópia de segurança</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de serviços de Azure BizTalk</strong></td>
</tr> 
<tr>
<td>Configuração e Runtime</td> 
<td>
<ul>
<li>Detalhes do perfil e parceiro</li>
<li>Acordos do parceiro</li>
<li>Assemblagem personalizada implementada</li>
<li>Pontes implementado</li>
<li>Certificados</li>
<li>Transformações implementadas</li>
<li>Tubagens</li>
<li>Modelos que criou e guardou no Portal de serviços do BizTalk</li>
<li>X12 mapeamentos ST01 e GS01</li>
<li>Números de controlo (editar)</li>
<li>Valores de Microfone de mensagem AS2</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Serviço de BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados de certificado SSL</li>
<li>Palavra-passe de certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Definições de serviço de BizTalk</td> 
<td>
<ul>
<li>Contagem de unidade de escala</li>
<li>Edition</li>
<li>Versão do produto</li>
<li>Região/Centro de dados</li>
<li>Espaço de nomes de serviço de controlo (ACS) de acesso e chave</li>
<li>Cadeia de ligação de base de dados de controlo</li>
<li>Cadeia de ligação de conta de armazenamento do arquivo</li>
<li>Cadeia de ligação de conta de armazenamento de monitorização</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens adicionais</strong></td>
</tr> 
<tr>
<td>Controlo da base de dados</td> 
<td>Quando o serviço de BizTalk é criado, são introduzidos os detalhes de base de dados de controlo, incluindo o servidor de base de dados do SQL Azure e o nome de base de dados de controlo. A base de dados de controlo não é feita automaticamente cópia.
<br/><br/>
<strong>Importante</strong><br/>
Se a base de dados de controlo é eliminada e recuperados as necessidades de base de dados, tem de existir uma cópia de segurança anterior. Se não existir uma cópia de segurança, a base de dados de controlo e os seus dados não estão recuperáveis. Esta situação, crie uma nova base de dados de controlo com o mesmo nome de base de dados. É recomendada geo replicação.</td>
</tr> 
</table>

## <a name="next"></a>Seguinte

Para criar Azure BizTalk serviços no portal do Azure clássico, aceda a [BizTalk serviços: portal clássico de aprovisionamento Azure utilizando](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criação de aplicações, aceda a [Azure BizTalk serviços](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
- [Serviço de cópia de segurança BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar BizTalk serviço a partir da cópia de segurança](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços de BizTalk: Gráfico de estado de aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços de BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
