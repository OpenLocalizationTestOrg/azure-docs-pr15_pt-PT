## <a name="repeatability-during-copy"></a>Repetibilidade durante a cópia

Ao copiar dados de e até armazena relacionais, tem de ter repetibilidade em mente para evitar resultados inesperados. 

Um setor pode ser voltar a executar automaticamente no Azure dados fábrica de acordo com a política de repetir especificada. Recomendamos que o utilizador defina uma política de repetir para protecção contra falhas breves. Por conseguinte repetibilidade é um aspecto importante para encarregam-durante movimentação de dados. 

**Como uma origem:**

> [AZURE.NOTE] Os exemplos seguintes são para Azure SQL, mas são aplicáveis a qualquer arquivo de dados que suporta retangular conjuntos de dados. Poderá ter de ajustar o **tipo** de origem e a propriedade de **consulta** (por exemplo: consulta em vez de sqlReaderQuery) para os dados armazenar.   

Normalmente, durante a leitura a partir de stores relacionais, que seria pretende ler apenas os dados correspondentes que setor. Uma forma para fazê-lo seria utilizando as variáveis WindowStart e WindowEnd disponíveis no Azure fábrica de dados. Leia sobre as variáveis e funções no Azure dados fábrica do mesmo aqui no artigo [agendar e execução](../articles/data-factory/data-factory-scheduling-and-execution.md) . Exemplo: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Esta consulta lê dados a partir 'AMinhaTabela' determinado intervalo de duração de setor. Voltar a executar deste setor seria também Certifique-se sempre este comportamento. 

Em outros casos, poderá pretender ler toda a tabela (Imaginemos para uma vez deslocar-se apenas) e pode definir a sqlReaderQuery da seguinte forma:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
