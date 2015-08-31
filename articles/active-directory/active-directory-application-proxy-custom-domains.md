<properties
	pageTitle="Utilisation des domaines personnalisés dans le proxy d'application Azure AD"
	description="Explique comment utiliser les domaines personnalisés dans le proxy d'application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="rkarlin"/>

# Utilisation des domaines personnalisés dans le proxy d'application Azure AD
> [AZURE.NOTE]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](https://msdn.microsoft.com/library/azure/dn532272.aspx).

En utilisant un domaine par défaut, vous pouvez définir la même URL comme URL interne et externe pour accéder à l'application afin que vos utilisateurs n’aient qu'une seule URL à se rappeler pour accéder à l'application, quel que soit l’endroit à partir duquel ils y accèdent. Cela vous permet également de créer un raccourci unique dans le volet d'accès de l'application. Si vous utilisez le domaine par défaut fourni par le proxy d'application Azure AD, il n'y a aucune autre configuration nécessaire pour activer votre domaine. Dans le cas où vous utilisez un domaine personnalisé, vous avez quelques procédures à suivre pour vous assurer que le proxy d'application reconnaît bien votre domaine et valide ses certificats.

## Sélection de votre domaine personnalisé

1. Publiez votre application en suivant les instructions dans la section Publier des applications avec le proxy d'application.
2. Une fois que l'application s'affiche dans la liste des applications, sélectionnez-la et cliquez sur **Configurer**.
3. Sous **URL externe**, entrez votre domaine personnalisé.
4. Si votre URL externe est https, vous serez invité à télécharger un certificat afin qu’Azure puisse valider l'URL de l'application. Vous pouvez également télécharger un certificat générique qui correspond à l'URL externe de l'application. Ce domaine doit se trouver dans la liste de vos [domaines vérifiés par Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure doit avoir un certificat pour l'URL du domaine de l'application ou un certificat générique qui correspond à l'URL externe pour l'application.
5. Veillez à ajouter un enregistrement DNS qui achemine l'URL interne vers l'application qui vous permet d'avoir la même URL pour l'accès interne et externe et un seul raccourci vers l'application dans la liste des applications de l'utilisateur.

## Forum aux questions sur l'utilisation des domaines personnalisés

Q : Puis-je sélectionner un certificat déjà téléchargé sans avoir à le télécharger à nouveau ? <br> R : Les certificats déjà téléchargés sont automatiquement liés à une application et il existe exactement un certificat correspondant au nom d'hôte de l'application. <br> …<br> Q : Comment ajouter un certificat et dans quel format télécharger le certificat exporté ? <br> R : Le certificat doit ensuite être téléchargé à partir de la page de configuration d'application. Le certificat doit être un fichier PFX. <br> …<br> Q : Peut-on utiliser des certificats ECC ? <br> R : Il n'existe aucune limitation explicite sur les méthodes de signature. <br> …<br> Q : Peut-on utiliser des certificats SAN ? <br> R : Oui.<br> …<br> Q : Peut-on utiliser des certificats génériques ? <br> R : Oui. <br> …<br> Q : Peut-on utiliser un certificat différent pour chaque application ? <br> R: Oui, sauf si les deux applications partagent le même hôte externe. <br> …..<br> Q : Si j'inscris un nouveau domaine, puis-je utiliser ce domaine ? <br> R : Oui, la liste des domaines est chargée à partir de la liste des domaines vérifiés du locataire. <br> …<br> Q: Que se passe-t-il lorsqu'un certificat expire ? <br> R : Vous obtenez un avertissement dans la section Certificat de la page de configuration d'application. Lorsqu'un utilisateur tente d'accéder à l'application, un avertissement de sécurité s'affiche. <br> …<br> Q : Que dois-je faire si je souhaite remplacer un certificat pour une application donnée ? <br> R : Téléchargez un nouveau certificat à partir de la page de configuration d'application<br> ...<br> Q : Puis-je supprimer un certificat et le remplacer ? <br> R : Lorsque vous téléchargez un nouveau certificat, si l'ancien certificat n'est pas en cours d'utilisation par une autre application, il sera automatiquement supprimé<br> ...<br> Q : Que se passe-t-il lorsqu'un certificat est révoqué<br> R : Aucune vérification de révocation n’est effectuée pour les certificats. Lorsqu'un utilisateur tente d'accéder à l'application, en fonction du navigateur, un avertissement de sécurité peut apparaître.<br> …<br> Q : Puis-je utiliser un certificat auto-signé ? <br> R : Oui, les certificats auto-signés sont autorisés. Notez que si vous utilisez une autorité de certification privée, le point de distribution de la liste de révocation de certificats (CDP) du certificat doit être public. <br> ...<br> Q : Existe-t-il un endroit où je peux afficher tous les certificats de mon client ? <br> R : Ce n'est pas pris en charge dans la version actuelle.<br>



## Ressources supplémentaires

* [Inscription à Azure en tant qu’organisation](..sign-up-organization.md)
* [Identité Azure](..fundamentals-identity.md)

<!---HONumber=August15_HO8-->