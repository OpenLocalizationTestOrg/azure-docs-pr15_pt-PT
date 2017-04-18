<properties
   pageTitle="Implementar a sua oferta ao Azure Marketplace | Microsoft Azure"
   description="Saiba mais sobre e percorra as instruções para implementar a sua oferta – máquina virtual imagem, serviço de programador, serviço de dados, etc. – ao Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implementar a sua oferta ao Azure Marketplace
Quando estiver satisfeito com a sua oferta (ou seja, ter testado cenários de cliente, marketing conteúdo, etc.) e estiver pronto para iniciar, do pedido **de emissão para produção** no separador **Publicar** .  

1. Os quatro passos em TUTORIAL página de publicação portal deve ser concluídas e verde. Para ofertas de Máquina Virtual, certifique-se de que as diretrizes seguintes são seguidas.

    ![desenho][img-pubportal-walkthru-checked]

2. Selecione o separador **Publicar** a partir da lista no lado esquerdo.

    ![desenho][img-pubportal-menu-publish]

3. Clique no botão **Pedir aprovação para transmitir para produção**. Assim que é apresentado o pedido, a equipa de aprovação executa uma revisão final e, em seguida, a sua oferta estarão disponível na Azure Marketplace.

    ![desenho][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] Em caso de máquinas virtuais, quando clica no botão aprovação pedido para transmitir para produção, os passos seguintes são executados atrás da cena. Poderá ver o progresso da cada passo no separador Publicar de publicação portal. Tem de verificar esta página em intervalos regulares (até que o estado apresenta "Previstos") para quaisquer informações de falha que precisa de correção a partir do seu expiram.

> - Na primeira o seu pedido de produção vai para a equipa de certificação que validar o vhd. No entanto, se está a atualizar a sua oferta já listada e o pedido tem tem apenas de marketing alterar, em seguida, o passo de certificação será ignorado.
> - No passo seguinte, o pedido ao sítio a equipa de validação de conteúdo que verifique o conteúdo da oferta de marketing.
> - Se os passos acima estiverem bem sucedidos, em seguida, a oferta é aprovada, de produção. Neste momento, o estado tornam-se "listado" no portal de publicação. No entanto, este estado "Previstos" não significam que o processo está concluído. Os passos seguintes têm de ser concluída antes da oferta está disponível no Azure Marketplace.
> - Assim que a oferta é aprovada produção no passo anterior, a replicação da oferta iniciar em todos os centros de dados Azure. Geralmente leva-o até 24-48hours para a replicação concluir mas poderá demorar uma semana, dependendo do tamanho do vhd. No entanto, se está a atualizar a sua oferta já listada e tem apenas de marketing alterar, em seguida, a replicação é mais rápida.
> - Quando a replicação estiver concluída, em seguida, a oferta estarão disponível no Azure Marketplace.

> Pode eliminar a oferta sempre enquanto a mesma se encontra no estado de **rascunho** (por exemplo, nunca **notificações Push para transição** ou **Push produção**). No separador **Histórico** , clique no botão **Rejeitar rascunho** na parte inferior da página para eliminar um rascunho.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de verificação de produção para todas as ofertas de Máquina Virtual

- Certifique-se de que se encontra um parceiro de certificados do Microsoft Azure
- No separador SKUs, a opção "Ocultar este SKU de mercado porque sempre deve ser comprada através de um modelo de solução" deve ser marcada como Sim apenas se o SKU faz parte de um modelo de solução. Em todos os outros casos, esta opção sempre deve ser marcada como não.
- Lembre-se: Não deve alterar a definição de visibilidade SKU assim que o SKU está listado. Vamos não suportam esta funcionalidade.
- Certifique-se de que os logótipos adiram as diretrizes de logótipo do Azure Marketplace indicadas abaixo.
- Descrição de oferta e SKU não deve ser o mesma.
- Do SKU título e oferecem longo resumo não devem ser o mesmo.
- Título SKU e oferecem resumo não devem ser o mesmo.
- Títulos de SKU não deve ser idênticos para uma oferta com múltiplas SKUs.

**Diretrizes de logótipo do Azure Marketplace**

- A estrutura Azure tem uma paleta de cores simples. Mantenha o número de principal e secundário cores no seu logótipo baixa.
- As cores do tema do Azure portal estão em brancas e preto. Por conseguinte, evite utilizar estas cores como a cor de fundo da sua logótipos. Utilize algumas cor que pretende tornar seu logótipos proeminentes no portal do Azure. Recomendamos que cores primárias simples. Se estiver a utilizar o fundo transparente, em seguida, certifique-se de que o logótipo/texto não está em branco ou preto.
- Não utilize um fundo com gradação no logótipo.
- Evite a colocar o texto, mesmo sua empresa ou marca corporativa nome, o logótipo.
- O aspeto e funcionalidade do seu logótipo deve ser 'simples' e deve evitar gradações de cor.
- Não deve ser esticado o logótipo.

**Directrizes adicionais para o logótipo da imagem:**

- O logótipo herói é opcional. O publisher pode optar por não carregar um logótipo de imagem. **No entanto uma vez carregados não pode ser eliminado no ícone de imagem a partir de publicação portal. Nesse momento, o parceiro tem de seguir as diretrizes do Azure Marketplace para ícones de herói else que não será aprovada a oferta de produção.**
- O nome a apresentar o Publisher, título SKU e a oferta de tempo de resumo são apresentadas numa cor do tipo de letra em branco. Por conseguinte, deverá evitar manter qualquer simplificada cor no fundo do ícone de imagem. Preto, fundo branco e transparente não é permitido para os ícones de imagem.
- O publisher apresentar nome, o título SKU, a oferta resumo tempo e o botão Criar estão incorporados através de programação dentro do logótipo herói assim que a oferta vai listada. Por isso, não deve introduzir qualquer texto enquanto está a estruturar o logótipo da imagem. Deixar apenas espaço em branco à direita, uma vez que o texto (ou seja, apresentação do Publisher atribuir um nome, título SKU, a oferta tempo resumo) será incluído através de programação por-ao longo do mesmo. O espaço em branco para o texto deve ser 415 x 100 à direita (e desvio por 370px a partir da esquerda).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de verificação de produção adicionais para Máquina Virtual já listados oferece

- Verificar se já existe uma oferta com o mesmo nome de oferta da sua empresa. Se Sim, em seguida, deve adicionar uma nova versão do SKU na oferta de existente em vez de criar uma nova oferta duplicada.
- Disco de dados não deve alterar entre duas versões da SKU do mesmo.
- Azure Marketplace não suporta a alteração dos preços das SKUS listadas como impactos-lo a faturação dos clientes existentes. Certifique-se de que não altere os preços das SKUs listadas nas regiões onde o SKU está disponível. No entanto, pode adicionar novos SKUs ou adicionar novas regiões a um SKU existente.


## <a name="next-steps"></a>Próximos passos
Assim que a oferta entra em direto, teste os cenários de cliente para validar que todas as funcionalidades de contratos e funcionam corretamente no ambiente de produção como testado e validadas no ambiente de teste.

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
