---
title: "Réseaux nommés dans Azure Active Directory | Microsoft Docs"
description: "En configurant des réseaux nommés, vous pouvez éviter que des adresses IP appartenant à votre organisation ne génèrent des faux positifs pour les connexions à partir de plusieurs zones géographiques et les connexions à partir d’adresses IP avec des événements présentant un risque d’activité suspecte."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 71116ade676677c5b6d14b07a038d043fba46032
ms.openlocfilehash: 02a1aae805aa36aa68f341197e44b19bc6236b7b


---
# <a name="named-networks-in-azure-active-directory"></a>Réseaux nommés dans Azure Active Directory

> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-known-networks-azure-portal.md)
> * [Portail Azure Classic](active-directory-known-networks.md)
> 
> 


Azure Active Directory crée un enregistrement pour chaque [événement à risque](active-directory-identity-protection-risk-events.md) détecté. Grâce aux informations sur les événements à risque disponibles dans les rapports de sécurité d’Azure Active Directory, vous pouvez obtenir des informations sur la probabilité de comptes d’utilisateur compromis au sein de votre environnement.   

Il est possible qu’Azure Active Directory détecte des faux positifs de *voyage impossible vers des emplacements inhabituels* et de *connexions à partir d’adresses IP avec des* [types d’événement à risque](active-directory-identity-protection-risk-events.md#risk-event-types) présentant une activité suspecte, pour des adresses IP qui appartiennent à votre organisation. 

Cela peut, par exemple, se produire dans les cas suivants : 

- Un utilisateur de votre bureau de Boston qui s’est connecté à distance à votre centre de données de San Francisco génère un événement à risque *Connexions depuis plusieurs zones géographiques*. 

- Un utilisateur de votre organisation qui tente de se connecter plusieurs fois avec un mot de passe incorrect génère le rapport *Connexions à partir d’adresses IP affichant une activité suspecte*. 

Pour empêcher ces cas de générer à tort des rapports d’événements à risque, vous devez ajouter des plages d’adresses IP nommées à la liste d’adresses IP publiques de votre organisation.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Pour ajouter les plages d'adresses IP publiques de votre organisation, procédez comme suit :

1. Connectez-vous au portail de gestion Azure.
 
2. Dans le volet gauche, cliquez sur **Active Directory**.

    ![Réseaux connus](./media/active-directory-known-networks-azure-portal/01.png)

3. Dans la section **Gérer** du panneau de répertoire, cliquez sur **Réseaux nommés**.

    ![Réseaux connus](./media/active-directory-known-networks-azure-portal/02.png)


4. Cliquez sur **Ajouter un emplacement**.

    ![Réseaux connus](./media/active-directory-known-networks-azure-portal/03.png)

5. Dans le panneau **Ajouter**, procédez comme suit :

    ![Réseaux connus](./media/active-directory-known-networks-azure-portal/04.png)

    a. Dans la zone de texte **Nom**, tapez un nom.

    b. Dans la zone de texte **Plage d’adresses IP**, tapez une plage d’adresses IP. La plage d’adresses IP doit être au format CIDR. Pour effectuer une mise à jour en bloc, vous pouvez télécharger un fichier CSV contenant les plages d’adresses IP. Un téléchargement ajoute les plages d’adresses IP dans le fichier au lieu de remplacer la liste.

    c. Cliquez sur **Create**.


**Ressources supplémentaires :**

* [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)
* [Connexions à partir d’adresses IP affichant une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Connexions depuis plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md)




<!--HONumber=Jan17_HO3-->


