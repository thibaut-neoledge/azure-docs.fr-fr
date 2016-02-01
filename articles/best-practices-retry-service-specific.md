<properties
   pageTitle="Guide spécifique relatif au service de nouvelle tentative | Microsoft Azure"
   description="Guide spécifique relatif au service pour définir le mécanisme de nouvelle tentative."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="masashin"/>

# Guide spécifique relatif au service de nouvelle tentative

![](media/best-practices-retry-service-specific/pnp-logo.png)

## Vue d’ensemble

La plupart des services Azure et des kits de développement logiciel (SDK) client incluent un mécanisme de nouvelle tentative. Cependant, ceux-ci diffèrent, car chaque service a des exigences et des caractéristiques différentes, et donc chaque mécanisme de nouvelle tentative est réglé sur un service spécifique. Ce guide résume les fonctionnalités de mécanisme de nouvelle tentative pour la majorité des services Azure et inclut des informations pour vous aider à utiliser, adapter ou étendre le mécanisme de nouvelle tentative pour ce service.

Pour obtenir des instructions générales sur la gestion des erreurs temporaires, des nouvelles tentatives de connexion et des opérations en fonction des services et des ressources, consultez [Guide sur les nouvelles tentatives](best-practices-retry-general.md).

Le tableau suivant récapitule les fonctionnalités de nouvelle tentative pour les services Azure décrits dans ce guide.

