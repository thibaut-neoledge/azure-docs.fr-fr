<properties
	pageTitle="Notifications de rapports Azure Active Directory"
	description="Comment utiliser les notifications de rapports Azure Active Directory pour signaler les connexions suspectes."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="kenhoff"/>

# Notifications de rapports Azure Active Directory

## Quels rapports génèrent les notifications par courrier électronique

Pour l’instant, seuls le rapport d’activité de connexion anormale et le rapport d’activité des connexions anormales utilisent le système de notification par courrier électronique.

## Que déclenche l’envoi de la notification par courrier électronique ?

Par défaut, Azure Active Directory est configuré pour envoyer automatiquement des notifications par courrier électronique à tous les administrateurs généraux. Le courrier électronique est envoyé dans les conditions suivantes pour chaque rapport.

Pour le rapport d’activité de connexion anormale :

- Sources inconnues : 10 événements
- Plusieurs échecs : 10 événements
- Adresses IP affichant une activité suspecte : 10 événements
- Périphériques infectés : 10 événements

Pour les utilisateurs ayant un rapport d’activité de connexion anormale :

- Sources inconnues : 10 événements
- Plusieurs échecs : 10 événements
- Adresses IP affichant une activité suspecte : 10 événements
- Périphériques infectés : 5 événements
- Rapport de connexions anormales : 15 événements

Le courrier électronique est envoyé si une des conditions mentionnées ci-dessus est remplie dans les 30 jours, ou lorsque le dernier courrier électronique a été envoyé il y a moins de 30 jours.

Les connexions dites anormales sont celles qui ont été identifiées comme « anormales » par nos algorithmes d’apprentissage automatique, car elles ont été effectuées depuis un emplacement inattendu et/ou à une heure également suspecte. Cela peut signifier qu'un pirate a essayé de se connecter à l'aide de ce compte. Le tableau ci-dessus fournit plus d'informations sur ce rapport.

## Qui reçoit les notifications par courrier électronique ?

Le courrier électronique est envoyé à tous les administrateurs généraux titulaires d'une licence Active Directory Premium. Par mesure de précaution, ce courrier électronique est également envoyé sur leur adresse de messagerie de secours. Les administrateurs doivent inclure aad-alerts-noreply@mail.windowsazure.com dans leur liste d'expéditeurs approuvés pour ne pas rater le courrier électronique.

## Quelle est la fréquence d’envoi de ces courriers électroniques ?

Une fois le premier courrier électronique envoyé, un second suivra lorsque 10 événements de connexion anormale se seront produits dans les 30 jours suivant l’envoi du premier courrier électronique. Comment puis-je accéder au rapport mentionné dans le courrier électronique ?

Lorsque vous cliquerez sur le lien, vous serez redirigé vers la page du rapport du portail de gestion Azure. Pour accéder au rapport, vous devez être à la fois :

- Un administrateur ou un coadministrateur de votre abonnement Azure
- Un administrateur général du répertoire, titulaire d’une licence Active Directory Premium. Pour plus d’informations, consultez les éditions d’Azure Active Directory.

## Puis-je désactiver ces courriers électroniques ?

Oui, pour désactiver les notifications liées à des connexions anormales dans le portail de gestion Azure, cliquez sur **Configurer**, puis sélectionnez **Désactivé** sous la rubrique **Notifications**.

## Étapes suivantes
- Curieux de savoir quels rapports de sécurité, d'audit et d'activité sont disponibles ? Découvrez [Rapports de sécurité, d'audit et d'activité d'Azure AD](active-directory-view-access-usage-reports.md)
- [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)

<!---HONumber=July15_HO3-->