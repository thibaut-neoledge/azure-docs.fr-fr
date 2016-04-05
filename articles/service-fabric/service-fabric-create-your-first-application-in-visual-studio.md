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
   ms.date="03/27/2016"
   ms.author="seanmck"/>

# Créer votre première application Service Fabric Azure dans Visual Studio

Le kit de développement logiciel Fabric Service inclut un complément à Visual Studio proposant des outils et des modèles pour la création, le déploiement et le débogage d’applications Service Fabric. Cette rubrique vous guide tout au long du processus de création de votre première application dans Visual Studio.

## Composants requis

Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement](service-fabric-get-started.md).

## Vidéo de procédure pas à pas

La vidéo suivante vous guide à travers les étapes de ce didacticiel :

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Création de l'application

Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Avec l’Assistant Nouveau projet, vous avez la possibilité de créer un projet d’application avec votre premier projet de service. Vous pouvez ajouter davantage de services plus tard.

1. Lancez Visual Studio en tant qu'administrateur.

2. Cliquez sur **Fichier > Nouveau projet > Cloud > Application Service Fabric**.

3. Nommez l’application, puis cliquez sur **OK**.

	![Boîte de dialogue Nouveau projet dans Visual Studio][1]

4. Sur la page suivante, vous serez invité à choisir le premier type de service à inclure dans votre application. Pour les besoins de ce didacticiel, nous allons choisir **Avec état**. Attribuez-lui un nom, puis cliquez sur **OK**.

	![Boîte de dialogue Nouveau service dans Visual Studio.][2]

	>[AZURE.NOTE] Pour plus d’informations sur les options, consultez [Choix d’une infrastructure](service-fabric-choose-framework.md).

	Visual Studio crée le projet d’application et le projet de service avec état et les affiche dans l’Explorateur de solutions.

	![Explorateur de solutions après la création de l’application de service avec état][3]

	Le projet d’application ne contient pas de code directement. Au lieu de cela, il fait référence à un ensemble de projets de service. En outre, il contient trois autres types de contenu :

	- **Profils de publication** : permet de gérer les préférences d’outils pour différents environnements.

	- **Scripts** : contient un script PowerShell de déploiement/mise à niveau de votre application. Ce script est utilisé par Visual Studio en arrière-plan et peut être appelé directement à la ligne de commande.

	- **Définition d’application** : le manifeste de l’application sous *ApplicationPackageRoot* et les fichiers de paramètres d’application associés sous *ApplicationParameters* définissent l’application et vous permettent de la configurer spécifiquement pour un environnement donné.

    Pour avoir une vue d’ensemble du contenu du projet de service, consultez l’article [Prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## Déployer et déboguer l’application.

Maintenant que vous disposez d’une application, essayez de l’exécuter.

1. Appuyez sur F5 dans Visual Studio pour déployer l’application en vue d’un débogage.

	>[AZURE.NOTE] Cela prend un certain temps la première fois que Visual Studio crée un cluster local pour le développement. Un cluster local exécute un code de plateforme équivalent à celui qui serait généré dans un cluster regroupant plusieurs machines sur un seul ordinateur. Vous verrez l’état de la création de clusters dans la fenêtre Sortie de Visual Studio.

	Une fois le cluster prêt, vous obtiendrez une notification de la part de l’application de gestionnaire de la barre d’état système de cluster local qui est incluse avec le kit de développement logiciel.

	![Notification de barre d’état système de cluster local][4]

2. Une fois que l’application a démarré, Visual Studio affiche automatiquement l’Observateur d’événements de diagnostic, où vous pouvez voir la sortie de suivi depuis le service.

	![Observateur d’événements de diagnostic][5]

	Dans le cas du modèle de service avec état, les messages indiquent simplement la valeur du compteur incrémentée dans la méthode `RunAsync` de MyStatefulService.cs.

3. Pour plus d’informations, développez un des événements, et notamment le nœud sur lequel le code s’exécute. Dans ce cas, il s’agit du \_Node\_2, bien que cela puisse être différent sur votre ordinateur.

	![Détails de l’Observateur d’événements de diagnostic][6]

	Le cluster local contient cinq nœuds hébergés sur un seul ordinateur. Il simule un cluster à cinq nœuds dans lequel les nœuds sont hébergés sur des machines distinctes. Examinons un des nœuds du cluster local pour simuler la perte d’un ordinateur et exercer le débogueur Visual Studio en même temps.

    >[AZURE.NOTE] Les événements de diagnostic d’application émis par le modèle de projet utilisent la classe `ServiceEventSource` incluse. Pour plus d’informations, consultez l’article [Comment analyser et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Recherchez dans votre projet de service la classe qui dérive de StatefulService (par exemple, MyStatefulService) et définissez un point d’arrêt sur la première ligne de la méthode `RunAsync`.

	![Point d’arrêt dans la méthode RunAsync de service avec état][7]

5. Cliquez avec le bouton droit sur l’application de barre d’état système du gestionnaire du cluster local et choisissez **Gérer le cluster Local** pour lancer Service Fabric Explorer.

    ![Lancer l’Explorateur de Fabric Service à partir du Gestionnaire de cluster Local.][systray-launch-sfx]

    Service Fabric Explorer offre une représentation visuelle de cluster, notamment l’ensemble des applications déployées et le jeu de nœuds physiques qui le composent. Pour en savoir plus sur Service Fabric Explorer, voir [Visualisation de votre cluster](service-fabric-visualizing-your-cluster.md).

6. Dans le volet gauche, développez **Cluster > Nœuds** et recherchez le nœud sur lequel votre code s’exécute.

7. Cliquez sur **Actions > Désactiver (Redémarrer)** pour simuler le redémarrage de l’ordinateur. (Notez que vous pouvez également procéder ainsi dans un menu contextuel, dans la vue Liste de nœuds du volet gauche en sélectionnez les points de suspension).

	![Arrêter un nœud Service Fabric Explorer][sfx-stop-node]

	Vous devez voir momentanément le point d’arrêt atteint dans Visual Studio, car le calcul vous faisiez sur un nœud bascule en toute transparence vers un autre.

8. Revenir à l’Observateur d’événements de diagnostic et prenez connaissance des messages. Notez que le compteur a continué de s’incrémenter, même si les événements proviennent en fait d’un autre nœud.

    ![Observateur d’événements de diagnostic après basculement][diagnostic-events-viewer-detail-post-failover]

### Nettoyage

  Avant de conclure, il est important de se rappeler que le cluster local est très réel. Même après l’arrêt du débogueur et la fermeture de Visual Studio, vos applications continuent de s’exécuter en arrière-plan. Selon la nature de vos applications, cette activité en arrière-plan peut accaparer une quantité significative de ressources de votre ordinateur. Vous disposez de plusieurs options assurer la gestion :

  1. Pour supprimer une application individuelle et toutes ses données, utilisez l’action **Supprimer l’application** dans Service Fabric Explorer avec le menu **ACTIONS** ou le menu contextuel dans la vue Liste des applications du volet gauche.
  
    ![Supprimer une application dans Service Fabric Explorer][sfe-delete-application]
    
  2. Après avoir supprimé l’application dans le cluster, vous pouvez choisir de **mettre hors-service le type de l’application**, ce qui supprime le package de celle-ci, notamment son code et sa configuration, dans le magasin d’images du cluster.
  3. Pour arrêter le cluster tout en conservant les données et les traces de l’application, cliquez sur **Arrêter le cluster local** dans l’application de la barre d’état système.

  4. Pour supprimer complètement le cluster, cliquez sur **Supprimer le cluster local** dans l’application de la barre d’état système. Notez que cette option se traduira par un autre déploiement lent la prochaine fois que vous appuierez sur F5 dans Visual Studio. Utilisez cette option uniquement si vous n’envisagez pas d’utiliser le cluster local pendant un certain temps, ou si vous avez besoin de libérer des ressources.



## Étapes suivantes

- [Voir comment présenter vos services sur Internet avec un service principal web](service-fabric-add-a-web-frontend.md)
- [En savoir plus pour créer un cluster dans Azure](service-fabric-cluster-creation-via-portal.md)
- [En savoir plus sur Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Essayer de créer un service en utilisant le modèle de programmation Reliable Actors](service-fabric-reliable-actors-get-started.md)

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
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png

<!---HONumber=AcomDC_0330_2016-->