<properties
    pageTitle="Configurar um nome de domínio para o ponto final de armazenamento de BLOBs | Microsoft Azure"
    description="Saiba como mapear um domínio de utilizador personalizadas para o ponto final de armazenamento Blob para uma conta de armazenamento Azure no Portal clássica do Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs

## <a name="overview"></a>Descrição geral

Pode configurar um domínio personalizado para aceder aos dados de BLOBs na sua conta de armazenamento Azure. O ponto final predefinido para o armazenamento de BLOBs é `<storage-account-name>.blob.core.windows.net`. Se mapear um domínio personalizado e subdomínio como **www.contoso.com** para o ponto final blob para a sua conta de armazenamento, em seguida, o utilizadores podem também aceder a dados de BLOBs na sua conta de armazenamento utilizar esse domínio.

>[AZURE.IMPORTANT] Armazenamento Azure ainda não suporta HTTPS com domínios personalizados. Estamos a cientes de que os clientes estiverem interessados nesta funcionalidade e irá estar disponível num lançamento futuro.

Existem duas formas de aponte o seu domínio personalizado para o ponto final blob para a sua conta de armazenamento. A forma mais simples é criar um registo CNAME mapeamento seu domínio personalizado e subdomínio para o ponto final blob. Um registo CNAME é uma funcionalidade DNS mapas um domínio de origem para um domínio de destino. Neste caso, o domínio de origem é o seu personalizado domínio e subdomínio – tenha em atenção que não o subdomínio sempre é necessário. O domínio de destino é o ponto final de serviço de Blobs.

