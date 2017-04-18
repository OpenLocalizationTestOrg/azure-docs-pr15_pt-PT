Azure vai determinar a sua aplicação utiliza Python **se ambas as condições são verdadeiras**:

- ficheiro de Requirements.txt na pasta raiz
- qualquer ficheiro .py na pasta raiz ou um runtime.txt que especifica python

Quando o que é o caso, irá utilizar um script de implementação específico Python, que efetua a sincronização padrão de ficheiros, bem como operações Python adicionais, como:

- Gestão automática do ambiente virtual
- Instalação de pacotes listados em requirements.txt utilizando pip
- Criação de Web adequado. config com base na versão Python selecionada.
- Recolher ficheiros estáticos para Django aplicações

Pode controlar determinados aspectos dos passos de implementação predefinido sem ter de personalizar o script.

Se pretender ignorar todos os passos de implementação específico Python, pode criar este ficheiro vazio:

    \.skipPythonDeployment

Para ter mais controlo sobre implementação, pode substituir o script de implementação predefinido ao criar os seguintes ficheiros:

    \.deployment
    \deploy.cmd

Pode utilizar a [interface de comandos Azure][] para criar os ficheiros.  Utilize este comando da sua pasta de projeto:

    azure site deploymentscript --python

Quando estes ficheiros não existirem, o Azure cria um script de implementação temporário e executa-lo.  É idêntico ao que criar com o comando acima.

[Interface de comandos Azure]: http://azure.microsoft.com/downloads/
