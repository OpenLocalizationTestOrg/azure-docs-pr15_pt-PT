<properties 
    pageTitle="Como utilizar Twilio para voz e SMS (Rubi) | Microsoft Azure" 
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos rubi." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como utilizar Twilio para voz e capacidades SMS no Rubi
Este guia demonstra como efetuar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem efetuar uma chamada e enviar uma mensagem curta mensagem serviço (SMS). Para mais informações sobre como Twilio e utilizar voz e SMS nas suas aplicações, consulte a secção [Os passos seguintes](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é um API do serviço web de telefonia que permite-lhe utilizar o seu existente idiomas de web e competências a criação de voz e as aplicações do SMS. Twilio é um serviço de terceiros (não uma funcionalidade Azure e não um produto da Microsoft).

**Voz Twilio** permite que as suas aplicações para o fazer e receber chamadas telefónicas. **Twilio SMS** permite que as suas aplicações para o fazer e receber mensagens SMS. **Twilio cliente** permite que as suas aplicações permitir a comunicação de voz utilizando ligações de Internet existentes, incluindo ligações móvel.

## <a id="Pricing"></a>Twilio preços e ofertas especiais
Obter informações sobre preços Twilio estão disponíveis em [Twilio preços] [twilio_pricing]. Os clientes Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever para esta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
Twilio API é um API RESTful que fornece voz e funcionalidade do SMS para aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte o artigo [Twilio API bibliotecas] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML que o informem Twilio de como a processar uma chamada ou SMS.

Por exemplo, a seguinte TwiML seria converter o texto **Olá mundo** voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tem de todos os documentos TwiML `<Response>` como os respetivos elemento de raiz. A partir daqui, pode utilizar Twilio verbos para definir o comportamento da sua aplicação.

### <a id="Verbs"></a>Verbos TwiML
Twilio verbos são etiquetas XML que indicam ao Twilio o que **fazer**. Por exemplo, o ** &lt;diga&gt; ** verbais instrui Twilio pronunciadas entregar uma mensagem a fazer uma chamada. 

Segue-se uma lista de Twilio verbos.

* ** &lt;Acesso telefónico&gt;**: liga-se o autor da chamada para outro telefone.
* ** &lt;Reunir&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* ** &lt;Desligar&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: é reproduzido um ficheiro de áudio.
* ** &lt;Pausa&gt;**: aguarda silenciosamente durante um número especificado de segundos.
* ** &lt;Registo&gt;**: regista voice o autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* ** &lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para TwiML num URL diferente.
* ** &lt;Rejeitar&gt;**: rejeitar uma chamada a receber para o número do Twilio sem faturação
* ** &lt;Diga&gt;**: converte texto em voz que é feito a fazer uma chamada.
* ** &lt;Sms&gt;**: envia uma mensagem de SMS.

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte o artigo [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte o artigo [Twilio API] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta de Twilio
Quando estiver pronto para obter uma conta de Twilio, inscrever-se ao [Tentar Twilio] [try_twilio]. Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Ao inscrever-se para uma conta de Twilio, obterá um número de telefone gratuito para a sua aplicação. Também irá receber uma conta SID e um token de auth. Ambos serão necessárias para efetuar chamadas de Twilio API. Para evitar acesso não autorizado à sua conta, manter token de autenticação seguro. SID da conta e o seu auth token estão visíveis na [página de conta Twilio][twilio_account], nos campos denominada **SID da conta** e **AUTH TOKEN**, respetivamente.

### <a id="VerifyPhoneNumbers"></a>Verifique se os números de telefone
Para além do número é fornecidas pela Twilio, também pode verificar números de controlo (ou seja, o número de telefone do telemóvel ou casa) para utilizar nas suas aplicações. 

Para obter informações sobre como verificar se um número de telefone, consulte o artigo [Gerir números] [verify_phone].

## <a id="create_app"></a>Criar uma aplicação Rubi
Uma aplicação Rubi que utiliza o serviço de Twilio e está em execução no Azure é em qualquer outra aplicação Rubi, que utiliza o serviço de Twilio. Enquanto os serviços de Twilio são RESTful e podem ser chamados de Rubi de várias formas, este artigo irá focar-se sobre como utilizar os serviços de Twilio com [Twilio biblioteca de ajuda para Rubi][twilio_ruby].

Em primeiro lugar, [configurar um novo Linux VM da Azure] [ azure_vm_setup] para funcionar como um anfitrião para a nova aplicação web rubi. Ignore os passos que envolvem a criação de uma aplicação de carris, basta configuração da compilação a VM. Certifique-se de que criar um ponto final com uma porta externa de 80 e uma porta interna de 5000.

Nos exemplos abaixo, vamos utilizar [Sinatra][sinatra], um quadro web muito simples para rubi. Mas certamente pode utilizar a biblioteca de ajuda Twilio para Rubi com quaisquer outros framework de web, incluindo Rubi sobre carris.

SSH para o seu novo VM e criar um diretório para a sua nova aplicação. Dentro desse diretório, crie um ficheiro chamado Gemfile e copie o código seguinte para o mesmo:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comandos executar `bundle install`. Isto irá instalar as dependências acima. Em seguida, crie um ficheiro denominado `web.rb`. Este será onde reside o código para a sua aplicação web. Cole o código seguinte para o mesmo:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste momento deverá conseguir executar o comando `ruby web.rb -p 5000`. Isto irá giratório cópia de um servidor web pequenas na porta 5000. Deverá conseguir navegar para esta aplicação no seu browser, visitando o URL configuração da compilação para sua VM Azure. Assim que pode chegar a aplicação web no browser, está pronto para começar a construir uma aplicação de Twilio.

## <a id="configure_app"></a>Configurar a aplicação para utilizar Twilio
Pode configurar a sua aplicação web para utilizar a biblioteca de Twilio ao atualizar o `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comandos, executar `bundle install`. Agora, abrir `web.rb` e esta linha na parte superior, incluindo:

    require 'twilio-ruby'

Está agora pronto para utilizar a biblioteca de ajuda do Twilio para Rubi na sua aplicação web.

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
A imagem seguinte mostra como efetuar uma chamada de saída. Conceitos chave incluem utilizando a biblioteca de ajuda de Twilio para Rubi para efetuar chamadas de REST API e compor TwiML. Substitua os valores para os números de telefone **de** e **até** e certifique-se de que verifique se o número de telefone **a partir** da sua conta Twilio antes de executar o código.

Adicionar esta função para `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Se lhe abrir cópia de segurança `http://yourdomain.cloudapp.net/make_call` num browser, que irá acionar a chamada à Twilio API para efetuar uma chamada telefónica. Os dois primeiros parâmetros na `client.account.calls.create` são bastante facilmente compreensíveis: o número a chamada é `from` e o número a chamada é `to`. 

O terceiro parâmetro (`url`) é o URL que Twilio os pedidos para obter instruções sobre o que fazer quando a chamada for estabelecida. Neste caso, podemos configuração da compilação um URL (`http://yourdomain.cloudapp.net`) que devolve um documento TwiML simple e utiliza o `<Say>` verbo para fazer algumas texto em voz e dizer "Macaco Olá" para a pessoa que recebe a chamada.

## <a id="howto_recieve_sms"></a>Como: receber uma mensagem de SMS
No exemplo anterior, podemos iniciados por uma chamada telefónica **envio** . Este hora, vamos utilizar o número de telefone Twilio nos forneceu durante a inscrição para uma mensagem a **receber** SMS do processo.

Em primeiro lugar, inicie sessão no seu [Twilio dashboard][twilio_account]. Clique no "Números" no painel de navegação superior e, em seguida, clique no número Twilio que lhe foram fornecidos. Verá duas URLs que pode configurar. URL do pedido de um URL de pedido de voz e um SMS. Estes são os URLs que Twilio chamadas sempre que é feita uma chamada telefónica ou um SMS é enviado para o seu número. Os URLs também são conhecidos como "web hooks".

Gostaríamos de saber a processar mensagens SMS a receber, por isso, vamos atualizar o URL para `http://yourdomain.cloudapp.net/sms_url`. Vá para a frente e clique em Guardar alterações na parte inferior da página. Agora, novamente `web.rb` vamos programa nossa aplicação para processar isto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de efetuar a alteração, certifique-se começar novamente a sua aplicação web. Agora, tomar fora do seu telemóvel e envie um SMS para o número de Twilio. Concorda deve obter uma resposta SMS que diz "tão, obrigado para o ping! Pedra Twilio e Azure! ".

## <a id="additional_services"></a>Como: utilizar os serviços de Twilio adicionais
Para além dos exemplos apresentados aqui, Twilio oferece APIs baseada na web que pode utilizar para tirar partido de funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para detalhes completos, consulte a [documentação da Twilio API] [twilio_api_documentation].

### <a id="NextSteps"></a>Próximos passos
Agora que aprendeu os princípios básicos para o serviço de Twilio, siga estas ligações para mais informações:

* [Diretrizes de segurança Twilio] [twilio_security_guidelines]
* [Twilio HowTos e o código de exemplo] [twilio_howtos]
* [Tutoriais Twilio guia de introdução][twilio_quickstarts] 
* [Twilio no GitHub] [twilio_on_github]
* [Falar com suporte de Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
