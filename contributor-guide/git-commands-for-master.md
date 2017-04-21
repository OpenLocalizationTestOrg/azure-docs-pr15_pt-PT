<properties pageTitle="Comandos de Git para criar um novo artigo ou atualizar um artigo existente" description="Passos para trabalhar com o Azure técnico GitHub repositórios de conteúdo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Comandos de Git para criar um novo artigo ou atualizar um artigo existente


## <a name="standard-process-working-from-master"></a>Processo padrão (a trabalhar a partir do modelo global)
Siga os passos neste artigo para criar um ramo local de trabalho no seu computador para que possa criar um novo artigo para a secção de documentação técnica de azure.microsoft.com ou atualizar um artigo existente.

1. Inicie Git festa (ou a ferramenta de linha de comandos que utiliza para Git).

 **Nota:** Se estiver a trabalhar no repositório de público, altere o azure-conteúdo-pr para conteúdo azure em todos os comandos.

2. Alterar para azure-conteúdo-pr:

        cd azure-content-pr
3. Veja o ramo principal:

        git checkout master

4. Crie um ramo trabalho local fresca derivado de ramo principal:

        git pull upstream master:<working branch>


5. Mover para o nova ramo de trabalho:

        git checkout <working branch>

6. Permitir que o seu bifurcação saber que criou o ramo local de trabalho:

        git push origin <working branch>

7. Criar um artigo de novo ou efetuar alterações a um artigo existente. Utilize o Explorador do Windows para abrir e criar novos ficheiros markdown e utilizar Atom (http://atom.io) como o editor de markdown. Após que criou ou modificou o artigo e imagens, ir para o próximo passo.

8. Adicionar e as alterações que efetuou a consolidação:

        git add .
        git commit –m "<comment>"
        
   Em alternativa adicionar apenas o específico de ficheiros que modificou:

        git add <file path>
        git commit –m "<comment>"

   Caso tenha eliminado ficheiros, tem de utilizar esta:
   
        git add --all
        git commit -m "<comment>"

9. Atualize o seu local ramo de trabalhar com alterações da montante:

        git pull upstream master

10. Notificações push as alterações ao seu bifurcação no GitHub:

        git push origin <working branch>

12. Quando estiver pronto para submeter o seu conteúdo para o montante ramo principal de teste de validação, e/ou publicação, na IU GitHub, crie um pedido de solicitação a partir do seu bifurcação para o ramo principal.

13. Se for um funcionário trabalhar no repositório de privado, as alterações que submete são automaticamente testadas e uma ligação de transição é escrita ao pedido de solicitação. Reveja o seu conteúdo faseado e início de sessão os comentários de pedido de solicitação ao adicionar o comentário **#sign-off** .  Isto indica que as alterações estão prontas para ser seguia direto.  Se não quiser que o pedido de solicitação para ser aceite - se apenas são teste as alterações - adicione a nota **#hold-off** ao pedido de solicitação.

14. O acceptor pedido solicitação revê o seu pedido de solicitação, fornece comentários e/ou aceita o pedido de solicitação. 

15. Opcionalmente, verifique a sua artigo publicado ou alterações na

 http://Azure.microsoft.com/Documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publicação

- Artigos não são publicados cerca de 10:00 AM e 3:00 PM hora do Pacífico, segunda a sexta. Pode demorar até 30 minutos para artigos que seja apresentado online depois da publicação. Lembre-se de que o seu pedido de solicitação tem de ser intercalados por um revisor de pedido de solicitação antes das alterações que podem ser incluídas na próxima publicação agendada executar. Tem de trabalhar com o seu revisor de pedido de solicitação antecedência para garantir que um pedido de solicitação intercalado para uma publicação específica executar. Caso contrário, PRs são revistas pela ordem que foram submetidos.

- Se for um funcionário trabalhar no repositório de privado, todos os pedidos de solicitação estão sujeitos a regras de validação de que precisam de ser resolvidos antes do pedido de solicitação pode ser intercalado. 

## <a name="working-with-release-branches"></a>Trabalhar com ramificações de lançamento

Quando estiver a trabalhar com uma ramificação lançamento, é a melhor forma de criar um ramo local de trabalho a partir do ramo de lançamento utilizar esta sintaxe de comando:

    git checkout upstream/<upstream branch name> -b <local working branch name>

Esta ação cria o ramo local diretamente a partir do ramo montante, evitando quaisquer intercalação local.

