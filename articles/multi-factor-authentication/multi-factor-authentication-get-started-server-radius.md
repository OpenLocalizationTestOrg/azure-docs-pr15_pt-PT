<properties 
    pageTitle="Servidor de autenticação Multifator de autenticação e RADIUS Azure"
    description="Esta é a página de autenticação multifator Azure que o podem ajudar na implementação de autenticação e RADIUS servidor de autenticação Multifator Azure."
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticação Multifator de autenticação e RADIUS Azure

A secção autenticação RADIUS permite-lhe ativar e configurar autenticação RADIUS para o servidor de autenticação Multifator Azure. RADIUS é um protocolo padrão para aceitar pedidos de autenticação e os pedidos de processo. O servidor de autenticação Multifator Azure age como um servidor RADIUS e é inserido entre o cliente RADIUS (por exemplo, aparelho VPN) e o destino de autenticação, que pode ser Active Directory (AD), um directório LDAP ou outro servidor RADIUS, para poder adicionar Azure a autenticação Multifator. Para autenticação Multifator de Azure funcione, tem de configurar o servidor de autenticação Multifator Azure para que possa comunicar com os servidores de cliente e o destino da autenticação. O servidor de autenticação Multifator Azure aceita pedidos a partir de um cliente RADIUS, valida credenciais contra o alvo de autenticação, adiciona a autenticação Multifator de Azure e envia uma resposta de volta para o cliente RADIUS. A autenticação inteira será bem sucedida apenas se a autenticação principal e a autenticação Multifator Azure teve êxito.

>[AZURE.NOTE]
>O servidor de MFA suporta apenas PAP (protocolo de autenticação de palavra-passe) e MSCHAPv2 (protocolo de autenticação de Handshake desafio da Microsoft) RADIUS protocolos quando serve como um servidor RADIUS.  Outros protocolos como EAP (protocolo de autenticação extensible) podem ser utilizados quando o servidor MFA age como um proxy RADIUS para outro servidor RADIUS que suporta esse protocolo como Microsoft NPS.
></br>
>Ao utilizar outros protocolos nesta configuração, não irão funcionar unidirecional tokens SMS e JURAMENTO uma vez que o servidor MFA não é possível iniciar uma resposta contestação RADIUS com êxito utilizar esse protocolo.


![Autenticação RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configuração de autenticação RADIUS

Para configurar a autenticação de RADIUS, instale o servidor de autenticação Multifator Azure num servidor do Windows. Se tiver um ambiente do Active Directory, o servidor deve ser associado ao domínio dentro da rede. Utilize o seguinte procedimento para configurar o servidor de autenticação Multifator Azure:

1. Dentro do servidor de autenticação Multifator Azure clique no ícone de autenticação RADIUS no menu à esquerda.
2. Selecione a caixa de verificação de autenticação RADIUS ativar.
3. No separador clientes altere a porta (s) de autenticação e contabilidade porta (s) se o serviço de autenticação RADIUS Multifator Azure deverá associar-portas normalizadas para ouvir para pedidos RADIUS de clientes que serão configuradas.
4. Clique em Adicionar... botão.
5. Na caixa de diálogo Adicionar cliente RADIUS, introduza o endereço IP do aparelho/servidor que irá autenticar para o servidor de autenticação Multifator Azure, nome de uma aplicação (opcional) e uma palavra-passe partilhado. O segredo partilhado terá de ser o mesmo no servidor de autenticação Multifator Azure e aparelho/servidor. O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile.
6. Selecione a caixa de correspondência de utilizador do exigir autenticação Multifator se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a autenticação multifator. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será isentos de autenticação multifator, deixe a caixa desmarcada. Consulte o artigo o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
7. Selecione a caixa token ativar contingência JURAMENTO se os utilizadores vão usar a autenticação de aplicação móvel do Azure a autenticação Multifator e que pretende utilizar os códigos de acesso JURAMENTO como um método de autenticação de contingência para a notificação de chamada, SMS ou push de telefone fora de banda.
8. Clique em OK.
9. Poderá Repita os passos 4 a 8 para adicionar clientes RADIUS adicionais.
10. Clique no separador de destino.
11. Se o servidor de autenticação Multifator Azure estiver instalado num servidor façam parte de um domínio no ambiente do Active Directory, selecione o domínio do Windows.
12. Se os utilizadores devem ser autenticados contra um directório LDAP, selecione enlace LDAP. Ao utilizar enlace LDAP, tem de clicar no ícone de integração de diretórios e editar a configuração de LDAP no separador Definições para que o servidor pode ligar para o seu diretório. Podem encontrar instruções para configurar LDAP no guia de configuração do LDAP Proxy.
13. Se os utilizadores devem ser autenticados contra outro servidor RADIUS, selecione RADIUS servidor (es).
14. Configurar o servidor de que o servidor irá proxy os pedidos de RADIUS ao clicar em Adicionar... botão.
15. Na caixa de diálogo Adicionar servidor RADIUS introduza o endereço IP do servidor RADIUS e uma palavra-passe partilhado. O segredo partilhado terá de ser o mesmo no servidor a servidor de autenticação Multifator Azure e o raio. Altere a porta de autenticação e porta de contabilidade se portas diferentes são utilizadas pelo servidor RADIUS.
16. Clique em OK.
17. Tem de adicionar o servidor de autenticação Multifator Azure como um cliente RADIUS no servidor RADIUS para que processará enviados à mesma a partir do servidor de autenticação Multifator Azure de pedidos de acesso. Tem de utilizar o mesmo segredo partilhado configurado no servidor de autenticação Multifator Azure.
18. Poderá Repita este passo para adicionar os servidores RADIUS adicionais e configurar a ordem pela qual o servidor deverá contactar o-los com os botões Mover para cima e mover para baixo. Este procedimento conclui a configuração do servidor de autenticação Multifator Azure. O servidor está agora a escutar as portas configuradas para pedidos de acesso RADIUS a partir de clientes configurados.   


## <a name="radius-client-configuration"></a>Configuração de cliente RADIUS

Para configurar o cliente RADIUS, utilize as diretrizes:

- Configure o seu servidor/aparelho para autenticar através do RADIUS ao endereço IP do Azure Multifator autenticação do servidor, que irá funcionar como o servidor RADIUS.
- Utilize o mesmo segredo partilhado que foi configurado acima.
- Configure o tempo limite RADIUS para 30 60 segundos para que houver tempo para validar as credenciais do utilizador, executar a autenticação multifator, receber a sua resposta e, em seguida, responder ao pedido de acesso RADIUS.