| **Service** | **Fonctionnalités de nouvelle tentative** | **Configuration de la stratégie** | **Portée** | **Fonctionnalités de télémétrie** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | Native dans le client | Par programme | Opérations individuelles et du client | TraceSource |
| **[Base de données SQL avec Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)** | Native dans le client | Par programme | Globale par domaine d’application | Aucune |
| **[Base de données SQL avec ADO.NET](#sql-database-using-ado-net-retry-guidelines)** | Topaz* | Déclarative et par programme | Instructions uniques ou blocs de code | Personnalisée |
| **[Service Bus](#service-bus-retry-guidelines)** | Native dans le client | Par programme | Gestionnaire d’espace de noms, fabrique de messagerie et client | ETW |
| **[Cache](#cache-redis-retry-guidelines)** | Native dans le client | Par programme | Client | TextWriter |
| **[DocumentDB](#documentdb-pre-release-retry-guidelines)** | Native dans le service | Non configurable | Globale | TraceSource |
| **[Recherche](#search-retry-guidelines)** | Topaz* (avec stratégie de détection personnalisée) | Déclarative et par programme | Blocs de code | Personnalisée |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (avec stratégie de détection personnalisée) | Déclarative et par programme | Blocs de code | Personnalisée |
**Topaz est le nom familier donné au bloc applicatif de gestion des erreurs temporaires inclus dans <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a>. Vous pouvez utiliser une stratégie de détection personnalisée avec Topaz pour la plupart des types de services, comme décrit dans ce guide. Les stratégies par défaut pour Topaz sont indiquées dans la section [Stratégies du bloc applicatif de gestion des erreurs temporaires (Topaz)](#transient-fault-handling-application-block-topaz-strategies) à la fin de ce guide. Notez que le bloc est maintenant une infrastructure open source et n’est pas directement pris en charge par Microsoft.

> [AZURE.NOTE] Pour la plupart des mécanismes de nouvelle tentative intégrés Azure, il n’existe actuellement aucune manière d’appliquer une autre stratégie de nouvelle tentative selon le type d’erreur ou d’exception au-delà de la fonctionnalité incluse dans la stratégie de nouvelle tentative. Par conséquent, la méthode la plus adaptée au moment de l’écriture du code consiste à configurer une stratégie qui offre les performances et la disponibilité moyennes optimales. Une façon d’ajuster la stratégie consiste à analyser les fichiers journaux pour déterminer le type d’erreurs temporaires qui se produisent. Par exemple, si la plupart des erreurs sont associées à des problèmes de connectivité réseau, vous pourriez tenter immédiatement une nouvelle tentative au lieu d’attendre un certain temps la première nouvelle tentative.

## Instructions relatives aux nouvelles tentatives pour le stockage Azure

Les services de stockage Azure incluent le stockage des tables et des objets blob, les fichiers et les files d’attente de stockage.

### Mécanisme de nouvelle tentative

Les nouvelles tentatives se produisent au niveau de chaque opération REST et font partie intégrante de l’implémentation de l’API client. Le kit de développement logiciel (SDK) de stockage client utilise des classes qui implémentent l’[interface IExtendedRetryPolicy](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).

Il existe différentes implémentations de l’interface. Les clients de stockage peuvent choisir parmi les stratégies spécialement conçues pour l’accès à des tables, des objets BLOB et des files d’attente. Chaque implémentation utilise une stratégie de nouvelle tentative différente qui définit essentiellement l’intervalle avant une nouvelle tentative et d’autres détails.

Les classes intégrées prennent en charge la stratégie linéaire (délai constant) et exponentielle avec répartition aléatoire des intervalles entre chaque nouvelle tentative. Il n’existe également aucune stratégie de nouvelle tentative à utiliser lorsqu’un autre processus gère les nouvelles tentatives à un niveau supérieur. Toutefois, vous pouvez implémenter vos propres classes de nouvelle tentative si vous avez des exigences spécifiques non fournies par les classes intégrées.

Les autres alternatives passent d’un emplacement de service de stockage principal à un emplacement de service de stockage secondaire si vous utilisez le stockage géo-redondant avec accès en lecture (RA-GRS) et que le résultat de la demande est une erreur renouvelable. Pour plus d’informations, consultez [Options de redondance du stockage Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

### Configuration de la stratégie (stockage Azure)

Les stratégies de nouvelle tentative sont configurées par programme. Une procédure classique consiste à créer et remplir une instance **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, ou **QueueRequestOptions**.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

L’instance d’options de demande peut ensuite être définie sur le client et toutes les opérations avec le client utiliseront les options de demande spécifiées.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

Vous pouvez remplacer les options de demande client en transmettant une instance remplie de la classe d’options de demande en tant que paramètre aux méthodes d’opération.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Vous utilisez une instance **OperationContext** pour spécifier le code à exécuter lorsqu’une nouvelle tentative se produit et qu’une opération est terminée. Ce code peut collecter des informations sur l’opération à utiliser dans les journaux et la télémétrie

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

En plus d’indiquer si un échec donne droit à une nouvelle tentative, les stratégies de nouvelle tentative étendues renvoient un objet **RetryContext** indiquant le nombre de nouvelles tentatives, les résultats de la dernière demande, et si la tentative suivante se déroulera dans l’emplacement principal ou secondaire (voir le tableau ci-dessous pour plus d’informations). Les propriétés de l’objet **RetryContext** peuvent être utilisées pour décider de l’éventualité d’une nouvelle tentative et du moment où elle doit se dérouler. Pour plus d’informations, reportez-vous à la [méthode IExtendedRetryPolicy.Evaluate](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

Le tableau suivant présente les paramètres par défaut pour les stratégies de nouvelle tentative intégrées.

| **Contexte** | **Paramètre** | **Valeur par défaut** | **Signification** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Table/Objet Blob/Fichier<br />QueueRequestOptions | MaximumExecutionTime<br /><br />ServerTimeout<br /><br /><br /><br /><br />LocationMode<br /><br /><br /><br /><br /><br /><br />RetryPolicy | 120 secondes<br /><br />Aucun<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />ExponentialPolicy | Durée d’exécution maximale pour la demande, y compris toutes les nouvelles tentatives potentielles.<br />Intervalle du délai d’attente du serveur pour la demande (la valeur est arrondie aux secondes). Si non spécifié, il utilisera la valeur par défaut pour toutes les demandes au serveur. En règle générale, la meilleure option consiste à omettre ce paramètre afin que le serveur par défaut soit utilisé.<br />Si le compte de stockage est créé avec l’option de duplication du stockage géo-redondant avec accès en lecture (RA-GRS), vous pouvez utiliser le mode de d’emplacement pour indiquer l’emplacement devant recevoir la demande. Par exemple, si **PrimaryThenSecondary** est spécifié, les demandes sont dans un premier temps toujours envoyées vers l’emplacement principal. En cas d’échec, la demande est envoyée vers l’emplacement secondaire.<br />Voir ci-dessous pour obtenir plus d’informations sur chaque option. |
| Stratégie exponentielle | maxAttempt<br />deltaBackoff<br /><br /><br />MinBackoff<br /><br />MaxBackoff | 3<br />4 secondes<br /><br /><br />3 secondes<br /><br />30 secondes | Nombre de nouvelles tentatives.<br />Intervalle de temporisation entre les tentatives. Multiples de ce laps de temps, y compris un élément aléatoire, seront utilisés pour les tentatives suivantes.<br />Ajouté à tous les intervalles des tentatives calculés à partir de deltaBackoff. Cette valeur ne peut pas être modifiée.<br />MaxBackoff est utilisé si l’intervalle des tentatives calculé est supérieur à MaxBackoff. Cette valeur ne peut pas être modifiée. |
| Stratégie linéaire | maxAttempt<br />deltaBackoff | 3<br />30 secondes | Nombre de nouvelles tentatives.<br />Intervalle de temporisation entre les tentatives. |

### Guide d’utilisation des nouvelles tentatives
Respectez les consignes suivantes lorsque vous accédez aux services de stockage Azure à l’aide de l’API client de stockage :

* Utilisez les stratégies de nouvelle tentative intégrées de l’espace de noms Microsoft.WindowsAzure.Storage.RetryPolicies, lorsqu’elles sont adaptées à vos besoins. Dans la plupart des cas, ces stratégies suffisent.
* Utilisez la stratégie **ExponentialRetry** dans les opérations de traitement par lots, les tâches en arrière-plan ou les scénarios non interactifs. Dans ces scénarios, vous pouvez généralement accorder plus de temps à la récupération du service, ce qui par conséquent accroît les chances de réussite de l’opération.
* Vous pouvez spécifier la propriété **MaximumExecutionTime** du paramètre **RequestOptions** pour limiter la durée d’exécution totale. Prenez en compte le type et la taille de l’opération lorsque vous choisissez une valeur de délai d’attente.
* Si vous avez besoin d’implémenter une nouvelle tentative personnalisée, évitez de créer des wrappers autour des classes de client de stockage. Utilisez plutôt les fonctionnalités permettant d’étendre les stratégies existantes via l’interface **IExtendedRetryPolicy**.
* Si vous utilisez le stockage géo-redondant avec accès en lecture (RA-GRS), vous pouvez utiliser **LocationMode** pour indiquer que les nouvelles tentatives accéderont à la copie en lecture seule secondaire du magasin en cas d’échec de l’accès principal. Toutefois, lorsque vous utilisez cette option vous devez vous assurer que votre application peut fonctionner correctement avec des données qui peuvent être obsolètes si la réplication à partir du magasin principal n’est pas encore terminée.

Pensez à commencer par les paramètres suivants pour les opérations liées aux nouvelles tentatives. Il s’agit de paramètres généraux. Vous devez par conséquent surveiller les opérations et optimiser les valeurs en fonction de votre propre scénario.

| **Contexte** | **Exemple de cible E2E<br />latence max** | **Stratégie de nouvelle tentative** | **Paramètres** | **Valeurs** | **Fonctionnement** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| Interactif, interface utilisateur,<br />ou premier plan | 2 secondes | Linéaire | maxAttempt<br />deltaBackoff | 3<br />500 ms | Tentative 1 - délai 500 ms<br />Tentative 2 - délai 500 ms<br />Tentative 3 - délai 500 ms |
| Arrière-plan<br />ou lot | 30 secondes | Exponentielle | maxAttempt<br />deltaBackoff | 5<br />4 secondes | Tentative 1 - délai ~3 sec<br />Tentative 2 - délai ~7 sec<br />Tentative 3 - délai ~15 sec |

## Télémétrie

Les nouvelles tentatives sont enregistrées dans un **TraceSource**. Vous devez configurer un **TraceListener** pour capturer les événements et les écrire dans un journal de destination approprié. Vous pouvez utiliser le **TextWriterTraceListener** ou **XmlWriterTraceListener** pour écrire les données dans un fichier journal, le **EventLogTraceListener** pour écrire dans le journal des événements Windows, ou le **EventProviderTraceListener** pour écrire les données de trace dans le sous-système ETW. Vous pouvez également configurer le vidage automatique de la mémoire tampon et le niveau de détail des événements qui seront enregistrés (par exemple, erreur, avertissement, information et informations détaillées). Pour plus d’informations, consultez [Journalisation côté client avec la bibliothèque cliente de stockage .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).

Les opérations peuvent recevoir une instance **OperationContext**, qui expose un événement **Retrying** pouvant être utilisé pour associer la logique personnalisée de télémétrie. Pour plus d’informations, consultez [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

## Exemples (stockage Azure)

L’exemple de code suivant montre comment créer deux instances **TableRequestOptions** avec des paramètres de nouvelle tentative différents ; une pour les demandes interactives et une pour les demandes d’arrière-plan. L’exemple définit ensuite ces deux jeux stratégies de nouvelle tentative sur le client de sorte qu’elles s’appliquent à toutes les demandes et définit également la stratégie interactive sur une demande spécifique afin qu’elle remplace les paramètres par défaut appliqués au client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## Plus d’informations

- [Recommandations de stratégie de nouvelle tentative de Bibliothèque cliente de stockage Azure](http://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [Bibliothèque cliente de stockage 2.0 - Mise en œuvre de stratégies de nouvelle tentative](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## Instructions relatives aux nouvelles tentatives pour la base de données utilisant Entity Framework 6

La base de données SQL est une base de données SQL hébergée disponible en différentes tailles et sous forme de service standard (partagé) et premium (non partagé). Entity Framework est un mappeur relationnel objet qui permet aux développeurs .NET de travailler avec des données relationnelles à l’aide d’objets spécifiques de domaine. Il élimine le recours à la plupart du code d’accès aux données que les développeurs doivent généralement écrire.

## Mécanisme de nouvelle tentative

La prise en charge de la fonctionnalité de nouvelle tentative est assurée lors de l’accès à la base de données SQL à l’aide d’Entity Framework 6.0 et version supérieure via un mécanisme appelé [Résilience des connexions/logique de nouvelle tentative](http://msdn.microsoft.com/data/dn456835.aspx). Une spécification complète est disponible dans le [wiki .NET Entity Framework](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec) sur Codeplex. Les principales fonctionnalités de ce mécanisme sont :

* L’abstraction principale est l’interface **IDbExecutionStrategy**. Cette interface :
  * Définit les méthodes synchrones et asynchrones **Execute***.
  * Définit les classes qui peuvent être utilisées directement ou configurées sur un contexte de base de données comme une stratégie par défaut mappée sur un nom de fournisseur ou un nom de fournisseur et un nom de serveur. En cas de configuration sur un contexte, les nouvelles tentatives se produisent au niveau des opérations de base de données individuelles. Il peut y en avoir plusieurs pour une opération de contexte donnée.
  * Définit à quel moment et comment proposer une nouvelle tentative en cas d’échec de connexion.
* Il inclut plusieurs implémentations intégrées de l’interface **IDbExecutionStrategy** :
  * Par défaut : aucune nouvelle tentative.
  * Par défaut pour la base de données SQL (automatique) : aucune nouvelle tentative, mais inspecte les exceptions et y inclut la suggestion d’utiliser la stratégie de base de données SQL.
  * Par défaut pour la base de données SQL : exponentielle (héritée de la classe de base) et logique de détection de base de données SQL.
* Il implémente une stratégie de temporisation exponentielle qui inclut la répartition aléatoire.
* Les classes de nouvelle tentative intégrées sont avec état et ne sont pas thread-safe. Toutefois, elles peuvent être réutilisées une fois l’opération en cours terminée.
* Si le nombre de tentatives spécifié est dépassé, les résultats sont inclus dans une nouvelle exception. Il ne se propage pas dans l’exception en cours.

## Configuration de la stratégie (base de données SQL utilisant Entity Framework 6)

La prise en charge de la nouvelle tentative est assurée lors de l’accès à la base de données SQL à l’aide d’Entity Framework 6.0 et versions ultérieures. Les stratégies de nouvelle tentative sont configurées par programme. La configuration ne peut pas être modifiée opération par opération.

Lorsque vous configurez une stratégie sur le contexte comme stratégie par défaut, vous définissez une fonction qui crée une nouvelle stratégie à la demande. Le code suivant montre comment vous pouvez créer une classe de configuration de nouvelle tentative qui étend la classe de base **DbConfiguration**.

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

Vous pouvez alors la spécifier comme stratégie de nouvelle tentative par défaut pour toutes les opérations à l’aide de la méthode **SetConfiguration** de l’instance **DbConfiguration** lorsque l’application démarre. Par défaut, Entity Framework détecte et utilise automatiquement la classe de configuration.

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

Vous pouvez spécifier la classe de configuration de nouvelle tentative pour un contexte en annotant la classe de contexte avec un attribut **DbConfigurationType**. Toutefois, si vous avez une seule classe de configuration, Entity Framework l’utilisera sans avoir à annoter le contexte.

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

Si vous devez utiliser des stratégies de nouvelle tentative différentes pour des opérations spécifiques ou désactiver des nouvelles tentatives pour des opérations spécifiques, vous pouvez créer une classe de configuration qui vous permet de suspendre ou d’échanger des stratégies en définissant un indicateur dans le **CallContext**. La classe de configuration peut utiliser cet indicateur pour changer de stratégie ou désactiver la stratégie indiquée et utilisée comme stratégie par défaut. Pour plus d’informations, consultez [Suspendre la stratégie d’exécution](http://msdn.microsoft.com/dn307226#transactions_workarounds) dans la page de Restrictions liées aux stratégies d’exécution de nouvelle tentative (Entity Framework 6 et versions supérieures).

Une autre technique d’utilisation de stratégies de nouvelle tentative spécifiques pour des opérations individuelles consiste à créer une instance de la classe de stratégie requise et à fournir la configuration requise au moyen de paramètres. Vous pouvez ensuite faire appel à sa méthode **ExecuteAsync**.

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

La façon la plus simple d’utiliser une classe **DbConfiguration** consiste à la localiser dans le même assembly que la classe **DbContext**. Toutefois, cela n’est pas approprié lorsque le même contexte est requis dans différents scénarios, comme dans le cas de stratégies de nouvelle tentative en arrière-plan et interactives. Si les différents contextes s’exécutent dans un AppDomain séparé, vous pouvez utiliser la prise en charge intégrée pour spécifier des classes de configuration dans le fichier de configuration ou la définir explicitement à l’aide de code. Si les différents contextes doivent s’exécuter dans le même AppDomain, une solution personnalisée sera nécessaire.

Pour plus d’informations, consultez [Configuration basée sur le code (Entity Framework 6 et versions supérieures)](http://msdn.microsoft.com/data/jj680699.aspx).

Le tableau suivant présente les paramètres par défaut pour la stratégie de nouvelle tentative intégrée lors de l’utilisation d’Entity Framework 6.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lorsque vous accédez à la base de données SQL à l’aide d’Entity Framework 6 :

* Choisissez l’option de service appropriée (partagée ou premium). Une instance partagée peut être soumise à des délais de connexion plus longs que d’ordinaire et à des limitations en raison de l’utilisation du serveur partagé par d’autres locataires. Si des performances prévisibles et des opérations fiables à faible latence sont requises, pensez à choisir l’option premium.
* Une stratégie d’intervalle fixe n’est pas recommandée pour une utilisation avec la base de données SQL Azure. Utilisez plutôt une stratégie de temporisation exponentielle car le service peut être surchargé et des délais plus longs laissent davantage de temps de récupération.
* Choisissez une valeur appropriée pour les délais d’attente de connexion et de commande lors de la définition des connexions. Basez le délai d’attente à la fois sur la conception de la logique métier et sur les tests. Vous devrez peut-être modifier cette valeur au fil du temps en fonction de l’évolution des volumes de données ou des processus d’entreprise. Un délai d’attente trop court peut entraîner des échecs de connexion prématurés lorsque la base de données est occupée. Un délai d’attente trop long peut empêcher la logique de nouvelle tentative de fonctionner correctement en attendant trop longtemps avant la détection d’un échec de connexion. La valeur d’expiration est un composant de la latence de bout en bout, bien que vous ne puissiez pas aisément déterminer le nombre de commandes qui seront exécutées lors de l’enregistrement du contexte. Vous pouvez modifier le délai d’attente par défaut en définissant la propriété **CommandTimeout** de l’instance **DbContext**.
* Entity Framework prend en charge les configurations de nouvelle tentative définies dans les fichiers de configuration. Toutefois, pour une flexibilité maximale sur Azure, vous devez envisager la création de la configuration par programme au sein de l’application. Les paramètres spécifiques pour les stratégies de nouvelle tentative, tels que le nombre de nouvelles tentatives et les intervalles, peuvent être stockés dans le fichier de configuration du service et être utilisés en cours d’exécution pour créer les stratégies appropriées. Cela permet de modifier les paramètres sans avoir à redémarrer l’application.

Pensez à commencer par les paramètres suivants pour les opérations liées aux nouvelles tentatives. Vous ne pouvez pas spécifier le délai entre chaque nouvelle tentative (il est fixe et généré sous la forme d’une séquence exponentielle). Vous ne pouvez spécifier que les valeurs maximales, comme indiqué ici, sauf si vous créez une stratégie de nouvelle tentative personnalisée. Il s’agit de paramètres généraux. Vous devez par conséquent surveiller les opérations et optimiser les valeurs en fonction de votre propre scénario.

| **Contexte** | **Exemple de cible E2E<br />latence max** | **Stratégie de nouvelle tentative** | **Paramètres** | **Valeurs** | **Fonctionnement** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| Interactif, interface utilisateur,<br />ou premier plan | 2 secondes | Exponentielle | MaxRetryCount<br />MaxDelay | 3<br />750 ms | Tentative 1 - délai 0 s<br />Tentative 2 - délai 750 ms<br />Tentative 3 - délai 750 ms |
| Arrière-plan<br /> ou lot | 30 secondes | Exponentielle | MaxRetryCount<br />MaxDelay | 5<br />12 secondes | Tentative 1 - délai 0 s<br />Tentative 2 - délai ~1 s<br />Tentative 3 - délai ~3 s<br />Tentative 4 - délai ~7 s<br />Tentative 5 - délai 12 s |

> [AZURE.NOTE] Les cibles de latence de bout en bout supposent le délai d’attente par défaut pour les connexions au service. Si vous spécifiez des délais de connexion, la latence de bout en bout sera prolongée de ce temps supplémentaire pour toute nouvelle tentative.

## Exemples (base de données SQL utilisant Entity Framework 6)

L’exemple de code suivant définit une solution d’accès aux données simple qui utilise Entity Framework. Il spécifie une stratégie de nouvelle tentative spécifique par la définition de l’instance d’une classe nommée **BlogConfiguration** qui étend **DbConfiguration**.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

Vous trouverez plusieurs exemples d’utilisation du mécanisme de nouvelle tentative d’Entity Framework dans [Résilience des connexions/logique de nouvelle tentative](http://msdn.microsoft.com/data/dn456835.aspx).

## Plus d’informations

* [Guide relatif à l’élasticité et aux performances de la base de données SQL Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## Instructions relatives aux nouvelles tentatives pour la base de données SQL utilisant ADO.NET

La base de données SQL est une base de données SQL hébergée disponible en différentes tailles et sous forme de service standard (partagé) et premium (non partagé).

### Mécanisme de nouvelle tentative

Les bases de données SQL n’intègrent aucune prise en charge pour les nouvelles tentatives en cas d’accès à l’aide d’ADO.NET. Toutefois, les codes de retour issus des demandes peuvent être utilisés pour déterminer le motif d’échec de ces dernières. La page [Limitation associée à Base de données SQL Azure](http://msdn.microsoft.com/library/dn338079.aspx) explique comment la limitation peut empêcher les connexions, les codes de retour en cas de situations spécifiques, et comment vous pouvez gérer celles-ci et les opérations de nouvelle tentative.

Vous pouvez utiliser le bloc applicatif de gestion des erreurs temporaires (Topaz) avec le package Nuget EnterpriseLibrary.TransientFaultHandling.Data (classe **SqlAzureTransientErrorDetectionStrategy**) pour implémenter un mécanisme de nouvelle tentative pour la base de données SQL.

Le bloc fournit également la classe **ReliableSqlConnection**, qui implémente l’ancienne API ADO.NET 1.0 (**IDbConnection** au lieu de **DbConnection**) et effectue de nouvelles tentatives et la gestion des connexions en interne. Bien que pratique, cela requiert de votre part l’utilisation d’un ensemble de méthodes différent permettant d’appeler des opérations avec de nouvelles tentatives. Il ne s’agit pas d’un simple remplacement direct. Il ne prend pas en charge l’exécution asynchrone, qui est recommandée lors de l’implémentation et l’utilisation des services Azure. En outre, étant donné que cette classe utilise ADO.NET 1.0, elle ne bénéficie pas des dernières améliorations et mises à jour pour ADO.NET.

### Configuration de la stratégie (base de données SQL utilisant ADO.NET 1.0)

Le bloc applicatif de gestion des erreurs temporaires prend en charge la configuration basée sur les fichiers et par programme. En général, vous devez utiliser la configuration par programme pour une flexibilité maximale (voir les notes de la section suivante pour plus d’informations). Le code suivant, qui ne pourrait être exécuté qu’une seule fois au démarrage de l’application, crée et remplit un **RetryManager** avec une liste de quatre stratégies de nouvelle tentative pouvant être utilisées avec la base de données SQL Azure. Il définit également les stratégies par défaut pour le **RetryManager**. Ce sont les stratégies qui seront utilisées pour les connexions et les commandes si une alternative n’est pas spécifiée lors de la création d’une connexion ou d’une commande.

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

Pour plus d’informations sur la façon dont vous pouvez utiliser les stratégies de nouvelle tentative que vous avez configurées lorsque vous accédez à la base de données SQL Azure, consultez la section [Exemples](#examples-sql-database-using-ado-net-) ci-dessous.

Les stratégies par défaut pour le bloc applicatif de gestion des erreurs temporaires sont indiquées dans la section [Stratégies du bloc applicatif de gestion des erreurs temporaires (Topaz)](#transient-fault-handling-application-block-topaz-strategies) à la fin de ce guide.

### Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lorsque vous accédez à la base de données SQL à l’aide d’ADO.NET :

* Choisissez l’option de service appropriée (partagée ou premium). Une instance partagée peut être soumise à des délais de connexion plus longs que d’ordinaire et à des limitations en raison de l’utilisation du serveur partagé par d’autres locataires. Si des performances prévisibles et des opérations fiables à faible latence sont requises, pensez à choisir l’option premium.
* Veillez à effectuer de nouvelles tentatives au niveau ou à l’étendue qui convient pour éviter les opérations non idempotent à l’origine d’une incohérence dans les données. Idéalement, toutes les opérations doivent être idempotent afin qu’elles puissent être répétées sans entraîner d’incohérence. Lorsque cela n’est pas le cas, la nouvelle tentative doit être effectuée à un niveau ou à une étendue qui autorise l’annulation de toutes les modifications associées en cas d’échec d’une opération ; par exemple, depuis une étendue transactionnelle. Pour plus d’informations, consultez [Couche d’accès aux données de Cloud Service Fundamentals - Gestion des erreurs temporaires](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* Une stratégie d’intervalle fixe n’est pas recommandée pour une utilisation avec la base de données SQL Azure à l’exception des scénarios interactifs comportant seulement quelques nouvelles tentatives à intervalles très courts. Envisagez plutôt d’utiliser une stratégie de temporisation exponentielle pour la majorité des scénarios.
* Choisissez une valeur appropriée pour les délais d’attente de connexion et de commande lors de la définition des connexions. Un délai d’attente trop court peut entraîner des échecs de connexion prématurés lorsque la base de données est occupée. Un délai d’attente trop long peut empêcher la logique de nouvelle tentative de fonctionner correctement en attendant trop longtemps avant la détection d’un échec de connexion. La valeur du délai d’attente est un composant de la latence de bout en bout ; Il est en fait ajouté au délai de nouvelle tentative spécifié dans la stratégie de nouvelle tentative pour chaque nouvelle tentative.
* Fermez la connexion après un certain nombre de nouvelles tentatives, même lorsque vous utilisez une logique de nouvelle tentative à temporisation exponentielle et renouvelez l’opération sur une nouvelle connexion. Le fait d’effectuer la même opération à plusieurs reprises sur la même connexion peut être un facteur entraînant des problèmes de connexion. Pour voir un exemple de cette technique, consultez [Couche d’accès aux données de Cloud Service Fundamentals - Gestion des erreurs temporaires](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Lorsque le regroupement de connexions est en cours d’utilisation (par défaut), il est probable que la même connexion sera choisie à partir du pool, même après la fermeture et la réouverture d’une connexion. Si c’est le cas, une technique permettant de résoudre ce problème consiste à appeler la méthode **ClearPool** de la classe **SqlConnection** pour marquer la connexion comme n’étant pas réutilisable. Toutefois, vous ne devez effectuer cette opération qu’après l’échec de plusieurs tentatives de connexion, et uniquement lorsque vous rencontrez la classe spécifique d’erreurs temporaires, relative notamment à des délais d’attente SQL (code d’erreur -2) liés à des connexions défectueuses.
* Si le code d’accès aux données utilise des transactions lancées en tant qu’instances **TransactionScope**, la logique de nouvelle tentative doit ouvrir de nouveau la connexion et lancer une nouvelle étendue de transaction. Pour cette raison, le bloc de code renouvelable doit englober l’ensemble de la portée de la transaction.
* Le bloc applicatif de gestion des erreurs temporaires prend en charge les configurations de nouvelle tentative entièrement définies dans les fichiers de configuration. Toutefois, pour une flexibilité maximale sur Azure, vous devez envisager la création de la configuration par programme au sein de l’application. Les paramètres spécifiques pour les stratégies de nouvelle tentative, tels que le nombre de nouvelles tentatives et les intervalles, peuvent être stockés dans le fichier de configuration du service et être utilisés en cours d’exécution pour créer les stratégies appropriées. Cela permet de modifier les paramètres sans avoir à redémarrer l’application.

Pensez à commencer par les paramètres suivants pour les opérations liées aux nouvelles tentatives. Il s’agit de paramètres généraux. Vous devez par conséquent surveiller les opérations et optimiser les valeurs en fonction de votre propre scénario.

| **Contexte** | **Exemple de cible E2E<br />latence max** | **Stratégie de nouvelle tentative** | **Paramètres** | **Valeurs** | **Fonctionnement** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interactif, interface utilisateur,<br />ou premier plan | 2 secondes | FixedInterval | Nombre de tentatives<br />Intervalle avant nouvelle tentative<br />Première nouvelle tentative rapide | 3<br />500 ms<br />true | Tentative 1 - délai 0 s<br />Tentative 2 - délai 500 ms<br />Tentative 3 - délai 500 ms |
| Arrière-plan<br />ou lot | 30 secondes | ExponentialBackoff | Nombre de tentatives<br />Temporisation min<br />Temporisation max<br />Temporisation Delta<br />Première nouvelle tentative rapide | 5<br />0 s<br />60 s<br />2 s<br />false | Tentative 1 - délai 0 s<br />Tentative 2 - délai ~2 s<br />Tentative 3 - délai ~6 s<br />Tentative 4 - délai ~14 s<br />Tentative 5 - délai ~30 s |

> [AZURE.NOTE] Les cibles de latence de bout en bout supposent le délai d’attente par défaut pour les connexions au service. Si vous spécifiez des délais de connexion, la latence de bout en bout sera prolongée de ce temps supplémentaire pour toute nouvelle tentative.

### Exemples (base de données SQL utilisant ADO.NET)

Cette section décrit comment vous pouvez utiliser le bloc applicatif de gestion des erreurs temporaires pour accéder à la base de données SQL Azure à l’aide d’un ensemble de stratégies de nouvelle tentative que vous avez configuré dans le **RetryManager** (comme indiqué dans la section précédente [Configuration de la stratégie](#policy-configuration-sql-database-using-ado-net-). L’approche la plus simple concernant l’utilisation du bloc s’effectue via la classe **ReliableSqlConnection**, ou en appelant les méthodes d’extension comme **OpenWithRetry** sur une connexion (voir la section [Bloc applicatif de gestion des erreurs temporaires](http://msdn.microsoft.com/library/hh680934.aspx) pour plus d’informations).

Toutefois, dans la version actuelle du bloc applicatif de gestion des erreurs temporaires, ces approches ne prennent pas en charge localement les opérations asynchrones par rapport à la base de données SQL. Les bonnes pratiques nécessitent d’utiliser uniquement des techniques asynchrones pour accéder aux services Azure, tels que la base de données SQL. Par conséquent, vous devez envisager les techniques suivantes pour utiliser le bloc applicatif de gestion des erreurs temporaires avec la base de données SQL.

Vous pouvez utiliser la prise en charge asynchrone simplifiée dans la version 5 du langage c# pour créer des versions asynchrones des méthodes fournies par le bloc. Par exemple, le code suivant montre comment vous pouvez créer une version asynchrone de la méthode d’extension **ExecuteReaderWithRetry**. Les modifications et ajouts au code d’origine sont mis en surbrillance. Le code source pour Topaz est disponible sur GitHub au niveau du [bloc applicatif de gestion des erreurs temporaires (« Topaz »)](http://topaz.codeplex.com/SourceControl/latest).

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

Cette nouvelle méthode d’extension asynchrone peut être utilisée de la même façon que les versions synchrones incluses dans le bloc.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

Toutefois, cette approche concerne uniquement des opérations ou des commandes individuelles et non des blocs d’instructions, où des limites transactionnelles peuvent être définies. En outre, elle ne concerne pas la situation de suppression des connexions défectueuses du pool de connexions afin que celles-ci ne soient pas sélectionnées pour des tentatives ultérieures. Vous trouverez un exemple de résolution des problèmes de manière synchrone dans [Couche d’accès aux données de Cloud Service Fundamentals - Gestion des erreurs temporaires](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management). En plus de générer de nouvelles tentatives de séquences arbitraires d’instructions de base de données, cela a pour effet d’effacer le pool de connexions pour supprimer les connexions non valides et de mettre en œuvre le processus dans son intégralité. Bien que le code illustré dans cet exemple soit synchrone, il est relativement facile à convertir en code asynchrone.

### Plus d’informations

Pour obtenir des informations détaillées sur le bloc applicatif de gestion des erreurs temporaires, consultez :

* [Utilisation du bloc applicatif de gestion des erreurs temporaires avec Azure](http://msdn.microsoft.com/library/hh680899.aspx)
* [La persévérance, le secret de la réussite : utilisation du bloc applicatif de gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719.aspx)
* [Couche d’accès aux données de Cloud Service Fundamentals - Gestion des erreurs temporaires](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

Pour obtenir des instructions générales afin d’exploiter au mieux la base de données SQL, consultez :

* [Guide relatif à l’élasticité et aux performances de la base de données SQL Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [Réduction des erreurs liées au pool de connexions dans SQL Azure](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## Instructions relatives aux nouvelles tentatives de Service Bus

Service Bus est une plate-forme de messagerie cloud qui propose l’échange de messages d’une façon faiblement couplée avec une amélioration de la mise à l’échelle et de la résilience pour les composants d’une application, si cette dernière est hébergée dans le cloud ou sur site.

### Mécanisme de nouvelle tentative

Service Bus met en œuvre des nouvelles tentatives à l’aide d’implémentations de la classe de base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx). Tous les clients Service Bus exposent une propriété **RetryPolicy** qui peut être définie sur une des implémentations de la classe de base **RetryPolicy**. Les implémentations intégrées sont :

* La [classe RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Elle expose les propriétés qui contrôlent l’intervalle de temporisation, le nombre de nouvelles tentatives et la propriété **TerminationTimeBuffer** utilisée pour limiter la durée totale nécessaire pour terminer l’opération.
* La [classe NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). Elle est utilisée lorsque de nouvelles tentatives au niveau de l’API Service Bus ne sont pas requises, notamment lorsque les nouvelles tentatives sont gérées par un autre processus dans le cadre d’un lot ou d’une opération en plusieurs étapes.

Les actions de Service Bus peuvent renvoyer une plage d’exceptions, comme celles répertoriées dans [Annexe : Exceptions de messagerie](http://msdn.microsoft.com/library/hh418082.aspx). La liste fournit plus d’informations sur ces exceptions si celles-ci indiquent qu’une nouvelle tentative de l’opération est requise. Par exemple, une exception [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indique que le client doit attendre un certain temps, puis recommencer l’opération. La survenue d’une exception **ServerBusyException** fait également basculer le Service Bus vers un mode différent, dans lequel un délai supplémentaire de 10 secondes est ajouté aux délais de nouvelle tentative calculés. Ce mode est réinitialisé au bout d’une courte période.

Les exceptions renvoyées à partir du Service Bus exposent la propriété **IsTransient** qui indique si le client doit retenter l’opération. La stratégie **RetryExponential** intégrée repose sur la propriété **IsTransient** de la classe **MessagingException**, qui est la classe de base pour toutes les exceptions Service Bus. Si vous créez des implémentations personnalisées de la classe de base **RetryPolicy**, vous pouvez combiner le type d’exception et la propriété **IsTransient** pour permettre un contrôle plus précis des actions liées aux nouvelles tentatives. Par exemple, vous pouvez détecter une exception **QuotaExceededException** et prendre des mesures pour vider la file d’attente avant de tenter de lui renvoyer un message.

### Configuration de la stratégie (Service bus)

Les stratégies de nouvelle tentative sont définies par programme et peuvent être considérées comme stratégie par défaut pour un **NamespaceManager** et un **MessagingFactory**, ou individuellement pour chaque client de messagerie. Pour définir la stratégie de nouvelle tentative par défaut pour une session de messagerie, vous définissez la propriété **RetryPolicy** de l’élément **NamespaceManager**.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             maxRetryCount: 3);

Notez que ce code utilise des paramètres nommés pour plus de clarté. Vous pouvez également omettre d’indiquer les noms car aucun des paramètres n’est facultatif.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

Pour définir la stratégie de nouvelle tentative par défaut pour tous les clients créée à partir d’une fabrique de messagerie, vous définissez l’élément **RetryPolicy** de **MessagingFactory**.

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    maxRetryCount: 3);

Pour définir la stratégie de nouvelle tentative pour un client de messagerie ou remplacer sa stratégie par défaut, vous définissez sa propriété **RetryPolicy** à l’aide d’une instance de la classe de stratégie requise :

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        maxRetryCount: 3);
```

Impossible de définir la stratégie de nouvelle tentative au niveau de l’opération individuelle. Elle s’applique à toutes les opérations pour le client de messagerie. Le tableau suivant présente les paramètres par défaut pour la stratégie de nouvelle tentative intégrée.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lors de l’utilisation de Service Bus :

* Lors de l’utilisation de l’implémentation intégrée de l’élément **RetryExponential**, ne mettez pas en œuvre d’opération de secours dès que la stratégie réagit aux exceptions liées à un serveur occupé et bascule automatiquement vers un mode de nouvelle tentative approprié
* Service Bus prend en charge une fonctionnalité appelée Espaces de noms associés, qui met en œuvre le basculement automatique vers une file d’attente de sauvegarde dans un espace de noms séparé en cas d’échec de la file d’attente dans l’espace de noms principal. Les messages de la file d’attente secondaire peuvent être renvoyés à la file d’attente principale lors de la récupération Cette fonctionnalité permet de traiter les erreurs temporaires. Pour en savoir plus, consultez [Modèles de messagerie asynchrone et haute disponibilité](http://msdn.microsoft.com/library/azure/dn292562.aspx).

Pensez à commencer par les paramètres suivants pour les opérations liées aux nouvelles tentatives. Il s’agit de paramètres généraux. Vous devez par conséquent surveiller les opérations et optimiser les valeurs en fonction de votre propre scénario.


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### Télémétrie

Service Bus enregistre les nouvelles tentatives sous forme d’événements ETW à l’aide d’**EventSource**. Vous devez associer un élément **EventListener** à la source d’événement pour capturer les événements et les afficher dans la visionneuse de performances ou les écrire dans un journal de destination approprié. Vous pouvez utiliser le [bloc applicatif de journalisation sémantique](http://msdn.microsoft.com/library/dn775006.aspx) pour effectuer cette opération. Les événements de nouvelle tentative se présentent sous la forme suivante :

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### Exemples (Service bus)

L’exemple de code suivant montre comment définir la stratégie de nouvelle tentative pour :

* Un gestionnaire d’espace de noms. La stratégie s’applique à toutes les opérations sur ce gestionnaire et ne peut pas être remplacée pour des opérations individuelles.
* Une fabrique de messagerie. La stratégie s’applique à tous les clients créés à partir de cette fabrique et ne peut pas être remplacée lors de la création de clients individuels.
* Un client de messagerie individuel. Après avoir créé un client, vous pouvez définir la stratégie de nouvelle tentative pour ce client. La stratégie s’applique à toutes les opérations sur ce client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## Plus d’informations

* [Modèles de messagerie asynchrone et haute disponibilité](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## Instructions relatives aux nouvelles tentatives de cache (Redis)

Cache Redis Azure est un service de cache de faible latence et d’accès aux données rapide basé sur le cache Redis open source connu. Il est sécurisé, géré par Microsoft et accessible à partir de n’importe quelle application dans Azure.

Les instructions de cette section sont basées sur l’utilisation du client StackExchange.Redis pour accéder au cache. Vous trouverez une liste des autres clients appropriés sur le [site web de Redis](http://redis.io/clients), qui peuvent avoir des mécanismes de nouvelle tentative différents.

Notez que le client StackExchange.Redis utilise le multiplexage via une connexion unique. L’utilisation recommandée consiste à créer une instance du client au démarrage de l’application et à utiliser cette instance pour toutes les opérations sur le cache. Pour cette raison, la connexion au cache est effectuée une seule fois. Ainsi, toutes les instructions de cette section sont associées à la stratégie de nouvelle tentative pour cette connexion initiale et non pour chaque opération qui accède au cache.

### Mécanisme de nouvelle tentative

Le client StackExchange.Redis utilise une classe de gestionnaire de connexion qui est configurée par le biais d’un ensemble d’options. Ces options incluent une propriété **ConnectRetry** qui spécifie le nombre de nouvelles tentatives en cas d’échec de la connexion au cache. Toutefois, la stratégie de nouvelle tentative est utilisée uniquement pour l’action de connexion initiale et ne comprend pas de temps d’attente entre les nouvelles tentatives.

### Configuration de la stratégie (cache Redis Azure)

Les stratégies de nouvelle tentative sont configurées par programme en définissant les options pour le client avant de se connecter au cache. Cela peut être effectué en créant une instance de la classe **ConfigurationOptions**, en remplissant ses propriétés et en la transmettant à la méthode **Connect**.

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Notez que la propriété **ConnectTimeout** spécifie le temps d’attente maximal en millisecondes), pas le délai entre deux tentatives.

Vous pouvez également spécifier les options sous forme de chaîne et la transmettre à la méthode **Connect**.

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Il est également possible de spécifier directement des options lorsque vous vous connectez au cache.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

Le tableau suivant présente les paramètres par défaut pour la stratégie de nouvelle tentative intégrée.

| **Contexte** | **Paramètre** | **Valeur par défaut**<br />(v 1.0.331) | **Signification** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConfigurationOptions | ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout | 3<br /><br />5 000 ms maximum plus SyncTimeout<br />1 000 | Le nombre de répétitions de tentatives de connexion pendant l’opération de connexion initiale.<br />Délai d’attente (ms) pour les opérations de connexion. Pas un délai entre chaque tentative.<br />Temps (ms) pour permettre des opérations synchrones. |

> [AZURE.NOTE]SyncTimeout contribue à la latence de bout en bout d’une opération. Toutefois, en général, l’utilisation des opérations synchrones est déconseillée. Pour plus d’informations, consultez [Pipelines et multiplexeurs](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).

## Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lors de l’utilisation du cache Redis Azure :

* Le client StackExchange Redis gère ses propres nouvelles tentatives, mais uniquement lors de l’établissement d’une connexion au cache au premier démarrage de l’application. Vous pouvez configurer le délai de connexion et le nombre de nouvelles tentatives nécessaires pour établir cette connexion. Toutefois, la stratégie de nouvelle tentative ne s’applique pas aux opérations sur le cache.
* Le mécanisme de nouvelle tentative ne comporte aucun délai entre chaque tentative. Simplement, il réessaie une connexion ayant échoué après l’expiration du délai de connexion spécifié et le nombre de fois défini.
* Au lieu d’utiliser un grand nombre de nouvelles tentatives, envisagez une solution de secours en accédant plutôt à la source de données d’origine.

## Télémétrie

Vous pouvez collecter des informations sur les connexions (mais pas sur d’autres opérations) à l’aide d’un élément **TextWriter**.

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Vous trouverez ci-dessous un exemple de la sortie générée.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## Exemples (cache Redis Azure)

L’exemple de code suivant montre comment vous pouvez configurer le paramètre de délai de connexion et le nombre de tentatives lors de l’initialisation du client StackExchange.Redis pour accéder au cache Redis Azure au démarrage de l’application. Notez que le délai de connexion est le temps que vous êtes disposé à attendre pour vous connecter au cache. Il ne s’agit pas du délai entre chaque nouvelle tentative.

Cet exemple montre comment définir la configuration à l’aide d’une instance de la classe **ConfigurationOptions**.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Cet exemple montre comment définir la configuration en spécifiant les options sous forme de chaîne.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Pour plus d’exemples, consultez [Configuration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) sur le site web de projet.

## Plus d’informations

* [Site web Redis](http://redis.io/)

## Instructions relatives aux nouvelles tentatives pour DocumentDB (version préliminaire)

DocumentDB est une base de données de documents en tant que service entièrement gérée dotée de fonctionnalités d’indexation et de requête enrichies sur un modèle de données JSON exempt de schéma. Elle offre des performances configurables et fiables, un traitement transactionnel JavaScript natif et est conçue pour le cloud avec une mise à l’échelle élastique.

## Mécanisme de nouvelle tentative

La version préliminaire du client DocumentDB inclut un mécanisme de nouvelle tentative interne et non configurable (cela est susceptible de changer dans les versions ultérieures). Les paramètres par défaut pour ce client varient selon le contexte d’utilisation. Certaines opérations utilisent une stratégie de temporisation exponentielle avec des paramètres codés de manière irréversible. D’autres spécifient uniquement le nombre de tentatives à effectuer et utilisent le délai entre chaque nouvelle tentative de l’instance [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx) qui est renvoyé à partir du service. Un délai de cinq secondes est utilisé si aucun délai n’est spécifié.

## Configuration de la stratégie (DocumentDB)

Aucun. Toutes les classes utilisées pour implémenter les nouvelles tentatives sont internes. Les paramètres de nouvelle tentative sont des constantes ou sont définis à l’aide de paramètres pour les constructeurs de classe.

Le tableau suivant présente les paramètres par défaut pour la stratégie de nouvelle tentative intégrée.

| **Contexte** | **Paramètres** | **Valeurs** | **Fonctionnement** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (interne) | MaxRetryAttemptsOnQuery<br /><br />MaxRetryAttemptsOnRequest | 3<br /><br />0 | Nombre de nouvelles tentatives pour les requêtes de document. Cette valeur ne peut pas être modifiée.<br />Nombre de nouvelles tentatives pour les autres demandes. Cette valeur ne peut pas être modifiée. |

## Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lorsque vous utilisez DocumentDB :

* Vous ne pouvez pas modifier la stratégie de nouvelle tentative par défaut.
* Pour plus d’informations sur les paramètres par défaut, consultez [TBD].

## Télémétrie

Les nouvelles tentatives sont enregistrées sous forme de messages de trace non structurés via un **TraceSource** .NET. Vous devez configurer un **TraceListener** pour capturer les événements et les écrire dans un journal de destination approprié.

## Instructions relatives aux nouvelles tentatives pour la recherche

Azure Search permet d’ajouter des fonctionnalités de recherche puissantes et sophistiquées à un site web ou une application, d’ajuster rapidement et aisément les résultats de recherche, et de construire des modèles de classement enrichis et optimisés.

### Mécanisme de nouvelle tentative

Il n’existe aucun mécanisme de nouvelle tentative intégré, car l’utilisation classique s’effectue par le biais de demandes HTTP. Pour mettre en œuvre les nouvelles tentatives, vous pouvez utiliser une implémentation générique d’un client REST et prendre des décisions sur le moment et l’éventualité d’une nouvelle tentative en fonction de la réponse du service. Pour plus d’informations, consultez la section [Instructions générales relatives aux nouvelles tentatives et à REST](#general-rest-and-retry-guidelines) plus loin dans ce guide.

### Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lors de l’utilisation d’Azure Search :

* Utilisez le code d’état renvoyé par le service pour déterminer le type d’échec. Les codes d’état sont définis dans [Codes d’état HTTP (Azure Search)](http://msdn.microsoft.com/library/dn798925.aspx). Le code d’état 503 (Service indisponible) indique que le service est surchargé et que la demande ne peut pas être traitée immédiatement. L’action appropriée consiste à recommencer l’opération uniquement après avoir laissé le temps au service de se rétablir. Le fait de procéder à une nouvelle tentative après un délai trop court est susceptible de prolonger l’indisponibilité.
* Consultez la section [Instructions générales relatives aux nouvelles tentatives et à REST](#general-rest-and-retry-guidelines) plus loin dans ce guide pour obtenir des informations générales sur les nouvelles tentatives liées à des opérations REST.

## Plus d’informations

* [API REST Azure Search](http://msdn.microsoft.com/library/dn798935.aspx)

## Instructions relatives aux nouvelles tentatives Azure Active Directory

Azure Active Directory (AD) est une solution cloud de gestion et des accès et des identités complète qui combine des services d’annuaire essentiels, une gestion avancée des identités, des services de sécurité et une gestion des accès de l’application. Azure AD offre également aux développeurs une plate-forme de gestion des identités pour permettre un contrôle d’accès à leurs applications, en fonction d’une stratégie et de règles centralisés.

### Mécanisme de nouvelle tentative

Il n’existe aucun mécanisme intégré pour Azure Active Directory dans la bibliothèque d’authentification d’Active Directory (ADAL). Vous pouvez utiliser le bloc applicatif de gestion des erreurs temporaires pour implémenter une stratégie de nouvelle tentative qui contient un mécanisme de détection personnalisé pour les exceptions renvoyées par Active Directory.

### Configuration de la stratégie (Azure Active Directory)

Lorsque vous utilisez le bloc applicatif de gestion des erreurs temporaires avec Azure Active Directory, vous créez une instance **RetryPolicy** basée sur une classe qui définit la stratégie de détection que vous souhaitez utiliser.

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

Appelez ensuite la méthode **ExecuteAction** ou **ExecuteAsync** de la stratégie de nouvelle tentative, en transmettant l’opération que vous souhaitez exécuter.

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

La classe de stratégie de détection reçoit des exceptions lorsqu’une défaillance se produit et doit détecter s’il s’agit probablement d’une erreur temporaire ou d’un échec permanent. En général, cette opération s’effectue par un examen du type d’exception et du code d’état. Par exemple, une réponse indiquant un service indisponible signifie qu’une nouvelle tentative doit être effectuée. Le bloc applicatif de gestion des erreurs temporaires n’inclut pas de classe de stratégie de détection qui convient pour une utilisation avec le client de la bibliothèque ADAL. Un exemple de stratégie de détection personnalisée est fourni dans la section [Exemples](#examples-azure-active-directory-) ci-dessous. L’utilisation d’une stratégie de détection personnalisée est similaire à celle d’une stratégie fournie avec le bloc.

Les stratégies par défaut pour le bloc applicatif de gestion des erreurs temporaires sont indiquées dans la section [Stratégies du bloc applicatif de gestion des erreurs temporaires (Topaz)](#transient-fault-handling-application-block-topaz-strategies) à la fin de ce guide.

## Guide d’utilisation des nouvelles tentatives

Respectez les consignes suivantes lors de l’utilisation d’Azure Active Directory :

* Si vous utilisez l’API REST pour Azure Active Directory, vous devez retenter l’opération uniquement si le résultat est une erreur comprise dans la plage 5xx (par exemple, 500 Erreur interne du serveur, 502 Passerelle incorrecte, 503 Service Indisponible et 504 Dépassement du délai de la passerelle). N’effectuez pas de nouvelle tentative pour toute autre erreur.
* Si vous utilisez la bibliothèque d’authentification d’Active Directory (ADAL), les codes HTTP ne sont pas immédiatement accessibles. Vous devrez créer une stratégie de détection personnalisée qui inclut la logique de vérification des propriétés des exceptions spécifiques de la bibliothèque ADAL. Consultez la section [Exemples](#examples-azure-active-directory-) ci-dessous.
* Une stratégie de temporisation exponentielle est recommandée pour une utilisation dans des scénarios de traitement par lots avec Azure Active Directory.

Pensez à commencer par les paramètres suivants pour les opérations liées aux nouvelles tentatives. Il s’agit de paramètres généraux. Vous devez par conséquent surveiller les opérations et optimiser les valeurs en fonction de votre propre scénario.


| **Contexte** | **Exemple de cible E2E<br />latence max** | **Stratégie de nouvelle tentative** | **Paramètres** | **Valeurs** | **Fonctionnement** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interactif, interface utilisateur,<br />ou premier plan | 2 secondes | FixedInterval | Nombre de tentatives<br />Intervalle avant nouvelle tentative<br />Première nouvelle tentative rapide | 3<br />500 ms<br />true | Tentative 1 - délai 0 s<br />Tentative 2 - délai 500 ms<br />Tentative 3 - délai 500 ms |
| Arrière-plan ou <br />lot | 60 secondes | ExponentialBackoff | Nombre de tentatives<br />Temporisation min<br />Temporisation max<br />Temporisation Delta<br />Première nouvelle tentative rapide | 5<br />0 s<br />60 s<br />2 s<br />false | Tentative 1 - délai 0 s<br />Tentative 2 - délai ~2 s<br />Tentative 3 - délai ~6 s<br />Tentative 4 - délai ~14 s<br />Tentative 5 - délai ~30 s |

## Exemples (Azure Active Directory)

L’exemple de code suivant montre comment vous pouvez utiliser le bloc applicatif de gestion des erreurs temporaires (Topaz) pour définir une stratégie de détection des erreurs temporaires personnalisée utilisable avec le client ADAL. Le code crée une nouvelle instance **RetryPolicy** basée sur une stratégie de détection personnalisée de type **AdalDetectionStrategy**, comme défini dans la liste de codes ci-dessous. Les stratégies de détection personnalisées pour Topaz implémentent l’interface **ITransientErrorDetectionStrategy** et renvoient la valeur true si une nouvelle tentative doit être effectuée, ou **false** si l’échec semble ne pas être temporaire et si une nouvelle tentative ne doit pas être effectuée.

	using System;
	using System.Linq;
	using System.Net;
	using System.Threading.Tasks;
	using Microsoft.Practices.TransientFaultHandling;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

Pour plus d’informations sur les nouvelles tentatives d’opérations de l’API Active Directory Graph et les codes d’erreur renvoyés, consultez :

* [Exemple de code : logique de nouvelle tentative](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Codes d’erreur Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## Plus d’informations

* [Implémentation d’une stratégie de détection personnalisée](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [Implémentation d’une stratégie de nouvelle tentative personnalisée](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [Émission de jetons et instructions relatives aux nouvelles tentatives](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## Instructions générales relatives aux nouvelles tentatives et à REST

Prenez en compte les éléments suivants lors de l’accès aux services Azure ou tiers :

* Utilisez une approche systématique pour gérer les nouvelles tentatives, peut-être en tant que code réutilisable, afin de pouvoir appliquer une méthodologie cohérente sur tous les clients et toutes les solutions.
* Envisagez d’utiliser une infrastructure de nouvelle tentative, telle que le bloc applicatif de gestion des erreurs temporaires pour gérer les nouvelles tentatives si le service cible ou le client ne dispose d’aucun mécanisme de nouvelle tentative intégré. Cela vous permettra d’implémenter un comportement cohérent de nouvelle tentative, et peut fournir une stratégie de nouvelle tentative par défaut appropriée pour le service cible. Toutefois, vous devrez peut-être créer un code personnalisé de nouvelle tentative pour les services ne disposant pas d’un comportement standard, qui ne reposent pas sur les exceptions pour indiquer les erreurs temporaires, ou si vous souhaitez utiliser une réponse **Retry-Response** pour gérer le comportement de la nouvelle tentative.
* La logique de détection temporaire dépend de l’API client réelle utilisée pour appeler les appels REST. Certains clients, tels que la nouvelle classe **HttpClient**, ne lanceront pas d’exceptions pour les demandes terminées avec un code d’état HTTP indiquant un échec. Cela améliore les performances, mais vous empêche d’utiliser le bloc applicatif de gestion des erreurs temporaires. Dans ce cas, vous pourriez encapsuler l’appel à l’API REST avec le code qui génère des exceptions pour les codes d’état HTTP indiquant un échec, qui peuvent ensuite être traités par le bloc. Vous pouvez également utiliser un mécanisme différent pour piloter les nouvelles tentatives.
* Le code d’état HTTP renvoyé par le service peut permettre d’indiquer si l’échec est temporaire. Vous devrez peut-être examiner les exceptions générées par un client ou l’infrastructure de nouvelle tentative pour accéder au code d’état ou pour déterminer le type d’exception équivalent. Les codes HTTP suivants indiquent habituellement qu’une nouvelle tentative est appropriée :
  * 408 Délai d’expiration de la requête
  * 500 Erreur interne du serveur
  * 502 Passerelle incorrecte
  * 503 Service indisponible
  * 504 Dépassement du délai de la passerelle
* Si vous basez votre logique de nouvelle tentative sur les exceptions, ce qui suit indique généralement un échec temporaire lorsqu’aucune connexion n’a pu être établie :
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* Dans le cas d’un état de service indisponible, le service peut indiquer le délai approprié avant une nouvelle tentative dans l’en-tête de réponse **Retry-After** ou dans un en-tête personnalisé différent (comme dans le service DocumentDB). Les services peuvent également envoyer des informations supplémentaires sous la forme d’en-têtes personnalisés ou en les intégrant au contenu de la réponse. Le bloc applicatif de gestion des erreurs temporaires ne peut pas utiliser les en-têtes « retry-after » personnalisés ou standard.
* N’effectuez pas de nouvelle tentative pour les codes d’état représentant des erreurs du client (erreurs comprises dans la plage 4xx), excepté pour le code 408 Délai d’expiration de la requête.
* Testez minutieusement vos stratégies et mécanismes de nouvelle tentative sous différentes conditions, en utilisant différents états de réseau et en variant les charges du système.

## Stratégie de nouvelle tentative

Voici les types d’intervalles de stratégie de nouvelle tentative classiques :

* **Exponentielle** : une stratégie de nouvelle tentative qui effectue un nombre spécifié de tentatives en utilisant une approche de secours exponentielle répartie de manière aléatoire pour déterminer l’intervalle entre deux tentatives. Par exemple :

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **Incrémentielle** : une stratégie de nouvelle tentative avec un nombre spécifié de nouvelles tentatives et un intervalle de temps incrémentiel entre deux tentatives. Par exemple :

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **LinearRetry** : une stratégie de nouvelle tentative qui effectue un nombre spécifié de nouvelles tentatives en utilisant un intervalle fixe spécifique entre deux tentatives. Par exemple :

		retryInterval = this.deltaBackoff;

## Plus d’informations

* [Stratégies de disjoncteur](http://msdn.microsoft.com/library/dn589784.aspx)

## Stratégies du bloc applicatif de gestion des erreurs temporaires (Topaz)

Le bloc applicatif de gestion des erreurs temporaires comporte les stratégies par défaut suivantes.

| **Stratégie** | **Paramètre** | **Valeur par défaut** | **Signification** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Exponentielle** | retryCount<br />minBackoff<br /><br />maxBackoff<br /><br />deltaBackoff<br /><br />fastFirstRetry | 10<br />1 seconde<br /><br />30 secondes<br /><br />10 secondes<br /><br />true | Nombre de nouvelles tentatives.<br />Durée de temporisation minimale. La valeur supérieure ou la temporisation calculée sera utilisée comme délai entre deux tentatives.<br />Durée de temporisation minimale. La valeur inférieure ou la temporisation calculée sera utilisée comme le délai entre deux tentatives.<br />Valeur utilisée pour calculer un delta aléatoire pour le délai exponentiel entre deux tentatives.<br />Si la première nouvelle tentative est effectuée immédiatement. |
| **Incrémentielle** | retryCount<br />initialInterval<br />Incrément<br /><br />fastFirstRetry<br />| 10<br />1 seconde<br />1 seconde<br /><br />true | Nombre de nouvelles tentatives.<br />Intervalle initial qui s’applique pour la première nouvelle tentative.<br />Valeur temporelle incrémentielle qui sera utilisée pour calculer le délai progressif entre deux tentatives.<br />Si la première nouvelle tentative est effectuée immédiatement. |
| **Linéaire (intervalle fixe)** | retryCount<br />retryInterval<br />fastFirstRetry<br /> | 10<br />1 seconde<br />true | Nombre de nouvelles tentatives.<br />Délai entre deux tentatives.<br />Si la première nouvelle tentative est effectuée immédiatement. |
Pour obtenir des exemples d’utilisation du bloc applicatif de gestion des erreurs temporaires, consultez les sections Exemples plus haut dans ce guide pour une base de données SQL Azure utilisant ADO.NET et Azure Active Directory.

<!---HONumber=AcomDC_0121_2016-->