Enquanto é possível colar um URI MongoLab no seu código, recomendamos que configurá-lo no ambiente para facilidade de gestão do. Desta forma, se o URI for alterada, pode atualizá-lo através do Portal do Azure sem ter de aceder ao código.


1. No Portal do Azure, selecione **Web Apps**.
1. Clique no nome da aplicação web na lista de aplicações Web.  
![WebAppEntry][entry-website]  
Apresenta o dashboard do Web App.

1. Na barra de menus, clique em **Configurar** .  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Desloque-se até à secção de cadeias de ligação.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Para o **nome**, introduza MONGOLAB_URI.
1. Para o **valor**, cole a cadeia de ligação que podemos obtidos na secção anterior.
1. Selecione **Custom** na lista pendente **tipo** de (em vez da predefinição **SQLAzure**).
1. Clique em **Guardar** na barra de ferramentas.  
![SaveWebApp][button-website-save]

**Nota:** Azure adiciona o **CUSTOMCONNSTR\_ ** prefixo a esta variável, que é o motivo pelo qual o código acima referências **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
