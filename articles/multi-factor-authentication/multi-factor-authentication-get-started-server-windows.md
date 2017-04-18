<properties 
    pageTitle="Autenticação do Windows e Azure servidor de autenticação Multifator"
    description="Esta é a página de autenticação multifator Azure que irá ajudar a implementar o autenticação do Windows e o servidor de autenticação Multifator Azure."
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

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação do Windows e Azure servidor de autenticação Multifator

A secção autenticação do Windows permite ao administrador ativar e configurar a autenticação do Windows para uma ou mais aplicações.  Segue-se uma lista de coisas a ter em conta antes da configurar a autenticação do Windows.

-  é necessário reiniciar o computador antes da autenticação Multifator Azure para serviços de Terminal estará em vigor.
-  Se 'Correspondência de utilizador exigir autenticação Multifator do Azure' está selecionada e não estiver na lista de utilizador, não conseguir iniciar sessão para o computador após reiniciar o computador.
-  IPs de confiança é dependente se a aplicação pode fornecer IP do cliente com a autenticação. Serviços de Terminal atualmente apenas é suportado.  







>[AZURE.NOTE]Esta funcionalidade não é suportada para serviços de Terminal seguro no Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger uma aplicação com autenticação do Windows, utilize o procedimento seguinte.

1. Em servidor de autenticação Multifator Azure clique no ícone de autenticação do Windows.
![Autenticação do Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Selecione a caixa de verificação de autenticação do Windows ativar. Por predefinição, esta caixa está desmarcada.
3. No separador aplicações permite ao administrador configurar um ou mais aplicações para a autenticação do Windows.
4. Seleccionar um servidor ou aplicação – Especifique se a aplicação de servidor/está ativada. Clique em OK.
5. Clique em Adicionar... botão.
6. No separador IPs de confiança permite-lhe ignorar sessões Azure Multifator autenticação para Windows com origem IPs específico. Por exemplo, se os funcionários utilizam a aplicação a partir do office e a partir de casa, pode decidir que não pretende telefones a tocar para autenticação Multifator de Azure enquanto no escritório. Para tal, teria especifique a sub-rede do office como entrada IPs de confiança.
7. Clique em Adicionar... botão.
8. Selecione IP único se pretender para ignorar um único endereço IP.
9. Selecione o intervalo de IP se gostaria ignorar um intervalo de IP inteiro. Exemplo 10.63.193.1-10.63.193.100.
10. Selecione sub-rede se pretender especificar um intervalo de IPs utilizando a notação sub-rede. Introduza IP de inicial a sub-rede e escolha a máscara de rede adequada a partir da lista pendente.
11. Clique em OK.
