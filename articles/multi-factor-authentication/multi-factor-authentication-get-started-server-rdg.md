<properties 
    pageTitle="Remoto Gateway de ambiente de trabalho e servidor de autenticação Multifator Azure utilizar RADIUS"
    description="Esta é a página de autenticação multifator Azure que irá ajudar a implementar o Gateway de ambiente de trabalho remoto (RD) e o servidor de autenticação Multifator Azure utilizando RADIUS."
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

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Remoto Gateway de ambiente de trabalho e servidor de autenticação Multifator Azure utilizar RADIUS

Em muitos casos, o Gateway de ambiente de trabalho remoto utiliza o NPS local para autenticar os utilizadores. Este documento descreve como encaminhar o pedido RADIUS saída a partir do Gateway de ambiente de trabalho remoto (através de NPS local) para o servidor de autenticação Multifator.

O servidor de autenticação Multifator devem ser instalado num servidor separado, que irá, em seguida, proxy o pedido RADIUS voltar a NPS no servidor de Gateway de ambiente de trabalho remoto. Após o NPS validar o nome de utilizador e palavra-passe, irá devolver uma resposta para o servidor de autenticação Multifator que executa o segundo factor de autenticação antes de devolver um resultado para o gateway.





## <a name="configure-the-rd-gateway"></a>Configure o Gateway RD

O Gateway RD tem de ser configurado para enviar a autenticação RADIUS para um servidor de autenticação Multifator Azure. Depois de ter sido instalado RD Gateway, configurada e se está a trabalhar, vá para as propriedades de RD Gateway. Aceda ao separador arquivo de REMATE RD e altere-o para utilizar um servidor Central com NPS em vez de servidor Local com NPS. Adicione um ou mais servidores de autenticação Multifator Azure como servidores RADIUS e especifique uma palavra-passe partilhada para cada servidor.





## <a name="configure-nps"></a>Configurar o NPS

O Gateway RD utiliza NPS para enviar o pedido RADIUS para Azure a autenticação Multifator. Um tempo limite deve ser alterado para impedir que o Gateway RD de esgotar o tempo limite antes de autenticação multifator estar concluída. Utilize o seguinte procedimento para configurar o NPS.

1. No NPS, expandir o menu de servidor e clientes RADIUS na coluna da esquerda e clique em grupos de servidor RADIUS remotos. Vá para as propriedades do grupo de servidor de GATEWAY TS. Edite o servidor (es) RADIUS apresentados e aceda ao separador balanceamento de carga. Alterar o "número de segundos sem resposta antes do pedido é considerado ignorados" e "Número de segundos entre pedidos quando o servidor é identificado como indisponível" para 30 60 segundos. Clique no separador conta/de autenticação e certifique-se de que as portas RADIUS especificadas correspondem as portas que o servidor de autenticação Multifator irá escutar.
2. NPS também tem de ser configurado para receber autenticação RADIUS novamente a partir do servidor de autenticação Multifator Azure. Clique em clientes do RADIUS no menu à esquerda. Adicione o servidor de autenticação Multifator Azure como um cliente RADIUS. Selecione um nome amigável e especificar um segredo partilhado.
3. Expanda a secção políticas no painel de navegação esquerdo e clique em políticas de pedido de ligação. Deve conter uma política de pedido de ligação denominado política de autorização de GATEWAY TS que foi criado quando o campo RD Gateway foi configurado. Esta política reencaminha pedidos RADIUS para o servidor de autenticação Multifator.
4. Copie esta política para criar um novo. Na nova política, adicione uma condição que corresponda ao nome amigável cliente com o nome amigável definir no passo 2 acima para o cliente de servidor de autenticação do Azure Multifator RADIUS. Altere o fornecedor de autenticação para o computador Local. Esta política assegura que, quando um pedido de raio é recebido a partir do servidor de autenticação Multifator Azure, a autenticação ocorre localmente em vez de enviar um pedido RADIUS novamente para o servidor de autenticação Multifator Azure que resulte numa condição ciclo. Para impedir que a condição de ciclo, tem de ser encomendada esta nova política acima a política original que reencaminha para o servidor de autenticação Multifator.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar uma autenticação Multifator Azure


--------------------------------------------------------------------------------



O servidor de autenticação Multifator Azure está configurado como um proxy RADIUS entre RD Gateway e NPS.  Devem ser instalada num servidor façam parte de um domínio que está separado do servidor de RD Gateway. Utilize o seguinte procedimento para configurar o servidor de autenticação Multifator Azure.

1. Abra o servidor de autenticação Multifator do Azure e clique no ícone de autenticação RADIUS. Selecione a caixa de verificação de autenticação RADIUS ativar.
2. No separador de clientes, certifique-se de que as portas correspondem o que é configurado no NPS e clique em Adicionar... botão. Adicione o endereço IP do servidor de RD Gateway, nome da aplicação (opcional) e uma palavra-passe partilhada. O segredo partilhado terá de ser o mesmo no servidor de autenticação Multifator Azure e RD Gateway.
3. Clique no separador de destino e selecione o botão de opção RADIUS servidor (es).
4. Clique em Adicionar... botão. Introduza o endereço IP, segredo partilhado e portas do servidor NPS. A menos que a utilizar um NPS central, o cliente RADIUS e destino RADIUS serão os mesmos. O segredo partilhado tem de corresponder a uma configuração na secção de cliente RADIUS do servidor NPS.

![Autenticação RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