O processo de mapeamento do seu domínio personalizado para o ponto final blob, no entanto, resultar num breve período de tempo de inatividade do domínio enquanto está a registar o domínio no [Portal clássica Azure](https://manage.windowsazure.com). Se o seu domínio personalizado está atualmente a suportar uma aplicação com um contrato de nível de serviço (SLA) que requer que não existem ser sem tempo de inatividade, pode utilizar o subdomínio Azure **asverify** para fornecer um passo intermédio registo para que os utilizadores poderão aceder ao seu domínio enquanto o DNS mapeamento demora local.

A tabela seguinte mostra os URLs de exemplo para aceder aos dados de BLOBs numa conta armazenamento denominada **mystorageaccount**. O domínio personalizado registado para a conta de armazenamento é **www.contoso.com**:

Tipo de recurso|Formatos de URL
---|---
Conta de armazenamento|**Predefinido URL:** http://mystorageaccount.blob.core.windows.net<p>**URL de domínio personalizado:** http://www.contoso.com</td>
Blob|**Predefinido URL:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL de domínio personalizado:** http://www.contoso.com/mycontainer/myblob
Contentor de raiz|**URL predefinido:** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$ raiz/myblob<p>**URL de domínio personalizado:** http://www.contoso.com/myblob ou http://www.contoso.com/$ raiz/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registe-se um domínio personalizado para a sua conta de armazenamento

Utilize este procedimento para registar o seu domínio personalizado, se não tiver dúvidas sobre ter o domínio não estar brevemente disponível para utilizadores ou se o seu domínio personalizado está não atualmente a alojar uma aplicação.

Se o seu domínio personalizado está atualmente a suportar uma aplicação que não pode ter qualquer tempo de inatividade, em seguida, utilize o procedimento descrito no <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">registar um domínio personalizado para a sua conta de armazenamento utilizando o subdomínio asverify intermédio</a>.

Para configurar um nome de domínio personalizado, tem de criar um novo registo CNAME com a sua entidade de registo de domínio. O registo CNAME Especifica um alias para um nome de domínio; Neste caso mapas o endereço do seu domínio personalizado para o ponto final armazenamento de BLOBs para a sua conta de armazenamento.

Cada entidade de registo tem um método semelhante, mas ligeiramente diferente para especificar um registo CNAME, mas o conceito é a mesma. Tenha em atenção que muitos pacotes de registo de domínio básicas não oferecem configuração de DNS, para que poderá ter de atualizar o seu pacote de registo de domínio antes de poder criar o registo CNAME.

1.  No [Portal clássica Azure](https://manage.windowsazure.com), navegue para o separador de **armazenamento** .

2.  No separador **armazenamento** , clique no nome da conta de armazenamento para as quais pretende mapear o domínio personalizado.

3.  Clique no separador **Configurar** .

4.  Na parte inferior do ecrã, clique em **Manage Domain** para apresentar a caixa de diálogo **Gerir domínio personalizado** . O texto na parte superior da caixa de diálogo, verá informações sobre como criar o registo CNAME. Para este procedimento, ignore o texto que refere-se para o subdomínio **asverify** .

5.  Inicie sessão no site do seu DNS entidade de registo e ir para a página para gerir DNS. Poderá encontrar esta numa secção como o **Nome de domínio**, **DNS**ou **Gestão de servidor de nomes**.

6.  Localize a secção para gerir CNAMEs. Poderá ter de aceder a uma página de definições avançadas e procure as palavras **CNAME**, **Alias**ou **subdomínios**.

7.  Crie um novo registo CNAME e fornecer um alias de subdomínio, como **www** ou **fotografias**. Em seguida, forneça um nome de anfitrião, que é o seu Blob ponto final de serviço, no formato **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). Para utilizar o nome do anfitrião é fornecido por si no texto da caixa de diálogo **Gerir domínio personalizado** .

8.  Depois de ter criado o registo CNAME, regressar à caixa de diálogo **Gerir domínio personalizado** e introduza o nome do seu domínio personalizado, incluindo o subdomínio, no campo **Nome de domínio personalizado** . Por exemplo, se o seu domínio é **contoso.com** , sendo o subdomínio **www**, introduza **www.contoso.com**; Se o seu subdomínio é **fotografias**, introduza **photos.contoso.com**. Tenha em atenção que o subdomínio não é necessário.

9. Clique no botão **Registar** para registar o seu domínio personalizado.

    Se o registo é bem sucedido, irá ver a mensagem **o seu domínio personalizado está ativo**. Os utilizadores podem agora ver blob dados no seu domínio personalizado, desde que tenham as permissões adequadas.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registe-se um domínio personalizado para a sua conta de armazenamento utilizando o subdomínio asverify intermédio

Utilize este procedimento para registar a sua página personalizada domínio se o seu domínio personalizado está atualmente a suportar uma aplicação com um SLA que requer que haver sem tempo de inatividade. Ao criar um CNAME que aponta a partir do asverify. &lt;subdomínio&gt;. &lt;customdomain&gt; para asverify. &lt;storageaccount&gt;. blob.core.windows.net, pode registar previamente o seu domínio com o Azure. Em seguida, pode criar um segundo CNAME que aponta a partir do &lt;subdomínio&gt;. &lt;customdomain&gt; para &lt;storageaccount&gt;. blob.core.windows.net, altura em que o tráfego para o seu domínio personalizado será direcionado para o seu ponto final blob.

O subdomínio asverify é um subdomínio especial reconhecido pelo Azure. Por prepending **asverify** a sua própria subdomínio, permitir Azure reconheçam o seu domínio personalizado sem modificar o registo DNS do domínio. Depois de modificar o registo DNS do domínio, ser mapeado para o ponto final blob com sem indisponibilidade.

1.  No [Portal clássica Azure](https://manage.windowsazure.com), navegue para o separador de **armazenamento** .

2.  No separador **armazenamento** , clique no nome da conta de armazenamento para as quais pretende mapear o domínio personalizado.

3.  Clique no separador **Configurar** .

4.  Na parte inferior do ecrã, clique em **Manage Domain** para apresentar a caixa de diálogo **Gerir domínio personalizado** . O texto na parte superior da caixa de diálogo, verá informações sobre como criar o registo CNAME utilizando o subdomínio **asverify** .

5.  Inicie sessão no site do seu DNS entidade de registo e ir para a página para gerir DNS. Poderá encontrar esta numa secção como o **Nome de domínio**, **DNS**ou **Gestão de servidor de nomes**.

6.  Localize a secção para gerir CNAMEs. Poderá ter de aceder a uma página de definições avançadas e procure as palavras **CNAME**, **Alias**ou **subdomínios**.

7.  Crie um novo registo CNAME e fornecer um alias de subdomínio que inclui o subdomínio asverify. Por exemplo, o subdomínio que especificar será no formato **asverify.www** ou **asverify.photos**. Em seguida, forneça um nome de anfitrião, que é o seu Blob ponto final de serviço, no formato **asverify.mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). Para utilizar o nome do anfitrião é fornecido por si no texto da caixa de diálogo **Gerir domínio personalizado** .

8.  Depois de ter criado o registo CNAME, regressar à caixa de diálogo **Gerir domínio personalizado** e introduza o nome do seu domínio personalizado no campo **Nome de domínio personalizado** . Por exemplo, se o seu domínio é **contoso.com** , sendo o subdomínio **www**, introduza **www.contoso.com**; Se o seu subdomínio é **fotografias**, introduza **photos.contoso.com**. Tenha em atenção que o subdomínio não é necessário.

9.  Clique na caixa de verificação que diz **Avançadas: Utilize o subdomínio 'asverify' para faça um pré-registo no meu domínio personalizado**.

10. Clique no botão **Registar** para faça um pré-registo no seu domínio personalizado.

    Se o preregistration for bem sucedido, irá ver a mensagem **o seu domínio personalizado está ativo**.

11. Neste momento, o seu domínio personalizado, tenha sido verificado pela Azure, mas o tráfego para o seu domínio ainda não está a ser encaminhado para a sua conta de armazenamento. Para concluir o processo, regressar ao Web site do seu DNS entidade de registo e criar outro registo CNAME que mapeie o subdomínio para o ponto final de serviço de Blobs. Por exemplo, especifique o subdomínio como **www** ou **fotografias**e o nome do anfitrião como **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). Este passo, o registo do seu domínio personalizado está concluído.

12. Por fim, pode eliminar o registo CNAME criada utilizando **asverify**, tal como estava necessária apenas como um passo intermédio.

Os utilizadores podem agora ver blob dados no seu domínio personalizado, desde que tenham as permissões adequadas.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Certifique-se de que o domínio personalizado referencia o ponto final de serviço de BLOBs

Para verificar que o seu domínio personalizado facto é mapeado para o seu ponto final de serviço de BLOBs, crie um blob num contentor público na sua conta de armazenamento. Em seguida, num browser, utilize um URI no seguinte formato para aceder o blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por exemplo, poderá utilizar o URI seguinte para aceder a um formulário web através de um subdomínio de personalizado **photos.contoso.com** mapas para um blob no seu contentor **myforms** :

-   http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Anular o registo de um domínio personalizado da sua conta de armazenamento

Para anular o registo de um domínio personalizado, siga estes passos: 

1. Inicie sessão no [Portal clássica Azure](https://manage.windowsazure.com). 

2. No painel de navegação, clique em **armazenamento**. 

3. Na página de **armazenamento** , clique no nome da conta de armazenamento para apresentar o dashboard. 

5. No Friso, clique em **Manage Domain**. 

6. Na caixa de diálogo **Gerir domínio personalizado** , clique em **Unregister**. 


## <a name="additional-resources"></a>Recursos adicionais

-   [Como mapear domínio personalizado para o ponto final de rede de entrega de conteúdos (CDN)](../cdn/cdn-map-content-to-custom-domain.md)
