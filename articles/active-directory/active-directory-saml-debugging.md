<properties 
    pageTitle="Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory | Microsoft Azure" 
    description="Découvrez comment déboguer une authentification unique basée sur SAML aux applications dans Azure Active Directory" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory

Lors du débogage d’une intégration d'application basée sur SAML, il est généralement conseillé d'utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler) pour afficher la requête SAML, la réponse SAML et le jeton SAML actuel émis à l'application. En examinant le jeton SAML, vous pouvez vous assurer que tous les attributs requis, le nom d'utilisateur dans l'objet SAML et l'URI de l'émetteur vous parviennent comme prévu.

![][1]

La réponse d'Azure AD qui contient le jeton SAML est généralement celle qui se produit après une redirection HTTP 302 depuis https://login.windows.net, et qui est envoyée à l’**URL de réponse** configurée de l'application.
 
Vous pouvez afficher le jeton SAML en sélectionnant cette ligne, puis **Inspecteurs > WebForms** dans le volet droit. À partir de là, cliquez sur la valeur **SAMLResponse** et sélectionnez **Send to TextWizard**. Puis sélectionnez **From Base64** à partir du menu **Transform** pour décoder le jeton et afficher son contenu.
 
**Remarque** : pour afficher le contenu de cette requête HTTP, Fiddler peut vous inviter à configurer le déchiffrement du trafic HTTPS, que vous devez effectuer.

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

<!---HONumber=Nov15_HO4-->