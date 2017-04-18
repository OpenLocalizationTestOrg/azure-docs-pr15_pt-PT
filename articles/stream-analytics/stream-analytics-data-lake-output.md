<properties
    pageTitle="Arquivo de Lake de dados de análise da cadeia saída | Microsoft Azure"
    description="Configuração de autenticação e de autorização de um Azure Lake de arquivo de dados numa tarefa da cadeia Analytics"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Resultado de arquivo de Lake de dados de análise da cadeia

Tarefas de análise da cadeia suportam vários métodos de saída, um ser uma [Azure dados Lake loja](https://azure.microsoft.com/services/data-lake-store/). Azure dados Lake arquivo é um repositório de escala hyper ao nível da empresa das cargas de trabalho analítico dados grande. Arquivo de dados de Lake permite-lhe armazenar dados de qualquer tamanho, tipo e ingestão velocidade para análise operacional e exploratória.

## <a name="authorize-a-data-lake-store-account"></a>Autorize uma conta do arquivo de Lake de dados

1.  Quando está seleccionado arquivo de dados de Lake como uma saída no portal de gestão do Azure, vai ser-lhe para autorizar a utilização da sua existente Lake de arquivo de dados ou para pedir acesso a pré-visualização de arquivo de Lake dos dados através do Portal clássica do Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Se já tem acesso ao arquivo de Lake de dados, clique em "Autorizar agora" e para uma altura breve uma página irá pop-up que indica "Redireccionar a autorização de...". A página será automaticamente fechado e será apresentada com a página que iria permitir-lhe configurar a saída de arquivo de Lake de dados.

Se de que não se inscreveu para a pré-visualização da loja de Lake de dados, pode seguir a ligação "Inscrever-se agora" para iniciar o pedido ou segue [obter instruções de introdução](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurar as propriedades de saída do arquivo de Lake de dados

Assim que tiver a conta de arquivo de Lake dados autenticada, pode configurar as propriedades para a saída de arquivo de Lake de dados. A tabela abaixo é a lista de nomes de propriedades e a respectiva descrição para configurar a saída de arquivo de Lake de dados.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este Lake arquivo de dados.</td>
</tr>
<tr>
<td>Conta de arquivo de Lake de dados</td>
<td>O nome da conta de armazenamento onde vai enviar a saída. Será apresentada uma lista de contas de arquivo de Lake dados à qual o utilizador com sessão iniciada portal do tem acesso a lista pendente.</td>
</tr>
<tr>
<td>Caminho do prefixo com padrão [<I>opcional</I>]</td>
<td>O caminho do ficheiro utilizado para escrever os seus ficheiros na conta de arquivo de dados Lake especificado. <BR>{date}, {tempo}<BR>Exemplo 1: pasta1/registos / {date} / {tempo}<BR>Exemplo 2: pasta1/registos / {date}</td>
</tr>
<tr>
<td>Formato de data [<I>opcionais</I>]</td>
<td>Se o token de data é utilizado no caminho do prefixo, pode selecionar o formato de data na qual os seus ficheiros estão organizados. Exemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se for utilizado o token de tempo no caminho do prefixo, especifique o formato da hora em que os seus ficheiros estão organizados. Atualmente, o valor de suportado apenas é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída. JSON, CSV e Avro são suportadas.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se em formato CSV ou JSON, uma codificação tem de ser especificada. UTF-8 é o formato de codificação suportado apenas neste momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para serializar os dados CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável apenas para serialização JSON. Linha separada Especifica que será formatada a saída fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que será formatado o resultado como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renovar a autorização de arquivo de Lake de dados

Atualmente, não existe uma limitação onde o token de autenticação tem de estar atualizados manualmente cada cerca de 90 dias para todas as tarefas com dados Lake loja saída. Também terá de autenticar novamente a sua conta do arquivo de Lake dados se tiver alterado a palavra-passe uma vez que a tarefa foi criada ou última autenticada. Um sintoma deste problema é sem saída de tarefa e um erro nos registos do operação que indica a necessidade de autorização de nova.

Para resolver este problema, pare o seu trabalho em execução e aceda ao seu resultado de arquivo de Lake de dados. Clique na ligação "Renovar autorização" e, para uma altura breve uma página irá pop-up que indica "Redireccionar a autorização de...". A página será automaticamente fechado e se tiver êxito, irá indicar "Autorização tiver sido com êxito renovada". , Em seguida, clique em "Guardar" na parte inferior da página, precisa e pode prosseguir por reiniciar o seu trabalho desde a última vez parado para evitar a perda de dados.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
