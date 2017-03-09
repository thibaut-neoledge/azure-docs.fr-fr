
---
title: "Activer Enterprise State Roaming dans Azure Active Directory | Microsoft Docs"
description: "Foire aux questions sur les paramètres Enterprise State Roaming sur les appareils Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique."
services: active-directory
keywords: enterprise state roaming, cloud windows, comment activer enterprise state roaming
documentationcenter: 
author: ningtan
manager: femila
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29
ms.lasthandoff: 01/05/2017


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Activer Enterprise State Roaming dans Azure Active Directory
Enterprise State Roaming est disponible pour toute organisation ayant souscrit un abonnement premium à Azure Active Directory (Azure AD). Pour plus d’informations sur l’obtention d’un abonnement Azure AD, consultez la [page produit Azure AD](https://azure.microsoft.com/services/active-directory).

Lorsque vous activez Enterprise State Roaming, votre organisation reçoit automatiquement les licences pour un abonnement gratuit à utilisation limitée pour Azure Rights Management. Cet abonnement gratuit est limité au chiffrage et au déchiffrage des paramètres d’entreprise et des données d’application synchronisées par le service Enterprise State Roaming ; pour utiliser toutes les fonctionnalités d’Azure Rights Management, vous devez souscrire un abonnement payant.

Après avoir obtenu un abonnement à Azure AD, procédez comme suit pour activer Enterprise State Roaming :

1. Connectez-vous au Portail Azure Classic.
2. Sur la gauche, sélectionnez **ACTIVE DIRECTORY**, puis sélectionnez le répertoire pour lequel vous souhaitez activer Enterprise State Roaming.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Cliquez sur l’onglet **CONFIGURER** en haut.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. Faites défiler la page vers le bas, sélectionnez **LES UTILISATEURS PEUVENT SYNCHRONISER LES PARAMÈTRES ET LES DONNÉES D’APPLICATION DE L’ENTREPRISE**, puis cliquez sur **ENREGISTRER**.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Pour qu’un appareil Windows 10 prenne en charge l’itinérance avec le service Enterprise State Roaming, l’appareil doit s’authentifier à l’aide d’une identité Azure AD. Pour les appareils joints à Azure AD, la connexion principale de l’utilisateur est l’identité Azure AD. Ainsi, aucune configuration supplémentaire n’est requise. Pour les appareils qui utilisent un annuaire Active Directory local traditionnel, l’administrateur informatique doit [connecter les appareils joints à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Stockage des données de synchronisation
Les données Enterprise State Roaming sont hébergées dans une ou plusieurs [régions Azure](https://azure.microsoft.com/regions/) qui correspondent le plus à la valeur de pays/région définie dans l’instance Azure Active Directory. Les données Enterprise State Roaming sont partitionnées selon les trois principales régions : Amérique du Nord, EMEA et Asie-Pacifique. Les données Enterprise State Roaming pour le client sont situées localement dans la région géographique, et ne sont pas répliquées entre régions.  Par exemple, les clients qui ont leur valeur de pays/région définie sur un des pays de la zone EMEA comme France ou Zambie verront leurs données hébergées sur une ou plusieurs des régions Azure en Europe.  Par exemple, les clients qui ont leur valeur de pays/région définie sur un des pays d’Amérique du Nord comme États-Unis ou Canada verront leurs données hébergées sur une ou plusieurs des régions Azure aux États-Unis.  Par exemple, les clients qui ont leur valeur de pays/région définie sur un des pays de la zone APAC comme Australie ou Nouvelle-Zélande verront leurs données hébergées sur une ou plusieurs des régions Azure en Asie.  Les pays d’Amérique du Sud et de l’Antarctique verront leurs données hébergées sur une ou plusieurs des régions Azure aux États-Unis.  La valeur de pays/région est définie dans le cadre du processus de création d’un répertoire Azure AD et ne peut pas être modifiée par la suite. 

Pour plus d’informations sur l’emplacement de stockage des données, veuillez soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Gérer Enterprise State Roaming
Les administrateurs Azure AD globaux peuvent activer et désactiver Enterprise State Roaming dans le portail Azure Classic.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Les administrateurs globaux peuvent limiter la synchronisation des paramètres à des groupes de sécurité spécifiques.

Les administrateurs globaux peuvent également afficher un rapport d’état de synchronisation par utilisateur particulier dans la liste **UTILISATEURS** de l’instance Active Directory et en cliquant sur l’onglet **APPAREILS** onglet et en sélectionnant la vue **Paramètres de synchronisation et données d’application d’entreprise des appareils**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>Conservation des données
Les données synchronisées sur Azure via Enterprise State Roaming sont conservées indéfiniment, sauf si elles sont supprimées manuellement ou déterminées comme étant obsolètes. 

**Suppression explicite :** les données sont supprimées lorsqu’un administrateur Azure supprime un utilisateur ou un répertoire, ou qu’un administrateur demande explicitement que les données soient supprimées.

* **Suppression d’un utilisateur**: lorsqu’un utilisateur est supprimé dans Azure AD, les données d’itinérance de l’utilisateur seront marquées pour suppression et seront supprimées après 90 à 180 jours. 
* **Suppression d’un répertoire**: la suppression d’un répertoire entier dans Azure AD est une opération à effet immédiat. Toutes les données de paramètres associées à ce répertoire sont marquées pour suppression et supprimées après 90 à 180 jours. 
* **Suppression à la demande**: si l’administrateur Azure AD souhaite supprimer manuellement des données d’utilisateur ou de paramètre spécifiques, il peut soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/). 

**Suppression de données obsolètes**: les données qui n’ont pas été utilisées depuis un an (« la période de rétention ») sont considérées comme obsolètes et peuvent être supprimées d’Azure. La période de rétention est susceptible de changer, mais ne sera pas inférieure à 90 jours. Il peut s’agir d’un ensemble spécifique de paramètres Windows/d’application ou de tous les paramètres d’un utilisateur. Par exemple :

* Si une collection de paramètres spécifique n’est utilisée par aucun appareil (par exemple, si une application est supprimée de l’appareil ou qu’un groupe de paramètres tels que « Thème » est désactivé pour tous les appareils d’un utilisateur), elle devient obsolète à la fin de la période de rétention et peut être supprimée. 
* Si un utilisateur a désactivé la synchronisation des paramètres sur tous ses appareils, aucune donnée de paramètres n’est utilisée. De plus, toutes les données de paramètres pour cet utilisateur deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 
* Si l’administrateur du répertoire Azure AD désactive Enterprise State Roaming pour l’ensemble du répertoire, la synchronisation des paramètres cessera pour tous les utilisateurs du répertoire. De plus, toutes les données de paramètres de tous les utilisateurs deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 

**Récupération de données supprimées**: la stratégie de rétention de données n’est pas configurable. Une fois que les données ont été définitivement supprimées, elles ne sont pas récupérables. Toutefois, il est important de noter que les données de paramètres seront supprimées sur Azure uniquement, et non sur l’appareil de l’utilisateur final. Si un appareil se reconnecte au service Enterprise State Roaming, les paramètres sont alors à nouveau synchronisés et stockés dans Azure.

## <a name="related-topics"></a>Rubriques connexes
* [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Dépannage](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

