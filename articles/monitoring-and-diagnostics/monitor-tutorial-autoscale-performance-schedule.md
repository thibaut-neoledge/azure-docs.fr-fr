---
title: "Ressources de mise à l’échelle automatique Azure basées sur les données de performances ou une planification | Microsoft Docs"
description: "Créer un paramètre de mise à l’échelle automatique pour un plan App Service à l’aide de données de mesure et d’une planification"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 7e8d97657e03b0eaff76365d3988f51c773e3b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Créer un paramètre de mise à l’échelle automatique pour des ressources Azure basées sur les données de performances ou une planification

Les paramètres de mise à l’échelle automatique vous permettent d’ajouter ou de supprimer des instances de service en fonction de conditions prédéfinies. Ces paramètres peuvent être créés via le portail. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer un paramètre de mise à l’échelle automatique. Ce didacticiel décrit en détail les opérations suivantes :

1. Création d’un plan App Service
2. Configuration d’un paramètre de mise à l'échelle automatique
3. Déclenchement d’une action de montée en charge
4. Déclenchement d’une action de réduction de l’échelle

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Créer une application web et un plan App Service
1. Cliquez sur l’option **Nouveau** dans le volet de navigation gauche
2. Recherchez et sélectionnez l’élément *Application web* puis cliquez sur **Créer**
3. Nommez l’application, par exemple *MyTestScaleWebApp*. Créez un nouveau groupe de ressources *myResourceGroup' et placez-le dans le groupe de ressources de votre choix.
4. En quelques minutes, vos ressources devraient être configurées. Nous faisons référence à l’application web et au plan App Service correspondant qui viennent d’être créés dans les étapes restantes de ce didacticiel.

    ![Créer un plan App Service dans le portail](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Naviguer jusqu’aux paramètres de mise à l’échelle automatique
1. Dans le volet de navigation de gauche, sélectionnez l’option **Moniteur**. Une fois la page chargée, sélectionnez l’onglet **Mise à l’échelle automatique**.
2. Les ressources de votre abonnement qui prennent en charge la mise à l’échelle automatique sont répertoriées dans cette liste. Identifiez le plan App Service qui a été créé précédemment dans le didacticiel, puis cliquez dessus.

    ![Naviguer jusqu’aux paramètres de mise à l’échelle automatique](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Dans le paramètre de mise à l’échelle automatique, cliquez sur le bouton **Activer la mise à l’échelle automatique**

Les quelques étapes suivantes vous aident à compléter l’écran de mise à l’échelle automatique afin qu’il ressemble à l’image suivante :

   ![Enregistrer un paramètre de mise à l'échelle automatique](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Configurer un profil par défaut
1. Fournir un **nom** pour le paramètre de mise à l’échelle automatique
2. Dans le profil par défaut, vérifiez que le **mode de mise à l’échelle** est défini sur 'Mettre à l'échelle à un nombre d'instances spécifique'
3. Définissez le nombre d’instances sur 1. Ce paramètre garantit que si aucun autre profil n’est actif ou en vigueur, le profil par défaut utilise un nombre d’instances de 1.

  ![Naviguer jusqu’aux paramètres de mise à l’échelle automatique](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Créer un profil de récurrence

1. Cliquez sur le lien **Ajouter une condition de mise à l’échelle**, situé sous le profil par défaut

2. Remplacez le **nom** de ce profil par 'Profil Lundi à vendredi'

3. Vérifiez que le **mode de mise à l’échelle** est défini sir 'Mettre à l'échelle selon une mesure'

4. Pour **Limites d’instance**, définissez la valeur **minimale** sur '1', la valeur **maximale** sur '2' et la **valeur par défaut** sur '1'. Cette opération garantit que ce profil ne met pas automatiquement à l’échelle le plan de service pour qu’il contienne moins de 1 instance ou plus de 2 instances. Si le profil n’a pas suffisamment de données pour prendre une décision, il utilise le nombre d’instances par défaut (dans ce cas, 1).

5. Pour **Planification**, sélectionnez 'Répéter des jours spécifiques'

6. Définissez le profil afin qu’il se répète du lundi au vendredi, de 9h00 PST à 18h00 PST. Cela garantit que ce profil s’applique uniquement de 9h00 à 18h00, du lundi au vendredi. À tous les autres moments, le profil 'Par défaut' est le profil utilisé par le paramètre de mise à l’échelle automatique.

## <a name="create-a-scale-out-rule"></a>Créer une règle de montée en charge

1. Dans le 'Profil Lundi à vendredi'

2. Cliquez sur le lien **Ajouter une règle**

3. Définissez **Source de la mesure** sur 'autre ressource'. Définissez le **type de ressource** sur ’App Services’ et la **ressource** sur l’application web créée précédemment dans ce didacticiel.

4. Définissez l’**agrégation du temps** sur 'Total', le **nom de la mesure** sur 'requêtes’ et la **statistique de fragment de temps** sur 'Somme'

5. Définissez l’**opérateur** sur 'Supérieur à', le **seuil** sur '10' et la **durée** sur '5 minutes'.

6. Définissez l’**opération** sur 'Augmenter le nombre de', le **nombre d’instances** sur '1' et le **délai d’attente** sur '5 minutes'

7. Cliquez sur le bouton **Ajouter**

Cette règle garantit que si votre application web reçoit plus de 10 requêtes dans un délai de 5 minutes ou moins, une instance supplémentaire est ajoutée à votre plan App Service pour gérer la charge.

   ![Créer une règle de montée en charge](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Créer une règle de diminution de la taille
Nous vous recommandons de toujours associer une règle de diminution de la taille à une règle de montée en charge. Ces deux règles garantissent que vos ressources ne sont pas surapprovisionnées. Un surapprovisionnement signifie que vous avez plus d’instances en cours d’exécution que nécessaires pour gérer la charge actuelle. 

1. Dans le 'Profil Lundi à vendredi'

2. Cliquez sur le lien **Ajouter une règle**

3. Définissez **Source de la mesure** sur 'autre ressource'. Définissez le **type de ressource** sur ’App Services’ et la **ressource** sur l’application web créée précédemment dans ce didacticiel.

4. Définissez l’**agrégation du temps** sur 'Total', le **nom de la mesure** sur 'requêtes’ et la **statistique de fragment de temps** sur 'Moyenne'

5. Définissez l’**opérateur** sur 'Inférieur à', le **seuil** sur '5' et la **durée** sur '5 minutes'.

6. Définissez l’**opération** sur 'Diminuer le nombre de', le **nombre d’instances** sur '1' et le **délai d’attente** sur '5 minutes'

7. Cliquez sur le bouton **Ajouter**

    ![Créer une règle de diminution de la taille](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Enregistrer** le paramètre de mise à l'échelle automatique

    ![Enregistrer un paramètre de mise à l'échelle automatique](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Déclencher une action de montée en charge
Pour déclencher la condition de montée en charge dans le paramètre de mise à l’échelle automatique que nous venons de créer, l’application web doit recevoir plus de 10 requêtes en moins de 5 minutes.

1. Ouvrez une fenêtre de navigateur et accédez à l’application web créée précédemment dans ce didacticiel. Vous trouverez l’URL de votre application web dans le portail Azure en accédant aux ressources de votre application web, puis en cliquant sur le bouton **Parcourir** de l’onglet 'Vue d’ensemble'.

2. Rechargez rapidement la page plus de 10 fois

3. Dans le volet de navigation de gauche, sélectionnez l’option **Moniteur**. Une fois la page chargée, sélectionnez l’onglet **Mise à l’échelle automatique**.

4. Dans la liste, sélectionnez le plan App Service utilisé tout au long de ce didacticiel

5. Dans le paramètre de mise à l’échelle automatique, cliquez sur l’onglet **Historique des exécutions**

6. Un graphique indique le nombre d’instances du plan App Service au fil du temps

7. En quelques minutes, le nombre d’instances devrait augmenter de 1 à 2.

8. Sous le graphique, vous pouvez visualiser les entrées du journal d’activité pour chaque action de mise à l’échelle effectuée par ce paramètre de mise à l’échelle automatique.

## <a name="trigger-scale-in-action"></a>Déclencher une action de diminution de la taille
La condition de diminution de la taille du paramètre de mise à l’échelle automatique se déclenche si moins de 5 requêtes sont envoyées à l’application web sur une période de 10 minutes. 

1. Vérifiez qu'aucune requête n’est envoyée à votre application web

2. Charger le portail Azure

3. Dans le volet de navigation de gauche, sélectionnez l’option **Moniteur**. Une fois la page chargée, sélectionnez l’onglet **Mise à l’échelle automatique**.

4. Dans la liste, sélectionnez le plan App Service utilisé tout au long de ce didacticiel

5. Dans le paramètre de mise à l’échelle automatique, cliquez sur l’onglet **Historique des exécutions**

6. Un graphique indique le nombre d’instances du plan App Service au fil du temps.

7. En quelques minutes, le nombre d’instances devrait diminuer de 2 à 1. Le processus dure au moins dix minutes.  

8. Sous le graphique, vous pouvez visualiser le jeu correspondant d’entrées du journal d’activité pour chaque action de mise à l’échelle effectuée par ce paramètre de mise à l’échelle automatique

    ![Afficher les actions de diminution de la taille](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez l’application web créée dans ce didacticiel.

2. Sur la page de votre ressource, cliquez sur **Supprimer**, confirmez la suppression en tapant **yes** dans la zone de texte, puis cliquez sur **Supprimer**.

3. Sélectionnez ensuite la ressource Plan App Service, puis cliquez sur **Supprimer**

4. Confirmez la suppression en tapant **yes** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application web simple et un plan App Service. Vous avez ensuite créé un paramètre de mise à l’échelle automatique qui fait évoluer le plan App Service en fonction du nombre de requêtes reçues par l’application web. Pour en savoir plus sur les paramètres de mise à l’échelle automatique, passez à la vue d’ensemble de la mise à l’échelle automatique.

> [!div class="nextstepaction"]
> [Archiver vos données de monitorage](./monitor-tutorial-archive-monitoring-data.md)
