<properties 
	pageTitle="Inscription pour l'authentification Azure Active Directory - Mobile Services" 
	description="Découvrez comment vous inscrire pour l'authentification Azure Active Directory dans votre application Mobile Services." 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="wesmc"/>

# Inscription de vos applications à des fins d'utilisation d'une connexion via un compte Azure Active Directory
## Vue d'ensemble


Cette rubrique montre comment inscrire vos applications afin d'utiliser Azure Active Directory comme fournisseur d'authentification pour Azure Mobile Services. 

## Inscription de votre application

>[AZURE.NOTE] Les étapes décrites dans cette rubrique sont destinées à être utilisées avec le didacticiel [Ajout de l'authentification à votre application Mobile Services](/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) lorsque vous souhaitez utiliser les [opérations de connexion orientées service](http://msdn.microsoft.com/library/azure/dn283952.aspx) avec votre application. Ou bien, si votre application doit recourir aux [opérations de connexion orientées client](http://msdn.microsoft.com/library/azure/jj710106.aspx) pour Azure Active Directory et à un service mobile principal .NET, vous devez commencer par le didacticiel [Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory](/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Connectez-vous au [portail de gestion Azure], sélectionnez **Mobile Services**, puis cliquez sur le service mobile.

    ![][1]

2. Cliquez sur l'onglet **Identité** de votre service mobile. 

    ![][2]

3. Faites défiler jusqu'à la section du fournisseur d'identité **Azure Active Directory** et copiez l'**URL de l'application** qui y est indiquée.

    ![][3]

4. Dans le portail de gestion, accédez à **Active Directory**, puis cliquez sur votre annuaire.

    ![][4] 

5. Cliquez en haut sur l'onglet **Applications**,  puis cliquez pour **AJOUTER** une application. 

    ![][10]

6. Cliquez sur **Ajouter une application développée par mon organisation**.

7. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.

    ![][5]

8. Dans la zone **URL de connexion**, collez l'ID d'application que vous avez copié dans les paramètres du fournisseur d'identité Active Directory de votre service mobile. Entrez le même identificateur de ressource unique dans la zone **URI ID d'application**. Ensuite, cliquez pour continuer.
 
    ![][6]


9. Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer**. Cliquez ensuite sur l'**ID client** de l'application.

    Si vous avez créé le service mobile pour utiliser le service principal .Net sur votre service mobile, remplacez également l'**URL de réponse** sous **Authentification unique** par l'URL de votre service mobile, suivie du chemin _signin-aad_. Par exemple, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Revenez sous l'onglet **Identité** de votre service mobile. En bas, collez le paramètre **ID client** du fournisseur d'identité Azure Active Directory.

  
11. Dans la liste **Locataires autorisés**, vous devez ajouter le domaine du répertoire dans lequel vous avez inscrit l'application (ex. : contoso.onmicrosoft.com). Vous pouvez trouver votre nom de domaine par défaut en cliquant sur l'onglet **Domaines** de votre domaine Active Directory.

    ![][11]
 
    Ajoutez votre nom de domaine à la liste **Locataires autorisés**, puis cliquez sur **Enregistrer**.    


    ![][9]



Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Portail de gestion Azure]: https://manage.windowsazure.com/


<!--HONumber=47-->
