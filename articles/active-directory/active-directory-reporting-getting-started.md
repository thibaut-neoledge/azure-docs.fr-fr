---
title: "Création de rapports Azure Active Directory : Prise en main | Microsoft Docs"
description: "Répertorie les différents rapports disponibles dans le rapport d'Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: fr-fr
ms.lasthandoff: 02/24/2017

---
# Prise en main de la création de rapports Azure Active Directory
<a id="getting-started-with-azure-active-directory-reporting" class="xliff"></a>
## Présentation
<a id="what-it-is" class="xliff"></a>
Azure Active Directory (Azure AD) comprend des rapports sur la sécurité, les activités et l’audit concernant votre annuaire. Voici une liste des rapports fournis :

### Rapports de sécurité
<a id="security-reports" class="xliff"></a>
* Connexions à partir de sources inconnues
* Connexions après plusieurs échecs
* Connexions depuis plusieurs zones géographiques
* Connexions depuis des adresses IP avec des activités suspectes
* Activité de connexion anormale
* Connexions à partir d’appareils potentiellement infectés
* Utilisateurs ayant une activité de connexion anormale

### Rapports d’activité
<a id="activity-reports" class="xliff"></a>
* Utilisation des applications : résumé
* Utilisation des applications : présentation détaillée
* Tableau de bord de l’application
* Erreurs de configuration de compte
* Appareils des utilisateur individuels
* Activité des utilisateurs individuels
* Rapport d'activité de groupes
* Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe
* Activité de réinitialisation de mot de passe

### Rapport d’audit
<a id="audit-reports" class="xliff"></a>
* Rapport d’audit d’annuaire

> [!TIP]
> Pour plus d’informations sur la création de rapports Azure AD, consultez la page [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
> 
> 

## Fonctionnement
<a id="how-it-works" class="xliff"></a>
### Pipeline de création de rapports
<a id="reporting-pipeline" class="xliff"></a>
Le pipeline de création de rapports se compose de trois étapes principales. Chaque fois qu’un utilisateur se connecte ou qu’une authentification est effectuée, voici ce qui se produit :

* Tout d’abord, l’utilisateur est authentifié (ou non) et le résultat est stocké dans les bases de données du service Azure Active Directory.
* À intervalles réguliers, toutes les connexions récentes sont traitées. À ce stade, nos algorithmes de sécurité et de surveillance des activités anormales recherchent toute activité suspecte dans les connexions récentes.
* Après traitement, les rapports sont écrits, mis en cache et placés dans le portail Azure Classic.

### Durée de génération des rapports
<a id="report-generation-times" class="xliff"></a>
En raison du volume important d’authentifications et de connexions traitées par la plateforme Azure AD, les connexions traitées les plus récentes datent en moyenne d’une heure. Dans de rares cas, il peut s’écouler jusqu’à 8 heures avant le traitement des connexions les plus récentes.

Vous trouverez la dernière connexion traitée en examinant le texte d’aide en haut de chaque rapport.

![Texte d’aide en haut de chaque rapport.](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Pour plus d’informations sur la création de rapports Azure AD, consultez la page [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
> 
> 

## Prise en main
<a id="getting-started" class="xliff"></a>
### Connectez-vous au portail Azure Classic
<a id="sign-into-the-azure-classic-portal" class="xliff"></a>
Tout d’abord, vous devez vous connecter au [portail Azure Classic](https://manage.windowsazure.com) comme administrateur global ou administrateur de conformité. Vous devez également être administrateur ou coadministrateur de service d’abonnement Azure ou utiliser l’abonnement Azure « Accès à Azure AD ».

### Accès aux rapports
<a id="navigate-to-reports" class="xliff"></a>
Pour afficher les rapports, accédez à l’onglet Rapports en haut de votre annuaire.

Si c’est la première fois que vous consultez les rapports, vous devez valider une boîte de dialogue avant de pouvoir les afficher. Il s’agit de vérifier qu’il est acceptable pour les administrateurs de votre organisation de consulter ces données, qui peuvent être considérées comme des informations privées dans certains pays.

![Boîte de dialogue](./media/active-directory-reporting-getting-started/dialogBox.png)

### Explorer chaque rapport
<a id="explore-each-report" class="xliff"></a>
Naviguez dans chaque rapport pour voir les données collectées et les connexions traitées. Vous trouverez une [liste de tous les rapports ici](active-directory-reporting-guide.md).

![Tous les rapports](./media/active-directory-reporting-getting-started/reportsMain.png)

### Télécharger les rapports au format CSV
<a id="download-the-reports-as-csv" class="xliff"></a>
Chaque rapport peut être téléchargé dans un fichier CSV (valeurs séparées par des virgules). Vous pouvez utiliser ces fichiers dans Excel, PowerBI ou un autre programme d’analyse pour analyser vos données.

Pour télécharger un rapport au format CSV, accédez au rapport et cliquez sur Télécharger en bas.

![Bouton de téléchargement](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Pour plus d’informations sur la création de rapports Azure AD, consultez la page [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
> 
> 

## Étapes suivantes
<a id="next-steps" class="xliff"></a>
### Personnalisation des alertes d’activité anormale dans les connexions
<a id="customize-alerts-for-anomalous-sign-in-activity" class="xliff"></a>
Accédez à l’onglet Configuration de votre annuaire.

Accédez à la section Notifications.

Activez ou désactivez la section Notification par courrier électronique des anomalies de connexion.

![Section Notifications](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Intégration avec l’API de création de rapports d’Azure AD
<a id="integrate-with-the-azure-ad-reporting-api" class="xliff"></a>
Consultez la rubrique [Prise en main de l’API de création de rapports](active-directory-reporting-api-getting-started.md).

### Application de l’authentification multifacteur aux utilisateurs
<a id="engage-multi-factor-authentication-on-users" class="xliff"></a>
Sélectionnez un utilisateur dans un rapport.

Cliquez sur le bouton Activer l’authentification multifacteur en bas de l’écran.

![Bouton Multi-Factor Authentication en bas de l’écran](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Pour plus d’informations sur la création de rapports Azure AD, consultez la page [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
> 
> 

## En savoir plus
<a id="learn-more" class="xliff"></a>
### Événements d'audit
<a id="audit-events" class="xliff"></a>
En savoir plus sur les événements audités dans l’annuaire dans les [Événements de rapport d'audit d'Azure Active Directory](active-directory-reporting-audit-events.md).

### Intégration de l’API
<a id="api-integration" class="xliff"></a>
Consultez [Prise en main de l’API de création de rapports](active-directory-reporting-api-getting-started.md) et la [documentation de référence de l’API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Prendre contact
<a id="get-in-touch" class="xliff"></a>
Envoyez un message à [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) pour faire part de vos commentaires, obtenir de l’aide ou poser une question.

> [!TIP]
> Pour plus d’informations sur la création de rapports Azure AD, consultez la page [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
> 
> 


