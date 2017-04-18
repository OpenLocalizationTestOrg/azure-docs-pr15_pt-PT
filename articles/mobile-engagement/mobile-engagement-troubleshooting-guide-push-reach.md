<properties 
   pageTitle="Azure Cativação Mobile guia - Push/alcance de resolução de problemas" 
   description="Resolver problemas de interação e notificação de utilizador no Azure Mobile Cativação" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Resolução de problemas de guia para notificações Push e atinja problemas

Seguem-se possíveis problemas que poderá encontrar com como o Azure Mobile Cativação envia informações aos seus utilizadores.
 
## <a name="push-failures"></a>Falhas de emissão

### <a name="issue"></a>Problema
- Puxa não funciona (na aplicação, fora da aplicação ou ambas).

### <a name="causes"></a>Faz com que
- Número de vezes uma push falha é uma indicação de que Azure Mobile Cativação, alcance ou outra função avançada do Azure Mobile Cativação não é corretamente integrada ou que uma atualização é necessária no SDK para corrigir um problema conhecido com uma nova plataforma SO ou dispositivo.
- Teste apenas uma push na aplicação e apenas um push fora do aplicação para determinar se um valor é um problema na aplicação ou aplicação fora do.
- Teste a partir de IU e a API como uma etapa de resolução de problemas para ver as informações de erro adicionais estão disponíveis ambos os locais.
- Terminar aplicação puxa não funciona, a menos que Azure Mobile Cativação e alcance sejam integrados no SDK.
- Puxa não irá funcionar se certificados não são válidos, ou se estiver a utilizar o produto vs. Dev Center corretamente (apenas para iOS). (**Nota:** "fora do aplicação" notificações push não poderão ser entregue ao iOS, se tiver o (Dev Center) de desenvolvimento e versões de produção (ordem de produção) da sua aplicação instalado no mesmo dispositivo, uma vez que o token de segurança associada com o certificado podem ser invalidadas pela Apple. Para resolver este problema, desinstale as versões Dev Center e ordem de produção da sua aplicação e voltar a instalar apenas a uma versão no seu dispositivo.)
- Terminar push aplicação contagens são processadas de forma diferente em diferentes plataformas (iOS mostra menos informações que Android se puxa nativas é desativadas num dispositivo, a API pode fornecer mais informações do que a IU estatística push).
- Terminar aplicação puxa pode ser bloqueadas por clientes ao nível do sistema operativo (iOS e Android).
- Terminar aplicação puxa serão apresentadas como desactivado no Azure Mobile Cativação IU se não são integradas corretamente, mas pode falhar em silêncio a partir da API.
- Na aplicação puxa não funciona, a menos que Azure Mobile Cativação e alcance sejam integrados no SDK.
- Puxa GCM e ADM não funciona, a menos que Azure Mobile Cativação e no servidor específico sejam integrados no SDK (apenas para Android).
- Na aplicação e Out of aplicação puxa deve ser testadas separadamente para determinar se é um problema Push ou atingir.
- Na aplicação puxa exigem que a aplicação seja aberto a receber.
- Na aplicação puxa é geralmente configuração sejam filtrados por uma tag de informações da aplicação optar ativamente pela ou optar de saída.
- Se utilizar uma categoria personalizada no atingir a apresentação de notificações de na aplicação, que tem de seguir o ciclo de vida correto da notificação de, ou, caso contrário, não pode ser limpa a notificação quando o utilizador dispensar.
- Se iniciar uma campanha com sem data de fim e um dispositivo recebe a aplicação em notificação mas não não é apresentada-lo ainda, o utilizador continuarão receber a notificação da próxima vez que iniciarem sessão na aplicação, mesmo se manualmente terminar da campanha.
- Para problemas com a API emissão, confirme que realmente quer utilizar a API Push em vez da API atinja (uma vez que a API atinja é utilizada com mais frequência) e que não são confusa os parâmetros de "carga útil" e "notificador".
- Teste a sua campanha push com ambos os um dispositivo ligado através da conta e 3G para eliminar a ligação de rede como uma origem de possível de problemas.

## <a name="push-testing"></a>Testes de emissão

### <a name="issue"></a>Problema
- Puxa pode ser enviadas para um dispositivo específico com base num ID de dispositivo.

### <a name="causes"></a>Faz com que

- Dispositivos de teste estão a configuração de forma diferente para cada plataforma, mas a causar um evento na sua aplicação num dispositivo de teste e está à procura de ID do seu dispositivo no portal do deverão funcionar para encontrar o seu ID de dispositivo de todas as plataformas.
- Dispositivos de teste têm um funcionamento diferente com IDFA vs. IDFV (apenas para iOS).


## <a name="push-customization"></a>Personalização de emissão

### <a name="issue"></a>Problema
- Advanced push conteúdo não irá funcionar item (do distintivo, simultaneamente, vibração, imagem, etc.).
- Ligações a partir de puxa não funcionam (fora da aplicação, na aplicação, para um Web site, para uma localização na aplicação).
- Estatísticas de push mostram que um push não foi enviada para o número de pessoas como esperado (demasiadas ou não suficiente).
- Notificações push duplicada e recebidas duas vezes.
- Não consegue registar o dispositivo de teste para Azure Mobile Cativação emite (com a sua própria aplicação ordem de produção ou Dev Center).

