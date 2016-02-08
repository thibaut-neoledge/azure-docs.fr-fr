<properties
    pageTitle="Effectuer un test de charge de votre application à l’aide de Visual Studio Team Services | Microsoft Azure"
    description="Apprenez à appliquer un test de contrainte à vos applications Azure Service Fabric à l’aide de Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Effectuez un test de charge de votre application avec Visual Studio Team Services

Cet article montre comment utiliser les fonctionnalités de test de charge de Microsoft Visual Studio pour soumettre une application à un test de contrainte. Il utilise un serveur principal de service avec état Azure Service Fabric et un serveur frontal web de service sans état. L’exemple d’application utilisé ici est un simulateur de localisation d’avions. Vous indiquez l’ID de l’avion, l’heure de départ et la destination. Le serveur principal de l’application traite les demandes, et le serveur frontal affiche l’avion correspondant aux critères sur une carte.

Le diagramme suivant illustre l’application Service Fabric que vous allez tester.

![Diagramme de l’exemple d’application de localisation d’avions][0]

## Composants requis
Avant de commencer, vous devez effectuer les opérations suivantes :

- Obtenir un compte Visual Studio Team Services. Vous pouvez en obtenir un gratuitement sur [Visual Studio Team Services](https://www.visualstudio.com).
- Obtenir et installer Visual Studio 2013 ou Visual Studio 2015. Cet article utilise Visual Studio 2015 Enterprise, mais Visual Studio 2013 et autres éditions fonctionnent de la même façon.
- Déployer votre application dans un environnement intermédiaire. Consultez la page [Publier une application sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md) pour plus d’informations à ce sujet.
- Comprendre le modèle d’utilisation de votre application. Cette information sert à simuler le modèle de charge.
- Comprendre l’objectif de votre test de charge. Ceci vous permet d’interpréter et d’analyser les résultats du test de charge.

## Créer et exécuter le projet de test de performance web et de charge

### Créer un projet de test de performance web et de charge

1. Ouvrez Visual Studio 2015. Choisissez **Fichier** > **Nouveau** > **Projet** dans la barre de menus pour ouvrir la boîte de dialogue **Nouveau projet**.

2. Développez le nœud **Visual C#** et choisissez **Test** > **Projet de test de performance web et de charge**. Donnez un nom au projet, puis cliquez sur le bouton **OK**.

    ![Capture d’écran de la boîte de dialogue Nouveau projet][1]

    Vous devriez voir un nouveau projet de test de performance web et de charge dans l’Explorateur de solutions.

    ![Capture d’écran de l’Explorateur de solutions affichant le nouveau projet][2]

### Enregistrer un test de performances web

1. Ouvrez le projet .webtest.

2. Cliquez sur l’icône **Ajouter un enregistrement** pour démarrer une session d’enregistrement dans votre navigateur.

    ![Capture d’écran de l’icône Ajouter un enregistrement dans un navigateur][3]

    ![Capture d’écran du bouton Enregistrer dans un navigateur][4]

3. Accédez à l’application Service Fabric. Le panneau d’enregistrement doit afficher les requêtes web.

    ![Capture d’écran de requêtes web dans le panneau d’enregistrement][5]

4. Effectuez une série d’actions qui, selon vous, seront réalisées par les utilisateurs. Ces actions sont utilisées comme modèle pour générer la charge.

5. Lorsque vous avez terminé, cliquez sur le bouton **Arrêter** pour arrêter l’enregistrement.

    ![Capture d’écran du bouton Arrêter][6]

    Le projet .webtest dans Visual Studio doit avoir capturé une série de requêtes. Les paramètres dynamiques sont automatiquement remplacés. À ce stade, vous pouvez supprimer toutes les demandes de dépendance supplémentaires et répétées qui ne font pas partie de votre scénario de test.

6. Enregistrez le projet, puis utilisez la commande **Exécuter le test** pour exécuter le test de performances web localement et vérifier que tout fonctionne correctement.

    ![Capture d’écran de la commande Exécuter le test][7]

### Paramétrer le test de performances web

Vous pouvez paramétrer le test de performances web en le convertissant en un test codé de performances web, puis en modifiant le code. Vous pouvez aussi lier le test de performances web à une liste de données afin que le test effectue une itération dans les données. Pour plus d’informations sur la conversion du test de performances web en test codé, consultez [Générer et exécuter un test de performances web codé](https://msdn.microsoft.com/library/ms182552.aspx). Pour plus d’informations sur la liaison de données à un test de performances web, consultez [Ajouter une source de données à un test de performances web](https://msdn.microsoft.com/library/ms243142.aspx).

Pour cet exemple, nous allons convertir le test de performances web en test codé afin de remplacer l’ID de l’avion par un GUID généré et ajouter davantage de requêtes pour envoyer les vols vers différentes destinations.

### Créer un projet de test de charge

Un projet de test de charge se compose d’un ou plusieurs scénarios décrits par le test de performances web et le test unitaire, ainsi que les paramètres supplémentaires de test de charge spécifiés. Les étapes suivantes montrent comment créer un projet de test de charge :

1. Dans le menu contextuel de votre projet de test de performances web et de charge, choisissez **Ajouter** > **Test de charge**. Dans l’Assistant **Test de charge**, cliquez sur le bouton **Suivant** pour configurer les paramètres du test.

2. Dans la section **Modèle de charge**, choisissez si vous souhaitez une charge utilisateur constante ou une charge par étape, qui commence par quelques utilisateurs et augmente leur nombre au fur et à mesure.

    Si vous avez une estimation fiable de la charge utilisateur et que vous voulez voir le fonctionnement du système actuel, choisissez **Charge constante**. Si votre objectif est de voir comment le système fonctionne avec des charges différentes, choisissez **Charge dans l’étape**.

3. Dans la section **Combinaison de tests**, cliquez sur le bouton **Ajouter**, puis sélectionnez le test que vous souhaitez ajouter au test de charge. Vous pouvez utiliser la colonne **Distribution** pour spécifier le pourcentage du nombre total de tests exécutés pour chaque test.

4. Dans la section **Paramètres d’exécution**, spécifiez la durée du test de charge.

    >[AZURE.NOTE] L’option **Itérations de tests** est disponible uniquement quand vous exécutez un test de charge localement à l’aide de Visual Studio.

5. Dans la section **Emplacement** de **Paramètres d’exécution**, spécifiez l’emplacement où les requêtes de tests de charge sont générées. L’assistant peut vous inviter à vous connecter à votre compte Team Services. Connectez-vous, puis choisissez un emplacement géographique. Quand vous avez terminé, cliquez sur le bouton **Terminé**.

6. Une fois le test de charge créé, ouvrez le projet .loadtest et choisissez les paramètres d’exécution actuels, par exemple **Paramètres d’exécution** > **Paramètres d’exécution1 [active]**. Cette opération ouvre les paramètres d’exécution dans la fenêtre **Propriétés**.

7. Dans la section **Résultats** de la fenêtre de propriétés de **paramètres d’exécution**, le paramètre **Stockage des détails de minuterie** doit avoir la valeur par défaut **Aucun**. Remplacez cette valeur par **Tous les détails individuels** pour obtenir plus d’informations sur le résultat du test de charge. Consultez la page [Test de charge](https://www.visualstudio.com/load-testing.aspx) pour plus d’informations sur la connexion à Visual Studio Team Services et l’exécution d’un test de charge.

### Exécuter le test de charge à l’aide de Visual Studio Team Services

Utilisez la commande **Exécuter le test de charge** pour démarrer les tests.

![Capture d’écran de la commande Exécuter le test de charge][8]

## Afficher et analyser les résultats du test de charge

À mesure que le test de charge progresse, le graphique affiche les informations sur les performances. Vous devez voir un graphique similaire à ce qui suit.

![Capture d’écran du graphique de performances pour les résultats des tests de charge][9]

1. Cliquez sur le lien **Télécharger le rapport** en haut de la page. Une fois le rapport téléchargé, cliquez sur le bouton **Afficher le rapport**.

    Sous l’onglet **Graphique**, vous pouvez voir les graphiques des différents compteurs de performances. Sous l’onglet **Résumé**, les résultats du test s’affichent. L’onglet **Tableaux** affiche le nombre total de tests de charge ayant réussi et échoué.

2. Cliquez sur les liens avec les numéros dans les colonnes **Test** > **Échec** et les colonnes **Erreurs** > **Nombre** pour afficher les détails de l’erreur.

    L’onglet **Détail** affiche les informations sur l’utilisateur virtuel et sur le scénario de test pour les demandes ayant échoué. Ces données peuvent être utiles si le test de charge regroupe plusieurs scénarios.

Pour plus d’informations sur l’affichage des résultats des tests de charge, consultez [Analyse des résultats des tests de charge dans la vue Graphiques de l’analyseur de test de charge](https://www.visualstudio.com/load-testing.aspx).

## Automatiser votre test de charge

Les tests de charge de Visual Studio Team Services proposent des API vous permettant de gérer les tests de charge et d’analyser les résultats dans un compte Team Services. Consultez la page [API Rest de test de charge Cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) pour plus d’informations.

## Étapes suivantes
- [Surveillance et diagnostic des services dans une configuration de développement d’ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png

<!---HONumber=AcomDC_0128_2016-->