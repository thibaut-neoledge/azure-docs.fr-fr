<properties
	pageTitle="FAQ Azure AD Connect | Microsoft Azure"
	description="Cette page comporte le Forum Aux Questions relatif à Azure AD Connect."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>

# FAQ Azure Active Directory Connect

## Installation Express

## Installation personnalisée

## Réseau
**Q : J'ai un pare-feu, un périphérique réseau ou autre chose qui limite la durée maximale pendant laquelle les connexions peuvent rester ouvertes sur mon réseau. Quel doit être le seuil de délai côté client lors de l’utilisation d’Azure AD Connect ?**

Tous les logiciels réseau, périphériques physiques ou autres limitant la durée maximale pendant laquelle les connexions peuvent rester ouvertes doivent utiliser un seuil d'au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où est installé le client Azure AD Connect et Azure Active Directory. Cela s'applique également à tous les outils de synchronisation de Microsoft Identity précédemment publiés.


**Q : Que faire si je reçois un message électronique me demandant de renouveler mon certificat Office 365 ?**

Utilisez les instructions décrites [ici](active-directory-aadconnect-o365-certs.md) pour renouveler le certificat.

## Résolution de problèmes

**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Recherche dans la Base de connaissances Microsoft (KB)](https://www.microsoft.com/fr-FR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Recherchez dans la Base de connaissances Microsoft (KB) des solutions techniques aux problèmes courants couverts par la garantie de réparation et d’assistance, relatifs à la prise en charge d’Azure AD Connect.

[Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=WindowsAzureAD)

- Vous pouvez naviguer et rechercher des questions et des réponses techniques de la communauté ou poser votre question en cliquant [ici](https://social.msdn.microsoft.com/Forums/azure/fr-FR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Service clientèle Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Cliquez sur ce lien pour bénéficier du support par le biais du portail Azure.

<!---HONumber=Oct15_HO3-->