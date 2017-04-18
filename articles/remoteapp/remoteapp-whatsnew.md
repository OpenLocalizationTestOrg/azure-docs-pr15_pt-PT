
<properties
    pageTitle="O que há de novo no Azure RemoteApp? | Microsoft Azure"
    description="Saiba mais sobre as alterações e melhorias efetuadas RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="whats-new-in-azure-remoteapp"></a>O que há de novo no Azure RemoteApp?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Uma das vantagens do Azure RemoteApp é que estamos sempre a trabalhar para melhorar a mesma. Sempre que recomendamos fazer, podemos irá anunciar essas alterações aqui.

## <a name="future-updates"></a>Futuras atualizações
Olhe - Sabia que a equipa do Azure RemoteApp publicações mensais atualizações no blogue RDS? Pode encontrar não apenas o que é a alteração no Azure RemoteApp, mas também outras informações sobre como utilizar RDS. Consulte o artigo seu blogue, [Blogue de serviços de ambiente de trabalho remoto](https://blogs.msdn.microsoft.com/rds/), para obter informações. Por exemplo, algumas semanas atrás, publicados uma entrada sobre [elevação e deslocar das cargas de trabalho com RemoteApp do Azure e Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>De Setembro de 2015
- Infopath adicionado à imagem de modelo e galeria do Microsoft Office 365. Se quiser partilhar Infopath, certifique-se atualizar as suas coleções de com a imagem mais recente.
- Atualizações de cliente:
    - Cliente do Windows atualizado para o tornar possíveis para os utilizadores para partilhar comentários, especialmente à volta de problemas de ligação.
    - iOS cliente atualizado para corrigir o erro de mensagens e para corrigir um problema onde as suas credenciais expiraram anterior que o esperado.
- Estamos a trabalhar no obter suporte do Office 2016 testada. Assim que estiver concluída, procure imagens atualizadas.
- Publicado um novo artigo sobre as [diferenças entre colecções na nuvem e híbrido](remoteapp-collections.md) - Isto irá ajudá-lo escolha o tipo de coleções de sites que funciona melhor para as aplicações - apenas na nuvem, nuvem + VNET ou híbrido.
- Pretende partilhar do QuickBooks utilizando RemoteApp do Azure mas não tem a certeza dos passos? Consulte o [artigo novo de Eric](remoteapp-quickbooks.md) informar exatamente o que fazer.

## <a name="august-2015"></a>Agosto de 2015
Alterações grandes aconteceu em Agosto - Eis os realces:

- Agora pode usar um VNET Azure uma coleção de nuvem! Consulte as [instruções de criação da nuvem](remoteapp-create-cloud-deployment.md) para obter os passos de novos.
- Proporcionou adicionar aplicações ao menu **Iniciar **para o cliente RemoteApp do Windows. Aplicações irão aparecer na lista da aplicação e, pode afixá-las ao menu **Iniciar **no Windows.
- Adicionar uma nova imagem na Galeria de Azure VM - Windows servidor remoto ambiente de trabalho anfitrião da sessão com o Microsoft Office 365 ProPlus.
- Fixo o cliente do Mac para que as aplicações com o windows modais deixarão de fixar.
- Documentados como pode utilizar a sua [subscrição do Office 365 ProPlus](remoteapp-officesubscription.md) com RemoteApp do Azure.
- Detalhadas como pode [proteger as aplicações e os dados](remoteapp-secure.md) na sua coleção de RemoteApp do Azure.

## <a name="july-2015"></a>De Julho de 2015

Julho definir a fase para alterações provenientes de Agosto, pelo que não é muito para falar sobre agora, principalmente as atualizações do documento. Eis as alterações mais recentes:

- Foi adicionado um separador **de suporte** para o portal para que possa aceder aos recursos de suporte, como os fóruns mais facilmente.
- Otimizados as informações de resolução de problemas para a criação de uma coleção de híbrido. Como dar saída [a mais recente e maior](remoteapp-hybridtrouble.md) para sugestões de resolução de problemas, como identificar as portas corretas para configurar para o seu VNET.
- Documentados como os [dados de utilizador](remoteapp-upd.md) é criada e guardada no Azure RemoteApp.
- Documentados como [Bloquear para baixo de aplicações](remoteapp-secure.md).
- Publicado os [cmdlets do Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- E, por fim, vamos começar uma conversação com alguns utilizadores RemoteApp do Azure sobre a terminologia. Procure por alterações à forma como podemos referir-se para as opções de coleções de sites diferente.

## <a name="june-2015"></a>Junho de 2015

Alterações tantas! A equipa foi muito ocupada em Junho:

- Restruturação e o Azure RemoteApp [página de destino](https://www.remoteapp.windowsazure.com/) - dar saída do mesmo!
- Atualização do software no todas as imagens disponíveis como parte da sua subscrição.
- Efetuadas melhorias coleções de híbrido, incluindo suporte de túnel e verificar o tamanho de sub-rede IP antes de tentar criar a coleção de forçada.
- Descobertas que o * universais não funcionam para câmaras Web. Em vez disso, tem de especificar o ID da instância ou GUID. Vamos vai ser atualizar as informações de redirecionamento para refletir que.
- Efetuadas-lo para que possa adicionar software de antivírus personalizado à sua imagem quando cria uma imagem do modelo a partir da Galeria Azure.

Temos mais alterações gradual em Julho, por isso Pedimos vai ser novamente com outra atualização mais rapidamente.

## <a name="may-2015"></a>Maio de 2015

Ter ocorrido um número de adições (e meses) desde podemos criada pela primeira vez neste tópico, para que esta lista prejudicando os publicitários um pouco e é desde o início do Março através de Maio. Dar saída destas novas funcionalidades:

- Automatizar tudo - RemoteApp do Azure tem agora [cmdlets no módulo Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
- [Criar uma imagem de RemoteApp do Azure a partir de uma máquina virtual Azure](remoteapp-image-on-azurevm.md). Torna carregar a imagem personalizada para Azure muito mais rápido.
- Utilize um VNET Azure em vez de um VNET RemoteApp para ligar os recursos da rede da empresa para Azure. Vamos atualizou as [instruções de coleções de sites híbrido](remoteapp-create-hybrid-deployment.md) para guiá-lo a criar um VNET Azure (é passo 1).
- Falar de VNETs, veja [as novas informações](remoteapp-vnetsizing.md) à volta de limitações e limites de tamanho VNET.
- E falar dos limites - apenas quais são os [limites de serviço e predefinições](../azure-subscription-service-limits.md)?

Pretende saber mais sobre RemoteApp do Azure? A equipa de RemoteApp foi saída em vigor no Ignite há alguns semanas. Veja o vídeo de Eric, [os conceitos básicos do Microsoft Azure RemoteApp gestão e administração](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Necessita de ver RemoteApp do Azure no mundo real? Consulte o artigo o tutorial de [executar qualquer aplicação em qualquer dispositivo em qualquer lugar](remoteapp-anyapp.md) - -mostra-lhe como partilhar o acesso com os seus utilizadores, incluindo a partilha de ficheiros de base de dados. Também temos um tutorial sobre como [tornar o Office 365](remoteapp-tutorial-o365anywhere.md) a executar o mesmo em qualquer dispositivo.

Obrigado por aderente connosco - trás próximo mês com as atualizações mais.


### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.
