<properties
    pageTitle="Suplementos de início de sessão após várias falhas"
    description="Um relatório que indica os utilizadores que têm com êxito sessão iniciada no depois de múltiplos consecutivos Ocorreu uma falha com início de sessão em tentativas."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Inícios de sessão após várias falhas
Este relatório indica os utilizadores que têm com êxito sessão iniciada no depois de múltiplos consecutivos Ocorreu uma falha com início de sessão em tentativas. As possíveis causas incluem:

- Utilizador tinha esquecido da sua palavra-passe</li><li>O utilizador está vítima de uma palavra-passe bem sucedida adivinhar bruta forçar ataque

Resultados deste relatório irão apresentar o número de consecutivas início de sessão no tentativas falhadas efetuadas antes do sessão com êxito e um carimbo de data/hora associada o primeira bem sucedido iniciar sessão.

**Definições do relatório**: pode configurar o número mínimo de início de sessão falhados consecutivos em tentativas que devem ocorrer antes que podem ser apresentado no relatório. Ao efetuar alterações a esta definição é importante ter em atenção que estas alterações não serão aplicadas a qualquer assinar falha existente ins atualmente que aparecem no seu relatório existente. No entanto, eles serão aplicados a todos os suplementos de início de sessão futuros. Só podem ser efetuadas alterações a este relatório por administradores licenciados.


![Suplementos de início de sessão após várias falhas](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
