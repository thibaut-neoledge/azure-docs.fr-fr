<properties
	pageTitle="Domaines multiples Azure AD Connect"
	description="Ce document décrit la définition et la configuration de plusieurs domaines de premier niveau avec O365 et Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="billmath"/>

#Prise en charge de plusieurs domaines

Vous êtes nombreux à avoir demandé comment configurer plusieurs domaines et sous-domaines Office 365 ou Azure AD de premier niveau avec la fédération. Même si une telle configuration est plutôt simple à réaliser, en raison de certains points sous-jacents, il existe quelques trucs et astuces à connaître pour éviter les problèmes suivants.

- Messages d’erreur en essayant de configurer des domaines supplémentaires pour la fédération
- Impossibilité pour les utilisateurs de sous-domaines de se connecter après la configuration de plusieurs domaines de premier niveau pour la fédération

## Présence de plusieurs domaines de premier niveau
Je vais vous guider dans la configuration d’un exemple d’organisation comme contoso.com qui veut un domaine supplémentaire nommé fabrikam.com.

Supposons que, dans mon système local, j’aie configuré AD FS avec le nom de service de fédération fs.jenfield.com.

Quand je m’inscris à Office 365 ou Azure AD, je choisis de configurer contoso.com en tant que domaine de ma première authentification. Pour cela, j’utilise Azure AD Connect ou Azure AD Powershell avec New-MsolFederatedDomain.

Ensuite, examinons les valeurs par défaut de deux des nouvelles propriétés de configuration du nouveau domaine Azure AD (interrogeables avec Get-MsolDomainFederationSettings) :

| Nom de la propriété | Valeur | Description|
| ----- | ----- | -----|
|IssuerURI | http://fs.jenfield.com/adfs/services/trust| Même si elle ressemble à une URL, cette propriété n’est que le nom du système d’authentification local, donc le chemin n’a pas besoin d’être résolu en quoi que ce soit. Par défaut, AD Azure affecte cette propriété à la valeur de l’identificateur du service de fédération dans ma configuration AD FS locale.
|PassiveClientSignInUrl|https://fs.jenfield.com/adfs/ls/|This est l’emplacement auquel les requêtes de connexion passive sont envoyées. Il se résout en mon système AD FS réel. Il existe en fait plusieurs propriétés « *Url », mais nous allons juste examiner un exemple pour illustrer la différence entre cette propriété et un URI comme IssuerURI.

Imaginons maintenant que j’ajoute mon deuxième domaine fabrikam.com. Là encore, je peux exécuter l’Assistant Azure AD Connect une deuxième fois ou utiliser PowerShell.

Si j’essaie d’ajouter le deuxième domaine en tant que domaine fédéré en utilisant Azure AD PowerShell, j’obtiens une erreur.

La raison de cette erreur tient à une contrainte dans Azure AD qui veut qu’IssuerURI n’ait pas la même valeur pour plusieurs domaines. Pour contourner ce problème, vous devez utiliser un IssuerURI différent pour le nouveau domaine. C’est à cela que sert le paramètre SupportMultipleDomain. Quand il est utilisé avec les applets de commande pour configurer la fédération (New-, Convert- et Update-MsolFederatedDomain), ce paramètre amène Azure AD à configurer la valeur IssuerURI selon le nom du domaine qui doit être unique pour tous les locataires dans Azure AD. Cette valeur doit donc être unique. Les règles de revendication présentent aussi une modification, mais j’y reviendrai dans quelques instants.

Ainsi, dans Powershell, si j’ajoute fabrikam.com en utilisant le paramètre SupportMultipleDomain,

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

j’obtiens la configuration suivante dans Azure AD :

- DomainName : fabrikam.com
- IssuerURI : http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl :https://fs.jenfield.com/adfs/ls/ 

Notez que même si la valeur IssuerURI a été définie selon mon domaine et qu’elle est donc unique, les valeurs des URL des points de terminaison sont quand même configurées pour pointer vers mon service de fédération sur fs.jenfield.com, comme elles le sont pour le domaine contoso.com d’origine. Ainsi, tous les domaines pointent quand même vers le même système AD FS.

