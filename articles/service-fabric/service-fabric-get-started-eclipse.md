---
title: Prise en main du plug-in Eclipse pour Azure Service Fabric | Microsoft Docs
description: Prise en main du plug-in Eclipse pour Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Prise en main du plug-in Eclipse pour le développement d’applications Java Fabric Service
Eclipse est l’un des IDE les plus utilisés par les développeurs Java. Dans cet article, nous expliquons comment vous pouvez configurer votre environnement de développement Eclipse pour utiliser Service Fabric. Cet article vous aidera à installer le plug-in, à créer des applications Service Fabric et à déployer votre application Service Fabric dans un cluster Service Fabric local ou distant.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Installer ou mettre à jour le plug-in Service Fabric sur Eclipse Neon
Service Fabric fournit un plug-in pour **l’IDE Eclipse pour les développeurs Java** qui peut simplifier le processus de génération et de déploiement des services Java.

1. Assurez-vous que vous disposez de la dernière version d’Eclipse **Neon** et de Buildship (version&1;.0.17 ou ultérieure). Vous pouvez vérifier les versions des composants installés en choisissant ``Help => Installation Details``. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update]. Pour vérifier si vous disposez de la dernière version d’Eclipse Neon et effectuer la mise à jour, vous pouvez accéder à ``Help => Check for Updates``.

2. Pour installer le plug-in Service Fabric, choisissez ``Help => Install New Software...``.
  1. Dans la zone de texte « Work with » (Utiliser), entrez : ``http://dl.windowsazure.com/eclipse/servicefabric``
  2. Cliquez sur Ajouter.

  ![Plug-in Eclipse Neon pour Service Fabric][sf-eclipse-plugin-install]

  3. Choisissez le plug-in Service Fabric et cliquez sur Suivant.
  4. Suivez les étapes d’installation et acceptez le contrat de licence d’utilisateur final.

Si le plug-in Eclipse Service Fabric est déjà installé, assurez-vous que vous avez la dernière version. Vous pouvez vérifier s’il peut être mis à jour en suivant - ``Help => Installation Details``. Recherchez ensuite Service Fabric dans la liste des plug-ins installés, puis cliquez sur Mettre à jour. Les mises à jour en attente seront extraites et installées.

