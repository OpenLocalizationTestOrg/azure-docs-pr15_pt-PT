<properties 
   pageTitle="Resolução de problemas de implementação de StorSimple | Microsoft Azure"
   description="Descreve como diagnosticar e corrigir erros que ocorrem quando implementar StorSimple pela primeira vez."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Resolução de problemas de implementação do dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo fornece úteis orientações resolução de problemas para a sua implementação do Microsoft Azure StorSimple. Descreve problemas comuns, causas possíveis e passos recomendados para o ajudar a resolver problemas que se poderá deparar ao configurar StorSimple. Esta informação se aplica ao dispositivo físico StorSimple no local e o dispositivo virtual StorSimple.

> [AZURE.NOTE] Problemas relacionados com a configuração de dispositivos poderá cara podem ocorrer quando implementar o dispositivo pela primeira vez ou pode ocorrem mais tarde, quando o dispositivo está operacional. Este artigo foca-se em resolução de problemas de implementação de pela primeira vez. Para resolver problemas de um dispositivo operacionais avançado, aceda a [resolução de problemas de dispositivo operacional](storsimple-troubleshoot-operational-device.md).

Este artigo também descreve as ferramentas para implementações StorSimple de resolução de problemas e fornece um exemplo de resolução de problemas passo a passo.

## <a name="first-time-deployment-issues"></a>Problemas de implementação de pela primeira vez

Se ocorrer um problema ao implementar o seu dispositivo pela primeira vez, considere o seguinte:

