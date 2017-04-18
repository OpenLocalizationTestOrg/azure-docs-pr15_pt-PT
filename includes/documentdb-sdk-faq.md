**1. como serão clientes notificado sobre o SDK retiring?**

Microsoft irá fornecer 12 meses notificação antecipada para o fim de suporte do retiring SDK para facilitar a uma transição suave para um SDK suportado. Além disso, os clientes serão notificados através de vários comunicação canais – Portal de gestão do Azure, Developer Center, mensagem do blogue, e comunicação direta para atribuído administradores do serviço.

**2. podem clientes autor aplicações utilizando um "to-be" SDK de DocumentDB retirada durante o período de 12 meses?** 

Sim, clientes terão acesso total a autor, implementar e modificar aplicações utilizando o SDK do DocumentDB retirada "to-be" durante o período de tolerância de 12 meses. Durante o período de tolerância de 12 meses, clientes aconselhável para migrar para uma versão suportada mais recente do DocumentDB SDK conforme adequado.

**3. podem clientes do autor e modificar aplicações utilizando um SDK DocumentDB retirada após o período de notificação de 12 meses?**

Depois do período de notificação de 12 meses, o SDK vai ser foi removido. Qualquer acesso ao DocumentDB por um aplicações utilizando um SDK retirado não irão ser permitido pela plataforma DocumentDB. Além disso, o Microsoft não irá fornecer suporte ao cliente sobre o SDK retirado.

**4. o que acontece às aplicações em execução do cliente que estão a utilizar uma versão não suportada DocumentDB SDK?**

Quaisquer tentativas efetuadas para ligar ao serviço de DocumentDB com uma versão SDK retirada serão rejeitadas. 

**5. serão aplicados funcionalidades novas e a todos os que não sejam reformado SDK**

Novas funcionalidades só serão adicionados para novas versões. Se estiver a utilizar uma versão antiga, não-reformado, do SDK pedidos de para DocumentDB continuarão a funcionar como anterior, mas não terá acesso a qualquer novas capacidades.  

**6. o que posso fazer se não é possível atualizar a minha aplicação antes de uma data de corte**

Recomendamos que Atualize para o mais recente SDK mais cedo possível. Assim que um SDK tenha sido sinalizada para a reforma tiver 12 meses para atualizar a sua aplicação. Se, por qualquer motivo, não conseguir concluir a atualização da aplicação neste intervalo de tempo, em seguida, contacte a [Equipa de DocumentDB](mailto:askdocdb@microsoft.com) e pedir o seu apoio antes da data limite.
