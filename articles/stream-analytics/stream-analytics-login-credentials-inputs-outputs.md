<properties 
    pageTitle="Análise de sequência: Rodar credenciais de início de sessão, para entradas e saídas | Microsoft Azure" 
    description="Saiba como atualizar as credenciais para a análise da cadeia entradas e saídas."
    keywords="credenciais de início de sessão"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Rodar credenciais de início de sessão para entradas e saídas na sequência de análise de tarefas

##<a name="abstract"></a>Resumo
Azure a análise da cadeia hoje não permite substituir as credenciais de uma entrada/saída enquanto a tarefa estiver em execução.

Enquanto a análise da cadeia de Azure suporta retomar uma tarefa a partir de saída última, queremos de partilhar todo o processo para minimizar o tempo de desfasamento entre a paragem e inicial da tarefa e rodar as credenciais de início de sessão.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1 - preparar o novo conjunto de credenciais:
Nesta peça é aplicável às seguintes entradas/saídas de:

* Armazenamento de BLOBs
* Concentradores de evento
* Base de dados SQL
* Armazenamento de tabela

Para outras entradas/saídas, avance com a parte 2.

###<a name="blob-storagetable-storage"></a>Armazenamento blob do armazenamento/tabela
1.  Vá para a extensão de armazenamento no portal de gestão do Azure:  
![graphic1][graphic1]
2.  Localize o armazenamento utilizado pela sua tarefa e ir para o mesmo:  
![graphic2][graphic2]
3.  Clique no comando de gerir as teclas de acesso:  
![graphic3][graphic3]
4.  Entre a chave primária do Access e a tecla de acesso secundário, **Escolha a não utilizado pela sua tarefa**.
5.  Gerar visitas:  
![graphic4][graphic4]
6.  Copie a chave gerada recentemente:  
![graphic5][graphic5]
7.  Continue a parte 2.

###<a name="event-hubs"></a>Concentradores de evento
1.  Aceda a extensão da Bus de serviço no portal de gestão do Azure:  
![graphic6][graphic6]
2.  Localize o espaço de nomes de Bus serviço utilizado pela sua tarefa e ir para o mesmo:  
![graphic7][graphic7]
3.  Se a sua tarefa utiliza uma política de acesso partilhado no espaço de nomes de Bus serviço, ir para o passo 6  
4.  Aceda ao separador concentradores evento:  
![graphic8][graphic8]
5.  Localize o concentrador de evento utilizado pela sua tarefa e ir para o mesmo:  
![graphic9][graphic9]
6.  Aceda ao separador Configurar:  
![graphic10][graphic10]
7.  No menu pendente nome da política, localize a política de acesso partilhado utilizada pela sua tarefa:  
![graphic11][graphic11]
8.  Entre a chave primária e chave secundária, **Escolha a não utilizado pela sua tarefa**.  
9.  Gerar visitas:  
![graphic12][graphic12]
10. Copie a chave gerada recentemente:  
![graphic13][graphic13]
11. Continue a parte 2.  

###<a name="sql-database"></a>Base de dados SQL

>[AZURE.NOTE] Nota: terá de ligar ao serviço de base de dados de SQL. Vamos para mostrar como fazer isto utilizando a experiência de gestão no portal de gestão do Azure, mas poderá optar por utilizar algumas ferramenta lado do cliente como SQL Server Management Studio também.

