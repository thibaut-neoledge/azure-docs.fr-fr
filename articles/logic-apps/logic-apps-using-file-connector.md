---
title: "Se connecter à un dossier de système de fichiers local dans Azure Logic Apps | Microsoft Docs"
description: "Utiliser la passerelle de données locale pour se connecter à un système de fichiers local dans le workflow de l’application logique"
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 336e3f2bba2fe3bb7abdd6959354349ff61ed6a8
ms.openlocfilehash: 3bbcb5e9c7a4731eb8eb410a94fddb93b2ea8416


---
# <a name="use-the-file-system-connector-with-the-on-premises-data-gateway-in-a-logic-app"></a>Utiliser le connecteur Système de fichiers avec la passerelle de données locale dans une application logique

La connectivité de cloud hybride est au cœur de Logic Apps. La passerelle de données locale vous permet de gérer les données et d’accéder en toute sécurité aux ressources locales à partir de Logic Apps. Dans cet article, nous montrons comment se connecter à un système de fichiers local avec un scénario simple : copie d’un fichier chargé sur Dropbox vers un partage de fichiers, puis envoi d’un e-mail.

## <a name="prerequisites"></a>Composants requis
- Installez et configurez la toute dernière [passerelle de données locale](https://www.microsoft.com/en-us/download/details.aspx?id=53127).
- Installez la dernière passerelle de données locale, version 1.15.6150.1 ou version ultérieure. La rubrique [Se connecter à la passerelle de données locale](http://aka.ms/logicapps-gateway) répertorie les étapes nécessaires. La passerelle doit être installée sur un ordinateur local avant de pouvoir effectuer le reste des étapes.

## <a name="use-file-system-connector"></a>Utiliser le connecteur de système de fichiers

1. Nous allons créer un déclencheur de Dropbox à la création d’un fichier. En procédant ainsi, il vous suffit de saisir « Système de fichiers » dans la recherche pour voir toutes les actions du connecteur de fichiers prises en charge.

 ![Rechercher le connecteur de fichiers](media/logic-apps-using-file-connector/search-file-connector.png)

2. Choisissez « Créer le fichier » et créez une connexion pour celui-ci.
 - Si vous n’avez pas de connexion existante, vous êtes invité à en créer une.
 - Cochez la case « Se connecter via la passerelle de données locale ». Davantage de propriétés sont affichées.
 - Sélectionnez le dossier racine. Il peut s’agir d’un dossier local situé sur la machine où est installée la passerelle de données locale ou d’un partage réseau auquel la machine a accès.
 - Entrez les nom d’utilisateur et mot de passe pour accéder à la passerelle.
 - Sélectionnez la passerelle que vous avez installée à l’étape précédente.
    
 > [!NOTE]
 > Le dossier racine est le dossier parent principal qui est utilisé pour les chemins relatifs pour toutes les actions liées aux fichiers.

 ![Configurer la connexion](media/logic-apps-using-file-connector/create-file.png)

3. Une fois tous les détails saisis, cliquez sur Créer. Logic Apps configure et teste la connexion pour vérifier son bon fonctionnement. Si tout est correct, vous voyez des options pour la carte que vous avez sélectionnée précédemment. Le connecteur Système de fichiers est maintenant prêt à être utilisé.

4. Prenez le fichier chargé sur la Dropbox et copiez-le dans le dossier racine du partage de fichiers local.

 ![Créer une action de fichier](media/logic-apps-using-file-connector/create-file-filled.png)

5. Une fois le fichier copié, envoyez un e-mail afin que les utilisateurs en soit informés. Comme les autres connecteurs, la sortie issue des actions précédentes est disponible dans le sélecteur de contenu dynamique.
 - Saisissez les destinataires, l’objet et le corps de l’e-mail. Utilisez le sélecteur de contenu dynamique pour choisir la sortie à partir du connecteur de fichiers afin d’enrichir l’e-mail.

 ![Envoyer l’action de messagerie](media/logic-apps-using-file-connector/send-email.png)

6. Enregistrez votre application logique et testez-le en chargeant un fichier sur la Dropbox. Le fichier en cours de copie vers le partage de fichiers local doit s’afficher et vous recevrez un e-mail de notification relatif à l’opération.

    > [!TIP] 
    > Découvrez comment [analyser vos Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

7. Maintenant vous disposez d’une application logique utilisant le connecteur de système de fichiers. Vous pouvez commencer à explorer les autres fonctionnalités proposées :

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

## <a name="next-steps"></a>Étapes suivantes
- Découvrez [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Créez une [connexion locale](../logic-apps/logic-apps-gateway-connection.md) vers Logic Apps.



<!--HONumber=Jan17_HO5-->


