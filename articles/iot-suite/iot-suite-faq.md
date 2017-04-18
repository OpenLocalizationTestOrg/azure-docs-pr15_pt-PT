<properties
  pageTitle="Perguntas mais frequentes do conjunto de aplicações do Azure IoT | Microsoft Azure"
  description="Perguntas mais frequentes do IoT conjunto de aplicações"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas mais frequentes do IoT conjunto de aplicações

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>O que é a diferença entre a eliminação de um grupo de recursos no portal do Azure e clicar em Eliminar numa solução pré-configurado no azureiotsuite.com?

- Se eliminar a solução pré-configurado no [azureiotsuite.com][lnk-azureiotsuite], eliminar todos os recursos que foram aprovisionados quando criou a solução pré-configurado; Se tiver adicionado recursos adicionais para o grupo de recursos, estas também são eliminadas. 

- Se eliminar o grupo de recursos no [portal do Azure][lnk-azure-portal], só eliminar recursos presentes nesse grupo de recursos. Também irá precisar de eliminar a aplicação de Azure Active Directory associada a solução pré-configurado no [portal clássica Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias IoT concentrador pode aprovisionar numa subscrição? 

Dez. Pode criar um [bilhetes de suporte do Azure] [ link-azuresupportticket] para elevar este limite, mas por predefinição, pode apenas aprovisionar dez IoT concentradores por subscrição, conforme descrito no [limites de subscrição Azure][link-azuresublimits]. Como resultado, uma vez que todas as soluções pré-configurado disposições um concentrador de IoT novo, apenas pode aprovisionar o até dez soluções pré-configurado numa subscrição determinado. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias DocumentDB pode aprovisionar numa subscrição?

Cinquenta. Pode criar um [bilhetes de suporte do Azure] [ link-azuresupportticket] para elevar este limite, mas por predefinição, apenas pode aprovisionar o cinquenta DocumentDB instâncias por subscrição. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantos APIs de mapas Bing gratuito pode aprovisionar numa subscrição?

Duas. Pode criar apenas dois interno transações nível 1 mapas Bing para planos Enterprise numa subscrição do Azure. Solução de controlo remota está aprovisionada por predefinição, com o plano de interno transações nível 1. Como resultado, apenas pode aprovisionar até duas remotas monitorização soluções numa subscrição sem modificações.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tenho uma implementação de solução monitorização remota com um mapa estático, como adicionar um mapa do Bing interativo? 
1. Obter o API de mapas Bing para o Enterprise QueryKey a partir do [Azure portal][lnk-azure-portal]: 
 1. Navegue para o grupo de recursos onde o seu API de mapas Bing para o Enterprise está no [portal do Azure][lnk-azure-portal].
 2. Clique em todas as definições, em seguida, Key Management. 
 3. Notará duas chaves: chave mestra e QueryKey. Copie o valor para QueryKey.

     > [AZURE.NOTE] Não tem uma API de mapas Bing para conta da empresa? Criar um no [portal do Azure] [ lnk-azure-portal] clicando + novo e procurar API de mapas Bing para o Enterprise e siga os pedidos para criar.

2. Separar para baixo o código mais recente da [Monitorização de Azure IoT Remote][lnk-remote-monitoring-github].

3. Execute um local ou na nuvem seguindo orientações de implementação de linha de comandos na pasta /docs/ no repositório de implementação. 

4. Depois de se tiver executado um local ou na nuvem implementação, procure na sua pasta de raiz para a *. user.config ficheiro criado durante a implementação. Abra este ficheiro num editor de texto. 

5. Altere a linha seguinte para incluir o valor que copiou do seu QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurado se tenho o Microsoft Azure para DreamSpark?
Neste momento, não é possível criar uma solução pré-configurado com um [Microsoft Azure para DreamSpark] [ lnk-dreamspark] conta. No entanto, pode criar uma [conta de avaliação gratuita para Azure] [ lnk-30daytrial] apenas de duas minutos, que permitirá cria uma solução pré-configurado.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso eliminar um inquilino do AAD?

Consulte no blogue de Eric Golpe [instruções passo a passo de eliminar um inquilino do Azure AD][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Próximos passos

Também pode explorar algumas das outras funcionalidades e capacidades de soluções de conjunto de aplicações IoT pré-configuradas automaticamente:

- [Descrição geral da solução do aspeto do Office manutenção pré-configurado][lnk-predictive-overview]
- [Segurança IoT aprofundadamente][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