- Se estiver a resolver um dispositivo físico, certifique-se de que o hardware foi instalado e configurado como descrito em [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
- Verificar a pré-requisitos para implementação. Certifique-se de que tem todas as informações descritas na [lista de verificação de configuração de implementação](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Reveja as notas de lançamento StorSimple para ver se o problema é descrito. As notas de lançamento incluem soluções para problemas de instalação conhecidos. 

Durante a implementação de dispositivo, mais comuns problemas de que os utilizadores nominal ocorrer quando execute o Assistente de configuração e quando registam o dispositivo através do Windows PowerShell para StorSimple. (Utilizar o Windows PowerShell para StorSimple para registar e configurar o seu dispositivo StorSimple. Para obter mais informações sobre o registo de dispositivo, consulte o artigo [passo 3: configurar e registar o seu dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

As secções seguintes podem ajudá-lo a resolver problemas que encontrar ao configurar o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo do Assistente de configuração de pela primeira vez

Os passos seguintes resumem o processo do Assistente de configuração. Para obter informações detalhadas de configuração, consulte o artigo [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md).

1. Execute o cmdlet [Invocar HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar o Assistente de configuração que irá guiá-lo através dos passos restantes. 
2. Configurar a rede: o Assistente de configuração permite-lhe configurar definições de rede para a interface de 0 rede de dados no seu dispositivo StorSimple. Estas definições incluem o seguinte:
  - Virtual IP (VIP), máscara de sub-rede e gateway – o cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) é executado em segundo plano. Configura o endereço IP, máscara de sub-rede e gateway para a interface de 0 rede de dados no seu dispositivo StorSimple.
  - Servidor de DNS principal – o cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) é executado em segundo plano. -Configura as definições de DNS para a sua solução StorSimple.
  - Servidor NTP – o cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) é executado em segundo plano. -Configura as definições do servidor NTP para a sua solução StorSimple.
  - Proxy web opcional – o cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) é executado em segundo plano. -Conjuntos e permite a configuração do proxy web para a sua solução StorSimple.
3. Configurar as palavras-passe: é o próximo passo configurar o administrador do dispositivo e StorSimple instantâneo Gestor as palavras-passe. Se estiver a executar atualização 1, em seguida, não terá de configurar a palavra-passe StorSimple instantâneo gestor.
  - A palavra-passe de administrador do dispositivo é utilizada para iniciar sessão no seu dispositivo. A palavra-passe de dispositivo de predefinida é a **Palavra-passe1**.
  - A palavra-passe StorSimple instantâneo gestor é necessária quando configurar um dispositivo para utilizar o Gestor de instantâneo StorSimple. Tem de definir pela primeira vez a palavra-passe no Assistente de configuração e, em seguida, pode definir e alterá-lo a partir do serviço do Gestor de StorSimple. Esta palavra-passe autentica o dispositivo com o Gestor de instantâneo StorSimple.
 
    > [AZURE.IMPORTANT] As palavras-passe são recolhidas antes do registo, mas aplicadas apenas depois de registar o dispositivo com êxito. Se existir uma falha de para aplicar uma palavra-passe, ser-lhe-á pedido fornecer novamente a palavra-passe até que as palavras-passe necessárias (que cumprem os requisitos de complexidade) são recolhidas.

4. Registar o dispositivo: o passo final é registar o dispositivo com o serviço do Gestor de StorSimple em execução no Microsoft Azure. O registo requer que para [obter a chave de registo do serviço](storsimple-manage-service.md#get-the-service-registration-key) a partir do portal clássico Azure e forneça-lo no Assistente de configuração. Depois do dispositivo está registado com êxito, é fornecida uma chave de encriptação de dados do serviço para si. Certifique-se de que manter esta chave de encriptação numa localização segura pois estará necessário para registar todos os dispositivos subsequentes com o serviço.

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implementação de dispositivo

As tabelas seguintes listam os erros comuns que poderá encontrar quando:

- Configure as definições de rede necessários.
- Configure as definições de proxy web opcional.
- Configure o administrador do dispositivo e StorSimple instantâneo Gestor as palavras-passe. 
- Registe-se o dispositivo. 

## <a name="errors-during-the-required-network-settings"></a>Erros durante as definições de rede necessários

| Não.| Mensagem de erro | Causas possíveis | Acção recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invocar HcsSetupWizard: Este comando só pode ser executado no controlador ativo. | Configuração foi a ser executada no controlador passivo.| Execute este comando do controlador de ativo. Para mais informações, consulte o artigo [identificar um controlador ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | HcsSetupWizard invocar: Dispositivo não está pronto. | Existem problemas com a conectividade de rede dados 0.| Verifique a conectividade de rede física dados 0.|
| 3 | Invocar HcsSetupWizard: Existe um conflito de endereços IP com outro sistema da rede (exceção de HRESULT: 0x80070263). | IP fornecido para os dados 0 já estava a ser utilizado por outro sistema. | Fornece um novo IP não está em utilização.|
| 4 | Invocar HcsSetupWizard: Um recurso de cluster Ocorreu uma falha. (Exceção de HRESULT: 0x800713AE). | Duplica VIP. O PI fornecido já está a ser utilizado.| Fornece um novo IP não está em utilização.|
| 5 | Invocar HcsSetupWizard: IPv4 inválidos endereço. | O endereço IP é fornecido num formato incorreto.| Verificar o formato e forneça o seu endereço IP novamente. Para obter mais informações, consulte o artigo [Endereçamento Ipv4][1]. |
| 6 | Invocar HcsSetupWizard: O endereço IPv6 inválido. | O endereço IP é fornecido num formato incorreto.| Verificar o formato e forneça o seu endereço IP novamente. Para obter mais informações, consulte o artigo [Endereçamento Ipv6][2].|
| 7 | Invocar HcsSetupWizard: Não existem mais pontos finais disponíveis a partir de mapeamento de pontos finais. (Exceção de HRESULT: 0x800706D9) | A funcionalidade de cluster não está a trabalhar. | [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as definições de proxy web opcional

| Não.| Mensagem de erro | Causas possíveis | Acção recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | HcsSetupWizard invocar: Parâmetro inválido (exceção de HRESULT: 0x80070057) | Um dos parâmetros fornecidos para as definições de proxy não é válido.| O URI não é fornecido no formato correto. Utilize o seguinte formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | HcsSetupWizard invocar: Servidor RPC não disponível (exceção de HRESULT: 0x800706ba) | A causa de raiz é uma das seguintes opções:<ol><li>Cluster não está até.</li><li>O controlador de passivo não é possível comunicar com o controlador de ativo e o comando é executado a partir do controlador de passiva.</li></ol> | Dependendo do problema raiz:<ol><li>[Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para se certificar de que o cluster é para cima.</li><li>Execute o comando do controlador de ativo. Se pretender executar o comando do controlador de passivo, terá de para se certificar de que o controlador de passivo pode comunicar com o controlador de ativo. Terá de [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) se este conectividade é interrompida.</li></ol> |
| 3 | Invocar HcsSetupWizard: Chamada de RPC falhou (exceção de HRESULT: 0x800706be) | Cluster é premida. | [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para se certificar de que o cluster é para cima.|
| 4 | HcsSetupWizard invocar: Não foi encontrado o recurso de Cluster (exceção de HRESULT: 0x8007138f) | O recurso de cluster não se encontra. Isto pode acontecer quando a instalação não foi correta. | Poderá ter de repor o dispositivo para as predefinições de fábrica do mesmo. [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para criar um recurso de cluster.|
| 5 | HcsSetupWizard invocar: Não está online o recurso de Cluster (exceção de HRESULT: 0x8007138c)| Recursos de cluster não estão online. | [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Erros relacionados com o administrador do dispositivo e para o Gestor de instantâneo StorSimple palavras-passe

A palavra-passe de administrador do dispositivo de predefinida é a **Palavra-passe1**. Esta palavra-passe expira após o primeiro início de sessão; Por conseguinte, terá de utilizar o Assistente de configuração para alterá-la. Tem de fornecer uma palavra-passe do novo dispositivo administrador quando registar o dispositivo pela primeira vez. 

Se utilizar o software de StorSimple instantâneo gestor em execução no anfitrião do Windows Server para gerir o dispositivo, também deve fornecer uma palavra-passe do Gestor de instantâneo StorSimple durante o registo de pela primeira vez. 

Certifique-se de que as palavras-passe cumprem os seguintes requisitos:

- A palavra-passe de administrador do dispositivo deve estar entre 8 e 15 carateres de comprimento.
- A palavra-passe StorSimple instantâneo Gestor deve ser 14 ou 15 carateres de comprimento.
- As palavras-passe deverão conter a 3 dos seguintes tipos de 4 caráter: minúsculas, maiúsculas, numéricos e especial. 
- A palavra-passe não pode ser igual as últimas 24 palavras-passe.

Além disso, tenha em atenção que as palavras-passe expirarem todos os anos e podem ser alteradas apenas depois de registar o dispositivo com êxito. Se o registo falhar por qualquer motivo, as palavras-passe não serão alteradas. Para mais informações sobre o administrador do dispositivo e palavras-passe de StorSimple instantâneo Gestor, aceda a [utilizar o serviço do Gestor de StorSimple para alterar as palavras-passe StorSimple](storsimple-change-passwords.md).

Poderá encontrar um ou mais dos seguintes erros ao configurar o administrador do dispositivo e StorSimple instantâneo Gestor as palavras-passe.

| Não.| Mensagem de erro | Acção recomendada |
| ---| ------------- | ------------------ | 
| 1  | A palavra-passe excede o comprimento máximo. |Utilize uma palavra-passe que cumpra estes requisitos:<ul><li>A palavra-passe de administrador do dispositivo tem de estar entre 8 e 15 carateres de comprimento.</li><li>A palavra-passe StorSimple instantâneo gestor tem de ser 14 ou 15 carateres de comprimento.</li></ul> | 
| 2 | A palavra-passe não cumpre o comprimento necessário. | Utilize uma palavra-passe que cumpra estes requisitos:<ul><li>A palavra-passe de administrador do dispositivo tem de estar entre 8 e 15 carateres de comprimento.</li><li>A palavra-passe StorSimple instantâneo gestor tem de ser 14 ou 15 carateres de comprimento.</lu></ul> |
| 3 | A palavra-passe tem de conter carateres em minúsculas. | As palavras-passe tem de conter 3 dos seguintes tipos de 4 caráter: minúsculas, maiúsculas, numéricos e especial. Certifique-se de que a sua palavra-passe preenche estes requisitos. |
| 4 | A palavra-passe tem de conter carateres numéricos. | As palavras-passe tem de conter 3 dos seguintes tipos de 4 caráter: minúsculas, maiúsculas, numéricos e especial. Certifique-se de que a sua palavra-passe preenche estes requisitos. |
| 5 | A palavra-passe tem de conter carateres especiais. | As palavras-passe tem de conter 3 dos seguintes tipos de 4 caráter: minúsculas, maiúsculas, numéricos e especial. Certifique-se de que a sua palavra-passe preenche estes requisitos. |
| 6 | A palavra-passe tem de conter 3 dos seguintes tipos de 4 caráter: maiúsculas, minúsculas, numéricos e especial. | A palavra-passe não contém os tipos de caracteres necessários. Certifique-se de que a sua palavra-passe preenche estes requisitos. |
| 7 | Parâmetro não corresponder a confirmação. | Certifique-se de que a palavra-passe cumpre todos os requisitos e que introduziu corretamente. |
| 8 | A palavra-passe não pode corresponder a predefinição. | A palavra-passe predefinida é a *Palavra-passe1*. Tem de alterar esta palavra-passe depois de iniciar sessão pela primeira vez. |
| 9 | A palavra-passe que introduziu não corresponder a palavra-passe do dispositivo. Introduza novamente a palavra-passe. | Selecione a palavra-passe e escreva-a novamente. |

As palavras-passe são recolhidas antes do dispositivo está registado, mas são aplicadas apenas depois de registo com êxito. O fluxo de trabalho de recuperação de palavra-passe requer o dispositivo sejam registados. 

> [AZURE.IMPORTANT] Em geral, se uma tentativa para aplicar uma palavra-passe falhar, em seguida, o software repetidamente tenta recolher a palavra-passe até que seja efetuada com êxito. Em casos raros, não pode ser aplicada a palavra-passe. Esta situação, pode registar o dispositivo e continuar, no entanto as palavras-passe não serão alteradas. Não receberá nenhuma indicação como para que a palavra-passe não foi alterado — a palavra-passe de administrador do dispositivo ou a palavra-passe StorSimple instantâneo gestor. Se esta situação ocorre, recomendamos que altere ambas as palavras-passe.

Pode repor as palavras-passe no portal do Azure clássica através do serviço de Gestor de StorSimple. Para obter mais informações, aceda a: 

- [Alterar a palavra-passe de administrador do dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Alterar a palavra-passe StorSimple instantâneo Gestor](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Erros durante o registo do dispositivo

Utilizar o serviço do Gestor de StorSimple em execução no Microsoft Azure para registar o dispositivo. Pode encontrar um ou mais dos seguintes problemas durante o registo do dispositivo.

| Não.| Mensagem de erro | Causas possíveis | Acção recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Erro 350027: Falha ao registar o dispositivo com o Gestor de StorSimple. |  | Aguarde alguns minutos e, em seguida, tente novamente a operação. Se o problema persistir, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Erro 350013: Ocorreu um erro no registo do dispositivo. Isto pode ser devido a chave de registo do serviço incorretos. | | Novamente registe o dispositivo com a chave de registo do serviço correto. Para obter mais informações, consulte o artigo [obter a chave de registo do serviço.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Erro 350063: Autenticação de serviço do Gestor de StorSimple passado, registo, mas ocorreu uma falha. Volte a tentar a operação após algum tempo. | Este erro indica que a autenticação com ACS passou mas a chamada register feita ao serviço de falhou. Isto pode ser um resultado de um falhas de rede esporádica. | Se o problema persistir, utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Erro 350049: Não foi possível contactar o serviço durante o registo. | Quando a chamada é feita para o serviço, é recebida uma exceção da web. Em alguns casos, isto poderá obter corrigido com repetir a operação mais tarde. | Verifique o seu endereço de IP e o nome de DNS e, em seguida, repita a operação. Se o problema persistir, [contactar a Microsoft Support.](storsimple-contact-microsoft-support.md) | 
| 5 | Erro 350031: O dispositivo já foi registado. | | Necessária nenhuma ação. |
| 6 | Erro 350016: Falha do registo de dispositivo. | |Certifique-se que a chave de registo está correta. |
| 7 | Invocar HcsSetupWizard: Ocorreu um erro enquanto registar o seu dispositivo; Isto pode ser devido ao endereço IP incorrecto ou nome de DNS. Verifique as definições de rede e tente novamente. Se o problema persistir, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md). (Erro 350050) | Certifique-se de que o dispositivo pode executar o ping a rede externa. Se não tiver conectividade de rede externa, o registo pode falhar com este erro. Este erro pode ser uma combinação de um ou mais dos seguintes procedimentos:<ul><li>IP incorreto</li><li>Sub-rede incorrecto</li><li>Gateway incorreto</li><li>Definições de DNS incorretas</li></ul> | Consulte os passos no [exemplo passo a passo de resolução de problemas](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invocar HcsSetupWizard: A operação atual falhou devido a um erro interno serviço [0x1FBE2]. Volte a tentar a operação após algures. Se o problema persistir, contacte o Microsoft Support. | Este é um erro genérico iniciado para todos os erros de invisíveis de utilizador de serviço ou agente. O motivo mais comuns poderá ser que a autenticação de ACS falhou. Uma causa possível para a falha é que existem problemas relacionados com a configuração do servidor NTP e hora no dispositivo não estiver definida corretamente. | Corrigir o tempo (se existem problemas) e, em seguida, repita a operação de registo. Se utilizar o comando definir HcsSystem - fuso horário para ajustar o fuso horário, capitalizar cada palavra no fuso horário do (por exemplo "Hora do Pacífico padrão").  Se este problema persistir, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para os passos seguinte. |
| 9 | Aviso: Não foi possível ativar o dispositivo. O administrador do seu dispositivo e palavras-passe do Gestor de instantâneo StorSimple não foram alteradas. | Se o registo falhar, o administrador do dispositivo e palavras-passe do Gestor de instantâneo StorSimple não são alteradas. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Ferramentas para implementações StorSimple de resolução de problemas

StorSimple inclui várias ferramentas que pode utilizar para resolver a sua solução StorSimple. Estes incluem:

- Pacotes de suporte e registos de dispositivo 
- Cmdlets concebidos especificamente para resolução de problemas 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pacotes e dispositivo estão disponíveis registos de suporte para resolução de problemas

Um pacote de suporte contém todos os registos relevantes que o podem ajudar a equipa do Microsoft Support com a resolução de problemas do dispositivo. Pode utilizar o Windows PowerShell para StorSimple para gerar um pacote de suporte encriptada, em seguida, pode partilhar com pessoal de suporte.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver os registos de início ou os conteúdos do pacote do suporte

1. Utilizar o Windows PowerShell para StorSimple para gerar um pacote de suporte conforme descrito no [criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md).

2. Transferir o [script desencriptação](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no computador cliente.

3. Utilize este [procedimento passo a passo](storsimple-create-manage-support-package.md#edit-a-support-package) para abrir e desencriptar o pacote de suporte.

4. Os registos do pacote de suporte desencriptados estão no formato de etw/etvx. Pode efetuar os seguintes passos para ver estes ficheiros no Visualizador de eventos do Windows:
  1. Execute o comando **eventvwr** no seu cliente do Windows. Isto irá iniciar o Visualizador de eventos.
  2. No painel de **ações** , clique em **Abrir registo guardadas** e aponte para os ficheiros de registo no formato de etvx/etw (o pacote de suporte). Agora pode ver o ficheiro. Depois de abrir o ficheiro, pode com o botão direito e guarde o ficheiro como texto.
   
    > [AZURE.IMPORTANT] Também pode utilizar o cmdlet **Get-rotina WinEvent** para abrir estes ficheiros no Windows PowerShell. Para mais informações, consulte o artigo [Get-rotina WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência do cmdlet do Windows PowerShell.

5. Quando abrir os registos no Visualizador de eventos, procure os seguintes registos que contêm problemas relacionados com a configuração do dispositivo:

  - hcs_pfconfig/operacional registo
  - hcs_pfconfig/configuração

6. Nos ficheiros de registo, procure cadeias relacionadas com os cmdlets designados pelo Assistente de configuração. Consulte o artigo [processo do Assistente de configuração de pela primeira vez](#first-time-setup-wizard-process) para obter uma lista destes cmdlets. 

7. Se não for possível descobrir a causa do problema, pode [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes. Utilize os passos no [artigo criar um pedido de suporte](storsimple-contact-microsoft-support.md#create-a-support-request) quando contactar o Microsoft Support para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponíveis para resolução de problemas

Utilize os seguintes cmdlets do Windows PowerShell para detetar erros de conectividade.

- `Get-NetAdapter`: Utilize este cmdlet para detectar o estado de funcionamento das interfaces de rede. 

- `Test-Connection`: Utilize este cmdlet para verificar a conectividade de rede dentro e fora da rede.

- `Test-HcsmConnection`: Utilize este cmdlet para verificar a conectividade de um dispositivo registado com êxito.

Se estiver a executar atualização 1 no seu dispositivo StorSimple, também estão disponíveis os seguintes cmdlets de diagnóstico.

- `Sync-HcsTime`: Utilize este cmdlet para apresentar a hora de dispositivo e forçar uma sincronização de tempo com o servidor NTP.

- `Enable-HcsPing`e `Disable-HcsPing`: utilizar estes cmdlets para permitir que os anfitriões executar o ping as interfaces de rede no seu dispositivo StorSimple. Por predefinição, as interfaces de rede StorSimple não responder a pedidos de ping.

- `Trace-HcsRoute`: Utilize este cmdlet como uma ferramenta de rastreio encaminhar. Envia pacotes a cada router a forma de um destino final durante um período de tempo e, em seguida, fórmula calcula resultados com base em pacotes devolvidos de cada salto. Uma vez que `Trace-HcsRoute` mostra o grau de perda de pacotes em qualquer determinado router ou ligação, pode pinpoint quais routers ou ligações estão a causar problemas de rede. 

- `Get-HcsRoutingTable`: Utilize este cmdlet para apresentar a tabela de encaminhamento de IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Resolver problemas com o cmdlet Get-NetAdapter

Ao configurar as interfaces de rede para uma implementação do dispositivo pela primeira vez, o estado de hardware não está disponível no serviço do Gestor de StorSimple IU porque o dispositivo ainda não está registado com o serviço. Para além disso, a página Estado do Hardware poderá não sempre corretamente refletir o estado do dispositivo, especialmente se existirem problemas que afetam a sincronização de serviço. Nas seguintes situações, pode utilizar o `Get-NetAdapter` cmdlet para determinar o estado de funcionamento e o estado das interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede no seu dispositivo

1. Inicie o Windows PowerShell para StorSimple e, em seguida, escreva `Get-NetAdapter`. 

2. Utilizar a saída de `Get-NetAdapter` cmdlet e as seguintes diretrizes para compreender o estado da interface de rede.
  - Se a interface é estável e ativada, o estado de **ÍndiceDeIf** é apresentado como **o**.
  - Se a interface está em bom estada, mas não está ligada física (por um cabo de rede), o **ÍndiceDeIf** é apresentada como **desativada**.
  - Se a interface está saudável mas não ativado, o estado de **ÍndiceDeIf** é mostrado como **NotPresent**.
  - Se a interface não existir, não é apresentado nesta lista. O serviço do Gestor de StorSimple IU ainda irá mostrar esta interface num estado falhado.

Para mais informações sobre como utilizar este cmdlet, aceda a [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) na referência do cmdlet do Windows PowerShell. 

As secções seguintes mostram amostras de saída da `Get-NetAdapter` cmdlet. 

 Estes exemplos controlador de 0 era o controlador de passivo e tiver sido configurado da seguinte forma:

- DADOS 0, dados 1, 2 de dados e rede de dados 3 interfaces existiam no dispositivo.
- DADOS 4 e cartões de interface de rede de 5 de dados não foram presentes; Por conseguinte, não estão listados no resultado.
- DADOS 0 foram ativados.

Controlador de 1 era o controlador de ativo e tiver sido configurado da seguinte forma:

- DADOS 0, dados 1, 2 de dados, dados 3, 4 de dados e rede de dados 5 interfaces existiam no dispositivo.
- DADOS 0 foram ativados.

**Amostra saída – controlador de 0**

Segue-se o resultado do controlador de 0 (o controlador passivo). DADOS 1, dados 2 e 3 de dados não estão ligado. DADOS 4 e 5 de dados não são incluídas uma vez que não estão presentes no dispositivo. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Amostra saída – controlador de 1**

Segue-se o resultado do controlador de 1 (o controlador ativo). Apenas os dados 0 interface da rede no dispositivo está configurada e a funcionar.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Resolver problemas com o cmdlet Testar ligação

Pode utilizar o `Test-Connection` cmdlet para determinar se o seu dispositivo StorSimple pode ligar à rede externa. Se todos os parâmetros de redes, incluindo o DNS estiverem configurados corretamente no Assistente de configuração, pode utilizar o `Test-Connection` cmdlet para executar o ping um endereço conhecido fora da rede, tais como o outlook.com. 

Devo ativar ping resolver problemas de conectividade com este cmdlet, se o ping estiver desativado.

Consulte os exemplos seguintes da saída a partir de `Test-Connection` cmdlet. 

> [AZURE.NOTE] Na primeira amostra, o dispositivo está configurado com um DNS incorreto. No segundo exemplo, o DNS está correto.
 
**Amostra saída – DNS incorretos**

No exemplo seguinte, não existe nenhuma exportação para os endereços IPV4 e IPV6, que indica que o DNS não for resolvido. Isto significa que não haja nenhuma conectividade para a rede externa e precisa de ser fornecido um DNS correto. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exemplo de saída – corrigir DNS**

No exemplo seguinte, o DNS devolve o endereço de IPV4, que indica que o DNS está configurado corretamente. Isto confirma que existe conectividade de rede externa. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Resolver problemas com o cmdlet HcsmConnection de teste

Utilizar o `Test-HcsmConnection` cmdlet para um dispositivo que já está ligado à e registado com o seu serviço de Gestor de StorSimple. Este cmdlet ajuda-o a verificar a ligação entre um dispositivo registado e o serviço do Gestor de StorSimple correspondente. Pode executar este comando no Windows PowerShell para StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o cmdlet HcsmConnection de teste

1. Certifique-se de que o dispositivo está registado.

2. Verifique o estado do dispositivo. Se o dispositivo está desativado no modo de manutenção ou offline, poderá ver os seguintes erros: 

   - ErrorCode.CiSDeviceDecommissioned – Isto indica que o dispositivo está desativado.
   - ErrorCode.DeviceNotReady – Isto indica que o dispositivo está no modo de manutenção.
   - ErrorCode.DeviceNotReady – Isto indica que o dispositivo não está online.

3. Certifique-se de que o serviço do Gestor de StorSimple está em execução (utilize o cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Se o serviço não está em execução, poderá ver os seguintes erros:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – Isto indica que ocorreu uma exceção quando executou Get-ClusterResource.

4. Verifique o token de serviço de controlo de acesso (ACS). Se de que inicia uma exceção da web, poderá o resultado de um problema de gateway, um método de autenticação de proxy em falta, um DNS incorreto ou uma falha de autenticação. Poderá ver os seguintes erros:

   - ErrorCode.CiSApplianceGateway – Isto indica uma exceção HttpStatusCode.BadGateway: o serviço de resolução do nome não foi possível resolver o nome do anfitrião. 
   - ErrorCode.CiSApplianceProxy – Isto indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): não foi possível autenticar o cliente com o servidor de proxy. 
   - ErrorCode.CiSApplianceDNSError – Isto indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução do nome não foi possível resolver o nome do anfitrião.
   - ErrorCode.CiSApplianceACSError – Isto indica que o serviço devolvido um erro de autenticação, mas existe conectividade.
   
    Se não-gerar uma exceção de web, verifique ErrorCode.CiSApplianceFailure. Isto indica que o aparelho falhou.

5. Verifique a conectividade de serviço de nuvem. Se o serviço inicia uma exceção da web, poderá ver os seguintes erros:

  - ErrorCode.CiSApplianceGateway – Isto indica uma exceção HttpStatusCode.BadGateway: um servidor proxy intermédio recebeu um pedido incorretas a partir de outro proxy ou do servidor original.
  - ErrorCode.CiSApplianceProxy – Isto indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): não foi possível autenticar o cliente com o servidor de proxy. 
  - ErrorCode.CiSApplianceDNSError – Isto indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução do nome não foi possível resolver o nome do anfitrião.
  - ErrorCode.CiSApplianceACSError – Isto indica que o serviço devolvido um erro de autenticação, mas existe conectividade.
  
    Se não-gerar uma exceção de web, verifique ErrorCode.CiSApplianceSaasServiceError. Isto indica um problema com o serviço do Gestor de StorSimple.
 
6. Verifique a conectividade de Azure Service Bus. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não é possível ligar ao serviço bus.
 
O registo de ficheiros CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terá de obter mais informações, tais como detalhes de exceção. 

Para mais informações sobre como utilizar o cmdlet, aceda a [Teste HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) no Windows PowerShell documentação de referência.

> [AZURE.IMPORTANT] Pode executar este cmdlet para o active e o controlador de passivo. 
 
Consulte os exemplos seguintes da saída a partir de `Test-HcsmConnection` cmdlet. 

**Exemplo de saída – registado com êxito dispositivo com o lançamento StorSimple (de Julho de 2014)**

A primeira amostra é a partir de um dispositivo que está registado com êxito com o serviço do Gestor de StorSimple e possui sem problemas de conectividade. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exemplo de resultado – dispositivo registado com êxito em execução StorSimple atualização 1**

Se estiver a executar atualização 1 no seu dispositivo StorSimple, não terá de executá-la com o parâmetro verboso.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exemplo de saída – offline dispositivo com o lançamento StorSimple (de Julho de 2014)**

Este exemplo é a partir de um dispositivo que tenha um estado **Offline** no portal do Azure clássico.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

O dispositivo não foi possível ligar utilizando a configuração do proxy web atual. Isto pode ser um problema com a configuração do proxy web ou um problema de conectividade de rede. Neste caso, deve certificar-se de que as definições de proxy web estão corretas e servidores proxy web estão online e acessíveis. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Resolver problemas com o cmdlet HcsTime de sincronização

Utilize este cmdlet para apresentar o tempo de dispositivo. Se o tempo de dispositivo tem um desvio com o servidor NTP, em seguida, pode utilizar este cmdlet para sincronizar forçar da hora com o seu servidor NTP. Se o desfasamento entre o dispositivo e servidor NTP for maior que 5 minutos, irá ver um aviso. Se o desvio exceder 15 minutos, o dispositivo subscreve offline. Ainda pode utilizar este cmdlet para forçar uma sincronização de tempo. No entanto, se o desvio exceder 15 horas, em seguida, não será capaz de serão apresentados a hora e uma mensagem de erro de sincronização de forçar.

**Exemplo de resultado – sincronizar de tempo forçada utilizando HcsTime de sincronização**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Resolver problemas com os cmdlets HcsPing ativar e desativar HcsPing

Utilize estes cmdlets para se certificar de que as interfaces de rede no seu dispositivo respondem a pedidos de ICMP ping. Por predefinição as interfaces de rede StorSimple não responder a pedidos de ping. Utilizar este cmdlet é a forma mais fácil de saber se o seu dispositivo está online e acessíveis.  

**Exemplo de resultado – HcsPing ativar e desativar HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Resolver problemas com o cmdlet rastreio HcsRoute

Utilize este cmdlet como uma ferramenta de rastreio encaminhar. Envia pacotes a cada router a forma de um destino final durante um período de tempo e, em seguida, fórmula calcula resultados com base em pacotes devolvidos de cada salto. Uma vez que o cmdlet mostra o grau de perda de pacotes em qualquer router determinado ou ligação, pode pinpoint quais routers ou ligações estão a causar problemas de rede.

**Exemplo de resultado que mostra como rastrear a rota de um pacote com rastreio HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Resolver problemas com o cmdlet Get-HcsRoutingTable

Utilize este cmdlet para ver a tabela de encaminhamento para o seu dispositivo StorSimple. Uma tabela de encaminhamento é um conjunto de regras que podem ajudar a determinar onde os pacotes de dados em viagem através de uma rede de protocolo Internet (IP) serão direcionados. 

A tabela de encaminhamento mostra as interfaces e o gateway que encaminha os dados para as redes especificadas. Também lhe dá a métrica do encaminhamento que é o decisor para o caminho redirecionado para alcançar um destino específico. No canto inferior a métrica do encaminhamento, quanto maior a preferência. 

Por exemplo, se tiver 2 interfaces de rede, dados 2 e 3 de dados, ligado à Internet. Se o métricas de encaminhamento para dados 2 e 3 de dados são 15 e 261 respetivamente, 2 de dados com a métrica do encaminhamento inferior é a interface preferida utilizada para chegar à Internet.

Se estiver a executar atualização 1 no seu dispositivo StorSimple, a interface de 0 rede de dados tem a preferência para o tráfego de nuvem mais alta. Isto significa que, mesmo se existirem outras interfaces com capacidade de nuvem, o tráfego de nuvem seria encaminhado através dos dados 0. 

Se executar o `Get-HcsRoutingTable` cmdlet sem especificar quaisquer parâmetros (como mostra o exemplo seguinte), o cmdlet vai exportar, tabelas de encaminhamento IPv4 e IPv6. Em alternativa, pode especificar `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` para obter uma tabela de encaminhamento relevante.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemplo de resolução de problemas de StorSimple passo a passo

O exemplo seguinte mostra passo a passo de resolução de problemas de uma implementação StorSimple. Cenário de exemplo, o registo de dispositivo falhar com uma mensagem de erro indicando que as definições de rede ou o nome de DNS está incorreto.

A mensagem de erro devolvida é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ser causado por um dos seguintes procedimentos:

- Instalação de hardware incorretos
- Interfaces de rede danificada
- Endereço IP incorrecto, máscara de sub-rede, gateway, servidor de DNS primário ou proxy da web
- Chave de registo incorreto
- Definições da firewall incorretos

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registo de dispositivo

1. Verifique a configuração do dispositivo: no controlador de ativo, executar `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] Tem de executar o Assistente de configuração no controlador ativo. Para verificar que estão ligadas para o controlador de ativo, veja a faixa apresentada na consola do série. A faixa indica se estiver ligado a ou controlador de 0 1 e, se o controlador de está ativa ou passiva. Para obter mais informações, vá para [identificar um controlador ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Certifique-se de que o dispositivo está ligado corretamente: Verifique a cabos no dispositivo novamente o plano de rede. Os cabos são específico para o modelo de dispositivo. Para obter mais informações, aceda ao [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Se estiver a utilizar 10 portas de rede GbE, terá de utilizar o fornecido QSFP SFP placas e cabos SFP. Para mais informações, consulte a [lista de cabos, parâmetros e máximo recomendado pelo fornecedor OEM para portas Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Verificar o estado de funcionamento da interface de rede:

   - Utilize o cmdlet Get-NetAdapter para detectar o estado de funcionamento das interfaces de rede para os dados 0. 
   - Se a ligação não está a funcionar, o estado de **ÍndiceDeIf** irá indicar que a interface é premida. Em seguida, terá de verificar a ligação de rede da porta para o aparelho e para o parâmetro. Também terá de regra de saída cabos incorretas. 
   - Se suspeitar que os dados 0 porta no controlador ativa tiver falhado, pode confirmar isto ligando-se aos dados porta 0 no controlador de 1. Para o confirmar, desligue o cabo de rede de trás do dispositivo do controlador de 0, ligue o cabo para controlador de 1 e, em seguida, execute o cmdlet Get-NetAdapter novamente. 
   Se os dados 0 porta um controlador falhar, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes. Poderá ter de substituir o controlador no sistema de.
 
4. Verifique a ligação para o parâmetro:
   - Certifique-se de que as interfaces de 0 rede de dados no controlador de 0 e controlador 1 no seu inclusão primária estão na mesma sub-rede. 
   - Verifique o concentrador ou router. Normalmente, deve ligar ambos os controladores da mesma concentrador ou router. 
   - Certifique-se de que os parâmetros de que utilizar para a ligação têm dados 0 para ambos os controladores no mesmo vLAN.
   
5. Elimine qualquer erros de utilizador:

  - Execute novamente o Assistente de configuração (executar **Invocar HcsSetupWizard**) e introduza os valores novamente para se certificar de que não existem erros. 
  - Verifique o registo de chave utilizada. A mesma chave de registo pode ser utilizada para ligar vários dispositivos para um serviço de Gestor de StorSimple. Utilize o procedimento na [obter a chave de registo do serviço](storsimple-manage-service.md#get-the-service-registration-key) para se certificar de que está a utilizar a chave de registo correto.

    > [AZURE.IMPORTANT] Se tiver vários serviços em execução, terá de se certificar de que a chave de registo para o serviço adequado é utilizada para registar o dispositivo. Se ter registado um dispositivo com o serviço do Gestor de StorSimple mal, terá de [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes. Poderá ter de efetuar a reposição de fábrica do dispositivo (que poderá resultar na perda de dados) para, em seguida, ligá-lo para o serviço pretendido.

6. Utilize o cmdlet Testar ligação para verificar que tem ligação à rede externa. Para obter mais informações, aceda a [resolução de problemas com o cmdlet testar a ligação](#troubleshoot-with-the-test-connection-cmdlet).

7. Verificar a existência interferências de firewall. Caso tenha verificado o que as definições de IP (VIP), sub-rede, gateway e DNS virtuais estão todas as corretas e continuar a ver problemas de conectividade, em seguida, é possível que está a firewall a bloquear comunicação entre o seu dispositivo e a rede externa. Tem de garantir que as portas 80 e 443 estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para mais informações, consulte o artigo [requisitos para o seu dispositivo StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Veja os registos. Aceda a [pacotes de suporte e disponíveis para resolução de problemas de registos de dispositivo](#support-packages-and-device-logs-available-for-troubleshooting).

9. Se os passos anteriores não resolver o problema, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

## <a name="next-steps"></a>Próximos passos
[Saiba como resolver problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
