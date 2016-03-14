<properties
	pageTitle="Azure AD Connect : FAQ | Microsoft Azure"
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
	ms.date="02/29/2016"
	ms.author="billmath"/>

# FAQ Azure AD Connect

## Installation générale
**Q : L’installation fonctionnera-t-elle si l’option 2FA est activée pour l’administrateur global AD Azure ?** Cela est pris en charge avec les builds de février 2016.

**Q : Existe-t-il une manière d’installer Azure AD Connect sans assistance ?** L’installation d’Azure AD Connect n’est possible qu’à partir de l’Assistant Installation. Une installation automatique et silencieuse n’est pas prise en charge.

**Q : Je possède une forêt dans laquelle un domaine ne peut pas être contacté. Comment installer Azure AD Connect ?** Cela est pris en charge avec les builds de février 2016.

## Réseau
**Q : J’ai un pare-feu, un périphérique réseau ou autre chose qui limite la durée maximale pendant laquelle les connexions peuvent rester ouvertes sur mon réseau. Quel doit être le seuil de délai côté client lors de l’utilisation d’Azure AD Connect ?** Tous les logiciels réseau, périphériques physiques ou autres limitant la durée maximale pendant laquelle les connexions peuvent rester ouvertes doivent utiliser un seuil d'au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où est installé le client Azure AD Connect et Azure Active Directory. Cela s'applique également à tous les outils de synchronisation de Microsoft Identity précédemment publiés.

**Q : Les domaines avec un nom en une seule partie sont-ils pris en charge ?** Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux utilisant des noms de domaine en une seule partie.

**Q : Les noms NetBios comportant un point sont-ils pris en charge ?** Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux dont le nom NetBios contient un point « . ».

## Fédération
**Q : Que faire si je reçois un e-mail me demandant de renouveler mon certificat Office 365** Suivez les instructions décrites dans la rubrique [Renouveler les certificats](active-directory-aadconnect-o365-certs.md) concernant le renouvellement du certificat.

**Q : « Mettre à jour automatiquement la partie de confiance » est défini pour la partie de confiance Office 365. Dois-je effectuer une action lorsque mon certificat de signature de jetons bascule automatiquement ?** Utilisez les instructions décrites dans l’article [Renouveler les certificats](active-directory-aadconnect-o365-certs.md).

## Environnement
**Q : Est-il possible de renommer le serveur une fois qu’Azure AD Connect est installé ?** Non. La modification du nom du serveur empêche le moteur de synchronisation de se connecter à la base de données SQL et le service de démarrer.

## Données d’identité
**Q : L’attribut UPN (userPrincipalName) dans Azure AD ne correspond pas à l’UPN local. Pourquoi ?** Reportez-vous aux articles suivants :

- [Les noms d’utilisateur dans Office 365, Azure ou Intune ne correspondent pas à l’UPN local ou à l’ID de connexion secondaire](https://support.microsoft.com/fr-FR/kb/2523192)
- [Les modifications ne sont pas synchronisées par l’outil de synchronisation Azure Active Directory une fois que vous avez modifié l’UPN d’un compte utilisateur afin qu’il utilise un autre domaine fédéré](https://support.microsoft.com/fr-FR/kb/2669550)

## Configuration personnalisée
**Q : Où réside la documentation sur les applets de commande PowerShell pour Azure Active Directory ?** À l’exception des applets de commande décrites sur ce site, les autres applets de commande PowerShell disponibles dans Azure AD Connect ne sont pas prises en charge par le client.

**Q : Puis-je utiliser « l’exportation serveur/importation serveur » dans le *Synchronization Service Manager* pour déplacer la configuration entre serveurs ?** Non. Cette option ne récupérera pas tous les paramètres de configuration et ne doit pas être utilisée. Vous devez plutôt utiliser l’Assistant pour créer la configuration de base sur le deuxième serveur et utiliser l’éditeur de règles de synchronisation pour générer des scripts PowerShell afin de déplacer une règle personnalisée entre les serveurs. Consultez [Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## Résolution de problèmes
**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Recherche dans la Base de connaissances Microsoft (KB)](https://www.microsoft.com/fr-FR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Recherchez dans la Base de connaissances Microsoft (KB) des solutions techniques aux problèmes courants couverts par la garantie de réparation et d’assistance, relatifs à la prise en charge d’Azure AD Connect.

[Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=WindowsAzureAD)

- Vous pouvez naviguer et rechercher des questions et des réponses techniques de la communauté ou poser votre question en cliquant [ici](https://social.msdn.microsoft.com/Forums/azure/fr-FR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Service clientèle Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Cliquez sur ce lien pour bénéficier du support par le biais du portail Azure.

<!---HONumber=AcomDC_0302_2016-->