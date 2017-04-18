Devido a desenvolvimento em curso, a versão de Android SDK instalada no Android Studio poderá não corresponder à versão o código. O SDK Android referenciados neste tutorial é a versão 23, o mais recente no momento da escrita. Pode aumentar o número da versão como novas versões do SDK apareçam e, recomendamos que utilize a versão mais recente disponível.

São os sintomas duas mais de erro de correspondência versão:

1. Quando cria ou reconstruir o projeto, poderá receber mensagens de erro Gradle como "**não foi possível encontrar destino Google Inc.:Google APIs:n**".

2. Objetos Android padrão no código que deverá resolver com base em `import` declarações podem ser gerar mensagens de erro.

Se qualquer um destes for apresentado, a versão do SDK Android instalado no Android Studio poderão não corresponder o destino da SDK do projeto transferido.  Para verificar a versão, efetue as seguintes alterações:


1. No Android Studio, clique em **Ferramentas** => **Android** => **SDK Gestor**. Se não tiver instalado a versão mais recente da plataforma SDK, em seguida, clique em para o instalar. Anote o número de versão.

2. No separador do Project Explorer, em **Gradle Scripts**, abra o ficheiro **build.gradle (modeule: aplicação)**. Certifique-se de que a **compileSdkVersion** e **buildToolsVersion** estão definidos para a versão mais recente do SDK instalada. As etiquetas podem ter este aspeto:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. No Explorador de projeto Studio Android clique com o botão direito do rato em project, selecione **Propriedades**e, na coluna esquerda selecione **Android**. Certifique-se de que o **Project construir destino** está definido para a mesma versão SDK como o **targetSdkVersion**.

4. No Android Studio, o ficheiro de manifesto já não é utilizado para especificar o destino SDK e a versão mínima do SDK, ao contrário as maiúsculas/minúsculas com Eclipse.
