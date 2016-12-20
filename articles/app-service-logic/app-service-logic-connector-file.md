---
title: "Utilisation du connecteur File dans des applications logiques | Microsoft Docs"
description: "Comment créer et configurer le connecteur File ou une application API et l’utiliser dans une application logique d’Azure App Service"
author: rajeshramabathiran
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07ceb81a-f8f6-4901-a7a2-06a9ac47efd5
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 61b3644214a7cb067dbb59e8fbd1464af78ae3e1


---
# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Utilisation et ajout du connecteur File dans votre application logique
> [!NOTE]
> Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.
> 
> 

Connectez-vous à un système de fichiers pour, entre autres, charger, télécharger vos fichiers sur un ordinateur hôte. Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données. Vous pouvez ajouter le connecteur File à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique. 

Le connecteur File utilise le Gestionnaire de connexion hybride pour la connectivité hybride vers le système de fichiers hôte.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Création d’un connecteur File pour votre application logique
Pour utiliser le connecteur File, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU située à gauche du portail Azure.
2. Recherchez « connecteur file ».
3. Sélectionnez **Connecteur File (version préliminaire)** dans les résultats de la recherche.
4. Cliquez sur le bouton **Créer**
5. Configurez le connecteur File comme suit :   
   ![][1]
   
   * **Nom** : indiquez le nom du connecteur File.
   * **Paramètres du package**
     * **Dossier racine** : indiquez le chemin du dossier racine sur l’ordinateur hôte. par exemple D:\FileConnectorTest
     * **Chaîne de connexion au Service Bus** : indiquez la chaîne de connexion au Service Bus. Vérifiez que l'espace de noms du Service Bus est de type Standard et NON Basique pour permettre l'utilisation de Service Bus Relays.  Service Bus Relay est utilisé pour se connecter au Gestionnaire de connexion hybride.
   * **Plan App Service** : sélectionnez ou créez un plan App Service.
   * **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
   * **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
   * **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
   * **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
6. Cliquez sur Créer. Un connecteur File est créé.

## <a name="configure-hybrid-connection-manager"></a>Configurer le Gestionnaire de connexion hybride
Une fois l'instance de l'application API créée, accédez à son tableau de bord.  Pour ce faire, cliquez sur Parcourir > Applications API > sélectionnez l’application API du connecteur File.  À ce stade, le Gestionnaire de connexion hybride doit être configuré.
Pour plus d'informations sur la configuration et le dépannage du Gestionnaire de connexion hybride, consultez la rubrique [Utilisation du Gestionnaire de connexion hybride].

## <a name="using-the-file-connector-in-your-logic-app"></a>Utilisation du connecteur File dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur File comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1. Créez une application logique et choisissez le groupe de ressources qui contient le connecteur File. Suivez les instructions indiquées dans la rubrique [Création d’une application logique].
2. Ouvrez « Déclencheurs et actions » dans l’application logique créée pour ouvrir le Concepteur d’applications logiques et configurez votre flux.
3. Le connecteur File s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.
4. Vous pouvez déposer l’application API du connecteur File dans l’éditeur en cliquant sur « Connecteur File ». Le connecteur File expose un déclencheur et 4 actions :   
   ![][5]
5. Chacun de ces éléments a des propriétés. L’image ci-dessous montre les propriétés du déclencheur et de l’action « Obtenir le fichier » :   
   ![][6]
6. Pour être utilisables dans votre flux, le déclencheur et l'action doivent être configurés. De même, d’autres actions peuvent être configurées.

> [!NOTE]
> Le déclencheur de fichier supprimera le fichier lorsque celui-ci sera correctement lu à partir du dossier.
> 
> 

## <a name="file-connector-rest-apis"></a>API REST du connecteur File
Pour utiliser le connecteur hors d’une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d’API, sélectionnez Parcourir -> Application API -> Connecteur File. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur :   
![][7]

Vous trouverez les détails de l’API dans la rubrique [Définition des API du connecteur File].

## <a name="do-more-with-your-connector"></a>En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure Logic Apps avant d’ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Création d’une application logique]: app-service-logic-create-a-logic-app.md
[File connector API definition]: https://msdn.microsoft.com/library/dn936296.aspx
[Utilisation du Gestionnaire de connexion hybride]: app-service-logic-hybrid-connection-manager.md



<!--HONumber=Nov16_HO3-->


