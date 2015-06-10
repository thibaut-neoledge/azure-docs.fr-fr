<properties
   pageTitle="Affichage de votre application dans la galerie d’applications Azure AD"
   description="Guide complet sur les ressources destinées aux développeurs pour Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/29/2015"
   ms.author="mbaldwin"/>


# Affichage de votre application dans la galerie d’applications Azure AD

Pour répertorier une application qui prend en charge l’authentification unique avec Azure Active Directory dans la [galerie Azure AD](http://azure.microsoft.com/marketplace/active-directory/all/) et dans Azure Marketplace, l’équipe Azure AD doit vérifier votre intégration. Vous devez envoyer les informations suivantes à <waadpartners@microsoft.com> :

- le nom d’utilisateur et le mot de passe d’un compte d’utilisateur de test dans une instance de test d’Azure Active Directory capable de se connecter à votre application ;
- l’URL et/ou des instructions sur la façon dont cet utilisateur test doit se connecter à l’application ;
- une brève description de la procédure de connexion entre votre application et Azure AD effectuée par vos clients. Il peut s’agir des moyens suivants :
  - l’intégration à l’application à l’aide de l’infrastructure de consentement d’Azure AD ;
  - la configuration d’Azure AD comme fournisseur d’identité (SAML 2.0, WS-Federation ou OpenID Connect) à l’aide d’un portail d’administration d’une application ;
  - la communication avec une personne de votre entreprise permettant de créer manuellement la connexion ;
- Si votre application prend en charge la configuration d’Azure AD comme fournisseur d’identité via un portail d’administration en libre-service, veuillez également fournir un compte d’administrateur de test pour la vérification.

Une fois le test terminé, l’application peut être répertoriée dans la galerie d’applications Azure Active Directory et dans Azure Marketplace. Répondez aux questions ci-dessous et adressez vos réponses à <waadpartners@microsoft.com>.

> Nom de l’entreprise :
>
> Site web de l’entreprise :
>
> Nom de l’application :
>
> Description de l’application (256 caractères maximum) :
>
> Site web de l’application (pour informatif) :
>
> Site web du support technique de l’application ou les informations de contact :
>
> Avez-vous intégré Azure AD à votre application et avez-vous testé le résultat (voir [Scénarios d’authentification Azure Active Directory](active-directory-authentication-scenarios.md)) ?
>
> ID de client de l’application, comme indiqué dans les détails de l’application à https://manage.windowsazure.com :
>
> URL d’inscription d’application à laquelle les clients accèdent pour s’inscrire et/ou acheter l’application :
>
> Sélectionnez jusqu’à trois catégories répertoriées sous votre application (pour les catégories disponibles, consultez la page [Applications Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=327881)) :
>
> Attacher une petite icône d’application (fichier PNG, 45 px par 45 px, couleur d’arrière-plan unie) :
>
> Attacher une grande icône d’application (fichier PNG, 215 px par 215 px, couleur d’arrière-plan unie) :
>
> Attacher un grand logo d’application (fichier PNG, 150 px par 122 px, couleur d’arrière-plan unie) :

<!---HONumber=58-->