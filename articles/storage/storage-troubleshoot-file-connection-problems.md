<properties
    pageTitle="Resolução de problemas de armazenamento do ficheiro Azure | Microsoft Azure"
    description="Resolução de problemas de armazenamento do ficheiro do Azure"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Resolução de problemas de armazenamento do ficheiro do Azure

Este artigo lista problemas comuns que estão relacionados com armazenamento de ficheiros do Microsoft Azure quando se liga a partir dos clientes Windows e Linux. Também fornece as causas possíveis e resoluções para estes problemas.

**Problemas gerais (ocorrem em clientes do Windows e Linux)**

- [Erro de quota ao tentar abrir um ficheiro](#quotaerror)

- [Desempenho lento quando aceder ao armazenamento de ficheiros do Azure a partir do Windows ou a partir do Linux](#slowboth)

**Problemas de cliente do Windows**

- [Desempenho lento quando aceder ao armazenamento de ficheiros do Azure a partir do Windows 8.1 ou Windows Server 2012 R2](#windowsslow)

- [Erro de 53 tentar montagem uma partilha de ficheiros do Azure](#error53)

- [Utilizar líquido foi efetuada com êxito mas não consigo ver o ficheiro Azure partilhar montada no Explorador do Windows](#netuse)

- [A minha conta de armazenamento contém "/" e o líquido utilizar falha de comando](#slashfails)

- [O meu pedido/serviço não consegue aceder a unidade de ficheiros do Azure montada.](#accessfiledrive)

- [Recomendações adicionais para otimizar o desempenho](#additional)

**Problemas de cliente Linux**

- [Erro "Qual está a copiar um ficheiro para um destino que não suporte encriptação" ao carregar/copiar ficheiros para ficheiros do Azure](#encryption)

- [Partilha de "Anfitrião é premida" erro no ficheiro existente ou a shell de deixa de responder quando efetuar a lista de comandos no ponto de montagem](#errorhold)

- [Erro de montagem 115 ao tentar aos ficheiros de montagem Azure na VM Linux](#error15)

- [VM Linux ocorrer atrasos aleatórios na comandos como "ls"](#delayproblem)

## <a name="general-problems"></a>Problemas gerais
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Erro de quota ao tentar abrir um ficheiro

No Windows, recebe mensagens de erro que ser semelhante ao seguinte:

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Não existe quota suficiente disponível para processar este comando**

**Valor inválido alça GetLastError: 53**

No Linux, recebe mensagens de erro que ser semelhante ao seguinte:

**<filename>[permissão negado]**

**Quota de disco excedida**

#### <a name="cause"></a>Causa

O problema ocorre porque atingiu o limite superior de identificadores abertos em simultâneo permitido para um ficheiro.

#### <a name="solution"></a>Solução

Reduzir o número de identificadores abertos em simultâneo fechando algumas alças e, em seguida, volte a tentar. Para mais informações, consulte [Microsoft Azure armazenamento desempenho e escalabilidade lista de verificação](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Desempenho lento ao aceder ao armazenamento de ficheiros a partir do Windows ou Linux

- Se não tiver um requisito de tamanho e/s mínimo específico, recomendamos que utilize 1 MB como o tamanho e/s para um desempenho ideal.

- Se souber o tamanho de um ficheiro que está a ampliar com escritas final e o software não tem problemas de compatibilidade quando cauda ainda não está escrito no ficheiro que contém os zeros à esquerda, em seguida, defina o tamanho do ficheiro com antecedência em vez de cada escrita a ser uma escrita expandir.

## <a name="windows-client-problems"></a>Problemas de cliente do Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Desempenho lento ao aceder ao armazenamento de ficheiros a partir do Windows 8.1 ou Windows Server 2012 R2

Para clientes que estiver a executar o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que a correcção [KB3114025](https://support.microsoft.com/kb/3114025) está instalada. Esta correcção melhora a criar e feche a alça de desempenho.

Pode executar o script para verificar se a correcção ter sido instalada no seguinte:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se correcção estiver instalada, é apresentado o seguinte resultado:

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1**

> [AZURE.NOTE]  Imagens do Windows Server 2012 R2 no Azure Marketplace têm a correcção KB3114025 instalado por predefinição, começando de Dezembro de 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Recomendações adicionais para otimizar o desempenho

Nunca crie ou abra um ficheiro para em cache e/s que está a pedir acesso de escrita, mas não acesso de leitura. Isto é, quando ligar **CreateFile ()**, especificar nunca apenas **GENERIC_WRITE**, mas sempre especificar **GENERIC_READ | GENERIC_WRITE**. Uma alça de só de leitura não é possível em cache pequenas escritas localmente, mesmo quando este é o identificador apenas aberto para o ficheiro. Isto impõe sanções um desempenho extremas escritas pequenas. Tenha em atenção que a "um" modo para CRT **fopen()** é aberta uma alça de só de leitura.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Erro 53" ao tentar montagem ou desmontar uma partilha de ficheiros do Azure

Este problema pode ser causado por seguintes condições:

#### <a name="cause-1"></a>Causa 1

"O erro de sistema 53 ocorreu. Acesso negado." Por motivos de segurança, ligações para partilhas de ficheiros do Azure são bloqueadas se não está encriptado o canal de comunicação e a tentativa de ligação não é efetuada a partir do Centro de dados do mesmo em que residem partilhas de ficheiros do Azure. Encriptação de canal de comunicação não é fornecida se o cliente do utilizador SO não suporta a encriptação SMB. Isto é indicado por um "erro de sistema 53 ocorreu. Acesso negado"mensagem de erro quando um utilizador tenta montagem uma partilha de ficheiros a partir no local ou de um centro de dados diferente. Windows 8, Windows Server 2012 e versões posteriores de cada pedido de negociação que inclui SMB 3.0, que suporta a encriptação.

#### <a name="solution-for-cause-1"></a>Solução para a causa 1

Ligar a partir de um cliente que cumpra os requisitos do Windows 8, Windows Server 2012 ou versões posteriores, ou que ligue a partir de uma máquina de virtual que está no Centro de dados do mesmo como a conta de armazenamento do Windows Azure que é utilizado para a partilha de ficheiros do Azure.

#### <a name="cause-2"></a>Causa 2

"Erro de sistema 53" quando que montagem uma partilha de ficheiros Azure pode ocorrer se porta 445 comunicação de saída ao centro de dados de ficheiros do Azure está bloqueada. Clique [aqui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver o resumo de ISPs que permitir ou não permitir acesso a partir de porta 445.

Comcast e algumas organizações de TI bloqueiam esta porta. Para compreender se esta é a razão atrás a mensagem "Erro de sistema 53", pode utilizar o Portqry para consultar o ponto final TCP:445. Se o ponto final TCP:445 é apresentado como filtrado, a porta TCP é bloqueada. Eis um exemplo de consulta:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se o TCP 445 a ser bloqueado por uma regra ao longo do caminho da rede, verá o seguinte resultado:

**Porta TCP 445 (serviço microsoft ds): FILTRADA**

Para mais informações sobre como utilizar o Portqry, consulte o artigo [Descrição do utilitário Portqry.exe da linha de comandos](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Solução para a causa 2

Trabalhar com a organização de TI para abrir a porta 445 saída para [intervalos Azure IP](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Causa 3

Também pode ser recebido "Erro de sistema 53" se NTLMv1 comunicação é activada no cliente. Está com dificuldades NTLMv1 ativado cria um cliente seguro menos. Por conseguinte, comunicação será bloqueada para os ficheiros do Azure. Para verificar se esta é a causa do erro, certifique-se de que a seguinte subchave de registo está definida para um valor de 3:

HKLM\System\CurrentControlSet\Control\LSA. > LmCompatibilityLevel.

Para mais informações, consulte o tópico de [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

#### <a name="solution-for-cause-3"></a>Solução para a causa 3

Para resolver este problema, reverta o valor de LmCompatibilityLevel na chave de registo HKLM\System\CurrentControlSet\Control\LSA. para o valor predefinido de 3.

Ficheiros Azure suporta apenas a autenticação NTLMv2. Certifique-se de que a política de grupo é aplicada aos clientes. Isto irá impedir este erro ocorrência. Isto é também considerado prática recomendada segurança. Para obter mais informações, consulte o artigo [como configurar os clientes utilizem NTLMv2 utilizando a política de grupo](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

A definição de política recomendada é **apenas resposta NTLMv2 enviar**. Isto corresponde a um valor de registo de 3. Os clientes utilizar apenas a autenticação NTLMv2 e utilizam segurança de sessão NTLMv2 se o servidor suporta-lo. Controladores de domínio aceitam autenticação LM, NTLM e NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Utilizar líquido foi efetuada com êxito mas não vir o ficheiro Azure partilhar montada no Explorador do Windows

#### <a name="cause"></a>Causa

Por predefinição, o Explorador do Windows não executar como administrador. Se executar **utilização líquida** a partir de uma linha de comandos de administrador, mapear a unidade de rede "Como administrador." Uma vez que as unidades mapeadas serem centrados em utilizador, a conta de utilizador que tem sessão iniciada numa não apresenta as unidades se estão instalados numa conta de utilizador diferentes.

#### <a name="solution"></a>Solução

Montagem partilhar a partir de uma linha de comandos não administrador. Em alternativa, pode seguir [Este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor de registo **EnableLinkedConnections** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>A minha conta de armazenamento contém "/" e o líquido utilizar falha de comando

#### <a name="cause"></a>Causa

Quando o comando **use líquido** é executado em linha de comandos (cmd.exe), é analisado adicionando "/" como opção de linha de comandos. Isto faz com que o mapeamento de unidade para falhar.

#### <a name="solution"></a>Solução

Pode utilizar qualquer um dos seguintes passos para resolver o problema:

• Utilize o seguinte comando do PowerShell:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

A partir de um ficheiro batch Isto pode ser feito como

`Echo new-smbMapping ... | powershell -command –`

• Colocar aspas em redor a tecla para contornar este problema, a menos que "/" é o primeiro caráter. Se for, utilizar o modo de interativo e introduza a palavra-passe separadamente ou gerar suas chaves para obter uma chave que não começa com o caráter de barra (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>O meu pedido/serviço não é possível aceder unidade Azure ficheiros montada

#### <a name="cause"></a>Causa

Unidades são instaladas por utilizador. Se a sua aplicação ou serviço está a ser executado numa conta de utilizador diferentes, os utilizadores não verá a unidade.

#### <a name="solution"></a>Solução

Montagem unidade da mesma conta de utilizador em qual é a aplicação. Isto pode ser feito utilizando ferramentas como psexec.

Em alternativa, pode criar um novo utilizador que tem os mesmos privilégios do que a conta de serviço ou sistema de rede e, em seguida, execute **cmdkey** e a **utilização líquida** dessa conta. O nome de utilizador deve ser o nome da conta de armazenamento e palavra-passe deve ser a chave de conta de armazenamento. Outra opção para **utilizar líquido** é para passar no nome da conta de armazenamento e a chave nos parâmetros de nome e palavra-passe de utilizador do comando **utilizar líquido** .

Depois de seguir estas instruções, poderá receber a seguinte mensagem de erro: "erro no sistema 1312 ocorreu. Não existe uma sessão de início de sessão especificado. Pode já ter sido terminado"quando executa **líquido utilizar** para a conta de serviço de rede do sistema. Se tal acontecer, certifique-se de que o nome de utilizador que lhe é transmitido **utilização líquida** inclui informações sobre o domínio (por exemplo: "[nome da conta de armazenamento]. file.core.windows .net").

## <a name="linux-client-problems"></a>Problemas de cliente Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Qual está a copiar um ficheiro para um destino que não suporte encriptação"

#### <a name="cause"></a>Causa

Ficheiros encriptados BitLocker podem ser copiados para ficheiros do Azure. No entanto, o armazenamento de ficheiros não suporta NTFS EFS. Por conseguinte, é provável que utilizam o EFS neste caso. Se tiver ficheiros que estão encriptados através do EFS, uma operação de cópia para o armazenamento de ficheiros pode falhar, a menos que o comando Copiar é desencriptar um ficheiro copiado.

#### <a name="workaround"></a>Solução

Para copiar um ficheiro para o armazenamento de ficheiros, primeiro tem de o desencriptar. Pode fazê-lo através de um dos seguintes métodos:

• Utilize **/d cópia**.

• Definir a seguinte chave de registo:

- Caminho = HKLM\Software\Policies\Microsoft\Windows\System
- Tipo de valor DWORD de =
- Nome = CopyFileAllowDecryptedRemoteDestination
- Valor = 1

No entanto, tenha em atenção que configurar a chave de registo afeta todas as operações de copiar para partilhas de rede.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Partilha de "Anfitrião é premida" erro no ficheiro existente ou a shell de deixa de responder quando executar a lista de comandos no ponto de montagem

#### <a name="cause"></a>Causa

Este erro ocorre no cliente Linux quando o cliente tem estado inativo por um determinado período de tempo. Quando este erro ocorre, o cliente desliga e a ligação do cliente o tempo limite.

#### <a name="solution"></a>Solução

Este problema agora fica fixo na kernel Linux como parte do [alterar conjunto](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), backport pendente para distribuição Linux.

Para funciona em torno este problema, suportar a ligação e evitar receber para uma fase idle, manter um ficheiro na partilha de ficheiro do Azure que tem de escrita periodicamente. Isto tem de ser uma operação de escrita, tal como reescrever a data de modificação/criado no ficheiro. Caso contrário, poderá obter resultados em cache e, a operação não poderá acionar a ligação.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>"Erro 115 montagem" ao tentar montagem Azure ficheiros na VM Linux

#### <a name="cause"></a>Causa

Linux distribuições ainda não suportam a funcionalidade de encriptação no SMB 3.0. Em algumas distribuições, o utilizador poderá receber uma mensagem de erro "115" se tentarem montagem Azure ficheiros utilizando SMB 3.0 devido a uma funcionalidade em falta.

#### <a name="solution"></a>Solução

Se o cliente de Linux SMB que é utilizado não suporta a encriptação, montagem Azure ficheiros utilizando SMB 2.1 a partir de uma VM Linux no Centro de dados do mesmo como a conta de armazenamento do ficheiro.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>VM Linux ocorrer atrasos aleatórios na comandos como "ls"

#### <a name="cause"></a>Causa

Isto pode ocorrer quando o comando de montagem não inclui a opção **serverino** . Sem **serverino**, o comando ls é executado um **stat** todos os ficheiros.

#### <a name="solution"></a>Solução

Verifique o **serverino** na sua entrada "/ etc/fstab":

azureuser.File.Core.Windows.NET/WMS/comer no/base/sampledir tipo cifs (rw, nodev, relatime, vers = 2.1, sec = ntlmssp, cache = estritamente, nome de utilizador = xxx, domínio = X, file_mode = 0755, dir_mode = 0755 serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Se não apresentar a opção **serverino** , desmontar e montagem Azure novamente ficheiros fazendo com que a opção de **serverino** selecionada.

## <a name="learn-more"></a>Saiba mais

- [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)

- [Introdução ao armazenamento de ficheiros do Azure em Linux](storage-how-to-use-files-linux.md)
