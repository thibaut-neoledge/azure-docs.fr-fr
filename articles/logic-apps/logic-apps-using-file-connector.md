---
title: "Se connecter aux systèmes de fichiers locaux à partir d’Azure Logic Apps | Microsoft Docs"
description: "Se connecter à un système de fichiers local à partir du flux de travail d’applications logiques par le biais de la passerelle de données locale et du connecteur du système de fichiers"
keywords: "systèmes de fichiers"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli; LADocs
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 9eab48d40c3d9db896724d3bdb574d89fdab2796
ms.lasthandoff: 04/04/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Se connecter aux systèmes de fichiers local à partir d’applications logiques avec le connecteur du système de fichiers

La connectivité cloud hybride est essentielle pour les applications logiques ; par conséquent, pour gérer les données et accéder en toute sécurité aux ressources locales, vos applications logiques peuvent utiliser la passerelle de données locale. Dans cet article, nous montrons comment se connecter à un système de fichiers local avec un scénario de base : copie d’un fichier chargé sur Dropbox vers un partage de fichiers, puis envoi d’un e-mail.

## <a name="prerequisites"></a>Composants requis

- Installez et configurez la toute dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127).
- Installez la dernière passerelle de données locale, version 1.15.6150.1 ou version ultérieure. La rubrique [Se connecter à la passerelle de données locale](http://aka.ms/logicapps-gateway) répertorie les étapes nécessaires. La passerelle doit être installée sur un ordinateur local avant de pouvoir effectuer le reste des étapes.

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Ajouter un déclencheur et des actions pour se connecter au système de fichiers

1. Créez une application logique et ajoutez ce déclencheur Dropbox : **Lorsqu’un fichier est créé**. 
2. Sous le déclencheur, choisissez **Étape suivante** > **Ajouter une action**. 
3. Dans la zone de recherche, entrez `file system` pour afficher toutes les actions prises en charge pour le connecteur du système de fichiers.

   ![Rechercher le connecteur de fichiers](media/logic-apps-using-file-connector/search-file-connector.png)

2. Choisissez l’action **Créer un fichier** et créez une connexion à votre système de fichiers.

   Si vous n’avez pas de connexion existante, vous êtes invité à en créer une.

   1. Choisissez **Se connecter via la passerelle de données locale**. D’autres propriétés s’affichent.
   2. Sélectionnez le dossier racine de votre système de fichiers.
      
       > [!NOTE]
       > Le dossier racine est le dossier parent principal qui est utilisé pour les chemins relatifs de toutes les actions liées aux fichiers. Vous pouvez spécifier un dossier local situé sur la machine où est installée la passerelle de données locale ; il peut également s’agir d’un partage réseau auquel la machine a accès.

   3. Entrez le nom d’utilisateur et le mot de passe de la passerelle.
   4. Sélectionnez la passerelle que vous avez installée précédemment.

       ![Configurer la connexion](media/logic-apps-using-file-connector/create-file.png)

3. Après avoir fourni tous les détails, choisissez **Créer**. 

   Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement. 
   Si la connexion est correctement configurée, des options s’affichent pour l’action sélectionnée précédemment. 
   Le connecteur Système de fichiers est maintenant prêt à être utilisé.

4. Spécifiez que vous souhaitez copier des fichiers de Dropbox vers le dossier racine de votre partage de fichiers local.

   ![Créer une action de fichier](media/logic-apps-using-file-connector/create-file-filled.png)

5. Une fois que votre application logique a copié le fichier, ajoutez une action d’Outlook qui envoie un e-mail afin que les utilisateurs concernés soient informés de la présence du nouveau fichier. Saisissez les destinataires, l’objet et le corps de l’e-mail. 

   Dans le sélecteur de contenu dynamique, vous pouvez choisir les sorties de données du connecteur de fichier pour pouvoir ajouter plus de détails à l’adresse e-mail.

   ![Envoyer l’action de messagerie](media/logic-apps-using-file-connector/send-email.png)

6. Enregistrez votre application logique. Testez votre application en chargeant un fichier sur Dropbox. Le fichier se copie sur le partage de fichiers local et vous recevrez un e-mail concernant l’opération.

   > [!TIP] 
   > Apprenez à [surveiller vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Félicitations, vous avez maintenant une application logique en état de fonctionnement qui peut se connecter à votre système de fichiers local. Essayez de découvrir les autres fonctionnalités offertes par le connecteur, par exemple :

- Créer un fichier
- Répertorier les fichiers dans un dossier
- Ajouter un fichier
- Supprimer un fichier
- Obtenir le contenu d’un fichier
- Obtenir le contenu d’un fichier à l’aide du chemin
- Obtenir les métadonnées d’un fichier
- Obtenir les métadonnées d’un fichier à l’aide du chemin
- Répertorier les fichiers dans le dossier racine
- Mettre à jour un fichier

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions ou y répondre et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

- [Se connecter à des données locales](../logic-apps/logic-apps-gateway-connection.md) à partir d’applications logiques
- En savoir plus sur [l’intégration d’entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)

