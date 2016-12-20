---
title: "Modèle de magasin de configurations externe | Azure | Microsoft Docs"
description: "Déplacez les informations de configuration depuis le package de déploiement d’application vers un emplacement centralisé."
categories:
- design-implementation
- management-monitoring
keywords: "modèle de conception"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 03bf94a643838a3f902348d3b3ce03fa9750da2c

---
   
# <a name="external-configuration-store"></a>Magasin de configurations externes

Déplacez les informations de configuration depuis le package de déploiement d’application vers un emplacement centralisé. Ainsi, vous pouvez gérer et contrôler les données de configuration plus facilement et partager les données de configuration entre les applications et les instances d’application.

## <a name="context-and-problem"></a>Contexte et problème

La plupart des environnements d’exécution d’application contiennent des informations de configuration qui sont conservées dans les fichiers déployés avec l’application. Dans certains cas, vous pouvez modifier ces fichiers pour changer le comportement de l’application après son déploiement. Toutefois, les modifications apportées à la configuration nécessitent le redéploiement de l’application, ce qui entraîne souvent des temps d’arrêt inacceptables et autres traitements administratifs.

En outre, les fichiers de configuration locale limitent la configuration à une seule application, alors que le partage des paramètres de configuration entre plusieurs applications pourrait parfois s’avérer utile. À titre d’exemple, citons les chaînes de connexion de base de données, les informations relatives aux thèmes de l’interface utilisateur ou les URL de files d’attente et de stockage utilisées par un ensemble d’applications connexe.

Il est difficile de gérer les modifications apportées aux configurations locales sur plusieurs instances en cours d’exécution de l’application, en particulier dans un scénario de type cloud. En effet, les instances risquent d’utiliser différents paramètres de configuration durant le déploiement de la mise à jour.

En outre, les mises à jour des applications et des composants peuvent nécessiter des modifications des schémas de configuration. De nombreux systèmes de configuration ne prennent pas en charge différentes versions des informations de configuration.

## <a name="solution"></a>Solution

Stockez les informations de configuration dans un stockage externe et fournissez une interface qui peut être utilisée pour lire et mettre à jour les paramètres de configuration rapidement et efficacement. Le type de magasin externe dépend de l’environnement d’hébergement et d’exécution de l’application. Dans un scénario de type cloud, il s’agit généralement d’un service de stockage cloud, mais ce peut être une base de données hébergée ou un autre système.

Le magasin de stockage que vous choisissez pour les informations de configuration doit avoir une interface qui offre un accès cohérent et facile à utiliser. Il doit exposer les informations dans un format correctement typé et structuré. Le cas échéant, l’implémentation doit également autoriser l’accès des utilisateurs afin de protéger les données de configuration et être suffisamment flexible pour permettre le stockage de plusieurs versions de la configuration (par exemple, développement, préproduction ou production, y compris plusieurs versions Release de chacune d’elles).

