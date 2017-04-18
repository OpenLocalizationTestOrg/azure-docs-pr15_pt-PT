<properties
    pageTitle="Sincronização do Azure AD Connect: referência de funções | Microsoft Azure"
    description="Referência de expressões de aprovisionamento declarativas na ligação do Azure AD sync."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização do Azure AD Connect: referência de funções

Na ligação do Azure AD, funções são utilizadas para manipular um valor do atributo durante a sincronização.  
A sintaxe das funções é expressa utilizando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita sintaxes várias, todas as sintaxes válidas são listadas.  
As funções estão a negrito e estes Certifique-se de que o tipo de transmitido correspondências o tipo de documentado.  
Se não corresponder o tipo, é devolvido um erro.

Os tipos de são expresso com a seguinte sintaxe:

- **Reciclagem** – binário
- **Booleano** – booleano
- **dt** – UTC data/hora
- **enumeração** – enumeração das constantes conhecidas
- **exp** – expressão, que é esperado avaliar para um valor booleano
- **mvbin** – múltiplo binário
- **mvstr** – múltiplo cadeia
- **mvref** – referência múltiplo
- **num** – numérico
- **ref** – referência
- **str** – cadeia
- **var** – uma variante do (quase) qualquer outro tipo
- **void** – não devolve um valor

As funções com tipos **mvbin**, **mvstr**e **mvref** só podem trabalhar em atributos de múltiplos valores. Funções com **posição**, **str**e **ref** trabalhar em atributos de valor único e múltiplos valores.

## <a name="functions-reference"></a>Referência de funções

