---
title: Utilisation de connecteurs de proxy d’application Azure AD | Microsoft Docs
description: Explique comment créer et gérer des groupes de connecteurs dans le proxy d’Application Azure AD.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: kgremban

---
# Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs - Version préliminaire publique
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portail Azure Classic](active-directory-application-proxy-connectors.md)
> 
> 

Les groupes de connecteurs sont utiles dans divers scénarios, notamment les suivants :

* Sites contenant plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant de trafic que possible au sein du centre de données, car les liaisons entre les centres de données sont coûteuses et lentes. Vous pouvez déployer des connecteurs dans chaque centre de données pour traiter uniquement les applications contenues dans le centre de données. Cette approche réduit au minimum les liaisons entre centres de données et fournit à vos utilisateurs une expérience entièrement transparente.
* Gestion d’applications installées sur des réseaux isolés qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteurs pour installer des connecteurs dédiés sur des réseaux isolés afin d’isoler les applications sur le réseau.
* Pour les applications installées sur IaaS pour l’accès au cloud, les groupes de connecteurs fournissent un service commun pour sécuriser l’accès à toutes les applications. Les groupes de connecteurs ne créent pas de dépendances supplémentaires sur votre réseau d’entreprise, et ne fragmentent pas l’expérience de l’application. Les connecteurs peuvent être installés sur chaque centre de données du cloud et servir uniquement les applications qui se trouvent sur ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
* Prise en charge des environnements à plusieurs forêts dans lesquels des connecteurs spécifiques peuvent être déployés par forêt et définis pour servir des applications spécifiques.
* Des groupes de connecteurs peuvent être utilisés dans les sites de récupération d’urgence pour détecter le basculement ou faire office de sauvegarde pour le site principal.
* Des groupes de connecteurs peuvent également être utilisés pour servir plusieurs sociétés à partir d’un client unique.

## Condition préalable : créez vos connecteurs
Pour regrouper vos connecteurs, vous devez vous assurer que vous avez [installé plusieurs connecteurs](active-directory-application-proxy-enable.md). Lorsque vous installez un nouveau connecteur, il est automatiquement ajouté au groupe de connecteurs **par défaut**.

## Étape 1 : créer des groupes de connecteurs
Vous pouvez créer autant de groupes de connecteurs que vous le souhaitez. La création de groupes de connecteurs s’effectue dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Azure Active Directory** pour accéder au tableau de bord de gestion de votre répertoire. À partir de là, sélectionnez **Applications d’entreprise** > **Proxy d’application**.
2. Sélectionnez le bouton **Groupes de connecteurs**. Le panneau Nouveau groupe de connecteurs s’affiche.
3. Donnez un nom à votre nouveau groupe de connecteurs, puis utilisez le menu déroulant pour sélectionner les connecteurs qui appartiennent à ce groupe.
4. Sélectionnez **Enregistrer** lorsque votre groupe de connecteurs est terminé.

## Étape 2 : affecter des applications à vos groupes de connecteurs
La dernière étape consiste à définir chaque application sur le groupe de connecteurs destiné à la servir.

1. Dans le tableau de bord de gestion de votre répertoire, sélectionnez **Applications d’entreprise** > **Toutes les applications** > l’application que vous souhaitez affecter à un groupe de connecteurs > **Proxy d’application**.
2. Sous **Groupe de connecteurs**, utilisez le menu déroulant pour sélectionner le groupe que l’application doit utiliser.
3. Sélectionnez **Enregistrer** pour appliquer la modification.

## Voir aussi
* [Activer le proxy d’application](active-directory-application-proxy-enable.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières nouvelles et mises à jour, consultez le site [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (blog sur le service Proxy d’application)

<!---HONumber=AcomDC_0914_2016-->