### <a name="causes"></a>Faz com que

- Para criar uma ligação para uma localização específica na aplicação requer "categorias" (apenas para Android).
- Esquemas de ligação abrangente para redirecionar utilizadores para uma localização alternativa depois de clicar numa notificação de emissão têm de ser criado no e geridas pela aplicação e o dispositivo SO não pelo Cativação Mobile diretamente. (**Nota:** Terminar aplicação notificações não é possível ligar diretamente em localizações de aplicação com iOS à medida que podem fazer com Android.)
- Os servidores de imagem externa tem de ser possível utilizar HTTP "Obter" e "HEAD" para conceito emite para trabalhar (apenas para Android).
- No seu código, pode desativar o agente do Azure Mobile Cativação quando o teclado é aberto e que o seu código ativar novamente o agente do Azure Mobile Cativação assim que o teclado estiver fechado, para que o teclado não afeta o aspeto da sua notificação (apenas para iOS).
- Alguns itens não funcionam em simulações de teste, mas apenas reais campanhas (do distintivo, simultaneamente, vibração, imagem, etc.).
- Sem dados lado do servidor são registados quando utilizar o botão "Testar" puxa. Apenas os dados são registados para campanhas de real push.
- Para ajudar a isolar o problema, resolver problemas com: teste, simular e uma campanha real de uma vez que cada funcionam forma ligeiramente diferente.
- O comprimento do tempo que sua "na aplicação" e campanhas de "sempre" estão agendadas para ser executada pode efeito números de entrega, uma vez que uma campanha só será enviada para utilizadores que estejam "na aplicação" enquanto executa a campanha (e os utilizadores que tenham as definições de dispositivo definidas para receber notificações de "fora do aplicação").
- As diferenças entre como Android e iOS lidar terminar notificações da aplicação torna-se difícil comparar diretamente push estatísticas entre a versão Android e iOS da sua aplicação. Android fornece mais informações notificação nível do sistema operativo que é que o iOS. Android relatórios quando uma notificação nativa é recebida, clicada ou eliminada no Centro de notificação, mas iOS não reportar esta informação, a menos que a notificação é clicada. 
- O motivo principal que são os números "premidos" diferentes que diferentes do que os números de "entregue" para alcançar campanhas que "na aplicação" e "fora do aplicação" notificações são contadas forma diferente. "Na aplicação" notificações são processadas pelo Cativação Mobile, mas "fora do aplicação" notificações são processadas pelo centro de notificação no sistema operativo do seu dispositivo.

## <a name="push-targeting"></a>Filtragem de emissão

### <a name="issue"></a>Problema
- Incorporado na filtragem de não funciona como esperado.
- Filtragem de etiqueta de informações da aplicação não funciona conforme esperado.
- Filtragem de localização geo não funciona conforme esperado.
- Opções de idioma não funcionam como esperado.

### <a name="causes"></a>Faz com que

- Certifique-se de que carregou etiquetas de informações da aplicação através da IU do Azure Mobile Cativação ou API.
- Limitação a velocidade de push ou quota push ao nível da aplicação ou limitar a audiência ao nível de campanha pode impedir que uma pessoa receber um push específico, mesmo se preencherem os critérios de filtragem. 
- Definir um "idioma" é diferente da filtrar com base em país ou região, que é também diferente a filtrar com base na localização Geo com base numa localização de telefone ou GPS.
- A mensagem no "idioma predefinido" é enviada para qualquer cliente que não tem o seu dispositivo definido para um dos idiomas alternativos que especificar.


## <a name="push-scheduling"></a>Agendamento de emissão

### <a name="issue"></a>Problema
- Agendamento push não funciona conforme esperado (enviada demasiado antecipada ou atrasadas).

### <a name="causes"></a>Faz com que

- Fusos horários pode problemas com o agendamento, especialmente quando utiliza o fuso horário dos utilizadores finais.
- Funcionalidades avançadas push podem atrasar puxa.
- Filtrar com base no telemóvel definições (em vez de etiquetas de informações da aplicação) podem atrasar emite desde Azure Mobile Cativação poderá ter de pedir dados a partir de telefone em tempo real antes de enviar um push.
- Campanhas criadas sem uma data de fim armazenam o push localmente no dispositivo e mostrá-la da próxima vez que a aplicação é aberta, mesmo se campanha é terminada manualmente.
- Iniciar campanha de mais do que um ao mesmo tempo, pode demorar mais tempo para digitalizar a sua base de utilizador (experimente para apenas iniciar uma campanha de cada vez com um máximo de quatro, também destino apenas aos seus utilizadores ativos para que os utilizadores antigos não têm de ser verificado).
- Se utilizar a opção "Ignorar audiência, push será enviado para utilizadores através da API" na secção "Campanha" de uma campanha de alcance, não irá enviar campanha automaticamente, terá de enviá-lo manualmente através da API atinja.
- Se utilizar uma categoria personalizada no atingir a apresentação de notificações de na aplicação, que tem de seguir o ciclo de vida correto de uma notificação, ou, caso contrário, não pode ser limpa a notificação quando o utilizador dispensar.

 