1.  Vá para a extensão de bases de dados SQL no portal de gestão do Azure:  
![graphic14][graphic14]
2.  Localize a base de dados do SQL utilizados pela sua ligação de tarefa e **clique no servidor** na mesma linha:  
![graphic15][graphic15]
3.  Clique no comando gerir:  
![graphic16][graphic16]
4.  Modelo global de base de dados do tipo:  
![graphic17][graphic17]
5.  Escreva no seu nome de utilizador, palavra-passe e clique em iniciar sessão:  
![graphic18][graphic18]
6.  Clique em nova consulta:  
![graphic19][graphic19]
7.  Escreva a seguinte consulta substituir < login_name > com o nome de utilizador e substituição de <enterStrongPasswordHere> com a sua nova palavra-passe:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Clique em executar:  
![graphic20][graphic20]
9.  Voltar a passo 2 e neste momento, clique na base de dados:  
![graphic21][graphic21]
10. Clique no comando gerir:  
![graphic22][graphic22]
11. Escreva o nome de utilizador, palavra-passe e clique em início de sessão:  
![graphic23][graphic23]
12. Clique em nova consulta:  
![graphic24][graphic24]
13. Escreva a seguinte consulta substituir < nomedoutilizador > com um nome que quer identificar este login no contexto desta base de dados (pode fornecer o mesmo valor que lhe atribuiu para < login_name >, por exemplo) e substituir < login_name > com o novo nome de utilizador:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Clique em executar:  
![graphic25][graphic25]
15. Agora deve fornecer o seu novo utilizador com as mesmas funções e privilégios de utilizador original tinha.
16. Continue a parte 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: Parar a tarefa de análise da cadeia
1.  Aceda a extensão da análise da cadeia no portal de gestão do Azure:  
![graphic26][graphic26]
2.  Localize o seu trabalho e ir para o mesmo:  
![graphic27][graphic27]
3.  Aceda ao separador entradas do tipo ou no separador resultados com base em se são rodar as credenciais de uma entrada de ou para um resultado.  
![graphic28][graphic28]
4.  Clique no comando parar e confirme que a tarefa foi interrompido:  
![graphic29][graphic29] esperar para a tarefa parar.
5.  Localize a entrada/saída que pretende rodar credenciais no e ir para o mesmo:  
![graphic30][graphic30]
6.  Avance para o parte 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: As credenciais da tarefa de análise da cadeia de edição

###<a name="blob-storagetable-storage"></a>Armazenamento blob do armazenamento/tabela
1.  Localize o campo de chave de conta de armazenamento e colá-lo a chave gerada recentemente:  
![graphic31][graphic31]
2.  Clique no comando Guardar e confirme a guardar as suas alterações:  
![graphic32][graphic32]
3.  Um teste de ligação iniciará automaticamente quando guarda as alterações, certifique-se de que seja passou com êxito.
4.  Avançar para a parte 4.

###<a name="event-hubs"></a>Concentradores de evento
1.  Localize o campo de chave de política do concentrador de evento e colá-lo a chave gerada recentemente:  
![graphic33][graphic33]
2.  Clique no comando Guardar e confirme a guardar as suas alterações:  
![graphic34][graphic34]
3.  Um teste de ligação iniciará automaticamente quando guarda as alterações, certifique-se de que já passou com êxito.
4.  Avançar para a parte 4.

###<a name="power-bi"></a>Power BI
1.  Clique na autorização de renovar:  
* ![graphic35][graphic35]
* Irá obter a confirmação seguinte:  
* ![graphic36][graphic36]
2.  Clique no comando Guardar e confirme a guardar as suas alterações:  
![graphic37][graphic37]
3.  Um teste de ligação iniciará automaticamente quando guarda as suas alterações, certifique-se-passou com êxito.
4.  Avançar para a parte 4.

###<a name="sql-database"></a>Base de dados SQL
1.  Localizar os campos nome de utilizador e palavra-passe e colá-los recentemente criado um conjunto de credenciais:  
![graphic38][graphic38]
2.  Clique no comando Guardar e confirme a guardar as suas alterações:  
![graphic39][graphic39]
3.  Um teste de ligação iniciará automaticamente quando guarda as alterações, certifique-se de que já passou com êxito.  
4.  Avançar para a parte 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: Começando a tarefa pela última vez parado
1.  Navegar nas entrada/saída:  
![graphic40][graphic40]
2.  Clique no comando iniciar:  
![graphic41][graphic41]
3.  Selecione a última vez parado e clique em OK:  
 ![graphic42][graphic42]
4.  Avance para o parte 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Remover o conjunto de credenciais antigo
Nesta peça é aplicável às seguintes entradas/saídas de:
* Armazenamento de BLOBs
* Concentradores de evento
* Base de dados SQL
* Armazenamento de tabela

###<a name="blob-storagetable-storage"></a>Armazenamento blob do armazenamento/tabela
Repita parte 1 para a tecla de acesso que tiver sido utilizada anteriormente pelo seu trabalho para renovar a chave de acesso agora não utilizadas.

###<a name="event-hubs"></a>Concentradores de evento
Repita parte 1 para a chave que tiver sido utilizada anteriormente pelo seu trabalho para renovar a chave agora não utilizada.

###<a name="sql-database"></a>Base de dados SQL
1.  Aceda novamente à janela da consulta a partir da parte 1 passo 7 em, escreva a seguinte consulta substituir < previous_login_name > com o nome de utilizador que foi anteriormente utilizado pelo seu trabalho:  
`DROP LOGIN <previous_login_name>`  
2.  Clique em executar:  
    ![graphic43][graphic43]  

Deve obter a confirmação seguinte: 

    Command(s) completed successfully.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
