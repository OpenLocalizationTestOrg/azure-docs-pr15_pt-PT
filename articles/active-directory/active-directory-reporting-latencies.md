<properties
   pageTitle="Azure Active Directory relatórios latências | Microsoft Azure"
   description="Quantidade de tempo que demora para eventos de elaboração de relatórios a aparecer na sua Azure Active Directory"
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

# <a name="azure-active-directory-report-latencies"></a>Latências de relatório do Azure Active Directory

*Esta documentação faz parte do [Azure elaboração de relatórios Guia do Active Directory](active-directory-reporting-guide.md).*

Relatório                                                  | Mínimo  | Média    | Máximo
------------------------------------------------------- | -------- | ---------- | ----------
**Relatórios de segurança**                                    |          |            |
Atividade de início de sessão irregular                              | 2 horas  | 4 horas    | 8 horas
Inícios de sessão a partir de origens desconhecidas                           | 2 horas  | 4 horas    | 8 horas
Inícios de sessão após várias falhas                        | 2 horas  | 4 horas    | 8 horas
Inícios de sessão a partir de várias zonas geográficas                      | 2 horas  | 4 horas    | 8 horas
Início de sessão-ins proveniente de endereços IP com atividade suspeita     | 2 horas  | 4 horas    | 8 horas
Inícios de sessão a partir de dispositivos possivelmente infectados                 | 2 horas  | 4 horas    | 8 horas
Utilizadores com atividade de início de sessão anómalo                   | 2 horas  | 4 horas    | 8 horas
Utilizadores com credenciais fuga                           | 2 horas  | 4 horas    | 8 horas
**Relatórios de aplicação**                                 |          |            |
Atividade de aprovisionamento de conta                           | 2 horas  | 4 horas    | 8 horas
Erros de aprovisionamento de conta                             | 2 horas  | 4 horas    | 8 horas
Utilização da aplicação                                       | 2 horas  | 4 horas    | 8 horas
Estado de sobreposição de palavra-passe                                | 2 horas  | 4 horas    | 8 horas
**Auditoria e relatórios de atividade**                            |          |            |
Relatório de auditoria                                            | 1 minuto | 15 minutos | 30 minutos
Repor a palavra-passe atividade (Azure AD)                      | 2 horas  | 4 horas    | 8 horas
Repor a palavra-passe atividade (Gestor de identidade)              | 2 horas  | 4 horas    | 8 horas
Repor a palavra-passe actividade de registo (Azure AD)         | 2 horas  | 4 horas    | 8 horas
Repor a palavra-passe actividade de registo (Gestor de identidade) | 2 horas  | 4 horas    | 8 horas
Self service na atividade de grupos (Azure AD)                 | 2 horas  | 4 horas    | 8 horas
Self service na atividade de grupos (Gestor de identidade)         | 2 horas  | 4 horas    | 8 horas
**Relatórios de RMS**                                         |          |            |
Utilizadores do RMS mais ativos                                   | 2 horas  | 4 horas    | 8 horas
Utilização do RMS                                               | 2 horas  | 4 horas    | 8 horas
Utilização do dispositivo RMS                                        | 2 horas  | 4 horas    | 8 horas
Utilização da aplicação RMS activado                           | 2 horas  | 4 horas    | 8 horas
**Privado pré-visualizar relatórios**                             |          |            |
Atividade de início de sessão do utilizador todos os                               | 2 horas  | 4 horas    | 8 horas
