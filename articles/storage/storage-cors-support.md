---
title: Prise en charge du service Partage des ressources cross-origine (CORS) | Microsoft Docs
description: "Découvrez comment activer la prise en charge du service CORS pour les services de stockage Microsoft Azure."
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.lasthandoff: 04/06/2017


---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Prise en charge du service Partage des ressources cross-origine (CORS) pour les services Azure Storage
Depuis la version 2013-08-15, les services Azure Storage prennent en charge le partage de ressources cross-origine (CORS) pour les services Blob Storage, Table Storage, Queue Storage et File Storage. CORS est une fonctionnalité HTTP qui permet à une application web exécutée dans un domaine d'accéder aux ressources d'un autre domaine. Les navigateurs web implémentent une restriction de sécurité appelée [stratégie de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) qui empêche une page web d’appeler des API d’un autre domaine ; CORS constitue un moyen sûr pour autoriser un domaine (le domaine d’origine) à appeler des API d’un autre domaine. Pour plus d’informations sur CORS, consultez la [Spécification CORS](http://www.w3.org/TR/cors/) .

Vous pouvez définir des règles CORS individuellement pour chaque service de stockage en appelant [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) et [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx). Une fois que vous avez défini les règles CORS du service, une demande authentifiée exécutée auprès du service à partir d'un autre domaine est évaluée pour déterminer si elle est autorisée conformément aux règles que vous avez spécifiées.

> [!NOTE]
> Notez que CORS n'est pas un mécanisme d'authentification. Toute demande adressée à une ressource de stockage lorsque CORS est activé doit avoir une signature d'authentification appropriée ou doit être exécutée sur une ressource publique.
> 
> 

## <a name="understanding-cors-requests"></a>Présentation des demandes CORS
Une demande CORS d'un domaine d'origine peut comprendre deux demandes distinctes :

* Une demande préliminaire, qui interroge les restrictions CORS imposées par le service. La demande préliminaire est obligatoire, sauf si la méthode de demande est une [méthode simple](http://www.w3.org/TR/cors/), à savoir GET, HEAD ou POST.
* La demande réelle, adressée à la ressource souhaitée.

### <a name="preflight-request"></a>Demande préliminaire
La demande préliminaire interroge les restrictions CORS qui ont été établies pour le service de stockage par le propriétaire du compte. Le navigateur web (ou tout autre agent utilisateur) envoie une demande OPTIONS qui comprend les en-têtes de demande, la méthode et le domaine d'origine. Le service de stockage évalue l'opération prévue selon un ensemble préconfiguré de règles CORS qui indiquent les domaines d'origine, les méthodes de demande et les en-têtes de demande qui peuvent être spécifiés dans une demande réelle à une ressource de stockage.

Si CORS est activé pour le service et que l'une des règles CORS correspond à la demande préliminaire, le service répond avec le code d'état 200 (OK) et inclut les en-têtes Access-Control dans la réponse.

Si CORS n'est pas activé pour le service ou si aucune règle CORS ne correspond à la demande préliminaire, le service répond avec le code d'état 403 (Interdit).

Si la demande OPTIONS ne contient pas les en-têtes CORS nécessaires (en-têtes Origin et Access-Control-Request-Method), le service répond avec le code d’état 400 (Demande incorrecte).

Notez qu'une demande préliminaire est évaluée par rapport au service (Blob, de File d'attente et de Table) et non par rapport à la ressource demandée. Le propriétaire du compte doit avoir activé CORS dans le cadre des propriétés du service de compte pour que la demande réussisse.

### <a name="actual-request"></a>Demande réelle
Une fois la demande préliminaire acceptée et la réponse retournée, le navigateur envoie la demande réelle sur la ressource de stockage. Le navigateur refuse immédiatement la demande réelle si la demande préliminaire est rejetée.

La demande réelle est traitée comme une demande normale au service de stockage. La présence de l'en-tête Origin indique que la demande est une demande CORS et que le service vérifiera les règles CORS correspondantes. Si une correspondance est trouvée, les en-têtes Access-Control sont ajoutés à la réponse et renvoyés au client. Si aucune correspondance n'est trouvée, les en-têtes Access-Control CORS ne sont pas renvoyés.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Activation de CORS pour les services de stockage Azure
Les règles CORS sont définies au niveau du service. Par conséquent, vous devez activer ou désactiver CORS pour chaque service (Blob, de File d'attente et de Table) séparément. Par défaut, CORS est désactivé pour tous les services. Pour activer CORS, vous devez définir les propriétés de service appropriées à l'aide de la version du 15/08/2013 ou version ultérieure, et ajouter des règles CORS aux propriétés de service. Pour plus d’informations sur l’activation ou la désactivation de CORS pour un service et sur la définition de règles CORS, consultez [Définition des propriétés du service Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Définition des propriétés du service de Table](https://msdn.microsoft.com/library/hh452232.aspx) et [Définition des propriétés du service de File d’attente](https://msdn.microsoft.com/library/hh452240.aspx).

Voici un exemple de règle CORS spécifiée via une opération Set Service Properties :

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Chaque élément inclus dans la règle CORS est décrit ci-dessous :

* **AllowedOrigins**: domaines d’origine qui sont autorisés à effectuer une demande auprès du service de stockage via CORS. Le domaine d'origine est celui d'où provient la demande. Notez que l'origine doit correspondance exactement (avec respect de la casse) à l'origine que l'utilisateur envoie au service. Vous pouvez également utiliser le caractère générique « * » pour autoriser tous les domaines d'origine à effectuer des demandes via CORS. Dans l’exemple ci-dessus, les domaines [http://www.contoso.com](http://www.contoso.com) et [http://www.fabrikam.com](http://www.fabrikam.com) peuvent envoyer des demandes au service à l’aide de CORS.
* **AllowedMethods**: méthodes (verbes de requête HTTP) que le domaine d’origine peut utiliser pour une demande CORS. Dans l'exemple ci-dessus, seules les demandes PUT et GET sont autorisées.
* **AllowedHeaders**: en-têtes de demande que le domaine d’origine peut spécifier dans la demande CORS. Dans l'exemple ci-dessus, tous les en-têtes de métadonnées commençant par x-ms-meta-data, x-ms-meta-target et x-ms-meta-abc sont autorisés. Notez que le caractère générique « * » indique que les en-têtes commençant par le préfixe spécifié sont autorisés.
* **ExposedHeaders**: en-têtes de réponse qui peuvent être envoyés dans la réponse à la demande CORS et exposés par le navigateur à l’émetteur de la demande. Dans l'exemple ci-dessus, il est demandé au navigateur d'exposer les en-têtes commençant par x-ms-meta.
* **MaxAgeInSeconds**: durée maximale pendant laquelle un navigateur doit mettre en cache la demande OPTIONS préliminaire.

Les services de stockage Azure prennent en charge la spécification d’en-têtes préfixés pour les éléments **AllowedHeaders** et **ExposedHeaders**. Pour autoriser une catégorie d'en-têtes, vous pouvez spécifier un préfixe commun à cette catégorie. Par exemple, le fait de spécifier *x-ms-meta** comme en-tête préfixé crée une règle qui établit une correspondance avec tous les en-têtes commençant par x-ms-meta.

Les limitations suivantes s'appliquent aux règles CORS :

* Vous pouvez spécifier jusqu'à cinq règles CORS par service de stockage (Blob, Table et File d'attente).
* La taille maximale de tous les paramètres de règles CORS dans la demande, à l'exception des balises XML, ne doit pas dépasser 2 Ko.
* La longueur d'un en-tête autorisé, d'un en-tête exposé ou d'une origine autorisée ne doit pas dépasser 256 caractères.
* Les en-têtes autorisés et les en-têtes exposés peuvent être :
  * des en-têtes littéraux, où le nom exact de l’en-tête est spécifié, tel que **x-ms-meta-processed**. Il ne peut pas y avoir plus de 64 en-têtes littéraux spécifiés sur la demande ;
  * des en-têtes préfixés, où un préfixe de l’en-tête est spécifié, tel que **x-ms-meta-data**. Le fait de spécifier un préfixe de cette façon autorise ou expose tout en-tête qui commence par le préfixe donné. Il ne peut pas y avoir plus de deux en-têtes préfixés spécifiés sur la demande.
* Les méthodes (ou verbes HTTP) spécifiées dans l’élément **AllowedMethods** doivent respecter les méthodes prises en charge par les API de service de stockage Azure. Les méthodes prises en charge sont DELETE, GET, HEAD, MERGE, POST, OPTIONS et PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Présentation de la logique d'évaluation des règles CORS
Quand un service de stockage reçoit une demande préliminaire ou réelle, il évalue cette demande en fonction des règles CORS que vous avez créées pour le service via l'opération Set Service Properties appropriée. Les règles CORS sont évaluées dans l'ordre dans lequel elles ont été définies dans le corps de la demande de l'opération Set Service Properties.

Les règles CORS sont évaluées comme suit :

1. Tout d’abord, le domaine d’origine de la demande est vérifié par rapport aux domaines répertoriés pour l’élément **AllowedOrigins** . Si le domaine d'origine figure dans la liste ou si tous les domaines sont autorisés avec le caractère générique « * », l'évaluation des règles continue. Si le domaine d'origine ne figure pas dans la liste, la demande échoue.
2. Ensuite, la méthode (ou le verbe HTTP) de la demande est comparée aux méthodes répertoriées dans l’élément **AllowedMethods** . Si la méthode figure dans la liste, l'évaluation des règles continue ; sinon, la demande échoue.
3. Si la demande correspond à une règle dans son domaine d'origine et sa méthode, cette règle est sélectionnée pour traiter la demande et aucune autre règle n'est évaluée. Cependant, avant que la demande réussisse, tous les en-têtes spécifiés dans la demande sont vérifiés par rapport aux en-têtes répertoriés dans l’élément **AllowedHeaders** . Si les en-têtes envoyés ne correspondent pas aux en-têtes autorisés, la demande échoue.

Étant donné que les règles sont traitées dans l'ordre dans lequel elles sont indiquées dans le corps de la demande, il est recommandé de spécifier les règles les plus restrictives en ce qui concerne les origines en premier dans la liste, afin qu'elles soient évaluées en priorité. Spécifiez les règles les moins restrictives (par exemple, une règle pour autoriser toutes les origines) à la fin de la liste.

### <a name="example--cors-rules-evaluation"></a>Exemple : évaluation de règles CORS
L'exemple suivant illustre un corps de demande partiel pour une opération visant à définir des règles CORS pour les services de stockage. Pour plus d’informations sur la construction de la requête, consultez [Définition des propriétés du service Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Définition des propriétés du service de File d’attente](https://msdn.microsoft.com/library/hh452232.aspx) et [Définition des propriétés du service de Table](https://msdn.microsoft.com/library/hh452240.aspx).

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Ensuite, tenez compte des demandes CORS suivantes :

| Demande |  |  | Réponse |  |
| --- | --- | --- | --- | --- |
| **Méthode** |**Origine** |**En-têtes de requête** |**Correspondance de règle** |**Résultat** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Première règle |Succès |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Deuxième règle |Succès |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Deuxième règle |Échec |

La première demande correspond à la première règle (le domaine d'origine correspond aux origines autorisées, la méthode correspond aux méthodes autorisées et l'en-tête correspond aux en-têtes autorisés). Par conséquent, elle réussit.

La deuxième demande ne correspond pas à la première règle, car la méthode ne correspond pas aux méthodes autorisées. Toutefois, elle correspond à la deuxième règle, donc elle réussit.

La troisième demande correspond à la deuxième règle en ce qui concerne le domaine d'origine et la méthode, donc aucune autre règle n'est évaluée. Toutefois, l’ *en-tête x-ms-client-request-id* n’étant pas autorisé par la deuxième règle, la demande échoue en dépit du fait que la sémantique de la troisième règle lui aurait permis de réussir.

> [!NOTE]
> Bien que cet exemple illustre une règle moins restrictive avant une règle plus restrictive, il est généralement recommandé de répertorier les règles les plus restrictives en premier.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Présentation de la définition de l'en-tête Vary
L’en-tête *Vary* est un en-tête HTTP/1.1 standard composé d’un ensemble de champs d’en-tête de demande qui indiquent au navigateur ou à l’agent utilisateur les critères sélectionnés par le serveur pour traiter la demande. L’en-tête *Vary* est principalement utilisé pour la mise en cache par les proxys, les navigateurs et les réseaux de distribution de contenu, qui s’en servent pour déterminer comment la réponse doit être mise en cache. Pour plus d’informations, consultez la spécification de l’ [en-tête Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Lorsque le navigateur ou un autre agent utilisateur met en cache la réponse d'une demande CORS, le domaine d'origine est mis en cache comme origine autorisée. Quand un deuxième domaine émet la même demande pour une ressource de stockage pendant que le cache est actif, l'agent utilisateur récupère le domaine d'origine mis en cache. Comme le deuxième domaine ne correspond pas au domaine mis en cache, la demande échoue (alors qu'elle devrait réussir en d'autres circonstances). Dans certains cas, le stockage Azure affecte à l’en-tête Vary la valeur **Origin** pour indiquer à l’agent utilisateur qu’il doit envoyer la demande CORS suivante au service lorsque le domaine demandeur diffère de l’origine mise en cache.

Le stockage Azure affecte à l’en-tête *Vary* la valeur **Origin** pour les demandes GET/HEAD réelles dans les cas suivants :

* Lorsque l'origine de la demande correspond exactement à l'origine autorisée définie par une règle CORS. Pour qu’il s’agisse d’une correspondance exacte, la règle CORS ne doit pas inclure le caractère générique « * ».
* Il n'y a aucune règle correspondant à l'origine de la demande, mais CORS est activé pour le service de stockage.

Si une demande GET/HEAD correspond à une règle CORS qui autorise toutes les origines, la réponse indique que toutes les origines sont autorisées et le cache de l'agent utilisateur autorise les demandes suivantes à partir de n'importe quel domaine d'origine pendant que le cache est actif.

Notez qu'en ce qui concerne les demandes à l'aide de méthodes autres que GET/HEAD, les services de stockage ne définissent pas l'en-tête Vary, car les réponses à ces méthodes ne sont pas mises en cache par les agents utilisateur.

Le tableau suivant indique comment le stockage Azure répond aux demandes GET/HEAD en fonction des cas mentionnés précédemment :

| Demande | Paramètre de compte et résultat de l'évaluation de la règle |  |  | Réponse |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **En-tête d’origine présent dans la demande** |**Règle(s) CORS spécifiée(s) pour ce service** |**Règle de correspondance existante qui autorise toutes les origines(*)** |**Règle de correspondance existante pour la correspondance exacte d’origine** |**La réponse inclut l’en-tête Vary avec la valeur Origin** |**La réponse inclut l’en-tête Access-Control-Allowed-Origin : «*  »** |**La réponse inclut l’en-têteAccess-Control-Exposed-Headers** |
| Non |Non |Non |Non |Non |Non |Non |
| Non |Oui |Non |Non |Oui |Non |Non |
| Non |Oui |Oui |Non |Non |Oui |Oui |
| Oui |Non |Non |Non |Non |Non |Non |
| Oui |Oui |Non |Oui |Oui |Non |Oui |
| Oui |Oui |Non |Non |Oui |Non |Non |
| Oui |Oui |Oui |Non |Non |Oui |Oui |

## <a name="billing-for-cors-requests"></a>Facturation des demandes CORS
Les demandes préliminaires ayant abouti sont facturées si vous avez activé CORS pour l’un des services de stockage de votre compte (en appelant [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) ou [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx)). Pour réduire les coûts, attribuez une valeur plus élevée à l’élément **MaxAgeInSeconds** dans vos règles CORS, de façon que l’agent utilisateur mette en cache la demande.

Les demandes préliminaires infructueuses ne seront pas facturés.

## <a name="next-steps"></a>Étapes suivantes
[Définition des propriétés du service Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Définition des propriétés du service de File d’attente](https://msdn.microsoft.com/library/hh452232.aspx)

[Définition des propriétés du service de Table](https://msdn.microsoft.com/library/hh452240.aspx)

[Spécification du Partage des ressources cross-origin (W3C)](http://www.w3.org/TR/cors/)


