<properties
    pageTitle="Trabalhar com domínios personalizados no Proxy de aplicação do Azure AD | Microsoft Azure"
    description="Folhas de rosto como trabalhar com domínios personalizados no Azure AD para o Proxy de aplicação."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabalhar com domínios personalizados no Proxy de aplicação do Azure AD

Utilizar um domínio predefinido permite-lhe definir o mesmo URL como o URL interno e externo para aceder a aplicação para que os utilizadores tenham apenas um URL de se lembrar de aceder à aplicação, independentemente do local onde está a aceder a partir de. Isto também lhe permite criar um atalho única no painel de acesso para a aplicação. Se utilizar o domínio predefinido fornecido pela Proxy de aplicação do Azure AD, não existe nenhuma configuração adicional, que pode ter de ativar o seu domínio. Na eventualidade de utilizar um domínio personalizado, existem algumas coisas que precisa para se certificar de que o Proxy de aplicação reconhece o seu domínio e validar os certificados.

## <a name="selecting-your-custom-domain"></a>Selecionar o seu domínio personalizado

1. Publica a sua aplicação de acordo com as instruções em [publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md).
2. Após a aplicação é apresentada na lista de aplicações, selecione-o e clique em **Configurar**.
3. Em **URL externo**, introduza o seu domínio personalizado.
4. Se o URL da sua externo https, ser-lhe-á pedido carregar um certificado para que esse Azure pode validar o URL da aplicação. Também pode carregar um certificado de caracteres universais que corresponde ao URL da externos da aplicação. Este domínio tem de estar dentro da lista da sua [Azure verificado domínios](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure tem de ter um certificado para o URL do domínio da aplicação ou um certificado de caracteres universais que corresponde ao URL da externa para a aplicação.
5. Certifique-se adicionar um registo DNS que encaminhe o URL interno para a aplicação que permite-lhe ter o mesmo URL para o access interno e externo e um única atalho para a aplicação na lista de aplicações do utilizador.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Perguntas mais frequentes sobre como trabalhar com domínios personalizados

P: selecione um certificado já carregou sem carregá-lo novamente?  
R: anteriormente certificados carregados automaticamente estão vinculados à aplicação e existe um certificado que corresponde ao nome do anfitrião do pedido.  

P: como adicionar um certificado e que formato devo o certificado exportado ser carregado no?  
R: o certificado deve ser carregado a partir da página de configuração da aplicação. O certificado deve ser um ficheiro PFX.  

P: podem ser utilizados certificados ECC?  
R: não existe nenhuma limitação explícita sobre métodos de assinatura.  

P: podem ser utilizados certificados de SAN?  
R: Sim.  

P: podem ser utilizados certificados de caracteres universais?  
R: Sim.  

P: pode ser utilizado um certificado diferente em cada aplicação?  
R: Sim, a menos que as duas aplicações partilham o mesmo anfitrião externo.  

P: se registar a um novo domínio, posso utilizar esse domínio?  
R: Sim, a lista de domínios é Moro a partir da lista de domínio verificado do inquilino.  

P: o que acontece quando uma orientação do diapositivo notas expira?  
R: irá obter um aviso na secção certificado na página de configuração da aplicação. Quando um utilizador tenta aceder à aplicação, um aviso de segurança pop-se para cima.  

P: o que devo fazer se pretender substituir um certificado para um determinado aplicação?  
R: carregue um certificado novo a partir da página de configuração da aplicação.  

P: posso eliminar um certificado e substituí-lo?  
R: ao carregar um novo certificado, se o certificado antigo não está a ser utilizado por outra aplicação, este será automaticamente eliminada.  

P: o que acontece quando um certificado foi revogado?  
R: verificações de revogação de não são executadas para certificados. Quando um utilizador tenta aceder a aplicação, consoante o browser, poderá aparecer um aviso de segurança.  

P: Posso utilizar um certificado autoassinado?  
R: Sim, certificados autoassinados são permitidos. Tenha em atenção que se estiver a utilizar uma autoridade de certificação privado, o CDP (ponto da distribuição do ponto de revogação de certificados) para o certificado deve ser público.  

P: existe um local para ver todos os certificados para o meu inquilino?  
R: Isto não é suportado na versão atual.  


## <a name="see-also"></a>Consulte também

- [Publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Adicionar o nome de domínio personalizado ao Azure AD](active-directory-add-domain.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
