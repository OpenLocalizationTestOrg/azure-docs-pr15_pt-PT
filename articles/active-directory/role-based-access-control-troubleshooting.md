<properties
    pageTitle="Resolução de problemas de controlo de acesso baseado em funções | Microsoft Azure"
    description="Obtenha ajuda com problemas ou perguntas sobre recursos de controlo de acesso com base da função."
    services="azure-portal"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="kgremban"/>

# <a name="role-based-access-control-troubleshooting"></a>Resolução de problemas de controlo de acesso baseado em funções

## <a name="introduction"></a>Introdução

[Controlo de acesso baseado em funções](role-based-access-control-configure.md) é uma funcionalidade poderosa que permite-lhe acesso de delegado extensivamente para recursos no Azure. Isto significa que pode achar certeza conceder à direita para utilizar exatamente o que precisam de uma pessoa específica e não mais. No entanto, por vezes o modelo de recursos para recursos Azure pode ser complicado e pode ser difícil compreender exatamente o que está a conceder permissões para.

Este documento permitirá que sabe o que esperar quando através de alguns das funções no portal do Azure. Estes três funções abrangem todos os tipos de recursos:

- Proprietário  
- Contribuinte  
- Leitor  

Os proprietários e contribuintes têm acesso total a experiência de gestão de, mas um contribuinte pode dar acesso a outros utilizadores ou grupos. Coisas obter um pouco mais interessantes com a função de leitor, pelo que é onde podemos irá dedique algum tempo. Consulte o [artigo get-iniciado do controlo de acesso baseado em funções](role-based-access-control-configure.md) para obter detalhes sobre como conceder acesso.

## <a name="app-service-workloads"></a>Aplicação serviço das cargas de trabalho

### <a name="write-access-capabilities"></a>Funcionalidades de acesso de escrita

Se conceder acesso de utilizador só de leitura para um único web app, algumas funcionalidades são desativadas que não seria de esperar. As seguintes capacidades da gestão necessitam de acesso de **escrita** para uma aplicação web (contribuinte ou proprietário) e não estarão disponíveis em qualquer cenário só de leitura.

- Comandos (por exemplo, iniciar, parar, etc.)
- Alterar definições como configuração geral, as definições de escala, as definições de cópia de segurança e definições de monitorização
- Aceder a credenciais publicação e outros segredos como as definições da aplicação e cadeias de ligação
- Transmissão de registos
- Configuração de registos de diagnóstico
- Consola (linha de comandos)
- Implementações ativas e recentes (para implementação contínua local git)
- Estimativa passam
- Testes Web
- Rede virtual (visível apenas para um leitor de se uma rede virtual anteriormente tiver sido configurada por um utilizador com acesso de escrita).

Se não consegue aceder a qualquer um destes mosaicos, terá de pedir ao seu administrador de acesso de contribuinte para a aplicação web.

### <a name="dealing-with-related-resources"></a>Trabalhar com recursos relacionados

Aplicações Web são complicadas pela presença de alguns recursos diferentes que inter-relação. Eis um grupo de recursos típica com alguns Web sites públicos:

![Grupo de recursos de aplicação Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Como um resultado, se conceder a alguém acesso a apenas a aplicação web, muitas das funcionalidades no pá Web site no portal do Azure será desactivado.

Estes itens requerem acesso de **escrita** para o **plano de serviço de aplicação** , que corresponde ao seu Web site:  

- Ver a aplicação web do preços camada (Free ou padrão)  
- Configuração de escala (número de instâncias, tamanho de máquina virtual, as definições de autoscale)  
- Quotas (armazenamento, largura de banda, CPU)  

Estes itens requerem acesso de **escrita** para todo o **grupo de recursos** que contém o seu Web site:  

- Os certificados SSL e enlaces (isto é porque os certificados SSL podem ser partilhados entre sites no mesmo grupo de recursos e a localização de geo)  
- Regras de alertas  
- Definições de Autoscale  
- Componentes de informações da aplicação  
- Testes Web  

## <a name="virtual-machine-workloads"></a>Máquina virtual cargas de trabalho

Muito como web Apps, algumas funcionalidades na pá máquina virtual requerem acesso de escrita para a máquina virtual ou para outros recursos no grupo de recursos.

Máquinas virtuais estão relacionados com nomes de domínio, redes virtuais, contas de armazenamento e regras de alertas.

Estes itens requerem acesso de **escrita** na **Máquina Virtual**:

- Pontos finais  
- Endereços IP  
- Discos  
- Extensões  

Estas requerem acesso de **escrita** para a **Máquina Virtual**e o **grupo de recursos** (juntamente com o nome de domínio) que está a ser:  

- Conjunto de disponibilidade  
- Carregar conjunto desequilibrado  
- Regras de alertas  

Se não consegue aceder a qualquer um destes mosaicos, terá de pedir ao seu administrador de acesso do colaborador ao grupo de recursos.

## <a name="see-more"></a>Mais informações, consulte
- [Função de controlo de acesso com base](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- [Funções incorporadas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que vêm padrão no RBAC.
- [Funções de personalizada no Azure RBAC](role-based-access-control-custom-roles.md): Saiba como criar funções personalizadas para corresponder às suas necessidades de acesso.
- [Criar um acesso alterar relatório de histórico](role-based-access-control-access-change-history-report.md): manter a par das alterar atribuições de funções no RBAC.
