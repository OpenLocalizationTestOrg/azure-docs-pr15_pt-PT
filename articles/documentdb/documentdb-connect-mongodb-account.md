<properties 
    pageTitle="Ligar a uma conta de DocumentDB com o suporte de protocolo para MongoDB | Microsoft Azure" 
    description="Saiba como ligar a uma conta de DocumentDB com o suporte de protocolo para MongoDB, agora disponível para a pré-visualização. Ligar utilizando a cadeia de ligação MongoDB." 
    keywords="cadeia de ligação mongodb"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Como ligar a uma conta de DocumentDB com o suporte de protocolo para MongoDB

Saiba como ligar a uma conta do Azure DocumentDB com o suporte de protocolo para MongoDB utilizando o formato URI de cadeia de ligação da MongoDB padrão.  

## <a name="get-the-accounts-connection-string-information"></a>Obter informações de cadeia de ligação da conta

1. Numa nova janela, inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra de **Navegação à esquerda** do pá a conta, clique em **Cadeia de ligação**. Para navegar para a **Conta pá**, na Jumpbar clique em **Mais serviços**, clique em **DocumentDB (NoSQL)** e, em seguida, selecione a conta de DocumentDB com o suporte de protocolo para MongoDB.

    ![Captura de ecrã da pá a todas as definições](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. A **Informações da cadeia de ligação** pá abre e tem todas as informações necessárias para ligar à conta com um controlador para MongoDB, incluindo uma cadeia de ligação previamente construídos.

    ![Captura de ecrã da pá da cadeia de ligação](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de cadeia de ligação

É importante que tenha em atenção que DocumentDB suporta o MongoDB ligação cadeia URI formato padrão, com apenas alguns requisitos específicos: DocumentDB contas exigem autenticação e comunicação segura através de SSL.  Assim, é o formato da cadeia de ligação:

    mongodb://username:password@host:port/[database]?ssl=true

Onde os valores desta cadeia estão disponíveis no pá a cadeia de ligação mostrada acima.

- Nome de utilizador (obrigatório)
    - Nome da conta DocumentDB
- Palavra-passe (obrigatório)
    - Palavra-passe da conta de DocumentDB
- Anfitrião (obrigatório)
    - Conta FQDN do DocumentDB
- Porta (obrigatório)
    - 10250
- Base de dados (opcional)
    - A base de dados predefinido utilizado pela ligação
- SSL = true (obrigatório)

Por exemplo, considere a conta apresentada as informações da cadeia de ligação de acima.  Uma cadeia de ligação válida é:
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Ligar-se com o controlador de c# para MongoDB
Tal como já mencionado, todas as contas de DocumentDB exigem autenticação e comunicação segura através de SSL. Enquanto o formato URI da cadeia de ligação de MongoDB suporta um ssl = parâmetro da cadeia de consulta verdadeiro, trabalhar com o MongoDB c# controlador requer o uso do objeto MongoClientSettings quando criar uma MongoClient.  Tendo em conta as informações da conta acima, o fragmento de código seguinte mostra como ligar à conta e trabalhar com a base de dados "Tarefas".

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Próximos passos


- Saiba como [utilizar MongoChef](documentdb-mongodb-mongochef.md) com uma conta de DocumentDB com o protocolo de suporte para MongoDB.
- Explore DocumentDB com o suporte de protocolo para MongoDB [amostras](documentdb-mongodb-samples.md).

 
