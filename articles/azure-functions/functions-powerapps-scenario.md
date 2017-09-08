---
title: Appeler une fonction dans PowerApps | Microsoft Docs
description: "Créez un connecteur personnalisé, puis appelez une fonction à l’aide du connecteur."
services: functions
keywords: "applications cloud, services cloud, PowerApps, processus métier, application métier"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 89d9fa8f11a4c6ae3860b91e246716aad071870f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---

# <a name="call-a-function-from-powerapps"></a>Appeler une fonction dans PowerApps
La plateforme [PowerApps](https://powerapps.microsoft.com) est conçue pour les experts d’entreprise qui souhaitent créer des applications sans code d’application traditionnel. Les développeurs professionnels peuvent utiliser Azure Functions pour étendre les fonctionnalités de PowerApps, tout en évitant les détails techniques liés à la création d’applications PowerApps.

Dans cette rubrique, vous allez créer une application en suivant un scénario de maintenance d’éoliennes. Cette rubrique vous montre comment appeler la fonction que vous avez définie dans [Créer une définition OpenAPI pour une fonction](functions-openapi-definition.md). La fonction détermine si la réparation d’urgence d’une éolienne est rentable.

![Application terminée dans PowerApps](media/functions-powerapps-scenario/finished-app.png)

Dans cette rubrique, vous allez apprendre à :

> [!div class="checklist"]
> * Préparer l’exemple de données dans Excel
> * Exporter une définition d’API
> * Ajouter une connexion à l’API
> * Créer une application et ajouter des sources de données
> * Ajouter des contrôles pour afficher les données dans l’application
> * Ajouter des contrôles pour appeler la fonction et afficher les données
> * Exécuter l’application pour déterminer si une réparation est rentable

## <a name="prerequisites"></a>Composants requis

+ Un [compte PowerApps](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md) actif avec les mêmes informations d’identification que votre compte Azure. 
+ Excel, car vous allez l’utiliser comme source de données de votre application.
+ Avoir suivi le didacticiel [Créer une définition OpenAPI pour une fonction](functions-openapi-definition.md)


## <a name="prepare-sample-data-in-excel"></a>Préparer l’exemple de données dans Excel
Vous allez commencer par préparer l’exemple de données à utiliser dans l’application. Copiez le tableau suivant dans Excel. 

| Intitulé      | Latitude  | Longtitude  | Dernier entretien | Production max. | Entretien nécessaire | Effort estimé | Notes inspection                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| Éolienne 1  | 47.438401 | -121.383767 | 23/02/2017       | 2 850      | Oui             | 6               | Deuxième problème à se produire ce mois-ci.       |
| Éolienne 4  | 47,433385 | -121,383767 | 08/05/2017        | 5400      | Oui             | 6               |                                            |
| Éolienne 33 | 47,428229 | -121,404641 | 20/06/2017       | 2 800      |                 |                 |                                            |
| Éolienne 34 | 47,463637 | -121,358824 | 19/02/2017       | 2 800      | Oui             | 7               |                                            |
| Éolienne 46 | 47,471993 | -121,298949 | 02/03/2017        | 1 200      |                 |                 |                                            |
| Éolienne 47 | 47,484059 | -121,311171 | 02/08/2016        | 3 350      |                 |                 |                                            |
| Éolienne 55 | 47,438403 | -121,383767 | 02/10/2016       | 2 400      | Oui             | 40               | Des pièces sont en cours d’acheminement pour celle-ci. |

1. Dans Excel, sélectionnez les données, puis, sous l’onglet **Accueil**, cliquez sur **Mettre sous forme de tableau**.

    ![Mettre sous forme de tableau](media/functions-powerapps-scenario/format-table.png)

1. Sélectionnez un style, puis cliquez sur **OK**.

1. Sélectionnez la table, puis, sous l’onglet **Création**, entrez `Turbines` comme **Nom de la table**.

    ![Nom de la table](media/functions-powerapps-scenario/table-name.png)

1. Enregistrez le classeur Excel.

## <a name="export-an-api-definition"></a>Exporter une définition d’API
Vous disposez déjà d’une définition OpenAPI pour votre fonction. Il s’agit de celle que vous avez créée dans la rubrique [Créer une définition OpenAPI pour une fonction](functions-openapi-definition.md). L’étape suivante de ce processus consiste à exporter la définition d’API pour que PowerApps et Microsoft Flow puissent l’utiliser dans une API personnalisée.

> [!IMPORTANT]
> N’oubliez pas que vous devez vous connecter à Azure avec les mêmes informations d’identification que celles utilisées pour les locataires PowerApps et Microsoft Flow. Cela permet à Azure de créer l’API personnalisée et de la rendre disponible pour PowerApps et Microsoft Flow.

1. Cliquez sur le nom de l’application de fonction (par exemple, **function-demo-energy**) > **Fonctionnalités de la plateforme** > **Définition de l’API**.

    ![Définition de l’API](media/functions-powerapps-scenario/api-definition.png)

1. Cliquez sur **Exporter vers PowerApps + Flow**.

    ![Source de la définition de l’API](media/functions-powerapps-scenario/export-api-1.png)

1. Dans le volet droit, utilisez les paramètres spécifiés dans le tableau.

    |Paramètre|Description|
    |--------|------------|
    |**Mode d’exportation**|Sélectionnez **Express** pour générer automatiquement l’API personnalisée. Quand vous sélectionnez l’option **Manuel**, la définition de l’API est exportée, mais vous devez ensuite l’importer dans PowerApps et Microsoft Flow manuellement.|
    |**Environment**|Sélectionnez l’environnement dans lequel l’API personnalisée doit être enregistrée. Pour plus d’informations, consultez [Présentation des environnements](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nom de l’API personnalisée**|Entrez un nom tel que `Turbine Repair`.|
    |**Nom de la clé API**|Entrez le nom que les concepteurs d’applications et de flux doivent voir dans l’interface utilisateur de l’API personnalisée. Notez que l’exemple comprend des informations utiles.|
 
    ![Source de la définition de l’API](media/functions-powerapps-scenario/export-api-2.png)

1. Cliquez sur **OK**. L’API personnalisée est maintenant créée et ajoutée à l’environnement que vous avez spécifié.

## <a name="add-a-connection-to-the-api"></a>Ajouter une connexion à l’API
L’API personnalisée (également appelée « connecteur personnalisé ») est disponible dans PowerApps. Toutefois, vous devez établir une connexion avec l’API avant de pouvoir l’utiliser dans une application.

1. Dans [web.powerapps.com](https://web.powerapps.com), cliquez sur **Connexions**.

    ![Connexions PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Cliquez sur **Nouvelle connexion**, faites défiler jusqu’au connecteur **Turbine Repair** (Réparation d’éolienne), puis cliquez dessus.

    ![Nouvelle connexion](media/functions-powerapps-scenario/new-connection.png)

1. Entrez la clé API, puis cliquez sur **Créer**.

    ![Créer une connexion](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Si vous partagez votre application avec d’autres utilisateurs, chaque personne qui collabore à la création de l’application ou qui l’utilise doit également entrer la clé API pour se connecter à l’API. Ce comportement est susceptible d’être modifié, auquel cas cette rubrique sera mise à jour.

## <a name="create-an-app-and-add-data-sources"></a>Créer une application et ajouter des sources de données
Vous êtes maintenant prêt à créer l’application dans PowerApps, et à ajouter les données Excel et l’API personnalisée comme sources de données de l’application.

1. Dans [web.powerapps.com](https://web.powerapps.com), dans le volet gauche, cliquez sur **Nouvelle application**.

1. Sous **Application vide**, cliquez sur **Mode téléphone**.

    ![Créer l’application de tablette](media/functions-powerapps-scenario/create-phone-app.png)

    L’application s’ouvre dans PowerApps Studio pour le web. L’image suivante montre les différentes parties de PowerApps Studio. Cette image correspond à l’application une fois terminée. Dans un premier temps, vous allez voir un écran vide dans le volet central.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) Barre de navigation gauche**, dans laquelle vous voyez une vue hiérarchique de tous les contrôles de chaque écran.

    **(2) Volet central**, où s’affiche l’écran dans lequel vous travaillez actuellement.

    **(3) Volet droit**, dans lequel vous définissez des options telles que le mode et les sources de données.

    **(4) Liste déroulante Propriété**, dans laquelle vous sélectionnez les propriétés auxquelles s’appliquent les formules.

    **(5) Barre de formule**, dans laquelle vous ajoutez des formules qui définissent le comportement de l’application (comme dans Excel).
    
    **(6) Ruban**, dans lequel vous ajoutez des contrôles et personnalisez les éléments de conception.

1. Ajoutez le fichier Excel en tant que source de données.

    1. Dans le volet droit, sous l’onglet **Données**, cliquez sur **Ajouter une source de données**.

        ![Ajouter une source de données](media/functions-powerapps-scenario/add-data-source.png)

    1. Cliquez sur **Ajouter des données statiques à votre application**.

        ![Ajouter une source de données](media/functions-powerapps-scenario/add-static-data.png)

        Normalement, vous lisez et écrivez des données dans une source externe. Mais ici, vous ajoutez les données Excel en tant que données statiques, car il s’agit d’un exemple.

    1. Accédez au fichier Excel que vous avez enregistré, sélectionnez le tableau **Turbines** (Éoliennes), puis cliquez sur **Se connecter**.

        ![Ajouter une source de données](media/functions-powerapps-scenario/choose-table.png)

1. Ajoutez l’API personnalisée en tant que source de données.

    1. Sous l’onglet **Données**, cliquez sur **Ajouter une source de données**.

    1. Cliquez sur **Turbine Repair** (Réparation d’éolienne).

        ![Connecteur Turbine Repair](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Ajouter des contrôles pour afficher les données dans l’application
Maintenant que les sources de données sont disponibles dans l’application, ajoutez un écran à votre application afin de consulter les données relatives aux éoliennes.

1. Dans l’onglet **Accueil**, cliquez sur **Nouvel écran** > **Écran Liste**.

    ![Écran Liste](media/functions-powerapps-scenario/list-screen.png)

    PowerApps ajoute un écran qui contient une *galerie* pour afficher des éléments et d’autres contrôles permettant de rechercher, trier et filtrer les éléments.

1. Remplacez le titre de la barre de titre par `Turbine Repair`, puis redimensionnez la galerie pour afficher plus de contrôles.

    ![Modifier le titre et redimensionner la galerie](media/functions-powerapps-scenario/gallery-title.png)

1. Sélectionnez la bibliothèque, puis, dans le volet droit, sous l’onglet **Données**, remplacez la source de données **CustomGallerySample** par **Turbines**.

    ![Modifier la source de données](media/functions-powerapps-scenario/change-data-source.png)

    Le jeu de données ne contient pas d’image. Vous devez donc modifier la disposition pour l’adapter aux données. 

1. Toujours dans le volet droit, remplacez la **Disposition** actuelle par **Titre, sous-titre et corps**.

    ![Modifier la disposition de la galerie](media/functions-powerapps-scenario/change-layout.png)

1. Enfin, dans le volet droit, modifiez les champs qui sont affichés dans la galerie.

    ![Modifier les champs de la galerie](media/functions-powerapps-scenario/change-fields.png)
    
    + **Corps1** = Dernier entretien
    + **Sous-titre2** = Entretien nécessaire
    + **Titre2** = Titre 

1. Sélectionnez la galerie, puis définissez la propriété **TemplateFill** sur la formule suivante : `If(ThisItem.IsSelected, Orange, White)`.

    ![Formule TemplateFill](media/functions-powerapps-scenario/formula-fill.png)

    Il est désormais plus facile de déterminer quel élément de la galerie est sélectionné.

    ![Élément sélectionné](media/functions-powerapps-scenario/selected-item.png)

1. Vous n’avez pas besoin de l’écran d’origine dans l’application. Dans le volet gauche, pointez sur **Écran1**, cliquez sur **...**, puis sur **Supprimer**.

    ![Supprimer l’écran](media/functions-powerapps-scenario/delete-screen.png)

Il existe beaucoup d’autres mises en forme que vous pouvez utiliser dans une application de production. Toutefois, nous allons passer directement à l’essentiel de ce scénario, c’est-à-dire l’appel de la fonction.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Ajouter des contrôles pour appeler la fonction et afficher les données
Vous disposez d’une application qui affiche des données récapitulatives pour chaque éolienne. Vous pouvez maintenant ajouter des contrôles qui appellent la fonction que vous avez créée et d’afficher les données qui sont retournées. L’accès à la fonction dépend de la manière dont vous l’avez nommée dans la définition OpenAPI. Dans ce cas, il s’agit de `TurbineRepair.CalculateCosts()`.

1. Dans le ruban, sous l’onglet **Insérer**, cliquez sur **Bouton**. Sous le même onglet, cliquez sur **Étiquette**

    ![Insérer un bouton et une étiquette](media/functions-powerapps-scenario/insert-controls.png)

1. Faites glisser le bouton et l’étiquette sous la galerie, puis redimensionnez l’étiquette. 

1. Sélectionnez le texte du bouton et remplacez-le par `Calculate costs`. L’application doit ressembler à l’image suivante :

    ![Application avec bouton](media/functions-powerapps-scenario/move-button-label.png)

1. Sélectionnez le bouton, puis entrez la formule suivante pour la propriété **OnSelect** du bouton.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Cette formule s’exécute lorsque l’utilisateur clique sur le bouton. Si l’élément de galerie sélectionné a une valeur de `Yes` pour **ServiceRequired**, la formule effectue ce qui suit :

    + Efface la *collection* `DetermineRepair` pour supprimer les données des appels précédents. Une collection est une variable tabulaire.

    + Affecte à la collection les données retournées par l’appel de la fonction `TurbineRepair.CalculateCosts()`. 
    
        Les valeurs passées à la fonction proviennent des champs **EstimatedEffort** et **MaxOutput** pour l’élément sélectionné dans la galerie. Ces champs n’apparaissent pas dans la galerie, mais ils peuvent toujours être utilisés dans les formules.

1. Sélectionnez l’étiquette, puis entrez la formule suivante pour la propriété **Text** de l’étiquette.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Cette formule utilise la fonction `First()` pour accéder à la première (et unique) ligne de la collection `DetermineRepair`. Elle affiche ensuite les trois valeurs retournées par la fonction : `message`, `costToFix` et `revenueOpportunity`. Ces valeurs sont vides jusqu’à la première exécution de l’application.

    L’application terminée doit ressembler à l’image suivante :

    ![Application terminée avant la première exécution](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Exécution de l'application
Votre application est terminée ! Il est temps de l’exécuter et de voir les appels de fonction à l’action.

1. Dans le coin supérieur droit de PowerApps Studio, cliquez sur le bouton d’exécution : ![Bouton d’exécution de l’application](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Sélectionnez une éolienne avec une valeur de `Yes` pour **ServiceRequired**, puis cliquez sur le bouton **Calculate costs** (Calculer les coûts). Vous devez obtenir un résultat similaire à celui de l’image suivante :

    ![Application terminée dans PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Essayez les autres éoliennes pour voir ce qui est retourné par la fonction.

## <a name="next-steps"></a>Étapes suivantes
Dans cette rubrique, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer l’exemple de données dans Excel
> * Exporter une définition d’API
> * Ajouter une connexion à l’API
> * Créer une application et ajouter des sources de données
> * Ajouter des contrôles pour afficher les données dans l’application
> * Ajouter des contrôles pour appeler la fonction et afficher les données
> * Exécuter l’application pour déterminer si une réparation est rentable

Pour en savoir plus sur PowerApps, consultez [Présentation de PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Pour connaître un autre scénario intéressant qui utilise Azure Functions, consultez [Créer une fonction qui s’intègre avec Azure Logic Apps](functions-twitter-email.md).
