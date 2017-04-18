<properties 
    pageTitle="Servidor LDAP autenticação e Azure autenticação Multifator"
    description="Esta é a página de autenticação multifator Azure que irá ajudar a implementar o LDAP autenticação e de servidor de autenticação Multifator Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Servidor LDAP autenticação e Azure autenticação Multifator


Por predefinição, o servidor de autenticação Multifator Azure está configurado para importar ou sincronizar os utilizadores a partir do Active Directory. No entanto, este pode ser configurado para vincular a directórios LDAP diferentes, tal como um diretório de AFONSO ou controlador de domínio do Active Directory específico. Quando configurado para ligar a um diretório através do LDAP, o servidor de autenticação Multifator Azure pode ser configurado para funcionar como um proxy LDAP para efetuar a autenticação. Também permite ao uso dos enlace LDAP como um alvo de RADIUS, para a pré-autenticação de utilizadores quando utilizando a autenticação do IIS ou para autenticação principal no Portal de utilizador do Azure Multifator autenticação.

Quando utilizar o Azure a autenticação Multifator como um proxy LDAP, o servidor de autenticação Multifator Azure é inserido entre o cliente LDAP (por exemplo, aparelho VPN, aplicação) e o servidor de directório LDAP para poder adicionar autenticação multifator. Para a autenticação Multifator de Azure a função, o servidor de autenticação Multifator Azure tem de ser configurado para comunicar com os servidores de cliente e o directório LDAP. Nesta configuração, o servidor de autenticação Multifator Azure aceita LDAP pedidos a partir do cliente servidores e aplicações e encaminha-os para o servidor de directório LDAP de destino para validar as credenciais principais. Se a resposta do directório LDAP mostra que primária credenciais são válidos, Azure a autenticação Multifator executa a autenticação do segundo factor e envia uma resposta de volta para o cliente LDAP. A autenticação inteira será bem sucedida apenas se a autenticação para o servidor LDAP e a autenticação multifator teve êxito.





## <a name="ldap-authentication-configuration"></a>Configuração de autenticação LDAP


Para configurar a autenticação de LDAP, instale o servidor de autenticação Multifator Azure num servidor do Windows. Utilize o seguinte procedimento:

1. Dentro do servidor de autenticação Multifator Azure clique no ícone de autenticação LDAP no menu à esquerda.
2. Selecione a caixa de verificação ativar a autenticação de LDAP.![Autenticação LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. No separador clientes altere as portas TCP e SSL se o serviço LDAP de autenticação Multifator de Azure deverá associar-portas normalizadas para escutar LDAP pedidos de clientes que serão configuradas.
4. Se planeia utilizar LDAPS partir do cliente para o servidor de autenticação Multifator Azure, tem de estar instalado um certificado SSL no servidor que o servidor está em execução no. Clique no botão Procurar... botão junto à caixa certificado SSL e selecione o certificado instalado que será utilizado para a ligação segura.
5. Clique em Adicionar... botão.
6. Na caixa de diálogo Adicionar cliente LDAP, introduza o endereço IP do aparelho, servidor ou da aplicação que irá autenticar para o servidor e nome de uma aplicação (opcional). O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile.
7. Selecione a caixa de correspondência de utilizador do exigir autenticação Multifator do Azure se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a autenticação de múltipla fatores. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será isentos de autenticação de múltipla fatores, deixe a caixa desmarcada. Consulte o artigo o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
8. Poderá Repita os passos 5 a 7 para adicionar clientes LDAP adicionais.
9. Quando a autenticação Multifator Azure está configurada para receber autenticação LDAP, tem a proxy esses autenticação para o directório LDAP. Por conseguinte, o separador de destino só apresenta por única, cinzento opção para utilizar um alvo LDAP. Para configurar a ligação ao directório LDAP, clique no ícone de integração de diretórios.
10. No separador Definições, selecione o uso específico LDAP configuração botão de opção.
11. Clique em Editar... botão.
12. Na caixa de diálogo Editar LDAP configuração, preencha os campos com as informações necessárias para ligar para o directório LDAP. Descrições dos campos estão incluídas na tabela abaixo. Nota: Esta informação também é incluída no ficheiro de ajuda de servidor de autenticação Multifator Azure.![Integração de diretórios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Teste a ligação LDAP ao clicar no botão de teste.
14. Se o teste de ligação LDAP foi efetuada com êxito, clique em OK.
15. Clique no separador de filtros. O servidor é pré-configurada para carregar os contentores, grupos de segurança e os utilizadores a partir do Active Directory. Se vincular para um directório LDAP diferente, provavelmente terá de editar os filtros de apresentado. Clique na ligação de ajuda para obter mais informações sobre filtros.
16. Clique em separador atributos. O servidor é pré-configurada para mapear os atributos do Active Directory.
17. Se vincular um directório LDAP diferente ou alterar os mapeamentos de atributo pré-configurada, clique em Editar... botão.
18. Na caixa de diálogo Editar atributos, modificar os mapeamentos de atributo LDAP para o seu diretório. Nomes de atributo podem ser escritos na ou seleccionados clicando em de... botão ao lado de cada campo.
19. Clique na ligação de ajuda para obter mais informações sobre os atributos.
20. Clique em OK.
21. Clique no ícone definições da empresa e selecione o separador Resolução do nome de utilizador.
22. se ligar ao Active Directory a partir de um servidor façam parte de um domínio, deverá conseguir deixar os identificadores de segurança de utilizar o Windows (SID) para a correspondência de botão de opção de nomes de utilizador seleccionado. Caso contrário, selecione o atributo de identificador exclusivo LDAP de utilização do botão de opção liste correspondente. Quando selecionada, o servidor de autenticação Multifator Azure tentar resolver cada nome de utilizador para um identificador exclusivo no directório LDAP. Uma pesquisa LDAP será executada no nome de utilizador atributos definidos na integração do diretório -> separador atributos. Quando um utilizador autentica, o nome de utilizador será ser resolvido para o identificador exclusivo no directório LDAP e o identificador exclusivo será utilizado para a correspondência de utilizador no ficheiro de dados de autenticação Multifator de Azure. Esta opção permite-comparações maiúsculas e minúsculas, como nome de utilizador assim tão longa e curta formatos Isto conclui a configuração do servidor de autenticação Multifator Azure. O servidor está agora a escutar as portas configuradas para pedidos de acesso LDAP a partir de clientes configurados e definir proxy os pedidos para o directório LDAP para autenticação.


## <a name="ldap-client-configuration"></a>Configuração do cliente LDAP

Para configurar o cliente LDAP, utilize as diretrizes:

- Configure o seu aparelho, o servidor ou a aplicação para autenticar através do LDAP para o servidor de autenticação Multifator Azure como se estivesse no diretório da sua LDAP. Deve utilizar as mesmas definições que normalmente utilizaria para ligar diretamente para o seu diretório LDAP, exceto o nome do servidor ou o endereço IP que será que o servidor de autenticação Multifator Azure.
- Configure o tempo limite LDAP para 30 60 segundos para que não existem está na altura de validar as credenciais do utilizador com o directório LDAP, executar a autenticação do segundo fator, receber a sua resposta e, em seguida, responder a pedidos de acesso a LDAP.
- Se utilizar LDAPS, o aparelho ou o servidor tornando as consultas LDAP deve confiar o certificado SSL instalado no servidor de autenticação Multifator do Azure.
