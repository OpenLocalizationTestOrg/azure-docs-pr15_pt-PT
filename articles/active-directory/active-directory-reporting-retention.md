<properties
    pageTitle="Políticas de retenção de relatório do Azure Active Directory | Microsoft Azure"
    description="Políticas de retenção de dados de relatório do Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory

*Esta documentação faz parte do [Azure elaboração de relatórios Guia do Active Directory](active-directory-reporting-guide.md).*

Relatórios do Azure Active Directory (Azure AD) retêm dados para um determinado número de dias, indicado aqui.

Relatório                                                  | Descrição
------------------------------------------------------- | -----------
Inícios de sessão a partir de origens desconhecidas                           | 30 dias
Inícios de sessão após várias falhas                        | 30 dias
Inícios de sessão a partir de várias zonas geográficas                      | 30 dias
Início de sessão-ins proveniente de endereços IP com atividade suspeita     | 30 dias
Inícios de sessão a partir de dispositivos possivelmente infectados                 | 30 dias
Atividade de início de sessão irregular                              | 30 dias
Utilizadores com atividade de início de sessão anómalo                   | 30 dias
Utilizadores com credenciais fuga                           | 30 dias
Relatório de auditoria                                            | 180 dias
Repor a palavra-passe atividade (Azure AD)                      | 30 dias
Repor a palavra-passe atividade (Gestor de identidade)              | 30 dias
Repor a palavra-passe actividade de registo (Azure AD)         | 30 dias
Repor a palavra-passe actividade de registo (Gestor de identidade) | 30 dias
Self service na atividade de grupos (Azure AD)                 | 30 dias
Self service na atividade de grupos (Gestor de identidade)         | 30 dias
Utilização da aplicação                                       | 30 dias
Atividade de aprovisionamento de conta                           | 30 dias
Estado de sobreposição de palavra-passe                                | 30 dias
Erros de aprovisionamento de conta                             | 30 dias
Utilização do RMS                                               | 30 dias
Utilizadores do RMS mais ativos                                   | 30 dias
Utilização do dispositivo RMS                                        | 30 dias
Utilização da aplicação RMS activado                           | 30 dias
