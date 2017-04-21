# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Passos a seguir ao retirar ou alterar o nome de um artigo técnico ACOM

Estas orientações são para PME quem são listadas como autor de um artigo que precisa de ser foi removida da secção de documentação técnica da azure.microsoft.com. Os passos também se aplicam se cujo nome foi mudado é um ficheiro.

Se é um membro da nossa Comunidade Azure e pensa que deve ser foi removido um artigo por qualquer motivo, deixe um comentário na sequência Disqus comentário para o artigo permitir que o autor saber está mal com o artigo.

Autores de PME é necessário seguir vários passos para retirar correctamente conteúdo para que os utilizadores do Web site não tenham uma experiência incorretas quando podemos retirar o conteúdo do site. Eliminar o artigo ou alterar o nome deve ser a última coisa que acontece!

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Passo 1: Configurar o artigo para não índice/não seguir e publicá-lo (recomendado)

A primeira coisa que deve fazer é voltar a publicar o artigo como não índice/não siga alguns semanas antes de ter eliminado. Isto é considerado o melhor prática "pré-trabalho" para reforma conteúdo. Este procedimento remove o artigo índices do motor de pesquisa para que as pessoas não encontrará o artigo na pesquisa. [Consulte o artigo wiki do interno para obter detalhes.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Passo 2: Gerir as ligações de entrada (obrigatório)

Determine se existirem ligações não Microsoft entradas ao seu conteúdo. Frequentemente, blogues, fóruns e outro conteúdo na web aponta para artigos. Com frequência, pode trabalhar com os proprietários de blogue para alterar estas ligações e pode remover ou atualizar ligações a partir de fórum mensagens. Ferramentas de análise da Web podem indicar-lhe se existem as ligações de entrada de tráfego alta que poderá ter de gerir desta forma.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Passo 3: Remover todas as ligaçoes cruzadas para o artigo do repositório de conteúdos técnico (obrigatório)

Não confiar redirecionamentos para encarregam-ligaçoes cruzadas a partir de outros artigos. Atualizar ou remover a cruz referências para o artigo estiver a eliminar ou mudar o nome, incluindo nos artigos pertencentes a outras pessoas.

1. Certifique-se de que está a trabalhar um ramo de local atualizado – executar `git pull upstream master` (ou a variação sobre este comando adequada.

2.  Pesquisar a pasta azure-conteúdo pr/artigos e a pasta azure-conteúdo-pr/inclui para qualquer, os seus artigos e inclui dessa ligação para o artigo que pretende retirar e quer remover as ligaçoes cruzadas ou substituí-los com uma ligaçoes adequada cruzadas nova. Pode utilizar uma pesquisa e substituir utility para localizar as ligaçoes cruzadas se tiver um instalado. Caso não pretenda, pode utilizar o Windows PowerShell gratuitamente! Eis como utilizar o PowerShell para localizar as ligaçoes cruzadas:

 um. Inicie o Windows PowerShell.

 b. Na linha de comandos do PowerShell, altere para a pasta de conteúdo de azure-pr\articles:

 `cd azure-content-pr\articles`

 c. Escreva este comando, que irá listar todos os ficheiros que contêm uma referência para o artigo que está a eliminar:

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Se preferir enviar a lista de nomes de ficheiro para um ficheiro de texto (no psoutput.txt neste caso, com nome), pode:

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Adicionar e consolidar todas as suas alterações, emissão-los ao seu bifurcação e criar um pedido de solicitação para mover as suas alterações a partir do seu bifurcação para o ramo mestra do repositório principal.

## <a name="step-4-update-the-fwlink-tool-required"></a>Passo 4: Atualizar a ferramenta de ligação FW (obrigatório)

Verifique se a ferramenta de ligação FW qualquer FWLinks que pode apontar para o artigo. Aponte para qualquer FWLinks conteúdo de substituição Se não estiverem no alias que possui a ligação, aderir ao mesmo. Se os proprietários não os irá atualizar a ligação, ficheiro de uma permissão com MSCOM para que a ligação alterada. Mais informações - [interno wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Passo 5: Remover todas as ligaçoes cruzadas para o artigo de outras páginas no azure.microsoft.com e criar um redirecionamento para a página retirada, se apropriado (obrigatório)

Terá de trabalhar com a pessoa que mantém e atualiza a página de destino de documentação do seu serviço de categorias. Se não sabe com quem essa pessoa está, contacte o seu parceiro de conteúdo de equipa. A pessoa que mantém e atualiza a página de destino do documento terá de efetuar dois procedimentos:

1. No Visual Studio, pesquise a solução de web ACOM **inteira** , para o ficheiro para retirar referências cruzadas. Remover as referências cruzadas ou substitua-os com uma referência cruzada atualizada. Terá de remover as ligações HTML, bem como as cadeias de recursos relacionados para as ligações HTML. Mais informações - consulte o artigo [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Se existir um artigo de substituição, crie um redireccionamento. Mais informações - consulte o artigo [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Verifique as alterações para o repositório.

## <a name="step-6-retire-the-article"></a>Passo 6: Retirar o artigo

Depois de que concluiu os passos anteriores, e essas alterações são direto, pode eliminar o artigo do repositório de. 

**Importantes:** Quando eliminar ficheiros, tem de utilizar o `git add --all` comando.

## <a name="step-7-remove-links-from-msdn-required"></a>Passo 7: Remover ligações da MSDN (obrigatório)

Reveja a ferramenta de perguntas e respostas conteúda para ligações quebradas para o tópico retirada ou cujo nome foi mudado e remover/fix as ligações em todos os tópicos do MSDN afetados.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Passo 8: Remover páginas em cache de motores de busca (apenas se realmente necessário)

Faça este apenas se o conteúdo tem de ser removido rapidamente devido a problemas de cliente legais ou extremas. Por melhores práticas do Google, eliminações de página de prioridade normal apenas deverão ser resolvidas por processos do motor de pesquisa natural. Aceda a estas páginas web para remover páginas web em cache de motores de busca:

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Ligações de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)
