<properties 
    pageTitle="Conhecidos redes | Microsoft Azure" 
    description="Ao configurar redes conhecidos, pode evitar ter endereços IP que são propriedade pela sua organização incluída na ins de início de sessão a partir de várias zonas geográficas e ins de início de sessão proveniente de endereços IP com relatórios de atividade suspeita." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Redes conhecidos


Pode utilizar o access do Azure Active Directory e relatórios de utilização para ganham visibilidade sobre a integridade e a segurança do diretório da sua organização. Com esta informação, um administrador de diretório melhor pode determinar onde possíveis riscos de segurança podem situar-se para que possam adequadamente planear a mitigar esses riscos.

É possível que os relatórios "*ins de início de sessão a partir de várias zonas geográficas*" e "*ins de início de sessão a partir de endereços IP com atividade suspeita*" Sinalizar incorretamente endereços IP que são realmente propriedade pela sua organização. 

Isto pode, por exemplo, acontecer quando: 

- Um utilizador no seu Boston office tem sessão iniciada no remotamente para o seu centro de dados em San Francisco accionadores o relatório "Assinar ins a partir de várias zonas geográficas" 

- Um utilizador da sua organização tentará início de sessão no várias vezes com accionadores uma palavra-passe incorretas o relatório "Assinar ins proveniente de endereços IP com atividade suspeita" 

Para impedir que nestes casos gerar enganadora relatórios de segurança, deve adicionar conhecidos intervalos de endereços IP à lista de endereço IP público da sua organização.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para adicionar os intervalos de endereços IP públicos da sua organização, execute os seguintes passos: 

1.  Início de sessão no [portal de gestão Azure](https://manage.windowsazure.com).

2.  No painel esquerdo, clique em **Do Active Directory**. <br><br>![Como funciona a deteção de aplicação na nuvem](./media/active-directory-known-networks/known-netwoks-01.png)

3.  No separador do **diretório** , selecione o seu diretório.

4.  No menu no canto superior, clique em **Configurar**. <br><br>![Como funciona a deteção de aplicação na nuvem](./media/active-directory-known-networks/known-netwoks-02.png)

5.  No separador configurar, aceda à **sua intervalos de endereços IP públicos organizações** <br><br>![Como funciona a deteção de aplicação na nuvem](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Clique em **Adicionar intervalos de endereços IP conhecido**.

7.  Adicionar o seu intervalos de endereços na caixa de diálogo que aparece e, em seguida, clique no botão verificar quando tiver terminado. <br><br>![Como funciona a deteção de aplicação na nuvem](./media/active-directory-known-networks/known-netwoks-04.png)


**Recursos adicionais**


* [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md)
* [Iniciar sessão ins a partir de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Suplementos de início de sessão a partir de várias zonas geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)