Lista de funções | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversão** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Data / hora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatoDataHora](#formatdatetime) | [Agora](#now)
[NumFromDate](#numfromdate) |  
**Diretório** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Avaliação** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[ÉNulo](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matemática** |  
[Bit. E](#bitand) | [Bit. ou](#bitor) | [RandomNum](#randomnum)
**Múltiplos valores** |  
[Contém](#contains) | [Contar](#count) | [Item](#item) | [ItemOrNull](#itemornull)
[Associação](#join) | [RemoveDuplicates](#removeduplicates) | [Dividir](#split) |
**Fluxo de um programa** |  
[Erro](#error) | [ISE](#iif)  | [Mudar](#switch)
**Texto** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[À esquerda](#left) | [Núm. carat](#len) | [Funções SuprEsq](#ltrim) | [Seg. texto](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Substituir](#replace)
[ReplaceChars](#replacechars) | [Para a direita](#right) | [RTrim](#rtrim) | [Cortar](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>Bit. E

**Descrição:**  
Função bit. e conjuntos de bits especificados em valores.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

- Valor1; valor2: valores numéricos que devem ser agrupadas em conjunto

**Observações:**  
Esta função converte ambos os parâmetros de representação binária e define um pouco para:

- 0 - se um ou ambos os bits correspondentes na *máscaras de introdução* e *Sinalizador* forem 0
- 1 - se ambos os bits correspondentes são 1.

Por outras palavras, devolve 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Devolve 7 porque hexadecimal "F" e "F7" avaliar a este valor.

----------
### <a name="bitor"></a>Bit. ou

**Descrição:**  
Função bit. ou conjuntos de bits especificados em valores.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

- Valor1; valor2: valores numéricos que devem ser agrupados com OR em conjunto

**Observações:**  
Esta função converte ambos os parâmetros de representação binária e define um pouco a 1, se um ou ambos os bits correspondentes na máscaras de introdução e sinalizador estão entre 1 e 0 se ambos os bits correspondentes forem 0. Por outras palavras, devolve 1 em todos os casos, exceto onde os bits correspondentes de ambos os parâmetros são 0.

----------
### <a name="cbool"></a>CBool

**Descrição:**  
A função CBool devolve um valor booleano com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão é avaliada para um valor diferente de zero, em seguida, CBool devolve True, de que outra devolve FALSO.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Devolve True se ambos os atributos têm o mesmo valor.

----------
### <a name="cdate"></a>CDate

**Descrição:**  
A função CDate devolve um DateTime UTC de uma cadeia. Data/hora não é um tipo de atributo nativo sincronizar, mas é utilizada por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

- Value: Uma cadeia com uma data, hora e, opcionalmente, fuso horário

**Observações:**  
A cadeia devolvida é sempre no UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Devolve que uma data/hora com base do colaborador hora de início

`CDate("2013-01-10 4:00 PM -8")`  
Devolve uma data/hora que representa "2013-01-11 12:00 AM"

----------
### <a name="cguid"></a>CGuid

**Descrição:**  
A função CGuid converte a representação de cadeia de um GUID respetiva representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

- Uma cadeia formatada este padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contém

**Descrição:**  
A função Contains encontrar uma cadeia dentro de um atributo múltiplos valor

**Sintaxe:**  
`num Contains (mvstring attribute, str search)`-entre maiúsculas e minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-entre maiúsculas e minúsculas

- atributo: o atributo múltiplos valor para procurar.
- pesquisa: cadeia para localizar no atributo.
- Casetype: CaseInsensitive ou CaseSensitive.

Devolve o índice remissivo no atributo múltiplos onde foi encontrada a cadeia. Se a cadeia não for encontrada, é devolvido 0.

**Observações:**  
Para os atributos de cadeia múltiplos valores, a procura localiza subcadeias os valores.  
Para os atributos de referência, a cadeia procurada tem de corresponder exatamente o valor a ser consideradas uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresses tem um endereço de e-mail principal (indicado por letras maiúsculas "SMTP:"), em seguida, regresse o atributo proxyAddress, de que outra devolver um erro.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Descrição:**  
A função ConvertFromBase64 converte o valor de codificação base64 especificado como uma cadeia de normal.

**Sintaxe:**  
`str ConvertFromBase64(str source)`-assume Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

- origem: Base64 cadeia codificada  
- Codificação: UTF8 Unicode, ASCII,

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos devolvem "*Olá mundo!*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Descrição:**  
A função de ConvertFromUTF8Hex converte o valor de Hex UTF8 codificado especificado para uma cadeia.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

- origem: UTF8 2 bytes com sinal codificada cadeia

**Observações:**  
A diferença entre esta função e ConvertFromBase64([],UTF8) no que o resultado é amigável para o atributo DN.  
Este formato é utilizado pelo Azure Active Directory como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Devolve "*Olá mundo!*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Descrição:**  
A função ConvertToBase64 converte uma cadeia de uma cadeia de base64 Unicode.  
Converte o valor de uma matriz de números inteiros respectiva representação de cadeia equivalente é codificada com base-64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devolve "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Descrição:**  
A função ConvertToUTF8Hex converte uma cadeia de um valor Hex UTF8 codificado.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Active Directory como formato do atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devolve 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Contar

**Descrição:**  
A função CONTAR devolve o número de elementos de um atributo múltiplos valor

**Sintaxe:**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Descrição:**  
A função CNum leva-o até uma cadeia e devolve um tipo de dados numéricos.

**Sintaxe:**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Descrição:**  
Converte uma cadeia de um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Descrição:**  
A função CStr converte um tipo de dados cadeia.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valor: pode ser um valor numérico, referência atributo ou booleano.

**Exemplo:**  
`CStr([dn])`  
Poderá devolver "cn = João, Cc = contoso, CC = com"

----------
### <a name="dateadd"></a>DateAdd

**Descrição:**  
Devolve uma data que contenham uma data à qual foi adicionado um intervalo de tempo especificado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

- intervalo: expressão de cadeia que corresponde ao intervalo de tempo que pretende adicionar. A cadeia tem de ter um dos seguintes valores:
 - AAAA ano
 - respostas do trimestre
 - m mês
 - y dia do ano
 - d dia
 - w dia da semana
 - ww semana
 - h hora
 - Minuto n
 - s segundo
- valor: O número de unidades que pretende adicionar. Pode ser positivo (para obter as datas no futuro) ou negativo (para obter as datas no passado).
- Data: DateTime que representa a data à qual o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Soma 3 meses e devolve um DateTime que representa "2001-04-01".

----------
### <a name="datefromnum"></a>DateFromNum

**Descrição:**  
Converte de função de DateFromNum formatar um valor de data do AD a um tipo de data/hora.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devolve uma data/hora que representa 2012-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Descrição:**  
A função DNComponent devolve o valor de um componente de DN especificado ir da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

- DN: o atributo de referência para interpretar
- ComponentNumber: O componente no DN para devolver

**Exemplo:**  
`DNComponent([dn],1)`  
Se for dn "cn = João, or..., =" devolve João

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Descrição:**  
A função DNComponentRev devolve o valor de um componente de DN especificado ir da direita (fim).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- DN: o atributo de referência para interpretar
- ComponentNumber - o componente no DN para devolver
- Opções: CC – ignorar todos os componentes com "cc ="

**Exemplo:**  
Se for dn "cn = João, or = Aveiro, or = das versões DG, or = US, Cc = contoso, CC = com", em seguida,  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Ambos devolvem-nos.

----------
### <a name="error"></a>Erro

**Descrição:**  
A função de erro é utilizada para devolver um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se o atributo accountName não existir, gerar um erro no objeto.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Descrição:**  
A função EscapeDNComponent leva-o até um componente de um DN e escapa-lo para que possa ser representado no LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Assegura que o objeto pode ser criado num directório LDAP mesmo se o atributo displayName tem carateres que devem ser escape no LDAP.

----------
### <a name="formatdatetime"></a>FormatoDataHora

**Descrição:**  
A função FormatoDataHora é utilizada para formatar uma data/hora a uma cadeia com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

- valor: um valor no formato de data/hora
- formato: uma cadeia representando o formato para converter.

**Observações:**  
Os valores possíveis para o formato podem ser encontrados aqui: [User-Defined formatos de data/hora (função Format)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resulta em "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0Z"

----------
### <a name="guid"></a>GUID

**Descrição:**  
A função GUID gera um novo GUID aleatório

**Sintaxe:**  
`str GUID()`

----------
### <a name="iif"></a>ISE

**Descrição:**  
A função Ise devolve uma de um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condição: qualquer valor ou expressão que possa ser avaliado como true ou false.
- valueIfTrue: se a condição devolver o valor verdadeiro, o valor devolvido.
- valueIfFalse: se a condição devolver o valor falso, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for um estagiários, devolve o alias de um utilizador com "t-" adicionada para o início do mesmo, mais preciso devolve o alias do utilizador como está.

----------
### <a name="instr"></a>InStr

**Descrição:**  
A função InStr localiza a primeira ocorrência de uma subcadeia numa cadeia

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- GroupDigits: cadeia a ser procurado
- stringmatch: cadeia a ser encontrado
- iniciar: posição para localizar a subcadeia de início
- comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde a subcadeia foi encontrada ou 0 se não foi encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalues a 5.

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia a 7

----------
### <a name="instrrev"></a>InStrRev

**Descrição:**  
A função InStrRev localiza a última ocorrência de uma subcadeia numa cadeia

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- GroupDigits: cadeia a ser procurado
- stringmatch: cadeia a ser encontrado
- iniciar: posição para localizar a subcadeia de início
- comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde a subcadeia foi encontrada ou 0 se não foi encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devolve 7

----------
### <a name="isbitset"></a>IsBitSet

**Descrição:**  
A função IsBitSet testa se um bit estiver definida ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

- valor: um valor numérico que é evaluated.flag: um valor numérico que tem o bit a avaliar

**Exemplo:**  
`IsBitSet(&HF,4)`  
Devolve verdadeiro porque bit "4" é definido o valor hexadecimal "F"

----------
### <a name="isdate"></a>IsDate

**Descrição:**  
Se a expressão pode ser avaliado como um tipo de data/hora, em seguida, a função IsDate é avaliada como verdadeira.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Observações:**  
Utilizada para determinar se CDate() pode ser efetuada com êxito.

----------
### <a name="isempty"></a>IsEmpty

**Descrição:**  
Se o atributo for presente no CS ou MV, mas é avaliada como uma cadeia vazia, em seguida, a função IsEmpty é avaliado como True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Descrição:**  
Se a cadeia pode ser convertida num GUID, em seguida, a função IsGuid avaliado como true.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Observações:**  
Um GUID é definido como uma cadeia de seguir um dos seguintes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Utilizada para determinar se CGuid() pode ser efetuada com êxito.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tem um formato GUID, devolver uma representação binária, caso contrário, é devolvido um nulo.

----------
### <a name="isnull"></a>ÉNulo

**Descrição:**  
Se a expressão devolver o valor nulo, a função ÉNulo devolve verdadeira.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um valor nulo é expressa pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devolve VERDADEIRO se o atributo não estiver presente na CS ou MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Descrição:**  
Se a expressão for nulo ou uma cadeia vazia, a função IsNullOrEmpty devolve verdadeira.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isto seria avaliar como VERDADEIRO se o atributo está ausente ou for apresentar, mas é uma cadeia vazia.  
O inverso desta função é denominado IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devolve VERDADEIRO se o atributo não se encontra presente ou for uma cadeia vazia na CS ou MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Descrição:**  
A função ÉNum devolve um valor booleano que indica se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Observações:**  
Utilizada para determinar se CNum() pode ser efetuada com êxito para analisar a expressão.

----------
### <a name="isstring"></a>IsString

**Descrição:**  
Se a expressão possa ser avaliada como um tipo de cadeia, em seguida, a função IsString é avaliado como True.

**Sintaxe:**  
`bool IsString(var expression)`

**Observações:**  
Utilizada para determinar se CVDate pode ser efetuada com êxito para analisar a expressão.

----------
### <a name="ispresent"></a>IsPresent

**Descrição:**  
Se a expressão é avaliada como uma cadeia que não é nulo e não está vazia, a função IsPresent devolve verdadeira.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função é denominado IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Item

**Descrição:**  
A função de Item devolve um item a partir de um cadeia/atributo múltiplos valor.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

- atributo: atributo múltiplos valor
- índice remissivo: índice para um item na cadeia de múltiplos valor.

**Observações:**  
A função de Item é útil juntamente com a função contém uma vez que a função último devolve o índice remissivo para um item no atributo múltiplos valor.

Emitir um erro se índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Devolve o endereço de e-mail principal.

----------
### <a name="itemornull"></a>ItemOrNull

**Descrição:**  
A função de ItemOrNull devolve um item a partir de um cadeia/atributo múltiplos valor.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

- atributo: atributo múltiplos valor
- índice remissivo: índice para um item na cadeia de múltiplos valor.

**Observações:**  
A função ItemOrNull é útil juntamente com a função contém uma vez que a função último devolve o índice remissivo para um item no atributo múltiplos valor.

Se índice estiver fora dos limites, em seguida, devolve um valor nulo.

----------
### <a name="join"></a>Associação

**Descrição:**  
A função de associação assume uma cadeia de múltiplos valor e devolve uma cadeia de valor único com o separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- atributo: atributo múltiplos valor que contenham cadeias de ser associadas.
- delimitador: qualquer cadeia, utilizada para separar subcadeias na cadeia devolvida. Se for omitido, o caráter de espaço ("") é utilizado. Se delimitador for uma cadeia de comprimento zero ("") ou nada, todos os itens na lista encontram-se concatenados com sem delimitadores.

**Observações**  
Existe paridade entre as funções associação e dividir. A função de associação utiliza uma matriz de cadeias e junta-las utilizando uma cadeia de delimitador, para devolver uma única cadeia. A função dividir assume uma cadeia e separa-lo no delimitador, para devolver uma matriz de cadeias. No entanto, uma diferença de chave é que associação pode concatenar cadeias com qualquer cadeia delimitador, dividir só pode separar cadeias de utilizar um caráter individual delimitador.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Foi devolvido:"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**Descrição:**  
A função LCase converte todos os carateres numa cadeia de minúsculas.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Devolve "teste".

----------
### <a name="left"></a>À esquerda

**Descrição:**  
A função esquerda devolve um número especificado de carateres a partir da esquerda de uma cadeia.

**Sintaxe:**  
`str Left(str string, num NumChars)`

- cadeia: a cadeia de carateres a partir de retorno
- NumChars: um número que identifica o número de carateres para devolver a partir do início (à esquerda) da cadeia

**Observações:**  
Uma cadeia que contém os carateres numChars numa cadeia de:

- Se numChars = 0, devolver uma cadeia vazia.
- Se numChars < 0, voltar a cadeia de entrada.
- Se a cadeia é nula, devolve uma cadeia vazia.

Se cadeia contiver menos carateres que o número numChars especificado no, é devolvida uma cadeia idêntica ao cadeia (isto é, que contém todos os carateres no parâmetro 1).

**Exemplo:**  
`Left("John Doe", 3)`  
Devolve "Joh".

----------
### <a name="len"></a>Núm. carat

**Descrição:**  
A função NÚM. CARACT Devolve o número de carateres numa cadeia.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
Devolve 8

----------
### <a name="ltrim"></a>Funções SuprEsq

**Descrição:**  
A função LTrim remove espaços em branco à esquerda de uma cadeia.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Devolve "teste"

----------
### <a name="mid"></a>Seg. texto

**Descrição:**  
A função seg. texto devolve um número especificado de carateres de uma posição especificada de uma cadeia.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

- cadeia: a cadeia de carateres a partir de retorno
- iniciar: posicionar um número que identifica o início numa cadeia de carateres a partir de retorno
- NumChars: um número que identifica o número de carateres para devolver a partir da posição na cadeia

**Observações:**  
A partir do início de posição na cadeia de carateres de numChars de retorno.  
Uma cadeia que contém carateres numChars do princípio de posição na cadeia:

- Se numChars = 0, devolver uma cadeia vazia.
- Se numChars < 0, voltar a cadeia de entrada.
- Se iniciar > o comprimento da cadeia, voltar a cadeia de entrada.
- Se iniciar < = 0, voltar a cadeia de entrada.
- Se a cadeia é nula, devolve uma cadeia vazia.

Se não existirem numChar carateres remanescentes na cadeia do princípio de posição, tantas carateres possível são devolvidos.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Devolve "hn fazer".

`Mid("John Doe", 6, 999)`  
Devolve "Silva"

----------
### <a name="now"></a>Agora

**Descrição:**  
A função Now devolve um DateTime especificando a data e hora atuais, de acordo com a data do sistema e a hora do seu computador.

**Sintaxe:**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Descrição:**  
A função de NumFromDate devolve uma data no formato de data do AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Devolve 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Descrição:**  
Os PadLeft função esquerda-consolas de uma cadeia para um período especificado utilizando um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

- cadeia: a cadeia de teclado de marcação.
- comprimento: um número inteiro que representa o comprimento da cadeia pretendido.
- padCharacter: uma cadeia que consiste um caráter individual para utilizar como o caráter de marcação

**Observações:**

- Se o comprimento da cadeia for menor que o comprimento, em seguida, padCharacter surgirá repetidamente até ao início (à esquerda) da cadeia até que esta tenha um comprimento igual ao comprimento.
- PadCharacter pode ser um caráter de espaço, mas não pode ser um valor nulo.
- Se o comprimento da cadeia for igual ou maior que o comprimento, é devolvida inalterada cadeia.
- Se a cadeia tiver um comprimento maior ou igual a comprimento, é devolvida uma cadeia idêntica ao cadeia.
- Se o comprimento da cadeia for menor que o comprimento, uma cadeia nova o comprimento pretendido é devolvida cadeia que contenham preenchida com um padCharacter.
- Se a cadeia é nula, a função devolverá uma cadeia vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Devolve "000000User".

----------
### <a name="padright"></a>PadRight

**Descrição:**  
Os PadRight função direita-consolas de uma cadeia para um período especificado utilizando um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

- cadeia: a cadeia de teclado de marcação.
- comprimento: um número inteiro que representa o comprimento da cadeia pretendido.
- padCharacter: uma cadeia que consiste um caráter individual para utilizar como o caráter de marcação

**Observações:**

- Se o comprimento da cadeia for menor que o comprimento, em seguida, padCharacter repetidamente surgirá para o fim (à direita) da cadeia até que esta tenha um comprimento igual ao comprimento.
- padCharacter pode ser um caráter de espaço, mas não pode ser um valor nulo.
- Se o comprimento da cadeia for igual ou maior que o comprimento, é devolvida inalterada cadeia.
- Se a cadeia tiver um comprimento maior ou igual a comprimento, é devolvida uma cadeia idêntica ao cadeia.
- Se o comprimento da cadeia for menor que o comprimento, uma cadeia nova o comprimento pretendido é devolvida cadeia que contenham preenchida com um padCharacter.
- Se a cadeia é nula, a função devolverá uma cadeia vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Devolve "User000000".

----------
### <a name="pcase"></a>PCase

**Descrição:**  
A função PCase converte o primeiro caráter de cada palavra delimitado por espaços de uma cadeia de maiúsculas e todos os outros carateres são convertidas em minúsculas.

**Sintaxe:**  
`String PCase(string)`

**Observações:**

- Esta função não fornece atualmente invólucro inicial para converter uma palavra que está completamente em maiúscula, tal como um acrónimo.

**Exemplo:**  
`PCase("TEsT")`  
Devolve "Teste".

`PCase(LCase("TEST"))`  
Devolve "teste"

----------
### <a name="randomnum"></a>RandomNum

**Descrição:**  
A função RandomNum devolve um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

- iniciar: um número que identifica o limite inferior do valor aleatório para gerar
- fim: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode devolver 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Descrição:**  
A função RemoveDuplicates leva-o até uma cadeia de valores múltiplos e certifique-se de cada valor é exclusivo.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress truncamento onde foram removidos todos os valores duplicados.

----------
### <a name="replace"></a>Substituir

**Descrição:**  
A função substituir substitui todas as ocorrências de uma cadeia com outra cadeia.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

- cadeia: uma cadeia para substituir valores.
- ValorAntigo: A cadeia para procurar e substituir.
- NovoValor: A cadeia a substituir.

**Observações:**  
A função reconhece os identificadores de especiais seguintes:

- \n – nova linha
- \r – símbolo de retorno
- \t – separador

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF com uma vírgula e um espaço e podem conduzir a "One Microsoft Way, Redmond, WA, E.U.A"

----------
### <a name="replacechars"></a>ReplaceChars

**Descrição:**  
A função ReplaceChars substitui todas as ocorrências dos carateres que se encontram na cadeia de ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

- cadeia: uma cadeia para substituir carateres na.
- ReplacePattern: uma cadeia que contém um dicionário com carateres para substituir.

O formato é {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} onde origem for o caráter para localizar e a cadeia para substituir por de destino.

**Observações:**

- A função leva-o até cada ocorrência de fontes definidos e substitui-los com os destinos.
- A origem tem de ser exatamente um caráter de (unicode).
- A origem não pode ser vazia ou mais de um caráter (erro de análise).
- O destino pode ter vários carateres, por exemplo, ö:oe, β:ss.
- O destino pode estar vazio que indica que o caráter deve ser removido.
- A origem é sensível às maiúsculas e tem de ser uma correspondência exata.
- (Vírgula) e: (dois pontos) são reservadas carateres e não pode ser substituído utilizar esta função.
- Espaços e outros carateres em branco na cadeia de ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devolve Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devolve "ONeil", a única escala está definido para ser removido.

----------
### <a name="right"></a>Para a direita

**Descrição:**  
A função DIREITA devolve um número especificado de carateres da direita (fim) de uma cadeia.

**Sintaxe:**  
`str Right(str string, num NumChars)`

- cadeia: a cadeia de carateres a partir de retorno
- NumChars: um número que identifica o número de carateres de retorno do fim (à direita) da cadeia

**Observações:**  
NumChars carateres são devolvidos a partir da última posição da cadeia.

Uma cadeia que contém os carateres numChars últimos numa cadeia:

- Se numChars = 0, devolver uma cadeia vazia.
- Se numChars < 0, voltar a cadeia de entrada.
- Se a cadeia é nula, devolve uma cadeia vazia.

Se cadeia contiver menos carateres que o número NumChars especificado no, é devolvida uma cadeia idêntica ao cadeia.

**Exemplo:**  
`Right("John Doe", 3)`  
Devolve "Silva".

----------
### <a name="rtrim"></a>RTrim

**Descrição:**  
A função RTrim remove espaços em branco no fim de uma cadeia.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
Devolve "Teste".

----------
### <a name="split"></a>Dividir

**Descrição:**  
A função dividir assume uma cadeia separada com delimitador e faz com que uma cadeia de múltiplos valor.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valor: a cadeia com um caráter delimitador para separar.
- delimitador: caráter que será utilizado como o delimitador individual.
- limite: número máximo de valores que pode devolver.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devolve uma cadeia de múltiplos valor com 2 elementos úteis para o atributo proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Descrição:**  
A função StringFromGuid leva-o até um GUID binário e converte-o para uma cadeia

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Descrição:**  
A função StringFromSid converte uma matriz de bytes que contém um identificador de segurança para uma cadeia.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Mudar

**Descrição:**  
A função de parâmetro é utilizada para devolver um valor único com base em condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: variante expressão que pretende avaliar.
- valor: valor a ser devolvido se a expressão correspondente for VERDADEIRO.

**Observações:**  
A lista de argumentos de função mudar consiste em pares de valores e expressões. As expressões são avaliadas da esquerda para a direita e, é devolvido o valor associado à primeira expressão a avaliar como verdadeiro. Se as partes não estão correctamente par, ocorre um erro de tempo de execução.

Por exemplo, se expr1 for VERDADEIRO, o parâmetro devolve valor1. Se expr-1 é FALSO, mas expr-2 for VERDADEIRO, o mudar devolve o valor-2 e assim sucessivamente.

Parâmetro devolve um nada se:

- Nenhum das expressões for VERDADEIRO.
- A primeira expressão verdadeira tem um valor correspondente, que é Null.

Mudar avalia todas as expressões, apesar de devolve apenas um deles. Por este motivo, deverá ver efeitos secundários não pretendidos. Por exemplo, se da avaliação de qualquer expressão resultar numa divisão por zero erro, ocorre um erro.

Valor também pode ser a função de erro, o que deve devolver uma cadeia personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devolve os idiomas falados na algumas cidades grandes, caso contrário, devolve um erro.

----------
### <a name="trim"></a>Cortar

**Descrição:**  
A função compactar remove espaços extra branco de uma cadeia e.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços extra para cada valor no atributo proxyAddress e.

----------
### <a name="ucase"></a>UCase

**Descrição:**  
A função UCase converte todos os carateres numa cadeia de maiúsculas.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Devolve "Teste".

----------
### <a name="word"></a>Word

**Descrição:**  
A função de Word devolve uma palavra contida dentro de uma cadeia, com base nos parâmetros descrever delimitadores a utilização e o número de word para devolver.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

- cadeia: a cadeia para devolver uma palavra a partir de.
- WordNumber: deve devolver um número que identifica qual o número de word.
- delimitadores: uma cadeia representando o delimiter(s) que deve ser utilizado para identificar palavras

**Observações:**  
Cada cadeia de carateres numa cadeia de separados por uma dos carateres em delimitadores estão identificados como palavras:

- Se o número < 1, devolve esvazie cadeia.
- Se a cadeia é nula, devolve uma cadeia vazia.

Se a cadeia contém menor número de palavras ou cadeia não contiver qualquer palavras identificadas por delimitadores, é devolvida uma cadeia vazia.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devolve "castanho"

`Word("This,string!has&many separators",3,",!&#")`  
Deve devolver "tem"

## <a name="additional-resources"></a>Recursos adicionais

* [Noções sobre declarativos expressões de aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD ligar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
