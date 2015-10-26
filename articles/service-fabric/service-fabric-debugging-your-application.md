<properties
   pageTitle="Débogage de votre application Service Fabric dans Visual Studio à l'aide de F5"
   description="Améliorez la fiabilité et les performances de vos services à l'aide de Visual Studio et d'un cluster de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2015"
   ms.author="jesseb"/>

# Débogage de votre application Service Fabric dans Visual Studio à l'aide de F5

Vous pouvez économiser du temps et de l'argent en déployant et déboguant votre application Service Fabric dans un cluster de développement d'ordinateur local. Visual Studio peut déployer l'application sur le cluster local et connecter automatiquement le débogueur à toutes les instances de votre application.

1. Démarrez un cluster de développement local en suivant les étapes de la section [Configuration de votre environnement de développement Service Fabric](service-fabric-get-started.md).

2. Appuyez sur **F5** ou cliquez sur **Déboguer** > **Démarrer le débogage**

    ![Démarrer le débogage d'une application][startdebugging]

3. Définissez des points d'arrêt dans votre code et parcourez l'application en cliquant sur les commandes du menu **Déboguer**.

    > [AZURE.NOTE]Visual Studio s'attache à toutes les instances de votre application. Pendant le parcours du code, les points d'arrêt peuvent être visités par plusieurs processus résultant de sessions simultanées. Essayez de désactiver le ou les points d’arrêt une fois qu’ils ont été atteints : définissez le point d’arrêt comme étant conditionnel sur l’ID de thread ou utilisant Événements de diagnostic.

4. La fenêtre **Événements de diagnostic** s'ouvre automatiquement pour afficher les événements de diagnostic en temps réel.

    ![Afficher les événements de diagnostic en temps réel][diagnosticevents]

5. Vous pouvez également ouvrir la fenêtre **Événements de diagnostic** dans l'Explorateur de serveurs. Sous **Azure**, cliquez avec le bouton droit sur **Cluster Service Fabric** > **Afficher les événements de diagnostic...**

    ![Ouvrir la fenêtre des événements de diagnostic][viewdiagnosticevents]

6. Les événements de diagnostics peuvent être consultés dans le fichier **ServiceEventSource.cs**, généré automatiquement, et sont appelés à partir du code d'application.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. La fenêtre **Événements de diagnostic** prend en charge le filtrage, la suspension et l'inspection des événements en temps réel. Le filtre est une simple recherche de chaîne du message d'événement, y compris son contenu.

    ![Filtrer, suspendre et reprendre ou examiner des événements en temps réel][diagnosticeventsactions]

8. Les services de débogage ont la même fonction que le débogage de toute autre application. Les points d'arrêt peuvent être définis normalement via Visual Studio pour faciliter le débogage. Bien que les collections fiables soient répliquées sur plusieurs nœuds, elles implémentent toujours IEnumerable, ce qui signifie que vous pouvez utiliser l'affichage des résultats dans Visual Studio pendant le débogage pour voir ce que vous avez stocké à l'intérieur. Définissez simplement un point d'arrêt n'importe où dans votre code.

    ![Démarrer le débogage d'une application][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

- [Tester un service Service Fabric](service-fabric-test-your-service-index.md).
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=Oct15_HO3-->