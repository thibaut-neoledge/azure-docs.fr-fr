<properties
	pageTitle="Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory | Microsoft Azure"
	description="En savoir plus sur la personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="asmalser"/>

#Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory

Aujourd’hui, Azure Active Directory prend en charge des milliers d’applications pré-intégrées dans la galerie d’applications Azure AD, notamment plus de 150 applications qui gèrent l’authentification unique à l’aide du protocole SAML 2.0. Lorsqu’un utilisateur s’authentifie auprès d’une application via Azure AD à l’aide de SAML, Azure AD envoie un jeton à l’application (via une redirection HTTP 302) que l’application valide et utilise pour connecter l’utilisateur au lieu de demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées « revendications ».

En jargon d’identité, une « revendication » concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](http://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML et l’ID unique de l’utilisateur unique est généralement représenté dans l’objet SAML.

Par défaut, Azure AD émet un jeton SAML à votre application qui contient une revendication NameIdentifier, avec une valeur du nom de l’utilisateur dans Azure AD (cette valeur identifie l’utilisateur de façon unique). Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML vers l’application, ouvrez l’enregistrement de l’application dans le portail de gestion Azure et sélectionnez l’onglet **Attributs** sous l’application.

![][1]

Il existe deux raisons possibles pour lesquelles vous devrez peut-être modifier les revendications émises dans le jeton SAML : •L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication •Votre application a été déployée d’une manière qui nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur (c’est-à-dire le nom d’utilisateur principal) stocké dans Azure Active Directory.

Vous pouvez modifier les valeurs de revendication par défaut en sélectionnant l’icône en forme de crayon qui apparaît à droite chaque fois que vous déplacez la souris sur l’une des lignes du tableau d’attributs de jeton SAML. Vous pouvez également supprimer les revendications (autres que NameIdentifier) à l’aide de l’icône **X** et ajouter de nouvelles revendications à l’aide du bouton **Ajouter un attribut utilisateur**.

##Modification de la revendication NameIdentifier

Pour résoudre le problème dans lequel l’application a été déployée à l’aide d’un nom d’utilisateur différent, cliquez sur l’icône en forme de crayon en regard de la revendication NameIdentifier. La boîte de dialogue qui s’affiche comporte plusieurs options :

![][2]

Dans le menu **Valeur d’attribut**, sélectionnez **user.mail** pour définir la revendication NameIdentifier sur l’adresse de messagerie de l’utilisateur dans l’annuaire ou sélectionnez **user.onpremisessamaccountname** pour définir le nom de compte SAM de l’utilisateur qui a été synchronisé à partir d’Azure AD en local.

Vous pouvez également utiliser la fonction ExtractMailPrefix() spéciale pour supprimer le suffixe de domaine de l’adresse de messagerie ou le nom d’utilisateur principal résultant de la première partie du nom de l’utilisateur transmis (par exemple, « joesmith » au lieu de joesmith@contoso.com).

![][3]

##Ajout de revendications

Lorsque vous ajoutez une nouvelle revendication, vous pouvez spécifier le nom de l’attribut (qui n’a pas strictement besoin de suivre un modèle d’URI, conformément à la spécification SAML) et vous pouvez définir la valeur sur n’importe quel attribut utilisateur stocké dans l’annuaire.

![][4]

Par exemple, si vous devez envoyer le service auquel appartient l’utilisateur dans son entreprise en tant que revendication (par ex. Sales), vous pouvez saisir toute valeur attendue par l’application, puis sélectionner **user.department** comme valeur.

Si pour un utilisateur donné, il n’y a pas de valeur stockée pour un attribut sélectionné, aucune revendication ne sera émise dans le jeton.

**Remarque :** **user.onpremisesecurityidentifier** et **user.onpremisesamaccountname** sont uniquement pris en charge lors de la synchronisation des données utilisateur issues de l’annuaire Active Directory local en utilisant la dernière version préliminaire de l’outil AAD Connect. Vous pouvez télécharger la version préliminaire de l’outil de connexion sur le lien ci-dessous :

http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=53949
	
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png

<!---HONumber=Nov15_HO4-->