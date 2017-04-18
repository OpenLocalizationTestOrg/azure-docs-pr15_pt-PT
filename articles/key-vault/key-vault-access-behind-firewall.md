<properties
    pageTitle="Aceder a chave cofre atrás firewall | Microsoft Azure"
    description="Saber como aceder à chave Cofre de uma aplicação protegido por uma firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Aceder a chave cofre atrás firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>P: a minha aplicação de cliente cofre chave tem de estar protegido por uma firewall, que endereços de portas/anfitriões/IP se deve abrir para permitir o acesso a chave Cofre?

Para aceder à sua aplicação de cliente do cofre chave tem de ser possível aceder à vários pontos finais para várias funcionalidades chave cofre.

- Autenticação (através do Azure Active Directory)
- Gestão da chave cofre (que inclui criar/leitura/actualizar/eliminar e também a definição de políticas de acesso) através do Gestor de recursos do Azure
- Aceder a e gerir objectos (teclas e segredos) armazenados num cofre chave propriamente dito, percorre o ponto de fim cofre chave específico (por exemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Dependendo do seu ambiente e configuração, existem algumas variações.   

## <a name="ports"></a>Portas

Todo o tráfego para cofre chave para todas as 3 funções (autenticação, gestão e plano acesso a dados) abrange HTTPS: porta 443. No entanto para LCR, haverá ocasionalmente tráfego HTTP (porta 80). Os clientes que suportam OCSP não devem atinja CRL, mas poderão, ocasionalmente, atingir [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticação

Aplicação de cliente do cofre chave terá de aceder ao Azure Active Directory pontos finais para autenticação. O ponto final utilizado depende da configuração de inquilino AAD e o tipo de capital – principal de utilizador, principal do serviço e o tipo de conta, por exemplo, Account Microsoft ou ID de organização.  

| Tipo de capital | Ponto final: porta |
|----------------|---------------|
| Utilizador com Account Microsoft<br> (por exemplouser@hotmail.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.CN:443<br><br>**Azure Governo dos Estados Unidos:**<br> us.microsoftonline.com:443 de início de sessão<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443<br><br> e <br>login.Live.com:443   |
| Principais de utilizador/serviço utilizar (por exemplo, o ID da Org com AADuser@contoso.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.CN:443<br><br>**Azure Governo dos Estados Unidos:**<br> us.microsoftonline.com:443 de início de sessão<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443 |
| Principais de utilizador/serviço utilizando organização ID + ADFS ou outro ponto final Federado (por exemplouser@contoso.com) | Todos os pontos finais acima para ID da Org plus ADFS ou outros pontos finais federados |

Existem outras situações complexas possíveis. Consulte [Azure Active Directory autenticação fluxo](/documentation/articles/active-directory-authentication-scenarios/), [Aplicações de integração com o Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para obter informações adicionais.  

## <a name="key-vault-management"></a>Gestão de cofre chave

Para gestão de cofre chave (CRUD e definir a política de acesso), a aplicação de cliente cofre chave tem de aceder ao ponto final do Gestor de recursos do Azure.  

| Tipo de operação | Ponto final: porta |
|----------------|---------------|
| Operações de plano de controlo do cofre chave<br> através do Gestor de recursos Azure | **Global:**<br> Management.Azure.com:443<br><br> **Azure China:**<br> Management.chinacloudapi.CN:443<br><br> **Azure Governo dos Estados Unidos:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Alemanha:**<br> Management.microsoftazure.de:443 |
| Gráfico do Azure Active Directory API | **Global:**<br> Graph.Windows.NET:443<br><br> **Azure China:**<br> Graph.chinacloudapi.CN:443<br><br> **Azure Governo dos Estados Unidos:**<br> Graph.Windows.NET:443<br><br> **Azure Alemanha:**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operações de cofre chave

Para todos os gestão de objeto (teclas e segredos) cofre chave e operações de criptografia, cliente cofre chave tem de aceder ao ponto final do cofre chave. Dependendo da localização do Cofre de palavras chave, o sufixo DNS de ponto final é diferente. O ponto final do Cofre de chave é o formato: < cofre nome >. < Região--sufixo dns específico-> conforme descrito na tabela abaixo.  

| Tipo de operação | Ponto final: porta |
|----------------|---------------|
| Operações de chave cofre como operações de criptografia em chaves, criado/leitura/actualizar/eliminar teclas e segredos, conjunto/obter etiquetas e outros atributos no objetos cofre chave (chaves/segredos)     | **Global:**<br> &lt;nome do cofre&gt;. vault.azure.net:443<br><br> **Azure China:**<br> &lt;nome do cofre&gt;. vault.azure.cn:443<br><br> **Azure Governo dos Estados Unidos:**<br> &lt;nome cofre&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;nome do cofre&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalos de endereços IP

Serviço de chave cofre sucessivamente utiliza outros recursos Azure como PaaS infraestrutura, por conseguinte, não é possível fornecer um intervalo específico de endereços IP que pontos finais de serviço do cofre chave terão a qualquer momento. No entanto se a firewall do suporta apenas endereço IP, intervalos, em seguida, consulte o documento de [Intervalos de IP do Microsoft Azure Centro de dados](https://www.microsoft.com/download/details.aspx?id=41653) .   Para a autenticação e de identidade (Azure Active Directory), a aplicação tem de conseguir ligar-se para os pontos finais descritos na [autenticação e endereços de identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Próximos passos

- Se tiver questões sobre cofre chave, visite os [Fóruns de Cofre de chave do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
