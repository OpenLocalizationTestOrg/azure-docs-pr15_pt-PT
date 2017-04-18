<properties 
    pageTitle="Definições de registo de deteção de aplicação para Proxy serviços em nuvem | Microsoft Azure" 
    description="O objectivo deste tópico é fornecer-lhe os passos que precisa para executar para definir a porta necessária em computadores com o agente de deteção de aplicação na nuvem." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Definições de registo de deteção de aplicação na nuvem para serviços de Proxy

Por predefinição, o agente de deteção de aplicação de nuvem está configurado para utilizar apenas as portas 80 e 443. Se estiver a planear sobre como instalar nuvem aplicação deteção num ambiente com um servidor proxy, que está a utilizar uma porta personalizada (nem 80 nem 443), terá de configurar o seu agentes para utilizar esta porta. A configuração baseia-se uma chave de registo.


O objectivo deste tópico é fornecer-lhe os passos que precisa para executar para definir a porta necessária em computadores com o agente de deteção de aplicação na nuvem.



**Para modificar a porta utilizada pelo computador com o agente de deteção de aplicação de nuvem, execute os seguintes passos:**


1. Inicie o editor de registo. <br> ![Executar](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Navegue até ao ou crie a seguinte chave de registo: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud Discovery\Endpoint de aplicação** 

3. Crie um novo valor de **cadeia com várias** denominado **portas**. ![Novo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Para abrir a caixa de diálogo **Editar cadeia com várias** , faça duplo clique sobre o valor de portas.


5. Na caixa de texto de dados de valor, escreva os seguintes valores e adicionar todas as portas personalizadas que são utilizadas pela sua organização: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Editar cadeia múltipla](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Clique em **OK** para fechar a caixa de diálogo **Editar cadeia com várias** .



**Recursos adicionais**


* [Como pode descobrir as aplicações de nuvem unsanctioned que são utilizadas na minha organização](active-directory-cloudappdiscovery-whatis.md) 


