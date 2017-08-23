---
title: "Emplacements nommés dans Azure Active Directory | Documents Microsoft"
description: "En configurant des emplacements nommés, vous pouvez éviter que des adresses IP appartenant à votre organisation ne génèrent de faux positifs pour le type d’événement à risque Voyage impossible vers des emplacements inhabituels."
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
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e11f19d518b22a7be4f8daf93304821b42e94a9b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---
# <a name="named-locations-in-azure-active-directory"></a>Emplacements nommés dans Azure Active Directory

Grâce à la fonctionnalité des emplacements nommés Azure Active Directory, vous pouvez désigner des plages d’adresses IP approuvées au sein d’une organisation. Dans votre environnement, vous pouvez utiliser les emplacements nommés dans le cadre de la détection d’[événements à risque](active-directory-reporting-risk-events.md). Cette fonction permet de réduire le nombre de faux-positifs signalés pour le type d’événement à risque *Voyage impossible vers des emplacements inhabituels*. 

## <a name="configuration"></a>Configuration

Pour configurer un emplacement nommé :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Dans le volet gauche, cliquez sur **Azure Active Directory**.

    ![Lien Azure Active Directory dans le volet gauche](./media/active-directory-named-locations/01.png)

3. Dans le panneau **Azure Active Directory**, sous la section **Sécurité**, cliquez sur **Accès conditionnel**.

    ![Commande Accès conditionnel](./media/active-directory-named-locations/05.png)


4. Dans le panneau **Azure Active Directory**, sous la section **Gérer**, cliquez sur **Emplacements nommés**.

    ![Commande Emplacements nommés](./media/active-directory-named-locations/06.png)


5. Dans le panneau **Emplacements nommés**, cliquez sur **Nouvel emplacement**.

    ![Commande Nouvel emplacement](./media/active-directory-named-locations/07.png)


6. Dans le panneau **Nouveau**, procédez comme suit :

    ![Panneau Nouveau](./media/active-directory-named-locations/08.png)

    a. Dans la zone de texte **Nom**, tapez un nom pour votre emplacement nommé.

    b. Dans la zone de texte **Plage d’adresses IP**, tapez une plage d’adresses IP. La plage d’adresses IP doit être au format *CIDR* (Classless Inter-Domain Routing).  

    c. Cliquez sur **Create**.



## <a name="what-you-should-know"></a>Ce que vous devez savoir

**Mises à jour en bloc** : lorsque vous créez ou mettez à jour des emplacements nommés, pour effectuer des mises à jour en bloc, vous pouvez charger ou télécharger un fichier CSV contenant les plages d’adresses IP. Un téléchargement ajoute les plages d’adresses IP dans le fichier au lieu de remplacer la liste.

![Liens Charger et Télécharger](./media/active-directory-named-locations/09.png)


**Limites** : vous pouvez définir un maximum de 60 emplacements nommés avec une plage d’adresses IP assignée à chacun d’eux. Si vous n’avez qu’un seul emplacement nommé configuré, vous pouvez définir jusqu’à 500 plages d’adresses IP pour celui-ci.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les événements à risque, consultez [Événements à risque dans Azure Active Directory](active-directory-reporting-risk-events.md).


