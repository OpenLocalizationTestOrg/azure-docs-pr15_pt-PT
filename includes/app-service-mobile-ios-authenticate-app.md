**Objectivo-C**: 

1. No seu Mac, abrir _QSTodoListViewController.m_ no Xcode e adicione o seguinte método. Alterar o _google_ para _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ se não estiver a utilizar o Google o seu fornecedor de identidade. Se utiliza o Facebook, [terá da lista branca Facebook domínios na sua aplicação](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Substituir `[self refresh]` no `viewDidLoad` no _QSTodoListViewController.m_ com o seguinte:

            [self loginAndGetData];

3. Prima _Executar_ para iniciar a aplicação e, em seguida, inicie sessão no. Quando iniciou sessão, deverá conseguir ver a lista a fazer e fazer atualizações.

**Swift**:

1. No seu Mac, abrir _ToDoTableViewController.swift_ no Xcode e adicione o seguinte método. Alterar o _google_ para _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ se não estiver a utilizar o Google o seu fornecedor de identidade. Se utiliza o Facebook, [terá de domínios de Facebook lista branca na sua aplicação](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no final do `viewDidLoad()` no _ToDoTableViewController.swift_. Adicionar uma chamada para `loginAndGetData()` no seu lugar:

            loginAndGetData()

3. Prima _Executar_ para iniciar a aplicação e, em seguida, inicie sessão no. Quando iniciou sessão, deverá conseguir ver a lista a fazer e fazer atualizações.
