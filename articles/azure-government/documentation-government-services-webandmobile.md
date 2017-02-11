---
title: Azure Government Web, mobile et API | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Government Web + Mobile
## <a name="app-services"></a>App Services
### <a name="variations"></a>Variantes
Azure App Services est mis à la disposition générale dans Azure Government.

L’adresse des applications Azure App Service créées dans Azure Government est différente de celle des applications créées dans le cloud public :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| App Service |*.azurewebsites.net |*.azurewebsites.us|

Certaines fonctionnalités d’App Service disponibles dans le cloud public ne sont pas encore disponibles dans Azure Government :

- Environnements App Service
- Déploiement d’applications
    - Livraison continue (version préliminaire)
- Settings
    - Intégration au réseau virtuel et connexions hybrides
    - Analyse de la sécurité
- Outils de développement
    - Test de performance
    - Explorateur de ressources
    - Débogage PHP
- Surveillance
    - Application Insights
    - Métriques par instance
    - Trafic HTTP Live
    - Événements liés aux applications
    - Journaux FREB
- Prise en charge et résolution de problèmes
    - App Service Advisor
    - Historique des défaillances
    - Diagnostics en tant que service
    - Résoudre


### <a name="considerations"></a>Considérations
Les informations suivantes identifient les limites d’Azure Government pour App Service :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Les données entrées, stockées et traitées au sein d’Azure App Service peuvent contenir des données contrôlées à l’exportation. Fichiers binaires exécutés au sein d’Azure App Service. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL. Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. |Les métadonnées n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre Azure App Service. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Groupes de ressources, Noms de ressources, Balises de ressources|

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au [blog Microsoft Azure Government. ](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


