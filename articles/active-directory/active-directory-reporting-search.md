<properties
	pageTitle="Recherche de rapports Azure Active Directory"
	description="Recherche d’événements de sécurité, d’activité et d’audit dans vos rapports Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/07/2015"
	ms.author="kenhoff"/>

# Recherche de rapports Azure Active Directory

Azure Active Directory permet aux administrateurs d’annuaires de rechercher des événements d’audit, d’activité et de sécurité de l’utilisateur sur de nombreux rapports.

Pour trouver le volet de recherche, accédez au **portail de gestion Azure -> Azure Active Directory -> Rapports.** Le volet se trouve en haut de la liste des rapports.

Pour rechercher des événements d’activité ou d’audit d’un utilisateur donné, sélectionnez une plage de dates dans les champs De et À, entrez l’UPN de l’utilisateur ou son nom d’affichage, puis cliquez sur la coche.

## Rapports inclus dans la recherche

Tous les rapports ne sont pas encore inclus dans les résultats de la recherche. Ce tableau indique quels rapports sont inclus.

|	Rapport |	Inclus |
|	------												|	--------			|
|	Connexions à partir de sources inconnues |	Non |
|	Connexions après plusieurs échecs |	Non |
|	Connexions depuis plusieurs zones géographiques |	Non |
|	Connexions à partir d’adresses IP affichant une activité suspecte |	Non |
|	Connexions à partir de périphériques potentiellement infectés |	Non |
|	Activité de connexion anormale |	Non |
|	Utilisateurs ayant une activité de connexion anormale |	Non |
|	Utilisateurs avec des informations d’identification volées |	Non |
|	Rapport d’audit |	Oui |
|	Activité de réinitialisation de mot de passe |	Oui |
|	Activité de l’enregistrement de la réinitialisation de mot de passe |	Oui |
|	Activité de groupes en libre-service |	Oui |
|	Utilisation des applications |	Non |
|	Activité d’approvisionnement de compte |	Oui |
|	État de substitution de mot de passe |	Non |
|	Erreurs de configuration de compte |	Non |
|	Utilisation de RMS |	Non |
|	Utilisateurs RMS les plus actifs |	Non |
|	Utilisation d’un périphérique RMS |	Non |

## En savoir plus

 - [Rapports Azure Active Directory](active-directory-view-access-usage-reports.md)
 - [Événements de rapport d’audit d’Azure Active Directory](active-directory-reporting-audit-events.md)

<!---HONumber=July15_HO4-->