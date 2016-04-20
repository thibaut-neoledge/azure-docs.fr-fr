<properties 
	pageTitle="Exemple Azure IoT MyDriving - Création d’une application | Microsoft Azure" 
	description="Créez une application offrant une démonstration complète de l’architecture d’un système IoT avec Microsoft Azure et notamment Stream Analytics, Machine Learning et Event Hubs." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>


# Création et déploiement de la solution MyDriving dans votre environnement

MyDriving est une solution Internet des objets (IoT, Internet of Things) qui recueille les données de votre voiture, les traite à l’aide de services Machine Learning et les affiche sur votre téléphone mobile. Le système principal se compose d’une variété de services fournis par Microsoft Azure. Les clients peuvent être des téléphones Android, iOS ou Windows 10.

Nous avons développé la solution MyDriving pour vous aider à créer votre propre système IoT. À partir du référentiel, vous pouvez obtenir des scripts Azure Resource Manager pour déployer l’architecture principale dans votre propre compte Azure. Vous pouvez ensuite reconfigurer les différents services, modifier les requêtes en fonction de vos propres données et ainsi de suite. Vous trouverez ces scripts, ainsi que du code pour l’application mobile, le projet d’API Azure App Service et d’autres ressources sur <https://github.com/Azure-Samples/MyDriving>.

Si vous n’avez pas encore essayé l’application, consultez le [guide de prise en main](iot-solution-get-started.md) (en anglais).

## Que devez-vous créer ?

Le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs) inclut une description détaillée de l’architecture. En résumé, nous avons configuré plusieurs éléments, et vous allez procéder de même pour créer un projet similaire :

* **Application cliente**. Elle s’exécute sur les téléphones Android, iOS et Windows 10. Nous utilisons la plateforme Xamarin pour partager une grande partie du code. Elle est stockée sur GitHub sous `src/MobileApp`. L’application effectue deux fonctions distinctes :
 * **Relais des données de télémétrie** de l’appareil OBD et de son propre service de localisation au service principal basé sur le cloud du système.
 * **Interface utilisateur** qui permet aux utilisateurs d’effectuer des interrogations concernant leurs trajets routiers enregistrés.
* **Service cloud** qui reçoit les données de trajet routier en temps réel et les traite. Lors de la création de ce service, la tâche principale consiste à choisir, paramétrer et lier une série de services Azure. Certaines parties nécessitent des scripts pour filtrer et traiter les données entrantes. Nous utilisons un modèle **ARM (Azure Resource Management)** pour configurer toutes les parties.
* **Application de service mobile**. Il s’agit du service web derrière la partie interface utilisateur de l’application pour appareil. Sa tâche principale consiste à interroger la base de données contenant les données stockées et traitées. Le code correspondant se trouve sur GitHub sous `src/MobileAppService`.
* **Visual Studio avec Xamarin** est notre environnement de développement. Disponible en tant que composant de Visual Studio et sous forme d’IDE autonome, Xamarin permet de générer le code de l’appareil multiplateforme. Pour la génération du code iOS, une instance de Xamarin doit être exécutée sur un ordinateur OSX. Cependant, si nécessaire, elle peut être exécutée en tant qu’agent, géré à partir de Visual Studio.
* Le **test unitaire** des applications pour appareil est effectué dans Xamarin Test Cloud.
* **GitHub** est le référentiel dans lequel nous stockons l’ensemble du code, des scripts et des modèles.
* **Visual Studio Team Services** est un service cloud utilisé pour gérer la génération et le test continus du service web et des applications pour appareil.
* **HockeyApp** est utilisé pour distribuer des versions du code de l’appareil. Au-delà de la gestion de la distribution, il collecte les rapports d’incident et d’utilisation, ainsi que les commentaires des utilisateurs.
* **Application Insights** surveille le service web mobile.


Voyons comment nous configurons tout cela. Notez que de nombreuses étapes sont facultatives.

## S’inscrire pour obtenir un compte

