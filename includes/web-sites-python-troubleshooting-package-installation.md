Alguns pacotes não podem instalar o utilizando pip quando executar no Azure.  Basta pode ser que o pacote não está disponível no índice de pacote Python.  É possível que não é necessário um compilador (não está disponível no computador a executar a aplicação web na aplicação de serviço de Azure um compilador).

Nesta secção, abordaremos maneiras de solucionar este problema.

### <a name="request-wheels"></a>Pedido de rodas

Se a instalação do pacote requer um compilador, deverá tentar contactar o proprietário do pacote solicitar de rodas disponíveis para o pacote.

Com a disponibilidade recente de [C++ compilador do Microsoft Visual para Python 2.7][], agora é mais fácil criar pacotes que tenham o código nativo para Python 2.7.

### <a name="build-wheels-requires-windows"></a>Construir rodas (requer o Windows)

Nota: Ao utilizar esta opção, certifique-se compilar o pacote de utilizar um ambiente Python que corresponde à plataforma/arquitetura/versão que é utilizada na aplicação web na aplicação de serviço de Azure (Windows/32-bit/2.7 ou 3.4).

Se não instalar o pacote porque requer um compilador, pode instalar o compilador no seu computador local e criar uma roda para o pacote que, em seguida, irá incluir no seu repositório.

Utilizadores de MAC/Linux: Se não tiver acesso a um computador Windows, consulte o artigo [Criar Máquina Virtual a executar o Windows][] para saber como criar uma VM no Azure.  Pode utilizá-lo para criar as rodas, adicioná-los para o repositório e rejeitar a VM se preferir. 

Para Python 2.7, pode instalar o [C++ compilador do Microsoft Visual para Python 2.7][].

Para Python 3.4, pode instalar o [Microsoft Visual C++ 2010 Express][].

Para criar rodas, terá do pacote de roda:

    env\scripts\pip install wheel

Irá utilizar `pip wheel` compilação dependência de:

    env\scripts\pip wheel azure==0.8.4

Esta ação cria um ficheiro de .whl na pasta \wheelhouse.  Adicione os ficheiros de pasta e a roda do \wheelhouse para o seu repositório.

Editar o seu requirements.txt para adicionar o `--find-links` opção na parte superior. Isto indica que pip para procurar uma correspondência exata na pasta local antes de avançar para o índice de pacote python.

    --find-links wheelhouse
    azure==0.8.4

Se quiser incluir todos os seus dependências na pasta \wheelhouse e não utilizar de todo o índice de pacote python, pode forçar pip para ignorar o índice de pacote adicionando `--no-index` na parte superior do seu requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizar a instalação

Pode personalizar o script de implementação para instalar um pacote no ambiente virtual com um programa de instalação alternativo, tal como mais facilmente\_instalar.  Consulte o artigo deploy.cmd para obter um exemplo que é colocada como comentário.  Certifique-se de que essas pacotes não estão listados na requirements.txt, para impedir que pip instalá-los.

Adicione esta para o script de implementação:

    env\scripts\easy_install somepackage

Também poderá conseguir utilizar mais facilmente\_para instalar a partir de um programa de instalação exe (alguns são zip compatíveis e tão fácil\_instalar suporta-los).  Adicionar o instalador para o seu repositório e invocar mais facilmente\_instalar ao passar o caminho para o ficheiro executável.

Adicione esta para o script de implementação:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Incluir o ambiente virtual no repositório (requer o Windows)

Nota: Ao utilizar esta opção, certifique-se utilizar um ambiente virtual que corresponde à plataforma/arquitetura/versão que é utilizada na aplicação web na aplicação de serviço de Azure (Windows/32-bit/2.7 ou 3.4).

Se incluir o ambiente virtual no repositório, pode impedir que o script de implementação efetuar a gestão do ambiente virtuais no Azure através da criação de um ficheiro em branco:

    .skipPythonDeployment

Recomendamos que elimine o ambiente virtual existente na aplicação, para evitar que permaneçam ficheiros a partir de quando o ambiente virtual gerido automaticamente.


[Criar uma máquina de Virtual a executar o Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ compilador para Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
