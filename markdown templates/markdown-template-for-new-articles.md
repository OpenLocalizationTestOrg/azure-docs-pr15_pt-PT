<properties
   pageTitle="Título da página que é apresentada nos resultados de pesquisa e do separador do browser"
   description="Descrição do artigo que será apresentada no páginas de destino e na maioria dos resultados da pesquisa"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Modelo de markdown (artigo cabeçalho 1 ou H1): cabeçalho na parte superior do artigo

Para copiar o markdown deste modelo, copie o artigo na sua repo local, ou clique no botão observou na IU GitHub e copie a markdown.

  ![Texto alternativo; não deixe em branco. Descreva a imagem.][8]

Parágrafo aqui: Lorem dolor amet, adipiscing elit. Risus no Phasellus interdum nulla, lacinia porta nisl imperdiet sed. Mauris dolor mauris, tempus sed lacinia nec, felis não euismod. Nunc semper porta ultrices. Maecenas neque nulla, condimentum vitae ipsum sentar amet, dignissim aliquet nisi.

## <a name="heading-2-h2"></a>Título 2 (H2)

Aenean sentar amet liderada nec purus placerat fermentum ac gravida odio. Aenean tellus lectus, faucibus no rhoncus no faucibus sed urna.  volutpat mi id purus ultrices iaculis nec não neque. [Texto da ligação para ligação fora azure.microsoft.com](http://weblogs.asp.net/scottgu). Nullam dolor de dictum na aliquam pharetra. Vivamus ac hendrerit mauris [texto da ligação de exemplo para a ligação para um artigo numa pasta de serviço](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Posso obter mais de 10 vezes tráfego do [Google]  [ gog] que a partir do [Yahoo]  [ yah] ou [MSN] [msn].

> [AZURE.NOTE] Texto da nota com avanço.  A palavra 'Nota' será adicionada durante a publicação. Ut da UE pretium lacus. Nullam purus é, iaculis sed é nível, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, da UE condimentum turpis nisi um purus.

1. Aenean sentar amet liderada nec **Purus** placerat fermentum ac gravida odio.

2. Aenean tellus lectus, faucius no **Rhoncus** no faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec não neque.

    ![Texto alternativo; não deixe em branco. Carro Recolectores no corridas vermelho.][5]

3. Nullam dolor de dictum na aliquam pharetra. Vivamus ac hendrerit mauris. Sed dolor dui, condimentum e varius por vehicula na nisl.

    ![Texto alternativo; não deixar em branco][6]


Suspendisse volutpat mi id purus ultrices iaculis nec não neque. Nullam dolor de dictum na aliquam pharetra. Vivamus ac hendrerit mauris. Otrus informatus: [1 de ligação para outro tópico de documentação azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Cabeçalho (H2)

Ut da UE pretium lacus. Nullam purus é, iaculis sed é nível, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, da UE condimentum turpis nisi um purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum prévia Lorem ipsum primis no faucibus orci luctus e ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam não <i>nisl molestie</i> pharetra eget um é. [Ligação 2 para outro tópico de documentação azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros nível lectus euismod auctor eget sentar amet liderada. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Título 2 (H2)

1. Maecenas sed condimentum nisi. Suspendisse potenti.

  + Fusce
  + Malesuada
  + Sem

2. Nullam em massa da UE tellus tempus hendrerit.

    ![Texto alternativo; não deixe em branco. Exemplo de um vídeo de 9 de canal.][7]

3. Quisque felis enim, fermentum ut aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Vestibul prévia Lorem ipsum primis no faucibus orci luctus e ultrices posuere cubilia Curae; Nullam ultricies, Lorem ipsum vitae volutpat hendrerit, eros do purus diam pretium, vitae tincidunt nulla lorem sed turpis: [3 de ligação para outro tópico de documentação azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
