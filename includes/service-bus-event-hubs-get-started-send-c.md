## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para concentradores de evento

Nesta secção, vamos gravará uma aplicação C para enviar eventos para o seu centro de evento. Vamos utilizar a biblioteca de protão AMQP do [projeto Apache Qpid](http://qpid.apache.org/). Isto é semelhante à utilização da filas Bus de serviço e tópicos com AMQP a partir do C como mostrado [aqui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para mais informações, consulte a [documentação de Qpid protão](http://qpid.apache.org/proton/index.html).

1. Na [página de Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), clique na ligação **Instalar protão Qpid** e siga as instruções dependendo do seu ambiente. Vamos irá partem do pressuposto de um ambiente Linux; Por exemplo, um [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04.

2. Para compilar a biblioteca de protão, instale os pacotes seguintes:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Transfira a biblioteca de [biblioteca Qpid protão](http://qpid.apache.org/proton/index.html) e extrai-lo, por exemplo:

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Crie um diretório de compilação, compilar e instalar:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. No seu diretório de trabalho, crie um novo ficheiro denominado **sender.c** com o seguinte conteúdo. Lembre-se substituir o valor para o seu nome de espaço de nomes e o nome do concentrador de evento (o último nome costuma ser `{event hub name}-ns`). Também tem de substituir uma versão de URL codificada da chave de **SendRule** criada anteriormente. Pode codificar URL-lo [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. Compile o ficheiro, partindo do princípio que **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] Este código utilizamos uma janela de saída de 1 para forçar o mais cedo possível as mensagens de saída. Em geral, a aplicação deverá tentar para mensagens de lote para aumentar o débito. Consulte o artigo [Qpid AMQP Messenger página](http://qpid.apache.org/components/messenger/index.html) para obter mais informações sobre como utilizar a biblioteca de Qpid protão em este e outros ambientes e por plataformas para a qual são fornecidos enlaces (Perl atualmente, PHP, Python e Rubi).
