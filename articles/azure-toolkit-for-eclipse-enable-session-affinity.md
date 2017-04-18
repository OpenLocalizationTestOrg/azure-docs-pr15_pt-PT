<properties
    pageTitle="Ativar afinidade sessão utilizando o Toolkit de Azure para Eclipse"
    description="Saiba como ativar afinidade sessão utilizando o Toolkit de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Ativar afinidade sessão #

Dentro do Toolkit de Azure para Eclipse, pode ativar a afinidade da sessão HTTP ou "sessões autocolantes", para as funções. A imagem seguinte mostra as propriedades de **Balanceamento de carga na** caixa de diálogo utilizada para ativar a funcionalidade de afinidade sessão:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Para ativar afinidade sessão para o seu perfil ##

1. A função no Project Explorer do Eclipse com o botão direito, clique em **Azure**e, em seguida, clique em **Balanceamento de carga**.
1. Na caixa de diálogo **Propriedades de balanceamento de carga WorkerRole1** :
    1. Verificar **afinidade do sessão Enable HTTP (sessões autocolantes) para esta função.**
    1. **Ponto final de introdução para utilizar**, selecione um ponto final teclado para utilizar, por exemplo, **http (público: 80, privado: 8080)**. A aplicação tem de utilizar este ponto final como respectivo ponto final HTTP. Pode ativar vários pontos finais para o seu perfil, mas pode selecionar apenas uma para suportar sessões autocolantes.
    1. Recriar a sua aplicação.

Quando ativada, se tiver mais do que uma instância de função, pedidos de HTTP provenientes de um cliente em particular irão continuar a ser processadas por na mesma instância de função.

O Toolkit de Eclipse permite isto instalando um módulo IIS especial denominado aplicação pedir encaminhamento (ARR) para cada um dos seus instâncias de função. ARR é reencaminhada pedidos de HTTP para a instância de papel adequado. O toolkit reconfigura automaticamente o ponto final selecionado para que o tráfego de HTTP receber pela primeira vez é encaminhado para o software ARR. O toolkit também cria um novo ponto final interno que o servidor de Java está configurado para ouvir. Esse é o ponto final utilizado pelo ARR para reencaminhar o tráfego HTTP para a instância de papel adequado. Desta forma, cada instância de funções na sua implementação com várias instâncias funciona como um proxy inverso para todas as outras instâncias, permitindo-sessões autocolantes.

## <a name="notes-about-session-affinity"></a>Notas sobre afinidade sessão ##

* Afinidade de sessão não funciona no emulador de cluster. As definições podem ser aplicadas o emulador cluster sem interferir com o processo de criação ou calcular execução emulador, mas a funcionalidade propriamente dito não funcionar dentro o emulador cluster.
* Activar a sessão afinidade irá resultar num aumento espaço em disco ocupado pela sua implementação no Azure, tal como será transferido e instalado no seu instâncias de função quando for iniciado seu serviço na nuvem Azure software adicional.
* O tempo a iniciar cada função irá demorar mais tempo.
* Um ponto final interno, a funcionar como um rerouter tráfego, tal como mencionado acima, será added.ss

Para obter um exemplo de como manter os dados da sessão quando está ativada afinidade sessão, consulte o artigo [como manter dados da sessão com a sessão afinidade][].

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

[Como manter os dados da sessão com a sessão afinidade][]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Como manter os dados da sessão com a sessão afinidade]: http://go.microsoft.com/fwlink/?LinkID=699539
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
