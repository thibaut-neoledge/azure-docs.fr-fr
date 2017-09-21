---
title: "Utiliser les révisions pour mettre à jour une API dans Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à apporter des modifications sans rupture à l’aide de révisions dans Gestion des API."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 212b163c49fdd133b0ed2d99b33035fcc391aec6
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.contentlocale: fr-fr
ms.lasthandoff: 09/21/2017

---

# <a name="make-non-breaking-changes-safely-using-revisions"></a>Apporter des modifications sans rupture en toute sécurité à l’aide de révisions
Ce didacticiel décrit comment apporter des modifications à votre API en toute sécurité et communiquer les modifications à vos développeurs.

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous devez créer un Service Gestion des API et disposer d’une API existante que vous pouvez modifier (à la place de l’API Conference).

## <a name="about-revisions"></a>À propos des révisions
Lorsque votre API est prête et commence à être utilisée par les développeurs, vous devez généralement veiller à ne pas interrompre les appelants de votre API lorsque vous apportez des modifications à cette dernière. Il est également utile d’informer les développeurs des modifications apportées. Les **révisions** le permettent dans Gestion des API Azure.

## <a name="walkthrough"></a>Procédure pas à pas
Au cours de cette procédure pas à pas nous ajoutons une révision, nous lui ajoutons une opération, puis nous créons une entrée de journal pour rendre cette révision actuelle.

## <a name="add-a-new-revision"></a>Ajouter une révision
1. Accédez à la page des **API** au sein du service Gestion des API sur le portail Azure.
2. Sélectionnez **API Conference** dans la liste des API, puis cliquez sur l’onglet **Révisions** dans le menu situé en haut de la page.
3. Sélectionnez **+ Ajouter une révision**

    > [!TIP]
    > Vous pouvez également choisir **Ajouter une révision** dans le menu contextuel (**... **) de l’API.
![Menu révisions situé près du haut de l’écran](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Indiquez une description pour la nouvelle révision qui vous aidera à vous souvenir à quoi elle sert.
5. Sélectionnez **Créer**
6. Vous venez de créer une nouvelle révision.

    > [!NOTE]
    > L’API d’origine reste dans **Revision 1**. Il s’agit de la révision que vos utilisateurs vont continuer à appeler, jusquà ce que vous choisissiez d’actualiser une autre révision.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apporter des modifications sans rupture à une révision
1. Sélectionnez l’onglet **Conception** situé près du haut de l’écran.
2. Notez que le **sélecteur de révision** (juste au-dessus de l’onglet Conception) affiche la révision actuelle en tant que **Revision 2**.

    > [!TIP]
    > Utilisez le sélecteur de révision pour basculer entre les révisions sur lesquelles vous souhaitez travailler.

3. Sélectionnez **+ Ajouter une opération**.
4. Définissez votre opération sur **POST** ainsi que le nom et le nom complet de l’opération sous forme de **Commentaires**
5. **Enregistrez** votre nouvelle opération.
6. Nous avons modifié **Revision 2**. Utilisez le **sélecteur de révision** situé près du haut de la page pour revenir à **Revision 1**.
7. Notez que votre nouvelle opération n’apparaît pas dans **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendre cette révision actuelle et ajouter une entrée au journal des modifications
1. Sélectionnez l’onglet **Révisions** dans le menu situé en haut de la page.
![Menu révision sur l’écran révision.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Ouvrez le menu contextuel (**... **) pour **Revision 2**.
3. Sélectionnez **Rendre actuel**.
![Rendre la révision actuelle et l’indiquer dans le journal des modifications](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Sélectionnez **Publier sur le journal des modifications public de cette API**
5. Indiquez une description de votre modification que les développeurs peuvent voir, par exemple, **« Ajout d’une nouvelle opération Commentaires ».**
6. **Revision 2** est maintenant la révision actuelle.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Parcourir le portail des développeurs pour voir les modifications et le journal des modifications
1. Sélectionnez **Portail des développeurs** dans le menu supérieur.
2. Sélectionnez **API**, puis **API Conference**.
3. Notez que votre nouvelle opération **Commentaires** est désormais disponible.
4. Sélectionnez **Historique des modifications de l’API** sous le nom de l’API.
5. Notez que l’entrée du journal des modifications apparaît dans cette liste.
![Journal des modifications sur le portail des développeurs](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Étapes suivantes
[Publier des versions d’API avec Gestion des API Azure](#api-management-getstarted-publish-versions.md)