> [!NOTE]
> Si l’installation ou la mise à jour du plug-in Eclipse Service Fabric prend beaucoup de temps sur votre Eclipse, cela s’explique par le fait qu’Eclipse tente d’extraire les métadonnées de toutes les modifications qui se sont produites sur tous les sites de mise à jour inscrits auprès de votre instance d’Eclipse. Par conséquent, pour accélérer cette opération, vous pouvez utiliser l’astuce suivante : accédez à `Available Software Sites` et décochez tous les éléments autres que celui qui pointe vers l’emplacement du plug-in Service Fabric `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Créer une application Service Fabric avec Eclipse

1. Accédez à ``File => New => Other`` Sélectionnez ``Service Fabric Project``. Cliquez sur ``Next``.

    ![Nouveau projet Service Fabric, page 1][create-application/p1]

2. Donnez un nom à votre projet. Cliquez sur ``Next``.

    ![Nouveau projet Service Fabric, page 2][create-application/p2]

3. Sélectionnez Modèle de service à partir de l’ensemble de modèles disponible (acteur, sans état, conteneur ou exécutable invité). Cliquez sur ``Next``.

    ![Nouveau projet Service Fabric, page 3][create-application/p3]

4. Entrez le nom du service et/ou les détails pertinents sur le service sur cette page, puis cliquez sur ``Finish``.

    ![Nouveau projet Service Fabric, page 4][create-application/p4]

5. Lorsque vous créez votre premier projet Service Fabric, vous serez invité à définir la perspective Service Fabric. Sélectionnez ``yes`` pour continuer.

    ![Nouveau projet Service Fabric, page 5][create-application/p5]

6. Après avoir créé le projet, celui-ci ressemble à ce qui suit :

    ![Nouveau projet Service Fabric, page 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Générer et déployer l’application Service Fabric avec Eclipse

* Cliquez avec le bouton droit sur l’application Service Fabric que vous venez de créer ci-dessus. Sélectionnez l’option ``Service Fabric`` dans le menu contextuel. Un sous-menu présentant plusieurs options apparaît alors. Il ressemble à ce qui suit :

    ![Menu clic droit Service Fabric][publish/RightClick]

  Une fois que vous cliquez sur les options de génération, nouvelle génération et de suppression, les actions prévues sont lancées.
  - ``Build Application`` crée l’application sans suppression.
  - ``Rebuild Application`` supprime, puis génère l’application.
  - ``Clean Application`` supprime l’application des artefacts générés.


* Vous pouvez également choisir de déployer, d’annuler le déploiement et de publier votre application depuis ce menu.
  - ``Deploy Application`` effectue un déploiement dans votre cluster local.
  - ``Publish Application...`` vous demande quel profil de publication vous souhaitez sélectionner entre ``Local.json`` et ``Cloud.json``. Ces fichiers JSON sont utilisés pour stocker des informations (telles que les points de terminaison de connexion et les informations de sécurité) requises pour se connecter au cluster (Azure) local ou cloud.

  ![Menu clic droit Service Fabric][publish/Publish]

* Il existe une autre méthode pour déployer votre application Service Fabric à l’aide des configurations d’exécution Eclipse.

  1. Choisissez ``Run => Run Configurations``. Sélectionnez la configuration d’exécution ``ServiceFabricDeployer`` sous ``Grade Project``.
  2. Sous l’onglet ``Arguments`` dans le volet droit, spécifiez **local** ou **cloud** pour ``publishProfile``. La valeur par défaut est **local**. Pour effectuer un déploiement dans un cluster cloud/distant, sélectionnez **cloud**.
  3. Vérifiez que les informations appropriées sont renseignées dans les profils de publication, en modifiant `Local.json` ou `Cloud.json` si nécessaire, avec les informations sur les points de terminaison et les informations d’identification de sécurité, le cas échéant.
  4. Dans le volet droit, vérifiez que ``Working Directory`` (sous ``Grade Project``) pointe vers l’application que vous souhaitez déployer. Si ce n’est pas le cas, cliquez simplement sur le bouton ``Workspace...`` et sélectionnez l’application souhaitée.
  5. Cliquez sur **Appliquer**, puis sur **Exécuter**.

Votre application est générée et déployée en quelques instants. Vous pouvez surveiller son état depuis Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Ajouter un service Service Fabric à votre application Service Fabric

L’ajout d’un service Service Fabric à une application Service Fabric existante est possible en procédant comme suit :

1. Cliquez avec le bouton droit sur le projet dans lequel vous souhaitez ajouter un service pour ouvrir le menu contextuel. Sélectionnez ensuite l’option « Service Fabric ». Un sous-menu présentant plusieurs options apparaît alors.

    ![Ajouter un service Service Fabric, page 1][add-service/p1]

2. Sélectionnez l’option `Add ServiceFabric Service`. Vous serez ensuite guidé tout au long des étapes nécessaires pour ajouter un service au projet.
3. Sélectionnez le modèle de service que vous souhaitez ajouter à votre projet et cliquez sur « Suivant ».

    ![Ajouter un service Service Fabric, page 2][add-service/p2]

4. Entrez le nom du service (et les autres détails nécessaires, le cas échéant), puis cliquez sur le bouton « Ajouter un service » ci-dessous.  

    ![Ajouter un service Service Fabric, page 3][add-service/p3]

5. Une fois le service ajouté, la structure de projet ressemble ce qui suit :

    ![Ajouter un service Service Fabric, page 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Mettre à niveau votre application Java Service Fabric

Supposons que vous avez créé le projet ``App1`` à l’aide de votre plug-in Eclipse Service Fabric et que vous avez déployé votre projet à l’aide du plug-in pour créer une application nommée ``fabric:/App1Application``, de type ``App1AppicationType`` et de version 1.0. Vous voulez à présent mettre à niveau votre application sans la retirer.

Modifiez votre application et reconstruisez le service modifié.  Mettez à jour le fichier manifeste du service modifié (``ServiceManifest.xml``) avec les versions mises à jour du service (Code, Config ou Données comme il convient). Modifiez également le manifeste de l’application (``ApplicationManifest.xml``) avec le numéro de version mis à jour de l’application et le service modifié.  

Pour mettre à niveau votre application à l’aide d’Eclipse, vous pouvez créer une configuration d’exécution en double et l’utiliser pour mettre à niveau votre application quand cela est nécessaire, en procédant comme suit :
1. Choisissez ``Run => Run Configurations``. Cliquez sur la petite flèche située à gauche de ``Grade Project`` dans le volet gauche.
2. Cliquez avec le bouton droit sur ``ServiceFabricDeployer`` et sélectionnez ``Duplicate``. Donnez un nouveau nom à cette configuration, par exemple ``ServiceFabricUpgrader``.
3. Dans le volet droit, sous l’onglet ``Arguments``, remplacez ``-Pconfig='deploy'`` par ``-Pconfig=upgrade``, puis cliquez sur ``Apply``.
4. Vous venez de créer et d’enregistrer une configuration d’exécution pour la mise à niveau de votre application, que vous pouvez ``Run`` lorsque vous le souhaitez. Elle se chargera d’obtenir la dernière version mise à jour du type d’application à partir du fichier de manifeste d’application.

Vous pouvez désormais contrôler la mise à niveau de l’application à l’aide de Service Fabric Explorer. Dans quelques minutes, l’application aura été mise à jour.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

