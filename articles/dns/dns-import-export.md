<properties
   pageTitle="Importar e exportar um ficheiro de zona de domínio para DNS Azure utilizando o clip | Microsoft Azure"
   description="Saiba como importar e exportar um ficheiro de zona DNS para Azure DNS utilizando o clip do Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um ficheiro de zona DNS com o clip do Azure


Este artigo irá guiá-lo através de como importar e exportar ficheiros de zona DNS para o DNS do Azure utilizando o clip do Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introdução a migração de zona DNS

Um ficheiro de zona DNS é um ficheiro de texto que contém os detalhes de cada registo do sistema de nomes de domínio (DNS) na zona. Segue-lo num formato padrão, tornando adequado para a transferir os seus registos DNS entre os sistemas DNS. Utilizar um ficheiro de zona é uma forma rápida, fiável e conveniente para transferir uma zona DNS ou desaparecer Azure DNS.

Azure DNS suporta a importação e exportação de ficheiros de zona utilizando a interface da linha de comandos Azure (CLI). O clip do Azure é uma ferramenta de linha de comandos em diferentes plataformas, usada para gerir serviços Azure. Está disponível para as plataformas Windows, Mac e Linux a partir da [página de transferências Azure](https://azure.microsoft.com/downloads/). Suporte de plataforma cruzada é particularmente importante para importar e exportar ficheiros de zona, porque o software de servidor de nome mais comuns, [VINCULAR](https://www.isc.org/downloads/bind/), normalmente é executada no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obter o seu ficheiro de zona DNS existente

Antes de importar um ficheiro de zona DNS para o Azure DNS, terá de obter uma cópia do ficheiro de zona. A origem deste ficheiro será dependem onde a zona de DNS está alojada atualmente.

- Se o seu DNS zone é alojado por um serviço de parceiro (tal como uma entidade de registo, dedicado fornecedor de alojamento de DNS ou fornecedor de nuvem alternativo), desse serviço deve fornecer a capacidade de transferir o ficheiro de zona DNS.

- Se o seu DNS zone estiver alojada num DNS do Windows, a pasta predefinida para os ficheiros de zona é **%systemroot%\system32\dns**. Também mostra o caminho completo para cada ficheiro de zona no separador **Geral** da consola de gestão de serviço de DNS.

- Se o seu DNS zone é alojado utilizando VINCULAR, a localização do ficheiro de zona para cada zona especificada na de ficheiro de configuração de VINCULAR **named.conf**.

**Trabalhar com ficheiros de zona da GoDaddy**<BR>
Ficheiros de zona transferidos a partir da GoDaddy têm um formato não ligeiramente padrão. É necessário corrigir este problema antes de importar estes ficheiros de zona para Azure DNS. Nomes de DNS na rdados de cada registo DNS estão especificados como nomes totalmente qualificados, mas que não tenham uma terminar "." Isto significa que são interpretadas por outros sistemas DNS como nomes relativos. Necessite de editar o ficheiro de zona acrescentar a terminar "." para os seus nomes antes de importá-los para Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um ficheiro de zona DNS para o DNS do Azure


Importar um ficheiro de zona, irá criar uma nova zona no Azure DNS em se um ainda não existir. Se já existir à zona, conjuntos de registos no ficheiro de zona têm de ser intercalados com os conjuntos de registos existentes.

### <a name="merge-behavior"></a>Comportamento de impressão em série

- Por predefinição, conjuntos de registos novos e existentes são intercalados. Registos idênticos dentro de um conjunto de registos Unido são retirar duplicados.

- Em alternativa, especificando o `--force` opção, o processo de importação irá substituir existente conjuntos de registos com os novos conjuntos de registos. Conjuntos de registo existente que não têm um registo correspondente definir no ficheiro de zona importados não serão removidos.

- Quando são intercalados conjuntos de registos, é utilizada a time to live (TTL) dos conjuntos de registos. Quando `--force` é utilizado, é utilizado o valor TTL do novo conjunto de registo.

- Início dos parâmetros de certificação (SOA) (exceto `host`) são sempre tirado do ficheiro de zona importados, independentemente de se `--force` é utilizado. Da mesma forma, para o registo de servidor de nome definido no vértice do zona, o valor TTL é sempre obtido o ficheiro de zona importados.

- Um registo CNAME importado não irá substituir um registo CNAME existente com o mesmo nome, a menos que a `--force` parâmetro for especificado.

- Quando surge um conflito de entre um registo CNAME e outro registo do mesmo nome mas tipo diferente (independentemente do qual é a existentes ou novas), é mantido o registo existente. Este é independente da utilização de `--force`.

### <a name="additional-information-about-importing"></a>Informações adicionais sobre como importar

As notas seguintes fornecem detalhes técnicos adicionais sobre a zona de processo de importação.

- O `$TTL` directiva é opcional e é suportada. Quando não `$TTL` directiva é dado, registos sem um TTL explícita será definido importado como um TTL predefinido de 3600 segundos. Quando dois registos no mesmo conjunto de registo especificar TTL diferente, é utilizado o valor mais baixo.

- O `$ORIGIN` directiva é opcional e é suportada. Quando não `$ORIGIN` estiver definido, o valor predefinido utilizado é o nome da zona conforme especificado na linha de comandos (plus a terminar ".").

- O `$INCLUDE` e `$GENERATE` diretivas do não são suportadas.

- Estes tipos de registo são suportados: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

- O registo SOA é criado automaticamente pelo Azure DNS quando é criada uma zona. Quando importar um ficheiro de zona, todos os parâmetros SOA são tirados da zona ficheiro *exceto* o `host` parâmetro. Este parâmetro utiliza o valor fornecido pelo Azure DNS. Isto é porque este parâmetro têm de referenciar primary name server fornecido pela Azure DNS.

- Também, o registo de servidor de nomes definir no vértice do zona é criado automaticamente pelo Azure DNS quando a zona é criada. É importado apenas o TTL deste conjunto de registos. Estes registos contêm os nomes dos servidores de nome fornecidas pela Azure DNS. Os dados de registo não são substituídos pelos valores contidos no ficheiro de zona importados.

- Durante a pré-visualização público, o Azure DNS suporta apenas única cadeia TXT records. Registos TXT múltiplas cadeias serão concatenados e truncados para 255 carateres.

### <a name="cli-format-and-values"></a>Formato de clip e valores


O formato do comando Azure clip para importar uma zona de DNS é:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`é o nome do grupo de recursos para a zona na Azure DNS.
- `<zone name>`é o nome da zona.
- `<zone file name>`é o nome/caminho do ficheiro de zona para serem importados.

Se não existir uma zona com este nome no grupo de recursos, será criado por si. Se já existir à zona, conjuntos de registos importados serão intercalados com conjuntos de registos existentes. Para substituir os conjuntos de registos existentes, utilize o `--force` opção.

Para verificar o formato de um ficheiro de zona sem realmente importá-lo, utilize o `--parse-only` opção.

### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um ficheiro de zona

Para importar um ficheiro de zona para o zone **contoso.com**.

1. Inicie sessão à sua subscrição do Azure utilizando o clip do Azure.

        azure login

2. Selecione a subscrição onde pretende criar a sua nova zona de DNS.

        azure account set <subscription name>

3. Azure DNS é um serviço de só de Gestor de recursos do Azure, para que o clip do Azure tem de ser mudado para o modo de Gestor de recursos.

        azure config mode arm

4. Antes de utilizar o serviço de Azure DNS, tem de registar a sua subscrição de utilizar o fornecedor de recurso Microsoft.Network. (Isto é uma operação única para cada subscrição).

        azure provider register Microsoft.Network

5. Se ainda não tiver uma, também tem de criar um grupo de recursos do Gestor de recursos.

        azure group create myresourcegroup westeurope

6. Para importar o zone **contoso.com** a partir de ficheiro **contoso.com.txt** para uma nova zona de DNS de grupo de recursos **myresourcegroup**, execute o comando `azure network dns zone import`.<BR>Este comando irá carregar o ficheiro de zona e analisá-lo. O comando será executado numa série de comandos no serviço do Azure DNS para criar a zona e conjuntos de todo o registo na zona. O comando também irá comunicar o progresso na janela da consola, juntamente com erros ou avisos. Porque conjuntos de registos são criados numa série, poderá demorar alguns minutos para importar um ficheiro de zona grande.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Passo 2. Verifique se a zona

Para verificar a zona de DNS depois de ter importado o ficheiro, pode utilizar qualquer um dos seguintes métodos:

- Pode listar os registos utilizando o seguinte comando clip Azure.

        azure network dns record-set list myresourcegroup contoso.com

- Pode listar os registos ao utilizar o cmdlet do PowerShell `Get-AzureRmDnsRecordSet`.

- Pode utilizar `nslookup` para verificar a resolução de nomes para os registos. Uma vez que a zona não está delegada ainda, terá de especificar explicitamente os servidores de nomes Azure DNS corretos. O exemplo abaixo mostra como obter os nomes dos servidores de nome atribuídos à zona. IT também mostra como consultar o registo "www" utilizando `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Passo 3. Delegação de atualização de DNS

Depois de ter verificado a zona tenha sido importada corretamente, terá de atualizar a delegação de DNS para apontarem para os servidores de nomes Azure DNS. Para mais informações, consulte o artigo [atualizar a delegação de DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um ficheiro de zona DNS do DNS do Azure

O formato do comando Azure clip para importar uma zona de DNS é:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`é o nome do grupo de recursos para a zona na Azure DNS.
- `<zone name>`é o nome da zona.
- `<zone file name>`é o nome/caminho do ficheiro de zona para ser exportados.

Como com a importação de zona, primeiro precisa para iniciar sessão, escolha a sua subscrição e configurar o clip do Azure para utilizar o modo de Gestor de recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um ficheiro de zona


1. Inicie sessão à sua subscrição do Azure utilizando o clip do Azure.

        azure login

2. Selecione a subscrição onde pretende criar a sua nova zona de DNS.

        azure account set <subscription name>

3. Azure DNS é um serviço de só de Gestor de recursos do Azure. O clip do Azure tem de ser mudado para o modo de Gestor de recursos.

        azure config mode arm

4. Para exportar o de zona Azure DNS de existente **contoso.com** no grupo de recursos **myresourcegroup** para o ficheiro **contoso.com.txt** (na pasta atual), executar `azure network dns zone export`. Este comando irá chamar o serviço de Azure DNS enumerar conjuntos de registo na zona e exportar os resultados para um ficheiro de zona VINCULAR compatível.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

