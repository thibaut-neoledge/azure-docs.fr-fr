---
title: Recherche de rapports Azure Active Directory
description: Recherche d’événements de sécurité, d’activité et d’audit dans vos rapports Azure Active Directory
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk

---
# Recherche de rapports Azure Active Directory
*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Azure Active Directory (Azure AD) permet aux administrateurs d’annuaires de rechercher des événements d’audit, d’activité et de sécurité de l’utilisateur sur de nombreux rapports.

Pour trouver le volet de recherche, accédez au **portail Azure Classic > Azure AD Directory > Rapports.** Le volet se trouve en haut de la liste des rapports.

Pour rechercher des événements d’activité ou d’audit d’un utilisateur donné, sélectionnez une plage de dates dans les champs De et À, entrez l’UPN de l’utilisateur ou son nom d’affichage, puis sélectionnez le bouton en forme de coche (OK).

## Rapports inclus dans la recherche
Tous les rapports ne sont pas encore inclus dans les résultats de la recherche. Ce tableau indique quels rapports sont inclus.

| Rapport | Inclus |
| --- | --- |
| Connexions à partir de sources inconnues |Non |
| Connexions après plusieurs échecs |Non |
| Connexions depuis plusieurs zones géographiques |Non |
| Connexions depuis des adresses IP avec des activités suspectes |Non |
| Connexions à partir d’appareils potentiellement infectés |Non |
| Activité de connexion anormale |Non |
| Utilisateurs ayant une activité de connexion anormale |Non |
| Utilisateurs avec des informations d’identification volées |Non |
| Rapport d'audit |Oui |
| Activité de réinitialisation de mot de passe |Oui |
| Activité de l’enregistrement de la réinitialisation de mot de passe |Oui |
| Activité de groupes en libre-service |Oui |
| Utilisation des applications |Non |
| Activité d’approvisionnement de compte |Oui |
| État de substitution de mot de passe |Non |
| Erreurs de configuration de compte |Non |
| Utilisation de RMS |Non |
| Utilisateurs RMS les plus actifs |Non |
| Utilisation d’un appareil RMS |Non |

## En savoir plus
* [Rapports Azure Active Directory](active-directory-view-access-usage-reports.md)
* [Événements de rapport d’audit d’Azure Active Directory](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_0928_2016-->