>  De nombreux systèmes de configuration intégrés lisent les données quand l’application démarre et les mettent en cache en mémoire afin de les rendre rapidement accessibles et de réduire au minimum l’impact sur les performances de l’application. Selon le type de magasin de stockage utilisé et la latence de ce magasin, il peut être utile d’implémenter un mécanisme de mise en cache dans le magasin de configuration externe. Pour plus d’informations, consultez [Conseils de mise en cache](https://msdn.microsoft.com/library/dn589802.aspx). La figure illustre une vue d’ensemble du modèle de magasin de configuration externe avec le cache local facultatif.

![Vue d’ensemble du modèle de magasin de configuration externe avec le cache local facultatif](images/external-configuration-store-overview.png)


## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte les points suivants lorsque vous choisissez comment implémenter ce modèle :

Choisissez un magasin de stockage qui offre des performances acceptables, un haut niveau de disponibilité, une certaine fiabilité et qui peut être sauvegardé dans le cadre du processus de maintenance et d’administration des applications. Dans une application hébergée sur le cloud, utiliser un mécanisme de stockage cloud est généralement un bon choix pour répondre à ces exigences.

Concevez le schéma du magasin de stockage afin qu’il puisse contenir des types d’informations variés. Vérifiez qu’il satisfait à toutes les contraintes de configuration requises, telles que les données typées, les collections de paramètres, la diversité des versions de paramètres et toute autre fonctionnalité nécessaire aux applications qui l’utilisent. Le schéma doit être facile à étendre pour prendre en charge des paramètres supplémentaires selon les besoins.

Prenez en considération les capacités physiques du magasin de stockage, sa relation au mode de stockage des informations de configuration et les effets sur les performances. Par exemple, le stockage d’un document XML contenant les informations de configuration nécessite que l’interface de configuration ou l’application analyse le document afin de lire les différents paramètres. Cela rend plus compliquée la mise à jour d’un paramètre, bien que la mise en cache des paramètres puisse aider à compenser la baisse des performances de lecture.

Considérez la façon dont l’interface de configuration doit permettre de contrôler l’étendue et l’héritage des paramètres de configuration. Par exemple, il peut être nécessaire de limiter l’étendue des paramètres de configuration à l’ordinateur, à l’application et à l’organisation. Il se peut qu’elle doive prendre en charge la délégation du contrôle de l’accès à différentes étendues et interdire à des applications spécifiques de remplacer les paramètres ou les autoriser à le faire.

Assurez-vous que l’interface de configuration peut exposer les données de configuration dans les formats requis (valeurs typées, collections, paires clé/valeur, conteneurs de propriétés, entre autres). 

Considérez la façon dont l’interface du magasin de configuration doit se comporter si les paramètres contiennent des erreurs ou font défaut dans le magasin de sauvegarde. Il peut être approprié de retourner les paramètres par défaut et de consigner les erreurs. Envisagez également des aspects tels que le respect de la casse dans les clés ou les noms des paramètres de configuration, le stockage et la gestion des données binaires et la gestion des valeurs null ou vides.

Déterminez la façon dont les données de configuration doivent être protégées afin que seul l’accès aux utilisateurs et applications appropriés soit autorisé. Il s’agit probablement d’une fonctionnalité de l’interface du magasin de configuration, mais il est également nécessaire de s’assurer que les données du magasin de stockage ne sont pas directement accessibles sans l’autorisation appropriée. Mettez en place une séparation stricte entre les autorisations requises pour lire et pour écrire des données de configuration. En outre, déterminez si vous devez chiffrer tout ou partie des paramètres de configuration et la façon dont vous devez implémenter cela dans l’interface du magasin de configuration.

Les configurations stockées de manière centralisée, qui modifient le comportement de l’application pendant l’exécution, sont extrêmement importantes et doivent être déployées, mises à jour et gérées en utilisant les mêmes mécanismes que pour le déploiement du code d’une application. Par exemple, les modifications qui peuvent affecter plusieurs applications doivent être effectuées à l’aide d’un test complet et d’une approche de déploiement intermédiaire afin que la modification soit appropriée pour toutes les applications qui utilisent cette configuration. Si un administrateur modifie un paramètre pour mettre à jour une application, l’opération risque d’affecter les autres applications qui utilisent le même paramètre.

Si une application met en cache les informations de configuration, elle doit être alertée si la configuration est modifiée. Il peut être possible d’implémenter une stratégie d’expiration sur les données de configuration mises en cache afin que ces informations soient automatiquement actualisées à intervalles réguliers et que toute modification soit récupérée (et traitée en conséquence). Le [modèle de reconfiguration à l’exécution](runtime-reconfiguration.md) peut être pertinent pour votre scénario.

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

Ce modèle est utile dans les situations suivantes :

- Paramètres de configuration partagés entre plusieurs applications et instances d’application, ou quand une configuration standard doit être appliquée à plusieurs applications et instances d’application.

- Système de configuration standard qui ne prend pas en charge tous les paramètres de configuration requis, tels que le stockage d’images ou les types de données complexes.

- En tant que magasin complémentaire pour certains des paramètres des applications, avec possibilité éventuelle pour celles-ci de remplacer tout ou partie des paramètres stockés de manière centralisée.

- Comme moyen de simplifier l’administration de plusieurs applications et éventuellement pour analyser l’utilisation des paramètres de configuration en consignant tout ou partie des types d’accès au magasin de configuration. 

## <a name="example"></a>Exemple

Dans une application hébergée par Microsoft Azure, un choix classique pour stocker les informations de configuration de manière externe consiste à utiliser Stockage Azure. Cette solution est résiliente, offre des performances élevées et est répliquée trois fois avec basculement automatique pour offrir une haute disponibilité. Le stockage Table Azure fournit un magasin de clés/valeurs avec la possibilité d’utiliser un schéma flexible pour les valeurs. Le stockage Blob Azure fournit un magasin hiérarchique basé sur un conteneur, qui peut contenir n’importe quel type de données dans des objets blob nommés individuellement.

L’exemple suivant montre comment un magasin de configuration peut être implémenté sur le stockage Blob pour stocker et exposer des informations de configuration. La classe `BlobSettingsStore` extrait le stockage Blob pour stocker les informations de configuration et implémente l’interface `ISettingsStore` illustrée dans le code suivant.

>  Ce code est fourni dans le projet _ExternalConfigurationStore.Cloud_ de la solution _ExternalConfigurationStore_, disponible à partir de [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store).

```csharp
public interface IsettingsStore
{
  string Version { get; }

  Dictionary<string, string> FindAll();

  void Update(string key, string value);
} 
```

Cette interface définit des méthodes pour récupérer et mettre à jour les paramètres de configuration stockés dans le magasin de configuration et inclut un numéro de version qui peut être utilisé pour détecter si des paramètres de configuration ont été modifiés récemment. La classe `BlobSettingsStore` utilise la propriété `ETag` de l’objet blob pour implémenter le contrôle de version. La propriété `ETag` est mise à jour automatiquement chaque fois que l’objet blob est écrit.

>  Par nature, cette solution simple expose tous les paramètres de configuration en tant que valeurs de chaîne plutôt que valeurs typées.

La classe `ExternalConfigurationManager` fournit un wrapper autour d’un objet `BlobSettingsStore`. Une application peut utiliser cette classe pour stocker et récupérer des informations de configuration. Cette classe utilise la bibliothèque Microsoft [Reactive Extensions](https://msdn.microsoft.com/en-us/library/hh242985(v=vs.103).aspx) pour exposer toutes les modifications apportées à la configuration par le biais d’une implémentation de l’interface `IObservable`. Si un paramètre est modifié en appelant la méthode `SetAppSetting`, l’événement `Changed` est déclenché et tous les abonnés à cet événement sont notifiés.

Notez que tous les paramètres sont également mis en cache dans un objet `Dictionary` à l’intérieur de la classe `ExternalConfigurationManager` pour un accès rapide. La méthode `SetAppSetting` met à jour ce cache, et la méthode `GetSetting` utilisée pour récupérer un paramètre de configuration lit les données à partir du cache. Si le paramètre est introuvable dans le cache, il est récupéré de l’objet `BlobSettingsStore`.

La méthode `GetSettings` appelle la méthode `CheckForConfigurationChanges` afin de déterminer si les informations de configuration dans le stockage blob ont été modifiées. À cette fin, elle examine le numéro de version et le compare au numéro de version actuel détenu par l’objet `ExternalConfigurationManager`. Si une ou plusieurs modifications ont eu lieu, l’événement `Changed` est déclenché et les paramètres de configuration mis en cache dans l’objet `Dictionary` sont actualisés. Il s’agit d’une application du [modèle de type cache-aside](cache-aside.md).

L’exemple de code suivant montre comment sont implémentés l’événement `Changed` et les méthodes `SetAppSettings`, `GetSettings` et `CheckForConfigurationChanges` :

```csharp
public class ExternalConfigurationManager : IDisposable
{
  // An abstraction of the configuration store.
  private readonly ISettingsStore settings;
  private readonly ISubject<KeyValuePair<string, string>> changed;
  ...
  private Dictionary<string, string> settingsCache;
  private string currentVersion;
  ...
  public ExternalConfigurationManager(ISettingsStore settings, ...)
  {
    this.settings = settings;
    ...
  }
  ...
  public IObservable<KeyValuePair<string, string>> Changed
  {
    get { return this.changed.AsObservable(); }
  }
  ...
  public void SetAppSetting(string key, string value)
  {
    ...
    // Update the setting in the store.
    this.settings.Update(key, value);

    // Publish the event.
    this.Changed.OnNext(
         new KeyValuePair<string, string>(key, value));

    // Refresh the settings cache.
    this.CheckForConfigurationChanges();
  }

  public string GetAppSetting(string key)
  {
    ...
    // Try to get the value from the settings cache.  
    // If there's a miss, get the setting from the settings store.
    string value;
    if (this.settingsCache.TryGetValue(key, out value))
    {
      return value;
    }
            
    // Check for changes and refresh the cache.
    this.CheckForConfigurationChanges();

    return this.settingsCache[key];
  }
  ...
  private void CheckForConfigurationChanges()
  {
    try
    {

      // Assume that updates are infrequent. Lock to avoid
      // race conditions when refreshing the cache.
      lock (this.settingsSyncObject)
      {          {
        var latestVersion = this.settings.Version;

        // If the versions differ, the configuration has changed.
        if (this.currentVersion != latestVersion)
        {
          // Get the latest settings from the settings store and publish the changes.
          var latestSettings = this.settings.FindAll();
          latestSettings.Except(this.settingsCache).ToList().ForEach(
                                kv => this.changed.OnNext(kv));

          // Update the current version.
          this.currentVersion = latestVersion;

          // Refresh settings cache.
          this.settingsCache = latestSettings;
        }
      }
    }
    catch (Exception ex)
    {
      this.changed.OnError(ex);
    }
  }
}
```

>  La classe `ExternalConfigurationManager` fournit également une propriété nommée `Environment`. Cette propriété prend en charge différentes configurations pour une application s’exécutant dans différents environnements, par exemple préproduction et production.

Un objet `ExternalConfigurationManager` peut également interroger l’objet `BlobSettingsStore` périodiquement pour déterminer si des modifications ont eu lieu (à l’aide d’une minuterie). Les méthodes `StartMonitor` et `StopMonitor` illustrées dans l’exemple de code ci-dessous démarrent et arrêtent la minuterie. La méthode `OnTimerElapsed` s’exécute quand la minuterie expire et appelle la méthode `CheckForConfigurationChanges` pour détecter les modifications éventuelles et déclencher l’événement `Changed`, comme décrit précédemment.

```csharp
public class ExternalConfigurationManager : IDisposable
{
  ...
  private readonly ISubject<KeyValuePair<string, string>> changed;
  private readonly Timer timer;
  private ISettingsStore settings;
  ...
  public ExternalConfigurationManager(ISettingsStore settings, 
                                      TimeSpan interval, ...)
  {
    ...

    // Set up the timer.
    this.timer = new Timer(interval.TotalMilliseconds)
    {
      AutoReset = false;
    };
    this.timer.Elapsed += this.OnTimerElapsed;

    this.changed = new Subject<KeyValuePair<string, string>>();
    ...    
  }

  ...
        
  public void StartMonitor()
  {
    if (this.timer.Enabled)
    {
      return;
    }

    lock (this.timerSyncObject)
    {
      if (this.timer.Enabled)
      {
        return;
      }
      this.keepMonitoring = true;

      // Load the local settings cache.
      this.CheckForConfigurationChanges();

      this.timer.Start();
    }
  }

  public void StopMonitor()
  {
    lock (this.timerSyncObject)
    {
      this.keepMonitoring = false;
      this.timer.Stop();
    }
  }

  private void OnTimerElapsed(object sender, EventArgs e)
  {
    Trace.TraceInformation(
          "Configuration Manager: checking for configuration changes.");

    try
    {
      this.CheckForConfigurationChanges();
    }
    finally
    {
      ...
      // Restart the timer after each interval.
      this.timer.Start();
      ...
    }    
  }  
  ...
}
```

La classe `ExternalConfigurationManager` est instanciée en tant qu’instance singleton par la classe `ExternalConfiguration` illustrée ci-dessous.

```csharp
public static class ExternalConfiguration
{
  private static readonly Lazy<ExternalConfigurationManager> configuredInstance 
                            = new Lazy<ExternalConfigurationManager>(
    () =>
    {
      var environment = CloudConfigurationManager.GetSetting("environment");
      return new ExternalConfigurationManager(environment);
    });

  public static ExternalConfigurationManager Instance
  {
    get { return configuredInstance.Value; }
  }
}
```

Le code suivant provient de la classe `WorkerRole` du projet _ExternalConfigurationStore.Cloud_. Il montre comment l’application utilise la classe `ExternalConfiguration` pour lire et mettre à jour un paramètre.

```csharp
public override void Run()
{
  // Start monitoring for configuration changes.
  ExternalConfiguration.Instance.StartMonitor();

  // Get a setting.
  var setting = ExternalConfiguration.Instance.GetAppSetting("setting1");
  Trace.TraceInformation("Worker Role: Get setting1, value: " + setting);

  Thread.Sleep(TimeSpan.FromSeconds(10));

  // Update a setting.
  Trace.TraceInformation("Worker Role: Updating configuration");
  ExternalConfiguration.Instance.SetAppSetting("setting1", "new value");

  this.completeEvent.WaitOne();
}
The following code, also from the `WorkerRole` class, shows how the application subscribes to configuration events.
C#
public override bool OnStart()
{ 
  ...
  // Subscribe to the event.
  ExternalConfiguration.Instance.Changed.Subscribe(
     m => Trace.TraceInformation("Configuration has changed. Key:{0} Value:{1}", 
          m.Key, m.Value),
     ex => Trace.TraceError("Error detected: " + ex.Message));
  ...
}
```

## <a name="related-patterns-and-guidance"></a>Conseils et modèles connexes

Les informations suivantes peuvent également être pertinentes durant l’implémentation de ce modèle :

- [Modèle de reconfiguration à l’exécution](runtime-reconfiguration.md). Outre stocker les paramètres de configuration de manière externe, il est utile de pouvoir les mettre à jour et appliquer les modifications sans redémarrer l’application. Explique comment concevoir une application afin qu’elle puisse être reconfigurée sans être redéployée ou redémarrée.

- Un exemple illustrant ce modèle est disponible sur [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store).



<!--HONumber=Nov16_HO3-->


