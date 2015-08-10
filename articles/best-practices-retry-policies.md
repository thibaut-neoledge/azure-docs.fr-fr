<properties
   pageTitle="Packages NuGet | Microsoft Azure"
   description="Conseils sur les Packages NuGet pour le travail de stratégie générale de nouvelles tentatives."
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
   ms.date="04/09/2015"
   ms.author="masashin"/>

# Packages NuGet

<p class="lead">Lorsque plusieurs composants commencent à communiquer, les défaillances temporaires sont plus difficiles à gérer de manière intelligente. Le travail temporaire de gestion des pannes géré par le package NuGet de la stratégie de nouvelles tentatives peut vous permettre de gérer ces tentatives dans une seule instance.</p>

> Ce document est basé sur un projet, comme preuve de concept. Il ne s’agit pas là d’un guide officiel.

Le code `TransientFaultHandling` des modèles et pratiques est recommandé pour le travail de stratégie générale de nouvelles tentatives.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Configuration

La section comporte des informations sur la configuration de la fonctionnalité de nouvelles tentatives :

Paramètre | Description
-------------------- | ----------------------
MaximumExecutionTime | Durée d'exécution maximale de la requête, y compris toutes les tentatives potentielles.
ServerTimeOut | Intervalle du délai d’inactivité du serveur de la requête
RetryPolicy | Stratégie de nouvelles tentatives. Consultez la section Stratégies ci-dessous

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Par programme :

- Prise en charge du paramètre sur le client.
- Activation du changement d'opérations fournies par le client

Configuration de l'application :

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Stratégies

### Exponential

Utilisé pour l'espacement des nouvelles tentatives d'appels de service répétées afin d’éviter progressivement la limitation du service.

__Approche :__

Augmentez progressivement l'intervalle d’interruption entre les tentatives suivantes. Ajoutez une répartition aléatoire de (+/-20 %) à l’intervalle d’interruption afin d’éviter toute tentative simultanée de la part des clients

__Configuration :__

Paramètre | Description
-------------------- | -------------------------------------------------------
maxAttempt | Nombre de tentatives.
deltaBackoff | Intervalle d’interruption entre les tentatives. Des multiples de cette durée seront utilisés pour les tentatives suivantes.
MinBackoff | Ajouté à tous les intervalles des tentatives calculés à partir de deltaBackoff.
FastFirst | Première tentative immédiate
MaxBackoff | MaxBackoff est utilisé si l'intervalle des tentatives calculé est supérieur à MaxBackoff. Cette valeur ne peut pas être modifiée.

__Logique d’implémentation :__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Linear

Utilisé pour l'espacement des tentatives d'appels de service répétées afin d’éviter la limitation du service.

__Approche :__

Effectuez un certain nombre de tentatives en utilisant un intervalle fixe spécifique entre les tentatives. Ajoutez une répartition aléatoire de (+/-20 %) à l’intervalle d’interruption afin d’éviter toute tentative simultanée de la part des clients.

__Configuration :__

Paramètre | Description
-------------------- | -------------------------------------------------------
maxAttempt | Nombre de tentatives.
deltaBackoff | Intervalle d’interruption entre les tentatives.
FastFirst | Première tentative immédiate

__Logique d’implémentation :__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Adaptive

Utilisé pour l'espacement des tentatives d'appels de service répétées basés sur le code d'erreur/les métadonnées transmises par le service dans l'en-tête de réponse.

__Approche :__

Effectuez un certain nombre de tentatives, en utilisant un intervalle d'interruption calculé, basé sur le code d'erreur/les métadonnées transmises par le service dans l'en-tête de réponse


__Configuration :__

Non configurable

__Logique d’implémentation :__

Basé sur le code d'erreur/les métadonnées transmises par le service dans l'en-tête de réponse

__Circuit Break :__

Basé sur [Circuit Breaker](http://msdn.microsoft.com/library/dn589784.aspx)

## Extensibilité

Interface publique qui peut être implémentée pour fournir une stratégie personnalisée de nouvelles tentatives

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Télémétrie

Enregistrez les tentatives en tant qu'événements ETW à l'aide d'EventSource. Voici les champs qui doivent être enregistrés pour chaque tentative

Paramètre | Description
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "5/9/2014 22:00:13"
operationEndTime | "5/9/2014 22:00:14"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | « Le nom distant n'a pas pu être résolu : retry-guidance-tests.servicebus.windows.net’.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM »

<!---HONumber=July15_HO5-->