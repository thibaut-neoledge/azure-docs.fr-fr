<properties
   pageTitle="Utilisation d’ElasticSearch en tant que magasin de trace d’applications Service Fabric | Microsoft Azure"
   description="Cet article décrit comment les applications Service Fabric peuvent utiliser ElasticSearch et Kibana pour le stockage, l’indexation et la recherche dans les traces d’applications (journaux)."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# Utilisation d’ElasticSearch en tant que magasin de trace d’applications Service Fabric
## Introduction
Cet article décrit comment les applications [Service Fabric](http://azure.microsoft.com/documentation/services/service-fabric/) peuvent utiliser **ElasticSearch** et **Kibana** pour le stockage, l’indexation et la recherche de traces d’applications. [ElasticSearch](https://www.elastic.co/guide/index.html) est un moteur de recherche et d’analyse en temps réel open source, distribué et évolutif, qui convient parfaitement à cette tâche et qui peut être installé sur des machines virtuelles Windows ou Linux exécutées sous Microsoft Azure. ElasticSearch peut traiter très efficacement les traces *structurées* produites à l’aide de technologies telles que **Event Tracing for Windows (ETW)**.

ETW est utilisé par le runtime Service Fabric pour sourcer les informations de diagnostic (traces) et constitue également la méthode recommandée pour le sourçage des informations de diagnostics des applications Service Fabric. Cela garantit une corrélation entre les traces fournies par le runtime et celles fournies par l’application, tout en facilitant la résolution des problèmes. Les modèles de projet Service Fabric dans Visual Studio incluent une API de journalisation (basée sur la classe .NET **EventSource**) qui émet des traces ETW par défaut. Pour obtenir une vue d’ensemble du traçage d’applications Service Fabric à l’aide d’ETW, consultez [cet article](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Pour que les traces s’affichent dans ElasticSearch, elles doivent être capturées en temps réel sur les nœuds de cluster Service Fabric (pendant l’exécution de l’application) et envoyées au point de terminaison ElasticSearch. Il existe deux options principales pour la capture de traces :

+ **Capture de traces dans le processus** : l’application, ou plus précisément, le processus de service est responsable de l’envoi des données de diagnostic dans le magasin de trace (ElasticSearch).

+ **Capture de traces hors processus** : un agent séparé capture les traces à partir du ou des processus de service et les envoie au magasin de trace.

Dans le reste de cet article, nous allons expliquer comment configurer ElasticSearch sur Azure, présenter les avantages et inconvénients pour les deux options de capture, et expliquer comment configurer un Fabric Service pour envoyer des données à ElasticSearch.


## Configuration d’ElasticSearch sur Azure
La façon la plus simple de configurer le service ElasticSearch sur Azure consiste à passer par les [**modèles Azure ARM**](../resource-group-overview.md). Un [modèle ARM de démarrage rapide pour ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) est disponible à partir du référentiel de modèles de démarrage rapide Azure. Ce modèle utilise des comptes de stockage distincts pour les unités d’échelle (groupes de nœuds) et peut approvisionner des nœuds client et serveur distincts avec des configurations différentes et divers nombres de disques de données associés.

Dans cet article, nous allons utiliser un autre modèle appelé **ES-MultiNode** à partir de la [branche Microsoft Patterns & Practices ELK](https://github.com/mspnp/semantic-logging/tree/elk/). Ce modèle est un peu plus facile à utiliser ; il crée un cluster ElasticSearch protégé par défaut par l’authentification de base HTTP. Avant de continuer, veuillez télécharger le [référentiel Microsoft P&P « elk »](https://github.com/mspnp/semantic-logging/tree/elk/) dans GitHub sur votre ordinateur (soit en clonant le référentiel, soit en téléchargeant un fichier ZIP). Le modèle ES-MultiNode se trouve dans le dossier du même nom.
>[AZURE.NOTE] Le modèle ES-MultiNode et les scripts associés prennent actuellement en charge la version 1.7 d’ElasticSearch. La prise en charge d’ElasticSearch 2.0 sera ajoutée à une date ultérieure.

### Préparation d’un ordinateur pour l’exécution des scripts d’installation d’ElasticSearch
Le moyen le plus simple d’exploiter le modèle ES-MultiNode consiste à utiliser un script PowerShell déjà fourni, appelé `CreateElasticSearchCluster`. Pour utiliser ce script, vous devez installer les modules Azure PowerShell ainsi qu’un outil appelé openssl. Ce dernier est indispensable pour la création d’une clé SSH que vous pouvez utiliser pour administrer votre cluster ElasticSearch à distance.

Remarque : le script `CreateElasticSearchCluster` est conçu pour faciliter l’utilisation du modèle ES-MultiNode à partir d’un ordinateur Windows. Il est possible d’utiliser le modèle sur un ordinateur non Windows, mais nous n’aborderons pas ce scénario dans cet article.

1. Si vous ne l’avez pas déjà fait, installez les [**modules Azure PowerSell**](http://go.microsoft.com/fwlink/p/?linkid=320376). À l’invite, cliquez sur Exécuter, puis sur Installer.
>[AZURE.NOTE]Azure PowerShell a subi un grand changement avec la version Azure PowerShell 1.0. CreateElasticSearchCluster est actuellement conçu pour fonctionner avec Azure PowerShell 0.9.8 et ne prend pas en charge la version préliminaire de Microsoft Azure PowerShell 1.0. Un script compatible avec Azure PowerShell 1.0 sera fourni à une date ultérieure.

2. L’outil **openssl** est inclus dans la distribution de [**Git pour Windows**](http://www.git-scm.com/downloads). Si vous ne l’avez pas déjà fait, installez [Git pour Windows](http://www.git-scm.com/downloads) maintenant (en utilisant les options d’installation par défaut).

3. En supposant que Git a bien été installé mais n’a pas été inclus dans le chemin d’accès système, ouvrez la fenêtre Microsoft Azure PowerShell et exécutez les commandes suivantes :

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Remplacez `<Git installation folder>` par l’emplacement de Git sur votre ordinateur ; la valeur par défaut est *C:\\Program Files\\Git*. Notez le point-virgule au début du premier chemin d’accès.

4. Vérifiez que vous êtes connecté à Azure (via l’applet de commande [*Add-AzureAccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx)) et que vous avez sélectionné l’abonnement que vous devez utiliser pour créer votre cluster ElasticSearch ([*Select-AzureSubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Si vous ne l’avez pas déjà fait, accédez au répertoire actuel dans le dossier ES-MultiNode.

### Exécution du script CreateElasticSearchCluster
Avant d’exécuter le script, ouvrez le fichier `azuredeploy-parameters.json` et vérifiez ou renseignez les valeurs pour les paramètres du script. Les paramètres suivants sont fournis :

|Nom du paramètre |Description|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Nom qui sera utilisé pour créer le nom DNS du cluster ElasticSearch visible publiquement (en ajoutant le domaine de la région Azure au nom fourni). Par exemple, si la valeur de ce paramètre est « myBigCluster » et si la région Azure choisie est l’ouest des États-Unis, le nom DNS qui sera généré pour le cluster sera myBigCluster.westus.cloudapp.azure.com. <br /><br />Ce nom servira également de racine pour les noms de nombreux artefacts associés au cluster ElasticSearch, tels que les noms de nœuds de données.|
|storageAccountPrefix |Préfixe du ou des comptes de stockage qui seront créées pour le cluster ElasticSearch. <br /><br /> La version actuelle du modèle utilise un compte de stockage partagé, mais ceci risque de changer à l’avenir.|
|adminUsername |Nom du compte administrateur chargé de la gestion du cluster ElasticSearch (les clés SSH correspondantes seront générées automatiquement)|
|dataNodeCount |Nombre de nœuds dans le cluster ElasticSearch. La version actuelle du script ne distingue pas les nœuds de données des nœuds de requête ; tous les nœuds joueront les deux rôles.|
|dataDiskSize |Taille des disques de données (en Go) qui sera allouée pour chaque nœud de données. Chaque nœud recevra 4 disques de données, exclusivement dédiés au service ElasticSearch.|
|region |Nom de la région Azure où le cluster ElasticSearch doit se trouver.|
|esClusterName |Nom interne du cluster ElasticSearch. <br /><br />Cette valeur doit être modifiée à partir de la valeur par défaut, sauf si vous envisagez d’exécuter plusieurs clusters ElasticSearch sur le même réseau virtuel (ce qui est actuellement impossible avec le modèle ES-MultiNode).|
|esUserName esPassword |Informations d’identification de l’utilisateur qui sera configuré pour avoir accès au cluster ES (selon l’authentification de base HTTP).|

Vous êtes maintenant prêt à exécuter le script. Exécutez la commande suivante : ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
``` où `<es-group-name>` est le nom du groupe de ressources Azure qui contiendra toutes les ressources du cluster.

>[AZURE.NOTE]Si vous obtenez un NullReferenceException à partir de l’applet de commande Test-AzureResourceGroup, cela signifie que vous avez oublié d’ouvrir une session sur Azure (`Add-AzureAccount`).

Si vous obtenez une erreur d’exécution du script et que vous constatez que l’erreur est due à une valeur de paramètre de modèle incorrecte, corrigez le fichier de paramètres et réexécutez le script avec un autre nom de groupe de ressources. Vous pouvez également réutiliser le même nom de groupe de ressources et nettoyer l’ancien script en ajoutant le paramètre `-RemoveExistingResourceGroup` à l’appel du script.

### Résultat de l’exécution du script CreateElasticSearchCluster
Après avoir exécuté le script `CreateElasticSearchCluster`, les principaux artefacts suivants seront créés. Par souci de clarté, nous supposerons que vous avez utilisé 'myBigCluster' comme valeur du paramètre `dnsNameForLoadBalancerIP` et que la région où vous avez créé le cluster est l’ouest des États-Unis.

|Artefact|Nom, emplacement et remarques|
|----------------------------------|----------------------------------|
|Clé SSH d’administration à distance |Fichier myBigCluster.key (dans le répertoire à partir duquel CreateElasticSearchCluster a été exécuté). <br /><br />Il s’agit de la clé que vous pouvez utiliser pour vous connecter au nœud admin et (via le nœud admin) aux nœuds de données du cluster.|
|Nœud admin |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Il s’agit d’une machine virtuelle dédiée à l’administration à distance du cluster ElasticSearch, la seule qui autorise les connexions SSH externes. Elle s’exécute sur le même réseau virtuel que tous les nœuds du cluster ElasticSearch, mais n’exécute pas les services ElasticSearch.|
|Nœuds de données |myBigCluster1... myBigCluster *N* <br /><br />Nœuds de données qui exécutent les services ElasticSearch et Kibana. Vous pouvez vous connecter à chaque nœud en utilisant le protocole SSH, mais uniquement via le nœud admin.|
|Cluster ElasticSearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />L’URL ci-dessus représente le point de terminaison principal du cluster ElasticSearch (notez le suffixe /es). Elle est protégée par l’authentification HTTP de base (les informations d’identification ont été spécifiées par les paramètres esUserName/esPassword du modèle ES-MultiNode). Le plug-in principal (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) est également installé sur le cluster pour l’administration de base.|
|Service Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Le service Kibana est configuré pour afficher des données à partir du cluster ElasticSearch créé ; il est protégé par les mêmes informations d’identification que celles du cluster lui-même.|

## Comparaison de la collecte de traces dans le processus et hors processus
Dans l’introduction, nous avons mentionné deux méthodes fondamentales de la collecte de données de diagnostic : dans le processus et hors processus. Chacune de ces méthodes présente ses propres avantages et inconvénients.

La **collecte de traces dans le processus** comporte les avantages suivants :

1. *Simplicité de configuration et de déploiement*

    * La configuration de la collecte de données de diagnostic s’inscrit simplement dans le cadre de la configuration de l’application. Elle reste toujours facilement « synchronisé » avec le reste de l’application.

    * Vous pouvez facilement réaliser une configuration par application ou par service.

    * La capture de traces hors processus implique généralement d’effectuer séparément le déploiement et la configuration de l’agent de diagnostic, ce qui représente une tâche administrative supplémentaire source d’erreurs. La technologie d’agent n’autorise souvent qu’une seule instance de l’agent par machine virtuelle (nœud), ce qui signifie que la configuration de la collecte des traces de diagnostic est partagée entre l’ensemble des applications et services qui s’exécutent sur ce nœud.

2. *Flexibilité*

    * L’application peut envoyer les données partout où elles sont nécessaires, tant qu’il existe une bibliothèque cliente capable de prendre en charge le système de stockage ciblé. Vous pouvez ajouter de nouveaux récepteurs si vous le souhaitez.

    * Vous avez également la possibilité d’implémenter des règles de capture, de filtrage et d’agrégation de données complexes.

    * La méthode de collecte de traces hors processus est souvent limitée par les récepteurs de données pris en charge par l’agent. Certains agents sont extensibles.

3. *Accès aux données d’application internes et contexte*

    * Le sous-système de diagnostic exécuté à l’intérieur du processus d’application/service peut facilement enrichir les traces d’informations contextuelles.

    * Dans l’approche hors processus, les données doivent être envoyées à un agent via un mécanisme de communication inter-processus du type Event Tracing for Windows (ETW). Cela peut imposer des limites supplémentaires.

La **collecte de traces hors processus** comporte les avantages suivants :

1. *Possibilité de surveiller l’application et de collecter les vidages sur incident*

    * La méthode de collecte de traces dans le processus peut échouer si l’application ne parvient pas à démarrer ou si elle se bloque. Un agent indépendant a beaucoup plus de chance de capturer des informations de dépannage cruciales.<br /><br />

2. *Maturité, fiabilité et performances éprouvées*

    * Un agent développé par un fournisseur de plateformes (tels que l’agent Microsoft Azure Diagnostics) a été soumis à des tests rigoureux et difficiles.

    * Avec la méthode de collecte de traces dans le processus, il convient de veiller à ce que l’activité d’envoi de données de diagnostic à partir d’un processus d’application n’interfère pas avec les tâches principales de l’application principal et n’introduise pas de problèmes de synchronisation ou de performances. Un agent exécuté indépendamment est moins sujet à ces problèmes et est généralement conçu pour limiter l’impact sur le système.

Bien sûr, il est possible de combiner ces deux approches et de tirer parti de leurs avantages respectifs ; en effet, cette approche combinée peut être la meilleure solution pour de nombreuses applications.

Dans cet article, nous utiliserons la **bibliothèque Microsoft.Diagnostic.Listeners** et la méthode de collecte de traces dans le processus pour transférer des données entre une application Service Fabric et le cluster ElasticSearch.

## Utilisation de la bibliothèque Listeners pour l’envoi de données de diagnostic à ElasticSearch
La bibliothèque Microsoft.Diagnostic.Listeners fait partie de l’application Fabric de l’exemple de cluster Party. Pour l’utiliser :

1. Téléchargez [l’exemple de cluster Party](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) sur GitHub.

2. Copiez les projets Microsoft.Diagnostics.Listeners et Microsoft.Diagnostics.Listeners.Fabric (dossiers entiers) du répertoire de l’exemple de cluster Party dans le dossier de solution de l’application supposé envoyer les données à ElasticSearch.

3. Ouvrez la solution cible, cliquez avec le bouton droit de la souris sur le nœud de solution dans l’Explorateur de solutions et choisissez « Ajouter un projet existant ». Ajoutez le projet Microsoft.Diagnostics.Listeners à la solution. Répétez la même procédure pour le projet Microsoft.Diagnostics.Listeners.Fabric.

4. Ajoutez aux deux projets ajoutés une référence de projet à partir de vos projets de service (chaque service censé envoyer des données à ElasticSearch devrait référencer les projets Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric).

    ![Références de projet aux bibliothèques Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric][1]

### Version préliminaire de Service Fabric et du package NuGet Microsoft.Diagnostics.Tracing (novembre 2015)
Les applications générées avec la version préliminaire de Service Fabric de novembre 2015 ciblent **.NET Framework 4.5.1** car il s’agit de la version la plus récente de .NET Framework prise en charge par Azure au moment du lancement de la version préliminaire. Malheureusement, cette version du framework ne dispose pas de certaines API EventListener dont a besoin la bibliothèque Microsoft.Diagnostics.Listeners. Étant donné qu’EventSource (le composant de base des API de journalisation dans les applications Fabric) et EventListener sont étroitement couplés, chaque projet qui utilise la bibliothèque Microsoft.Diagnostics.Listeners doit utiliser une autre implémentation d’EventSource, fournie par le **package Microsoft.Diagnostics.Tracing NuGet** créé par Microsoft. Ce package est entièrement rétrocompatible avec la version d’EventSource incluse dans le framework, ce qui signifie qu’aucune modification de code n’est nécessaire à l’exception des modifications de l’espace de noms référencé.

Pour commencer à utiliser l’implémentation Microsoft.Diagnostics.Tracing de la classe EventSource, procédez comme suit pour chaque projet de service qui doit envoyer des données à ElasticSearch :

1. Cliquez avec le bouton droit sur le projet de service et sélectionnez « Gérer les packages NuGet ».

2. Basculez vers la source du package nuget.org (le cas échéant) et recherchez « Microsoft.Diagnostics.Tracing ».

3. Installez le package `Microsoft.Diagnostics.Tracing.EventSource` (et ses dépendances).

4. Ouvrez le fichier ServiceEventSource.cs ou ActorEventSource.cs dans votre projet de service et remplacez la directive `using System.Diagnostics.Tracing` sur le fichier par la directive `using Microsoft.Diagnostics.Tracing`.

Ces étapes ne seront pas nécessaires dès lors que **.NET Framework 4.6** est pris en charge par Microsoft Azure.

### Instanciation et configuration de l’écouteur ElasticSearch
La dernière étape nécessaire pour envoyer des données de diagnostic à ElasticSearch consiste à créer une instance de `ElasticSearchListener` et à la configurer avec les données de connexion ElasticSearch. L’écouteur capture automatiquement tous les événements déclenchés via les classes EventSource définies dans le projet de service. Il doit être actif pendant la durée de vie du service ; autrement dit, l’idéal est de le créer dans le code d’initialisation du service. Voici comment se présenterait le code d’initialisation d’un service sans état une fois les modifications nécessaires introduites (les ajouts sont signalés dans les commentaires commençant par `****`) :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Les données de connexion ElasticSearch doivent être placées dans une section distincte du fichier de configuration du service (PackageRoot\\Config\\Settings.xml). Le nom de la section doit correspondre à la valeur transmise au constructeur `FabricConfigurationProvider`, par exemple :

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Les valeurs de `serviceUri`, `userName` et `password` correspondent respectivement à l’adresse du point de terminaison du cluster ElasticSearch, au nom d’utilisateur et au mot de passe ElasticSearch. `indexNamePrefix` désigne le préfixe des indices ElasticSearch ; la bibliothèque Microsoft.Diagnostics.Listeners crée chaque jour un index pour ses données.

### Vérification
On y est ! Chaque fois que le service est exécuté, il commence à envoyer des traces au service ElasticSearch spécifié dans la configuration. Vous pouvez vérifier ce point en ouvrant l’interface utilisateur Kibana associée à l’instance ElasticSearch cible (dans notre exemple, l’adresse de la page serait http://myBigCluster.westus.cloudapp.azure.com/) et en vérifiant que les index comportant le préfixe de nom choisi pour l’instance `ElasticSearchListener` sont effectivement créés et alimentés par des données.

![Kibana affichant des événements d’application du cluster Party][2]

## Étapes suivantes
- [En savoir plus sur le diagnostic et la surveillance d’un service Fabric Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!----HONumber=AcomDC_1217_2015-->