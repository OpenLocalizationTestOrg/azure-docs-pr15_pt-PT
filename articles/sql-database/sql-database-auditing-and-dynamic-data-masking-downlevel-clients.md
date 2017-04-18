<properties
    pageTitle="Suportam de clientes de nível inferior da base de dados SQL e ponto final IP muda para a auditoria | Microsoft Azure"
    description="Saiba mais sobre o suporte de clientes do nível inferior de base de dados SQL e IP alterações de ponto final para auditoria."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>Base de dados SQL - suporte de clientes e alterações de ponto final IP de auditoria


[Auditoria](sql-database-auditing-get-started.md) automaticamente funciona com clientes do SQL que suporta o redirecionamento TDS.


##<a id="subheading-1"></a>Suporte de clientes

Qualquer cliente que implementa TDS 7.4 deve também suporta o redirecionamento. Exceções a esta incluem JDBC 4.0 na qual não é totalmente suportada a funcionalidade de redirecionamento e não foi implementado Tedious para Node.JS no qual redirecionamento.

"Clientes de nível inferior", ou seja, que suporte TDS 7.3 e abaixo - o FQDN do servidor na ligação de cadeia da versão deve ser modificada para:

Original FQDN do servidor na cadeia de ligação: <*nome do servidor*>. database.windows.net

Modificado FQDN do servidor na cadeia de ligação: <*nome do servidor*> .database. **seguro**. windows.net

Uma lista parcial das "Clientes de nível inferior" inclui:

- .NET 4.0 e abaixo,
- ODBC 10.0 e abaixo.
- JDBC (enquanto JDBC suporta TDS 7.4, a funcionalidade de redirecionamento TDS não é totalmente suportada)
- Entediante (para Node.JS)

**Comentário:** O servidor acima modificação FDQN poderá ser útil também para aplicar uma política de auditoria de nível do SQL Server sem necessidade de um passo de configuração de cada base de dados (mitigação temporária).

##<a id="subheading-2"></a>Alterações de ponto final IP quando activar auditoria

Tenha em atenção que quando ativa auditoria, irá alterar o ponto final de IP da base de dados. Se tiver as definições da firewall estritamente, actualize essas definições da firewall, consoante adequado.

O ponto final IP de base de dados novo será dependem de região de base de dados:

| Região de base de dados | Possíveis IP os pontos finais |
|----------|---------------|
| América do Norte China  | 139.217.29.176, 139.217.28.254 |
| Leste China  | 42.159.245.65, 42.159.246.245 |
| Leste Austrália  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sudeste Austrália | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sul do Brasil  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Central (EUA)  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Este asiático   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Leste dos EUA 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Leste dos EUA   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Índia central  | 104.211.98.219, 104.211.103.71 |
| Sul Índia   | 104.211.227.102, 104.211.225.157 |
| Índia Ocidental  | 104.211.161.152, 104.211.162.21 |
| Japão leste   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japão oeste    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| América do Norte Central (EUA)  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa Norte  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Sul Central (EUA)  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste asiático  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa Ocidental  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Ocidental dos e.u.a.  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canadá Central  | 13.88.248.106 |
| Leste Canadá  |  40.86.227.82 |