D’autre part, le paramètre SupportMultipleDomain permet de veiller à ce que le système AD FS inclue la valeur Issuer appropriée dans les jetons émis pour Azure AD. Pour cela, c’est la partie domaine de l’UPN des utilisateurs qui est prise et définie en tant que domaine dans issuerURI, c’est-à-dire https://{upn suffixe}/adfs/services/trust. Ainsi, pendant l’authentification auprès d’Azure AD ou Office 365, l’élément Issuer du jeton de l’utilisateur est employé pour localiser le domaine dans Azure AD. Si aucune correspondance ne peut être trouvée, l’authentification échoue.

Par exemple, si l’UPN d’un utilisateur est johndoe@fabrikam.com, l’élément Issuer dans les émissions AD FS du jeton a la valeur http://fabrikam.com/adfs/services/trust. Il correspond à la configuration Azure AD et donc l’authentification réussit.

Vous trouverez ci-dessous la règle de revendication personnalisée qui implémente cette logique :

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

Maintenant, dans ma configuration, j’avais inscrit contoso.com en premier sans le commutateur supportMultipleDomains et avec la valeur IssuerURI par défaut. Quand j’ajoute fabrikam.com, je dois en fait m’assurer que contoso.com est également configuré pour utiliser le commutateur supportMultipleDomains, car la mise à jour de la règle de revendication n’envoie plus la valeur IssuerURI par défaut et l’authentification échoue en raison d’une disparité d’IssuerURI. Ne vous inquiétez pas, nous vous avertirons à ce propos avant de vous autoriser à utiliser le commutateur supportMultipleDomains sur un autre domaine.

Pour corriger ce problème, nous devons aussi mettre à jour la configuration pour le domaine contoso.com. L’Assistant Azure AD Connect détermine facilement quand la procédure ci-dessus doit être effectuée et ce qu’il convient de faire quand vous ajoutez un deuxième domaine. Sur la première passe, si vous êtes déjà dans la configuration SupportMultipleDomain, nous ne remplaçons rien.

Dans PowerShell, vous devez fournir le commutateur SupportMultipleDomain manuellement.

Consultez les informations ci-dessous pour connaître les étapes détaillées permettant d’effectuer la transition d’un domaine unique vers plusieurs domaines.

Après cela, nous avons donc une configuration pour deux domaines dans Azure AD :

- DomainName : contoso.com
- IssuerURI : http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl :https://fs.jenfield.com/adfs/ls/ 
- DomainName : fabrikam.com
- IssuerURI : http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl :https://fs.jenfield.com/adfs/ls/ 

L’authentification fédérée pour les utilisateurs des domaines contoso.com et fabrikam.com fonctionne désormais. Il ne reste qu’un seul problème : l’authentification des utilisateurs dans les sous-domaines.

##Sous-domaines
Supposons que j’ajoute mon sous-domaine sub.contoso.com à Azure AD. En raison de la façon dont Active Directory Azure gère les domaines, le sous-domaine hérite des paramètres du domaine parent, ici contoso.com. Cela signifie que la valeur IssuerURI pour user@sub.contoso.com doit être http://contoso.com/adfs/services/trust. Toutefois, la règle standard implémentée ci-dessus pour

Azure AD génère un jeton avec un émetteur sous la forme http://sub.contoso.com/adfs/services/trust, ce qui ne correspond pas à la valeur requise du domaine. L’authentification va donc échouer. Heureusement, nous avons également une solution de contournement pour ce problème, mais elle n’est pas aussi bien intégrée à nos outils. Vous devez mettre à jour la partie de confiance AD FS pour Microsoft Online manuellement.

Vous devez configurer la règle de revendication personnalisée afin qu’elle retire tous les sous-domaines du suffixe UPN de l’utilisateur pendant la construction de la valeur Issuer. La procédure exacte à suivre est indiquée dans les étapes ci-dessous.

En résumé, vous pouvez avoir plusieurs domaines portant des noms disparates, ainsi que des sous-domaines, tous fédérés sur le même serveur AD FS. Il suffit de quelques étapes supplémentaires pour garantir la bonne définition des valeurs Issuer pour tous les utilisateurs.

<!---HONumber=AcomDC_1203_2015-->