-   [**Visual Studio Dev Essentials**](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Ce programme gratuit permet d’accéder facilement à de nombreux services et outils de développement, y compris Visual Studio, Visual Studio Team Services et Azure. Il vous offre un crédit de 25 $ par mois sur Azure pendant douze mois. Il inclut également des abonnements à la formation Pluralsight et à Xamarin University. Vous pouvez aussi vous inscrire séparément pour bénéficier des niveaux gratuits d’[Azure](https://azure.com) et de [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx). Cependant, dans ce cas, vous n’obtenez pas de crédits Azure.

-   [**HockeyApp**](https://rink.hockeyapp.net/) (facultatif) pour gérer la distribution de test des applications mobiles et collecter les données de télémétrie.

-   [**Xamarin**](https://xamarin.com/) (obligatoire) pour générer l’application mobile et lancer des exécutions de débogage et des tests sur [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [**GitHub**](https://github.com/Azure-Samples/MyDriving/) (facultatif) pour créer des référentiels publics gratuits pour votre propre code (les référentiels privés sont payants). Vous pouvez également utiliser le plan de base dans Visual Studio Team Services pour les référentiels privés.

-   [**Power BI**](https://powerbi.microsoft.com/) (facultatif) pour créer des visualisations de données enrichies sur l’ensemble du système.

> [AZURE.NOTE] Vous n’avez pas besoin d’un compte GitHub pour accéder au code MyDriving sur [https://github.com/Azure-Samples/MyDriving](https://github.com/Azure-Samples/MyDriving).

## Installer les outils de développement

La configuration suivante concerne le développement de la solution complète : application multiplateforme iOS, Android et Windows Phone 10 avec un système Azure principal.

Vous pouvez également utiliser Xamarin Studio sur Mac ou Windows pour développer les applications mobiles, si vous ne travaillez pas sur le système Azure principal.

Vous trouverez une description plus complète de cette configuration [ici](https://msdn.microsoft.com/library/mt613162.aspx).

### Ordinateur de développement Windows

L’outil Windows central est Visual Studio, qui permet de travailler avec l’application MyDriving pour Android et Windows, le projet d’API App Service et les extensions de microservice.

Visual Studio intègre d’autres composants utiles, tels que Xamarin, Git et des émulateurs.

Installer :

-   [**Visual Studio 2015 avec Xamarin**](https://www.visualstudio.com/products/visual-studio-community-vs) toute édition (l’édition Community est gratuite)

-   [**SQLite pour plateforme Windows universelle**](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Requis pour générer le code Windows Phone 10.

-   [Kit de développement logiciel (SDK) Azure pour VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) : vous offre le Kit de développement logiciel (SDK) pour l’exécution des applications dans Azure et des outils de ligne de commande pour la gestion d’Azure.

-   [Kit de développement logiciel (SDK) Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Requis pour générer l’extension de [microservice](../service-fabric/service-fabric-get-started.md).

Extensions Visual Studio : sous Outils, vous pouvez voir Android, iOS, Xamarin, etc. Dans le cas contraire, ouvrez le **Panneau de configuration**, puis cliquez sur **Programmes et fonctionnalités**, **Microsoft** **Visual Studio 2015**, puis **Modifier**. Sous **Développement multiplateforme**, choisissez **C#/.Net (Xamarin)**. Par la même occasion, vérifiez que **GitHub** est installé.

### Ordinateur de développement Mac

Un ordinateur Mac (Yosemite ou version ultérieure) est requis si vous souhaitez développer pour iOS. Même si nous utilisons Visual Studio avec Xamarin sur Windows pour développer et gérer tout le code, Xamarin utilise un agent installé sur un Mac afin de générer et signer le code iOS.

![](./media/iot-solution-build-system/image1.png)

(Vous pouvez également utiliser Xamarin Studio directement sur le Mac pour développer des applications multiplateformes.)

Vous n’avez pas besoin du Mac si vous ne souhaitez pas inclure iOS comme plateforme cible.

Installer :

-   [**Xamarin Studio** pour iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). Vous pouvez également configurer Visual Studio et Xamarin sur un Mac exécutant une machine virtuelle Windows. Consultez la page [Configuration, installation et vérifications pour les utilisateurs Mac](https://msdn.microsoft.com/library/mt488770.aspx) sur MSDN.

-   (Facultatif) [Outils de développement Azure](https://azure.microsoft.com/downloads/).

Activez l’ouverture de session à distance sur le Mac. Ouvrez System Preferences, Sharing, puis cochez Remote Login.

Lorsque vous ouvrez un projet iOS dans Visual Studio sous Windows, le plug-in Xamarin vous invite à entrer l’ID du Mac.

## Extraire le référentiel GitHub

Extrayez une copie locale de <https://github.com/Azure-Samples/MyDriving> à l’aide du bouton **Download ZIP** sur GitHub, Visual Studio ou un autre client Git.

Décompressez le fichier dans un dossier avec un nom de chemin d’accès court, comme C:\\code.

Si vous souhaitez contribuer à notre code ou bénéficier des mises à jour correspondantes, vous pouvez également cloner le référentiel comme suit :

> git clone <https://github.com/Azure-Samples/MyDriving.git>

## Code du développeur Bing Cartes

[Inscrivez-vous pour obtenir une clé d’API Bing Cartes](https://msdn.microsoft.com/library/ff428642.aspx).

Vous devez effectuer ce remplacement à la ligne 22 dans `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`



## Générer l’application de démonstration

Ouvrez ces solutions dans Visual Studio :

-   src\\MobileApps\\MyDriving.sln
-   src\\MobileAppService\\MyDrivingService.sln
-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

Vous obtiendrez des invites pour :

-   approuver des projets potentiellement non fiables ; choisir de les ouvrir si vous souhaitez continuer ;

-   définir le mode développeur si vous travaillez sur un nouvel ordinateur Windows 10 ;

-   entrer vos informations d’identification Xamarin ;

-   vous connecter au Mac Xamarin. Si vous n’avez pas de Mac, cliquez avec le bouton droit sur le projet iOS dans Visual Studio, puis choisissez **Décharger le projet**.

Régénérez la solution.

### Problèmes de génération

Voici quelques anomalies que nous avons déjà rencontrées :

-   Le projet VINLookupApplication ne se charge pas : assurez-vous que vous avez installé le [Kit de développement logiciel (SDK) Azure pour VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   Le projet Service Fabric n’est pas généré : commencez par générer les projets d’interface et assurez-vous que vous avez installé le Kit de développement logiciel (SDK) Service Fabric.

-   L’application Android n’est pas générée :

    -   Cliquez sur Outils, Android, Gestionnaire du Kit de développement logiciel (SDK) Android et assurez-vous qu’Android 6 (API 23)/plateforme du Kit de développement logiciel (SDK) est installé.

    -   Supprimez ce répertoire, puis régénérez :<br/> `%LocalAppData%\Xamarin\zips`

## Se familiariser avec le code

Dans la solution, vous trouverez les éléments suivants :

-   Extensions Azure

    -   Service Fabric

-   HDInsight : scripts pour le traitement des données de trajet dans Azure

-   Mobile Apps : les applications pour appareil

-   MobileAppsService/MyDrivingService : le service web principal

-   Power BI : définition de rapport Power BI

-   Scripts

    -   ARM : modèles utilisés pour la génération des ressources Azure

    -   PowerShell : scripts utilisés pour exécuter les modèles ARM

    -   SQLDatabase : bases de données de débogage

-   SQLDatabase

    -   CreateTables : définitions de schéma

-   StreamingAnalytics : requêtes qui transforment le flux de données entrant

## Exécuter les applications en mode de développement

### Service principal

Définissez MyDrivingService comme projet de démarrage, puis appuyez sur F5 pour exécuter le service web principal. Une vue de la liste des API s’affiche dans le navigateur.

### Clients mobiles

Les [applications mobiles sont développées dans Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).

-   [Débogage d’Android dans Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)

### iOS

-   [Débogage sous iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)

### Windows Phone

-   [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)


## HockeyApp

HockeyApp gère la distribution de votre application Android, iOS ou Windows pour appareil aux utilisateurs de test en les informant des nouvelles versions. Il collecte également des rapports d’incidents utiles, les commentaires des utilisateurs avec captures d’écran, ainsi que des mesures sur l’utilisation.

[Commencez par charger](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) votre application générée. Puis, connectez-vous à HockeyApp (<https://rink.hockeyapp.net>) à partir de votre ordinateur de développement. Dans le tableau de bord du développeur, cliquez sur **New App**, puis faites glisser les fichiers générés dans la fenêtre. (Par la suite, vous pourrez automatiser cette tâche avec votre service de build.)

Vous accédez maintenant au tableau de bord de votre application.

![](./media/iot-solution-build-system/image2.png)

Répétez le processus pour chaque plateforme sur laquelle votre application s’exécute.

- Utilisez l’[ID de l’application](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) du tableau de bord pour envoyer des données d’incident et des commentaires à partir de votre application. Dans MyDriving, mettre à jour les ID dans src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs 
-   [**Invitez les utilisateurs de test**](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Vous obtenez une URL pour recruter les utilisateurs testeurs. Ils pourront s’inscrire pour rejoindre votre équipe, télécharger l’application et vous envoyer des commentaires.

-   Si vous préférez une version bêta plus ouverte, définissez une distribution publique : cliquez sur **Manage App, Distribution, Download = Public**. Désormais, tout le monde peut télécharger votre application et vous envoyer des commentaires. De même, tous les utilisateurs recevront une notification lorsque vous publierez une nouvelle version. Vous pouvez également leur fournir des rapports d’incident.
![](./media/iot-solution-build-system/image3.png)

-   [**Liez les rapports d’incident à Visual Studio Team Services**](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs) : cliquez sur **Manage App, Visual Studio Team Services**. HockeyApp peut créer automatiquement des éléments de travail dans Team Services lorsque des rapports d’incident sont disponibles ou lors de la réception des commentaires.

> Pour en savoir plus, voir <https://hockeyapp.net>.

## Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatise les tests d’interface utilisateur sur les appareils réels dans le cloud. À l’aide de l’infrastructure NUnit, vous écrivez des tests qui exécutent votre application dans l’interface utilisateur.

Pour utiliser Xamarin, vous intégrez le Kit de développement logiciel (SDK) [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) à votre application. Il est fourni en tant que package NuGet. Vous le trouverez dans l’application de démonstration. Par ailleurs, il est inclus lorsque vous créez des projets de test avec les modèles Xamarin.

![](./media/iot-solution-build-system/image4.png)

Un exemple de projet de test est inclus avec l’application dans le référentiel : dans [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), regardez sous [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/

Si vous utilisez la build Visual Studio Team Services, il est facile d’écrire des tests unitaires d’interface utilisateur Xamarin et de les exécuter dans le cadre de votre build.

## Déployer des services Azure

Reportez-vous aux instructions détaillées de **scripts/README.md** pour effectuer un déploiement automatique des services Azure et des services de build de Team Services.

### Que fait le script de déploiement ?

Microsoft Azure offre une multitude de services, que vous pouvez utiliser pour générer des applications cloud. Si de nombreux services peuvent être utilisés individuellement (App Service/Web Apps, par exemple), ils offrent les meilleures performances lorsqu’ils sont interconnectés pour former un système intégré, comme celui que nous utilisons dans MyDriving.

Il est possible de créer et d’interconnecter manuellement des services Azure, mais il est beaucoup plus rapide et plus fiable d’utiliser des modèles ARM (Azure Resource Manager). [ARM](../resource-group-overview.md) automatise le déploiement des ressources d’une solution et la réalisation d’interconnexions entre elles.

Vous trouverez le modèle pour le système MyDriving dans le référentiel GitHub, sous [Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Il fournit une vue concise et très complète de la façon dont les différents services de notre architecture sont interconnectés. Nous vous expliquons tout cela en détail dans les [Guide de référence MyDriving](http://aka.ms/mydrivingdocs), mais vous pouvez en apprendre beaucoup en lisant simplement le contenu du modèle lui-même.

> [AZURE.NOTE] Le coût de la plupart des services Azure est basé sur le niveau tarifaire. Si vous découvrez Azure, vous pouvez l’[essayer gratuitement](https://azure.microsoft.com/free/). Toutefois, si vous pensez que vous n’utiliserez pas certains composants du système MyDriving, veillez à les supprimer pour éviter des coûts élevés. Consultez la section *Coûts d’exploitation estimés*, plus loin dans cet article, pour obtenir un récapitulatif des dépenses courantes liées aux services.

#### Modifier le modèle

Pour personnaliser votre déploiement, par exemple pour supprimer des composants inutiles ou en ajouter d’autres, commencez par faire des copies des fichiers scenario\_complete.params.json et scenario\_complete.json, dans lesquelles vous pourrez apporter des modifications.

Le fichier scenario\_complete.params.json vous permet de remplacer les différentes valeurs par défaut, telles que la référence du service ou le type de réplication de stockage, comme indiqué dans le tableau suivant. Les valeurs par défaut correspondent aux options les moins coûteuses.

| **Paramètre** | **Description** | **Valeur par défaut** |
|--------|---------|-------|
| Référence IoT Hub | Niveau pour le service Azure IoT Hub | F1 |
| Type de compte de stockage | Type de réplication de stockage | LRS standard |
| Objectif de service SQL | Consommation des emplacements de concurrence | DW100 |
| Référence du plan d’hébergement | Plan de service pour App Service | F1 |

Dans scenario\_complete.json :

-   Recherchez « baseName » et indiquez à la place le nom que vous souhaitez utiliser.

-   Recherchez « Create » : chacune de ces sections crée une ressource.

-   Définissez des valeurs appropriées pour sqlServerAdminLogin et sqlServerAdminPassword.

-   Avant de supprimer une section qui crée une ressource, vérifiez si elle a des dépendances en recherchant son nom dans le reste du fichier. Notez que chaque section créant un service inclut une section *dependsOn* répertoriant ses dépendances.

Voici les éléments configurés par le modèle (pour obtenir des informations plus détaillées, reportez-vous au [Guide de référence](http://aka.ms/mydrivingdocs)) :

| **Service** | **Description et détails**  
|---|----
| Comptes de stockage | Le modèle crée trois comptes :                                                                                                                                                                       
||- Une base de données SQL qui reçoit la télémétrie agrégée à partir de Stream Analytics et sert de magasin de stockage pour les tables Azure App Service qui exposent ces données par le biais de points de terminaison d’API.                      
| | - Blob Storage qui accumule des données d’historique à partir d’une autre tâche Stream Analytics en vue de leur traitement par HDInsight.                                                                                         
| | - Une base de données SQL qui reçoit les résultats des données traitées par HDInsight pour une utilisation avec Power BI.                                                                                                                 
| IoT Hub | Établit une connexion bidirectionnelle avec chaque appareil connecté. Dans la solution MyDriving, l’application mobile fait office de passerelle de champ pour l’envoi de données à IoT Hub. IoT Hub sert alors d’entrée pour Stream Analytics. |
| Hub d’événements | Une sortie pour une tâche Stream Analytics, qui met en file d’attente la sortie vers les extensions créées avec Azure Service Fabric.                                                                                               
| SQL Data Warehouse |                                                                                                                                                                                                            
| Tâches Stream Analytics (ASA) | Connectent les entrées et sorties avec une requête, qui est utilisée pour agréger les données d’historique et en temps réel des API App Service, de Machine Learning, des extensions et de Power BI.                               
| Espace de travail Machine Learning | Inclut des expériences, le code R et le service API.                                                                                                                                                              
| Fabrique de données | Reformation Machine Learning planifiée.                                                                                                                                                                     
| Plan d’hébergement Service Fabric | Pour les extensions.                                                                                                                                                                                            
| App Service (« Application mobile ») | Héberge le projet d’API Mobile Apps qui fournit des points de terminaison pour l’application mobile. Le code de l’API doit être déployé vers le service App Service à partir de Visual Studio.                                                         
| Règles d'alerte | Vous envoient un e-mail si des échecs sont signalés par les réponses de l’application.                                                                                                                                            
| Application Insights | Pour surveiller les performances des API dans le service App Service. Vous devez configurer la connexion dans Visual Studio.                                                                                          
| Key Vault | Pour enregistrer le certificat de cluster du service web.                                                                                                                                                                

#### Exécuter le modèle

Pour obtenir des instructions détaillées, voir **scripts/README.md**.

Pour configurer tous ces services dans votre propre compte Azure à l’aide du script, effectuez l’une des opérations suivantes :

-   Utilisation de PowerShell :

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* est l’[emplacement Azure](https://azure.microsoft.com/regions/), par exemple « Europe du Nord » ou « États-Unis de l’Ouest ». Utilisez `Get-AzureLocation` pour obtenir la liste des emplacements disponibles.

 -   *resourceGroupName* est le nom que vous souhaitez donner au groupe auquel appartiendront toutes les ressources. Lorsque vous avez terminé avec les ressources, vous pourrez les supprimer simultanément en supprimant ce groupe.

-   Exécutez DeploymentScripts/Bash/deploy.sh avec Bash.

-   Ouvrez et générez la solution Visual Studio DeploymentScripts/VS/DeployARM.sln.

Notez qu’à chaque exécution du modèle, celui-ci crée un ensemble de ressources avec de nouveaux noms. Pour supprimer les ressources, accédez au portail et supprimez le groupe de ressources.

Si le script échoue pour une raison quelconque, vous pouvez le réexécuter.

Le script vous donne la possibilité de configurer une intégration continue dans Visual Studio Team Services. Si vous avez configuré un projet Team Services, vous obtiendrez une URL https://yourAccountName.visualstudio.com. Entrez l’URL complète lorsque vous y êtes invité. Vous pouvez lui donner un nouveau nom ou le nom existant d’un projet Team Services.



## Visual Studio Team Services

Nous utiliser Team Services sur ce projet, principalement pour ses fonctionnalités de génération et de test. Cependant, il offre également une excellente prise en charge de la collaboration : gestion des tâches avec les tableaux Kanban, vérification de code intégrée aux tâches et au contrôle de code source, builds contrôlées, etc. Il s’intègre efficacement à d’autres outils tels que GitHub, Xamarin, HockeyApp et, bien sûr, Visual Studio. Vous pouvez y accéder par le biais de l’interface web ou de Visual Studio, selon la situation et la méthode vous convenant le mieux.

Les étapes relatives aux définitions de build et de mise en production utilisent une variété de services de plug-in, disponibles sur le [Marketplace](https://marketplace.visualstudio.com/VSTS) Team Services. Outre les utilitaires de base permettant d’exécuter des lignes de commande ou copier des fichiers, certains services appellent des builds de Xamarin, d’Android et d’autres fournisseurs et fonctionnent en interface avec HockeyApp.

![](./media/iot-solution-build-system/image5.png)

### Définitions de build

Nous disposons de définitions de build pour chacune des cibles principales et de variantes pour les tests de fonctionnalités et de régression :

-   MyDriving.Services – l’application web principale pour l’application mobile

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-Feature

    -   MyDriving.Xamarin.Android-Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-Feature

    -   MyDriving.Xamarin.iOS-Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-Feature

    -   MyDriving.Xamarin.UWP-Regression

Pour obtenir des détails complets sur notre configuration, consultez la section 4.7 du [Guide de référence MyDriving](http://aka.ms/mydrivingdocs), *Build and Release Configuration* (en anglais). Le même modèle général y est appliqué :

1.  Restauration des packages NuGet. Nous ne conservons pas le code compilé dans le référentiel. Pour chaque build, les premières étapes consistent donc à restaurer les packages NuGet.

2.  Activation de la licence. La génération est effectuée dans le cloud. Ainsi, lorsqu’une licence est requise (en particulier pour le service de build Xamarin), nous devons activer notre licence sur l’ordinateur de build actuel. Puis, nous la désactivons immédiatement, pour pouvoir l’utiliser sur un autre ordinateur.

3.  Génération à l’aide du service approprié. Nous utilisons les builds Xamarin pour les applications mobiles et une build Visual Studio pour le service web principal.

4.  Tests de build

5.  Exécution des tests. Nous exécutons les tests d’application mobile dans Xamarin Test Cloud.

6.  Publication des résultats de build à l’emplacement de dépôt.

Le déclencheur pour les builds principales est défini sur Intégration continue. Autrement dit, la build est exécutée chaque fois que du code est archivé dans la branche principale.

![](./media/iot-solution-build-system/image6.png)

### Définitions de mise en production

Les définitions de mise en production sont configurées de façon similaire.

Pour le service web, nous configurons le déploiement en tant qu’application web Azure :

![](./media/iot-solution-build-system/image7.png)

Puis, nous définissons le déclencheur de mise en production pour le déploiement continu. Autrement dit, chaque archivage suivi de résultats de génération réussis entraîne une mise à jour de l’application web :

![](./media/iot-solution-build-system/image8.png)

Pour les applications mobiles, nous effectuons le déploiement dans HockeyApp :
![](./media/iot-solution-build-system/image9.png)

## Application Insights

[Application Insights](../application-insights/app-insights-overview.md) collecte les données de télémétrie relatives aux performances et à l’utilisation de vos services web. Le Kit de développement logiciel (SDK) Application Insights envoie les données de télémétrie du service vers la ressource Application Insights dans Azure.

Accédez à la ressource Application Insights configurée par le modèle. Vous pouvez alors consulter les graphiques de performances de votre [projet Mobile App Service](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Ils indiquent les demandes et temps de réponse du serveur, les échecs et le nombre d’exceptions. Vous disposez également de graphiques sur les temps de réponse des dépendances, portant sur les appels à la base de données et aux API REST comme Machine Learning. Si des problèmes de performances sont identifiés, vous pourrez savoir quelle partie de votre système en est la cause.

![Exemple de graphique de performances](./media/iot-solution-build-system/image11.png)

Si vous avez configuré un service web manuellement, vous pouvez aisément obtenir les mêmes graphiques :

![Dans le panneau du service web, choisissez Outils, Extensions, Ajouter. Sélectionnez Application Insights.](./media/iot-solution-build-system/image12.png)

Cette fonctionnalité instrumente votre application avec le Kit de développement logiciel (SDK) Application Insights.

Vous pouvez ajouter une télémétrie personnalisée (ou instrumenter une application exécutée en dehors d’Azure) en [ajoutant le Kit de développement logiciel (SDK) Application Insights](../application-insights/app-insights-asp-net.md) au moment du développement. Cela s’avère utile pour consigner les métriques qui dépendent de l’application, par exemple la longueur moyenne des trajets des utilisateurs ou le kilométrage total.

![Dans Visual Studio, cliquez sur le projet avec le bouton droit et sélectionnez Ajouter Application Insights.](./media/iot-solution-build-system/image10.png)

Application Insights envoie des alertes par courrier électronique s’il détecte un nombre inhabituel de réponses d’échec. Vous pouvez également configurer vos propres alertes pour différentes métriques, telles que les temps de réponse.

Pour vous assurer que votre service web est toujours opérationnel, vous pouvez configurer des [tests de disponibilité](../application-insights/app-insights-monitor-web-app-availability.md), qui effectuent un test ping sur votre site à partir de différents emplacements dans le monde toutes les quinze minutes. Là encore, vous recevrez un message électronique en cas de problème potentiel.



## Coûts d’exploitation estimés

Il est extrêmement économique d’exécuter une application comme celle-ci à petite échelle. De nombreux services sont disponibles avec des niveaux d’entrée de gamme gratuits, qui limitent considérablement les coûts de développement et d’exploitation à petite échelle. Bien sûr, vos propres applications ne doivent pas nécessairement utiliser toutes les fonctionnalités présentées dans MyDriving.

Voici une estimation approximative des coûts encourus pour une configuration de développement de MyDriving.

Nous indiquons également des alternatives que nous n’avons *pas* utilisées. #

Nous partons de l’hypothèse suivante :

-   Une équipe de cinq personnes maximum (+ parties prenantes pour observation).

-   En fonctionnement depuis un mois environ.

-   100 utilisateurs avec 4 trajets par jour.

>[AZURE.NOTE] Si vous découvrez Azure, vous pouvez utiliser un [compte d’essai gratuit](https://azure.microsoft.com/free/).

| **Service/composant** | **Remarques** | **Coût par mois** |
|--------|--------|----------------|
| Environnement de développement multiplateforme [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) avec [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee).<br/>| Visual Studio Community. <br/>(# [VS Pro](https://www.visualstudio.com/vs-2015-product-editions) requis pour [Xamarin.Forms](https://xamarin.com/forms), pour la conception multiplateforme à partir d’une seule base de code.) | 0 $ |
| [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Connexion de données bidirectionnelle avec les appareils. | 8 000 messages + 0,5 Ko/message gratuit | 0 $ |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Traitement des données de flux à volume élevé. | Facturation de 0,031 $ par unité de diffusion en continu par heure pendant l’activation. Vous choisissez le nombre d’unités de diffusion en continu et en ajoutez pour monter en puissance. | 23 $ |
| [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Réponses adaptatives. | 10 $/siège/mois <br/> + expérience de 3 h * 1 $/heure d’expérience <br/> + 3,5 h de temps processeur de l’API * 2 $/heure de production UC <br/> le temps processeur de l’API est basé sur une reformation de 5 min/jour, une augmentation pouvant se produire avec un volume supérieur de données d’entrée ; <br/> + 2 min/jour de notation pour traiter 400 trajets/jour. | 20 $ |
| [Azure App Service](https://azure.microsoft.com/pricing/details/app-service/) <br/> pour l’hébergement des applications mobiles principales | Niveau B1 : applications web de production | 56 $ |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Gestion des builds, des tests unitaires et de la mise en production. Gestion des tâches. | Agents privés, 5 utilisateurs| 0 $ |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Surveillance des performances et de l’utilisation des sites et services web.| Niveau Gratuit | 0 $ |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribution des applications bêta, collecte des commentaires et des données d’utilisation et d’incident. | Deux applications gratuites pour les nouveaux utilisateurs.<br/> 30 $ par mois par la suite. | 0 $ |
| [Xamarin](https://store.xamarin.com/)<br/> Code sur une plateforme unifiée pour plusieurs appareils. | Essai gratuit. <br/>25 $ par mois par la suite.| 0 $ |
| [Instance SQL](https://azure.microsoft.com/pricing/details/sql-database/) pour Azure App Service| Niveau de base ; modèle de base de données unique | 5 $ |
| [Service Fabric](../service-fabric/services/service-fabric.md) (facultatif) | Exécution d’un cluster local. | 0 $ |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Affichage polyvalent et analyse des données par flux et statiques.| Niveau gratuit : 1 Go, 10 ko lignes/heure, actualisation quotidienne <br/> # 10 $/utilisateur/mois pour [limites supérieures](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), options de connexion supplémentaires et collaboration | 0 $ |
| [Stockage](https://azure.microsoft.com/pricing/details/storage/) | L (localement redondant) &lt; 100 Go 0,024 $/Go | 3 $ |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) | 0,60 $ par activité * (8 - 5 FOC)| 2 $ |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) (cluster à la demande) <br/> pour reformation quotidienne | 3 nœuds A3 à 0,32 $/heure pour 1 heure par jour x 31 jours | 30 $ |
| [Hub d'événements](https://azure.microsoft.com/pricing/details/event-hubs/) | De base avec unité de débit 11 $/mois + entrée 0,028 $ | 11 $ |
| Dongle OBD || 12 $ |
| **Total**| | **157 $** |

Voir aussi :

-   Récapitulatif des [limites et quotas de service Azure](../azure-subscription-service-limits/#iot-hub-limits).

-   [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) Azure



## Guide de référence

Nous vous recommandons de consulter le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs), qui offre une description complète de la conception du système et de ses composants.


## Commentaires 

Nous avons créé MyDriving pour vous aider à lancer vos propres systèmes IoT. Aussi, nous souhaiterions connaître votre avis sur son fonctionnement. Indiquez-nous si vous rencontrez des problèmes ou des difficultés, si un point d’extension permettrait de mieux l’adapter à votre situation ou si vous trouvez une méthode répondant mieux à certains de vos besoins. Faites-nous également part de vos suggestions pour améliorer MyDriving ou cette documentation.

Vous pouvez également signaler un [problème sur GitHub] ou laisser un commentaire ci-dessous (édition fr-FR).

Nous espérons recevoir bientôt de vos nouvelles.

<!----HONumber=AcomDC_0406_2016--->