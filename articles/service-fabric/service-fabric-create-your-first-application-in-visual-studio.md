<properties
   pageTitle="Créer votre première application Service Fabric dans Visual Studio | Microsoft Azure"
   description="Créer, déployer et déboguer une application Service Fabric à l’aide de Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# Créer votre première application Service Fabric dans Visual Studio

Le kit de développement logiciel Fabric Service inclut un complément à Visual Studio proposant des outils et des modèles pour la création, le déploiement et le débogage d’applications Service Fabric. Cette rubrique vous guide tout au long du processus de création de votre première application dans Visual Studio.

## Composants requis

Avant de commencer, assurez-vous que vous avez [configuré votre environnement de développement](service-fabric-get-started.md).

## Création de l'application

Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. L’Assistant Nouveau projet vous permet de créer un projet d’application avec votre premier projet de service. Vous pouvez ajouter davantage de services plus tard.

1. Lancez Visual Studio en tant qu’administrateur.

2. Cliquez sur **Fichier > Nouveau projet > Cloud > Application Service Fabric**.

3. Nommez l’application, puis cliquez sur OK.

	![Boîte de dialogue Nouveau projet dans Visual Studio][1]

4. Dans la boîte de dialogue suivante, vous serez invité à choisir le premier type de service à inclure dans votre application. Pour les besoins de ce didacticiel, nous allons choisir « Service avec état ». Attribuez-lui un nom, puis cliquez sur OK.

	![Boîte de dialogue Nouveau service dans Visual Studio.][2]

	>[AZURE.NOTE]Pour plus d’informations sur les options, consultez [Choix d’une infrastructure](service-fabric-choose-framework.md).

	Visual Studio crée le projet d’application et le projet de service avec état et les affiche dans l’Explorateur de solutions.

	![Explorateur de solutions après la création de l’application de service avec état][3]

	Le projet d’application ne contient pas de code directement. Au lieu de cela, il fait référence à un ensemble de projets de service. En outre, il contient trois autres types de contenu :

	- **Profils de publication** : permet de gérer les préférences d’outils pour différents environnements.

	- **Scripts** : script PowerShell de déploiement/mise à niveau de votre application. Ce script est utilisé par Visual Studio en arrière-plan et peut être appelé directement à la ligne de commande.

	- **Définition d’application** : le manifeste d’application et les fichiers de paramètres d’application associés définissent l’application et vous permettent de la configurer spécifiquement un environnement donné.

    Pour avoir une vue d’ensemble du contenu du projet de service, consultez [prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## Déployer et déboguer l’application.

Maintenant que vous disposez d’une application, essayez de l’exécuter.

1. Appuyez sur F5 dans Visual Studio pour déployer l’application en vue d’un débogage.

	>[AZURE.NOTE]Cela prend un certain temps la première fois que Visual Studio crée un cluster local pour le développement. Un cluster local exécute un code de plateforme équivalent à celui qui serait généré dans un cluster regroupant plusieurs machines sur un seul ordinateur. Vous verrez l’état de la création de clusters dans la fenêtre Sortie de Visual Studio.

	Une fois le cluster prêt, vous obtiendrez une notification de la part de l’application de gestionnaire de la barre d’état système de cluster local incluse avec le kit de développement logiciel.

	![Notification de barre d’état système de cluster local][4]

2. Une fois que l’application a démarré, Visual Studio affiche automatiquement l’Observateur d’événements de diagnostic, où vous pouvez voir la sortie de suivi depuis le service.

	![Observateur d’événements de diagnostic][5]

	Dans le cas du modèle de service avec état, les messages indiquent simplement la valeur du compteur incrémentée dans la méthode `RunAsync` de MyStatefulService.cs.

3. Développez un des événements pour obtenir plus de détails, y compris le nœud sur lequel le code s’exécute, dans ce cas, le nœud 2, bien qu’il puisse être différent sur votre machine.

	![Détails de l’Observateur d’événements de diagnostic][6]

	Le cluster local est constitué de cinq nœuds hébergés sur un ordinateur unique, qui imite un cluster à cinq nœuds dont les nœuds se trouvent sur des ordinateurs distincts. Examinons un des nœuds du cluster local pour simuler la perte d’un ordinateur et exercer le débogueur Visual Studio en même temps.

    >[AZURE.NOTE]Les événements de diagnostic d’application émis par le modèle de projet utilisent la classe `ServiceEventSource` incluse. Pour plus d’informations, voir [Comment analyser et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

4. Recherchez dans votre projet de service la classe qui dérive de StatefulService (par ex. MyStatefulService) et définissez un point d’arrêt sur la première ligne de la méthode `RunAsync`.

	![Point d’arrêt dans la méthode RunAsync de service avec état][7]

5. Cliquez avec le bouton droit de la souris sur l’application de barre d’état système du gestionnaire du cluster local et choisissez Gérer le cluster Local pour lancer Service Fabric Explorer.

    ![Lancer l’Explorateur de Fabric Service à partir du Gestionnaire de cluster Local.][systray-launch-sfx]

    Service Fabric Explorer offre une représentation visuelle de cluster, notamment l’ensemble des applications déployées et le jeu de nœuds physiques qui le composent. Pour en savoir plus sur Service Fabric Explorer, voir [Visualisation de votre cluster](service-fabric-visualizing-your-cluster).

6. Dans le volet gauche, développez **Cluster > Nœuds** et recherchez le nœud sur lequel votre code s’exécute.

7. Cliquez sur **Actions > Désactiver (Redémarrer)** pour simuler le redémarrage de l’ordinateur.

	![Arrêter un nœud Service Fabric Explorer][sfx-stop-node]

	Vous devez voir momentanément le point d’arrêt atteint dans Visual Studio, car le calcul vous faisiez sur un nœud bascule en toute transparence vers un autre.

8. Revenir à l’Observateur d’événements de diagnostic et prenez connaissance des messages. Notez que le compteur a continué de s’incrémenter même si les événements proviennent en fait d’un autre nœud.

    ![Observateur d’événements de diagnostic après basculement][diagnostic-events-viewer-detail-post-failover]

### Nettoyage

  Avant de conclure, il est important de se rappeler que le cluster local est très réel. Même après l’arrêt du débogueur et la fermeture de Visual Studio, vos applications continueront de s’exécuter en arrière-plan. Selon la nature de vos applications, cette activité en arrière-plan peut accaparer une quantité significative de ressources de votre ordinateur. Vous disposez de plusieurs options assurer la gestion :

  1. Pour supprimer une application individuelle et toutes ses données, utilisez l’action **Supprimer application** dans l’Explorateur Service Fabric.

  2. Pour arrêter le cluster et conserver les données et les suivis de l’application, cliquez sur **Arrêter le cluster** dans l’application de la barre d’état système.

  3. Pour supprimer complètement le cluster, cliquez sur **Supprimer le cluster** dans l’application de la barre d’état système. Notez que cette option entraînera un ralentissement du déploiement lors de la prochaine utilisation de la touche F5 dans Visual Studio et qu’il ne faut l’utiliser que si vous n’envisagez pas d’utiliser le cluster local pendant un certain temps, ou si vous avez désespérément besoin de récupérer des ressources.



## Étapes suivantes

- [Voir comment vous exposer vos services à internet avec WebAPI](service-fabric-add-a-web-frontend.md)
- [En savoir plus pour créer un cluster dans Azure](service-fabric-cluster-creation-via-portal.md)
- [En savoir plus sur la création de services fiables](service-fabric-reliable-services-quick-start.md)
- [Essayer de créer un service en utilisant le modèle de programmation Reliable Actor](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=AcomDC_1125_2015-->