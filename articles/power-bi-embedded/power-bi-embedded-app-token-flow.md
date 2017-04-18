<properties
   pageTitle="Autenticação e autorização com o Power BI incorporado"
   description="Autenticação e autorização com o Power BI incorporado"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticação e autorização com o Power BI incorporado

O serviço do Power BI incorporado utiliza **as teclas** e **Tokens de aplicação** para autenticação e autorização, em vez de autenticação de utilizador final explícitos. Neste modelo, a sua aplicação gere autenticação e autorização para os utilizadores finais. Quando for necessário, a sua aplicação cria e envia os Tokens de aplicação que indica o nosso serviço para compor o relatório pretendido. Esta estrutura não necessita de sua aplicação para utilizar o Azure Active Directory para autenticação de utilizador e autorização, apesar de poder continuar.

## <a name="two-ways-to-authenticate"></a>Duas formas para autenticar

**Chave** - pode utilizar teclas para todas as chamadas do Power BI incorporado REST API. Podem encontrar as teclas no **portal do Azure** ao clicar em **todas as definições** e, em seguida, **as teclas de acesso**. Trate sempre a sua chave de como se fosse uma palavra-passe. Estas teclas tem permissões para efetuar qualquer REST API ligar para uma coleção específica da área de trabalho.

Para utilizar uma chave da fazer uma chamada de descanso, adicione o cabeçalho de autorização seguinte:            

    Authorization: AppKey {your key}

**Token de aplicação** - aplicação tokens são utilizadas para todos os pedidos de incorporação. Está a foi concebidos para ser executar lado do cliente, pelo que está a restringido a um único relatório e é melhor prática definir uma hora de expiração.

Tokens de aplicação estão JWT (JSON Web Token) está assinada por uma das suas chaves.

O token de aplicação pode conter as seguintes afirmações:

| Afirmação      | Descrição        |
|--------------|------------|
| **ver**      | A versão do token de aplicação. 0.2.0 é a versão atual.       |
| **sociedade mista**      | O destinatário pretendido do token de. Para utilizar incorporado do Power BI: "https://analysis.windows.net/powerbi/api".  |
| **ISS**      |  Uma cadeia que indica a aplicação que emitido o token.    |
| **tipo**     | O tipo de token de aplicação que está a ser criado. Atual o único tipo suportado é a **incorporar**.   |
| **WCN**      | Nome da colecção de área de trabalho do token está a ser emitida para.  |
| **WID**      | ID de área de trabalho do token está a ser emitido para.  |
| **eliminar**      | ID do token está a ser emitido para um relatório.     |
| **nome de utilizador** (opcional) |  Utilizado com RLS, esta é uma cadeia que o pode ajudar a identificar o utilizador quando aplicar RLS regras. |
| **funções** (opcional)   |   Uma cadeia que contém as funções para selecionar quando aplicar regras de segurança de nível de linha. Se passando mais do que uma função, devem ser passados como uma matriz de cadeia.    |
| **EXP** (opcional)    |   Indica a hora em que o token irá expirar. Estes devem ser passados no como Unix selos de tempo.   |
| **NBF** (opcional)    |   Indica a hora em que o token inicia a ser válida. Estes devem ser passados no como Unix selos de tempo.   |

Um token de aplicação de exemplo terá o seguinte aspeto:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Quando descodificar,-terá o seguinte aspeto:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Eis como funciona o fluxo

1. Copie as teclas de API para a sua aplicação. Pode obter as teclas no **Portal do Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Token afirma uma afirmação e tem um tempo de expiração.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Token obtém a sessão iniciada com uma teclas de acesso de API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Pedidos de utilizador para ver um relatório.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Token for validado com as teclas de acesso uma API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI incorporado envia um relatório ao utilizador.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Depois de **Power BI incorporado** envia um relatório para o utilizador, o utilizador pode ver o relatório na sua aplicação personalizada. Por exemplo, se tiver importado a [amostra de analisar PBIX de dados de vendas](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), a aplicação web do exemplo teria o seguinte aspeto:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Consulte também
- [Introdução ao Microsoft Power BI incorporado exemplo](power-bi-embedded-get-started-sample.md)
- [Cenários comuns de incorporado do Microsoft Power BI](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI incorporado](power-bi-embedded-get-started.md)
