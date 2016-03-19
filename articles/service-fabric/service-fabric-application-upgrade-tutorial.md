 <properties
   pageTitle="Didacticiel de mise à niveau d'applications Service Fabric | Microsoft Azure"
   description="Cet article vous présente l'expérience de déploiement d'une application Service Fabric, la modification du code et le déploiement d'une mise à niveau à l'aide de Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/04/2016"
   ms.author="subramar"/>



# Didacticiel sur la mise à niveau d'une application Service Fabric à l'aide de Visual Studio

Azure Service Fabric simplifie le processus de mise à niveau des applications cloud en veillant à ce que seuls les services modifiés soient mis à niveau et que l'intégrité de l'application soit analysée tout au long de la mise à niveau. Il restaure automatiquement la version précédente de l'application en cas de problème. Les mises à niveau des applications Service Fabric s'effectuent *sans interruption de service*, étant donné que l'application peut être mise à niveau sans interruption du service. Ce didacticiel explique comment effectuer une simple mise à niveau propagée à partir de Visual Studio.


## Étape 1 : Créer et publier l'exemple Visual Objects

Vous pouvez effectuer ces étapes en téléchargeant l'application à partir de GitHub et en ajoutant les fichiers **webgl-utils.js** et **gl-matrice-min.js** au projet comme indiqué dans le fichier Lisezmoi de l'exemple. Sans cela, l'application ne fonctionnera pas. Après les avoir ajoutés au projet, générez et publiez l'application en cliquant avec le bouton droit sur le projet d'application, **VisualObjectsApplication** et en sélectionnant la commande **Publier** dans l'élément de menu Service Fabric, comme ci-après.

![Menu contextuel pour une application Service Fabric][image1]

Une autre fenêtre s'affiche et vous pouvez définir le **point de terminaison de connexion** sur **cluster local**. La fenêtre doit ressembler à ce qui suit avant de cliquer sur **Publier**.

![Publication d'une application Service Fabric][image2]

À présent, vous pouvez cliquer sur **Publier** dans la boîte de dialogue. Vous pouvez utiliser l'[Explorateur de Service Fabric pour afficher le cluster et l'application](service-fabric-visualizing-your-cluster.md). L'application Visual Objects possède un service web accessible en tapant [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) dans la barre d'adresse de votre navigateur. Vous devez normalement voir 10 objets visuels flottants en rotation à l'écran.

## Étape 2 : Mettre à jour l'exemple Visual Objects

Vous pouvez remarquer qu'avec la version qui a été déployée à l'étape 1, les objets visuels ne sont pas en rotation. Mettons à niveau cette application vers une application dans laquelle les objets visuels sont en rotation.

Sélectionnez le projet VisualObjects.ActorService dans la solution VisualObjects et ouvrez le fichier **StatefulVisualObjectActor.cs**. Dans ce fichier, accédez à la méthode `MoveObject`, placez en commentaire `this.State.Move()` et effacez le commentaire de `this.State.Move(true)`. Cette modification fait pivoter les objets lorsque le service a été mis à niveau. Vous pouvez maintenant générer (pas régénérer) la solution, qui va générer les projets modifiés. Si vous sélectionnez **Régénérer tout**, vous devrez mettre à jour les versions de tous les projets.

Nous devons également attribuer une version à notre application. Vous pouvez utiliser l'option **Edit Manifest Files** (Modifier les fichiers du manifeste) de Visual Studio pour modifier la version après avoir cliqué avec le bouton droit sur la solution. Cela affiche la boîte de dialogue des versions d'édition comme suit :

![Boîte de dialogue Contrôle de version][image3]

Sélectionnez l'onglet **Edit Manifest Versions** (Modifier les versions du manifeste) et mettez à jour les versions des projets modifiés et leurs packages de code, ainsi que l'application vers la version 2.0.0. Une fois les modifications effectuées, le manifeste doit se présenter comme suit (les parties en gras présentent les modifications) :

![Mise à jour des versions][image4]

Les outils Visual Studio peuvent déployer automatiquement les versions si vous sélectionnez **Automatically update application and service versions** (Mettre à jour automatiquement les versions de l'application et du service). Si vous utilisez [SemVer](http://www.semver.org), vous devez mettre à jour le code et/ou la version du package de configuration uniquement si cette option est sélectionnée.

Enregistrez les modifications et cochez la case **Upgrade the Application** (Mettre à niveau l'application).


## Étape 3 : Mettre à niveau votre application

Vous devez vous familiariser avec les [paramètres de mise à niveau d'application](service-fabric-application-upgrade-parameters.md) et le [processus de mise à niveau](service-fabric-application-upgrade.md) pour bien comprendre les différents paramètres de mise à niveau, les délais d'attente et les critères d'intégrité qui peuvent être appliqués. Pour cette procédure pas à pas, conservez les valeurs par défaut (mode non surveillé) des critères d'évaluation de l'intégrité du service. Vous pouvez configurer ces paramètres en sélectionnant **Configure Upgrade Settings** (Configurer les paramètres de mise à niveau) et en les modifiant comme vous le souhaitez.

À présent, nous sommes prêts à démarrer la mise à niveau de l'application en sélectionnant **Publier**. Cela va mettre à niveau votre application vers la version 2.0.0 dans laquelle les objets pivotent. Vous constaterez que Service Fabric met à niveau un domaine de mise à jour à la fois (certains objets seront mis à niveau en premier, suivis par d'autres) et le service est accessible pendant ce temps avec votre client (navigateur).


À présent, alors que la mise à niveau de l'application se poursuit, vous pouvez la surveiller à l'aide de l'Explorateur Service Fabric à l'aide de l'onglet **Upgrades in Progress** (Mises à niveau en cours) sous les applications.

Dans quelques minutes, tous les domaines de mise à jour seront mis à niveau (terminé) et la fenêtre de sortie Visual Studio doit également indiquer que la mise à niveau est terminée. De plus, vous pouvez normalement noter que *tous* les objets visuels sont désormais en rotation dans la fenêtre de votre navigateur.

Vous souhaiterez peut-être essayer de modifier les versions et de passer de la version 2.0.0 à la version 3.0.0 en guise d'exercice, voire même de revenir de la version 2.0.0 à la version 1.0.0. Amusez-vous avec les délais d'attente et les stratégies de contrôle d'intégrité pour vous familiariser avec ceux-ci. Lorsque vous déployez sur un cluster Azure, les paramètres que utilisés seront différents des paramètres que vous utilisez lorsque vous déployez vers un cluster local. Nous vous recommandons de définir les délais d'expiration de façon plus restrictive.


## Étapes suivantes

La [mise à niveau de votre application à l'aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l'application à l'aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utiliser la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l'application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).



[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=AcomDC_0211_2016-->