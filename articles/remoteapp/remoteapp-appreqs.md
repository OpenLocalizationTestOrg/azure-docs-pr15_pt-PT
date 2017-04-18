
<properties
    pageTitle="Requisitos da aplicação para RemoteApp do Azure | Microsoft Azure"
    description="Saiba mais sobre os requisitos para as aplicações que pretende utilizar no RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="app-requirements"></a>Requisitos da aplicação

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp suporta transmissão 32 bits ou 64 bits baseados no Windows aplicações a partir de uma imagem do Windows Server 2012 R2. Maioria das versões de 32 bits ou 64 bits baseados no Windows aplicações existentes executar "tal como está" no RemoteApp do Azure (Serviços de ambiente de trabalho remoto ou anteriormente conhecido como os serviços de Terminal) ambiente. No entanto, existe uma diferença entre a executar e a funcionar corretamente - algumas aplicações funcionam corretamente e executar bem, enquanto outras pessoas não o fizer. As seguintes informações fornecem orientações para desenvolver aplicações num ambiente de serviços de ambiente de trabalho remoto e testar para garantir a compatibilidade.

Sugestão: Estamos a trabalhar no criar alguns exemplos de trabalho de aplicações para si. Verá novos tópicos que descrevem através do Microsoft Access, QuickBooks e V de aplicação no RemoteApp.

## <a name="requirements"></a>Requisitos de
Estes três requisitos, se seguido, ajudam a sua aplicação executar corretamente no RemoteApp:

1.  Aplicações que cumprem todos os [requisitos de certificação para aplicações de ambiente de trabalho do Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) e adiram [diretrizes de programação de serviços de ambiente de trabalho remoto](https://msdn.microsoft.com/library/aa383490.aspx) terá concluída compatibilidade com RemoteApp.
2.  Aplicações nunca devem armazenar dados localmente na imagem ou instâncias de RemoteApp que podem ser perdidas.  Depois de criar uma coleção de RemoteApp, as instâncias são clonar são sem estado e deverão conter apenas aplicações. Armazenar dados numa origem externa ou dentro do perfil de utilizador.
3.  Imagens personalizadas nunca devem conter dados que podem ser perdidos.  

## <a name="testing-your-apps"></a>Testar as suas aplicações
Utilize estes passos para testar aplicações:

1.  Instalar o Windows Server 2012 R2 e a sua aplicação
2.  Ativar o ambiente de trabalho remoto
3.  Crie duas contas de utilizador, utilizadora e UtilizadorB, adicionar ambas as contas de utilizador ao grupo de segurança de ambiente de trabalho remoto.
4.  Verificar a compatibilidade de múltiplas sessões estabelecendo duas simultâneas RDS sessões ao PC ao iniciar a aplicação.
5.  Validar o comportamento de aplicação

## <a name="application-development-guidelines"></a>Directrizes de desenvolvimento de aplicações
Utilize as seguintes diretrizes para desenvolver aplicações para RemoteApp.

### <a name="multiple-users"></a>Vários utilizadores

- Instalar uma [aplicação de um único utilizador ](https://msdn.microsoft.com/library/aa380661.aspx)pode criar problemas num ambiente de vários utilizadores.
- Aplicações devem [armazenar informações específicas do utilizador](https://msdn.microsoft.com/library/aa383452.aspx) nas localizações específicas do utilizador, separadamente a partir da informação global que se aplica a todos os utilizadores.
- RemoteApp utiliza vários [espaços de nomes para objetos kernel](https://msdn.microsoft.com/library/aa382954.aspx); um espaço de nomes global é utilizado principalmente pelos serviços em aplicações de cliente/servidor.
- Não é seguro para partem do princípio de que o nome do computador ou o [endereço IP](https://msdn.microsoft.com/library/aa382942.aspx) atribuídos ao computador estão associadas a um único utilizador porque vários utilizadores podem ser iniciados em simultâneo para um servidor de anfitrião da sessão ambiente de trabalho remoto (anfitrião da sessão de RD).

### <a name="performance"></a>Desempenho
- Desative [efeitos gráficos](https://msdn.microsoft.com/library/aa380822.aspx) antes de adicionar a sua aplicação para RemoteApp.
- Para maximizar a disponibilidade de CPU para todos os utilizadores, desativar [tarefas em segundo plano](https://msdn.microsoft.com/library/aa380665.aspx) ou criar tarefas em segundo plano eficiente que não estão consumir muitos recursos.
- Deve ajustar e [a utilização de tópico](https://msdn.microsoft.com/library/aa383520.aspx) da aplicação para um ambiente de vários utilizadores, com vários processadores de saldo.
- Para otimizar o desempenho, é aconselhável para as aplicações para [detetar](https://msdn.microsoft.com/library/aa380798.aspx) se estiverem em execução numa sessão de cliente.
