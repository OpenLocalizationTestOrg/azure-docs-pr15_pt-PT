<properties
    pageTitle="Segurança para concentradores de notificação"
    description="Este tópico explica segurança para concentradores notificação Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Segurança

##<a name="overview"></a>Descrição geral

Este tópico descreve o modelo de segurança do Azure notificação concentradores. Porque notificação concentradores são uma entidade Bus de serviço, estes implementam o modelo de segurança do mesmo como Bus de serviço. Para mais informações, consulte os tópicos de [Autenticação do serviço de Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Acesso partilhado assinatura segurança (SA) 

Notificação concentradores implementa um esquema de segurança de nível de entidade chamado SA (partilhados acesso assinatura). Este esquema permite entidades mensagens declarar até 12 regras de autorização na sua descrição que concedam os direitos nessa entidade.

Cada regra contém um nome, um valor de chave (segredo partilhado) e um conjunto de direitos, tal como é explicado na secção "Violação de segurança". Ao criar um concentrador de notificação, duas regras são criadas automaticamente: uma com direitos de ouvir (que utiliza a aplicação de cliente) e uma com todos os direitos (que utiliza o back-end aplicação).

Quando efetuar a gestão de registo a partir das aplicações de cliente, se as informações enviadas através de notificações não é sensível (por exemplo, meteorologia atualizações), é uma forma comum para aceder a um concentrador de notificação para dar o valor de chave da regra acesso só de ouvir para a aplicação de cliente e para dar o valor de chave do acesso completo a regra para o aplicação back-end.

Não é recomendado que incorporar o valor de chave nas aplicações de cliente da loja Windows. Uma forma de evitar incorporar o valor de chave é para que a aplicação de cliente recuperá-la a partir da aplicação back-end no arranque.

É importante compreender a tecla com o access ouvir permite a uma aplicação de cliente para se registar no qualquer etiqueta. Se a sua aplicação tem Restringir registos a etiquetas específicas para clientes específicos (por exemplo, quando etiquetas representam IDs de utilizador), o seu back-end aplicação deve efetuar os registos. Para obter mais informações, consulte Gestão de registo. Tenha em atenção que desta forma, a aplicação de cliente não terão acesso direto aos concentradores de notificação.

##<a name="security-claims"></a>Violação de segurança

Semelhante a outras entidades, operações de notificação concentrador são permitidas três violação de segurança: ouvir, enviar e gerir.

| Afirmação | Descrição | Operações permitidas |
|-------|-------------|--------------------|
| Ouvir | Atualização/criar, ler e eliminar registos únicos | Criar/actualizar registo<br><br>Registo de leitura<br><br>Todos os registos para uma alça de leitura<br><br>Eliminar registo |
| Enviar | Enviar mensagens para o concentrador de notificação | Enviar mensagem |
| Gerir | CRUDs concentradores notificação (incluindo a atualizar as credenciais PNS e chaves de segurança) e leitura registos com base nas etiquetas | Concentradores criar/actualizar/leitura/eliminar notificação<br><br>Ler registos por etiqueta |


Notificação concentradores aceitam em afirmações concedidas por tokens de controlo de acesso do Microsoft Azure e por gerado com chaves partilhadas configuradas diretamente no centro do notificação de tokens de assinatura.