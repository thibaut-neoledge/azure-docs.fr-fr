---
title: "Azure AD Connect : Étapes suivantes et gestion Azure AD Connect | Microsoft Docs"
description: "Apprenez à étendre la configuration par défaut et les tâches opérationnelles pour Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: aa20b20c86763791eb579883b5273ea79cc714b5
ms.openlocfilehash: 20195cb0d653cf0b1dc87af0fedbf9e49f259737


---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Étapes suivantes et gestion d’Azure AD Connect
Les rubriques suivantes relatives aux fonctionnalités avancées vous permettent de personnaliser Azure Active Directory Connect pour l’adapter aux besoins et aux spécifications de votre organisation.  

## <a name="add-additional-sync-administrators"></a>Ajouter des administrateurs de synchronisation supplémentaires
Par défaut, seul l’utilisateur qui a effectué l’installation et les administrateurs locaux sont en mesure de gérer le moteur de synchronisation installé. Pour que d’autres personnes puissent accéder au moteur de synchronisation et en assurer la gestion, localisez le groupe nommé ADSyncAdmins sur le serveur local et ajoutez ces personnes à ce groupe.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Attribution des licences aux utilisateurs Azure AD Premium et Enterprise Mobility
Maintenant que vos utilisateurs ont été synchronisés dans le cloud, vous devez leur attribuer une licence afin qu’ils puissent commencer à utiliser les applications de cloud telles qu’Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Pour l’attribution d’une licence Azure AD Premium ou Enterprise Mobility Suite
- - -
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory**à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Licences**.
5. Sur la page des licences, sélectionnez Active Directory Premium ou Enterprise Mobility Suite, puis cliquez sur **Attribuer**.
6. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l'icône de coche pour enregistrer les modifications.

## <a name="verifying-the-scheduled-synchronization-task"></a>Vérification de la tâche de synchronisation planifiée
Si vous souhaitez vérifier l’état d’une synchronisation, vous pouvez le faire dans le portail Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Pour la vérification de la tâche de synchronisation planifiée
- - -
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory**à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Intégration du répertoire**.
5. Sous Intégration avec un répertoire actif local, notez l’heure de la dernière synchronisation.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Début de la tâche de synchronisation planifiée
Si vous devez exécuter une tâche de synchronisation, vous pouvez le faire en l’exécutant une nouvelle fois depuis l’Assistant Azure AD Connect.  Vous devez fournir vos informations d’identification Azure AD.  Dans l’Assistant, sélectionnez la tâche **Personnalisation des options de synchronisation** et cliquez sur Suivant dans l’Assistant. À la fin, vérifiez que la case **Démarrer le processus de synchronisation dès que la configuration initiale est terminée** est cochée.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Pour plus d’informations sur le planificateur de synchronisation Azure AD Connect, consultez [Planificateur Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md)

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tâches supplémentaires disponibles dans Azure AD Connect
Après l’installation initiale d’Azure AD Connect, vous pouvez toujours redémarrer l’Assistant depuis la page de démarrage ou le raccourci du bureau Azure AD Connect.  Vous remarquerez qu’en passant à nouveau par l’Assistant de nouvelles options apparaissent sous forme de tâches supplémentaires.  

Le tableau suivant fournit un résumé de ces tâches et une brève description de chacune d’elles.

![Règle de jointure](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Informations supplémentaires | Description |
| --- | --- |
| Afficher le scénario sélectionné |Vous pouvez afficher votre solution Azure AD Connect actuelle.  Cela inclut les paramètres généraux, les répertoires synchronisés, les paramètres de synchronisation, etc. |
| Personnalisation des options de synchronisation |Vous pouvez modifier la configuration actuelle, y compris l’ajout de forêts Active Directory supplémentaires à la configuration ou l’activation des options de synchronisation telles que l’utilisateur, le groupe, l’appareil ou le mot de passe en écriture différée. |
| Activation du mode intermédiaire |Cela vous permet d’organiser les informations qui seront synchronisées plus tard, mais rien ne sera exporté vers Azure AD ou Active Directory.  Cela vous permet d’afficher un aperçu des synchronisations avant qu’elles ne s’effectuent. |

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


