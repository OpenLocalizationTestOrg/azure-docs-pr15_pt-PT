<properties
    pageTitle="O que fazer em caso de um Azure service interrupção afeta Azure chave Cofre | Microsoft Azure"
    description="Saiba o que fazer em caso de uma interrupção de serviço Azure afeta Azure chave cofre."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure chave cofre disponibilidade e redundância

Azure chave cofre funcionalidades de várias camadas de redundância para se certificar de que as sua teclas e segredos permanecem disponíveis para a sua aplicação mesmo se componentes individuais do serviço de falharem.

O conteúdo do Cofre de chave é de replicadas dentro de uma região e para uma região secundária, pelo menos, 150 milhas ausente, mas dentro a geografia mesmo. Isto mantém alta durabilidade dos seus teclas e segredos.

Se componentes individuais dentro do serviço de chave cofre falharem, componentes alternativos dentro de uma região passo para servir o seu pedido para se certificar de que não existe nenhuma degradação da funcionalidade. Não necessita de tomar qualquer ação acionado isto. -Acontece automaticamente e serão transparente para si.

No evento raro que não está disponível uma região Azure completa, os pedidos de que faz das Azure chave cofre nesse região são automaticamente encaminhada (*Ocorreu uma falha ao longo*) para uma região secundária. Quando a região primária está disponível novamente, pedidos são encaminhados anterior (*falhou novamente*) para a região principal. Novamente, não tem de tomar qualquer ação, uma vez que isto acontece automaticamente.

Existem alguns avisos ter em consideração:

* Em caso de falha na ligação região, poderá demorar alguns minutos para que o serviço falhar ao longo do. Poderão falhar pedidos que são efectuados durante este período de tempo até que esteja concluída a activação pós-falha.
* Depois de um activação pós-falha estiver concluída, é Cofre de palavras chave no modo só de leitura. São pedidos que são suportados deste modo:
 * Lista chaves cofres
 * Obter as propriedades de cofres chaves
 * Lista segredos
 * Obter segredos
 * Teclas de lista
 * Obter chaves (propriedades de)
 * Encriptar
 * Desencriptar
 * Moldagem de texto
 * Moldagem
 * Verifique se
 * Início de sessão
 * Cópia de segurança
* Após uma activação pós-falha é falhado novamente, todos os tipos de pedido (incluindo a leitura *e* pedidos de escrita) estão disponíveis.
