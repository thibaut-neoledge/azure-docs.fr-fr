<properties 
	pageTitle="Configuration des chaînes de connexion Azure" 
	description="Découvrez comment configurer les chaînes de connexion à un compte de stockage dans Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Configuration des chaînes de connexion Azure

## Vue d'ensemble
Une chaîne de connexion contient les paramètres nécessaires pour accéder à votre compte de stockage dans Azure. Vous pouvez configurer une chaîne de connexion des manières suivantes :

- en vous connectant à l'émulateur de stockage Azure lors du test local de votre service ou application ;

- en vous connectant à un compte de stockage dans Azure à l'aide des points de terminaison par défaut des services de stockage ;

- en vous connectant à un compte de stockage dans Azure à l'aide des points de terminaison explicites des services de stockage.

Si votre application est un service cloud exécuté dans Azure, l'emplacement le plus pratique pour stocker votre chaîne de connexion est le [Schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Si votre application s'exécute dans un autre environnement (par exemple sur le Bureau), vous allez probablement stocker votre chaîne de connexion dans un fichier app.config ou un autre fichier de configuration. Vous pouvez utiliser la classe Azure CloudConfigurationManager pour accéder à la chaîne de connexion au moment de l'exécution, quel que soit son environnement d'exécution.

##Connexion à l'émulateur de stockage

L'émulateur de stockage est un compte local avec un nom et une clé connus. Le nom et la clé du compte étant identiques pour tous les utilisateurs, vous pouvez utiliser un format de chaîne raccourci pour faire référence à l'émulateur de stockage dans une chaîne de connexion. Affectez  `UseDevelopmentStorage=true` comme valeur de la chaîne de connexion.

Vous pouvez aussi spécifier un proxy HTTP à utiliser lorsque vous testez votre service sur l'émulateur de stockage. Cela peut être utile pour observer les demandes et les réponses HTTP pendant que vous déboguez des opérations sur les services de stockage. Pour spécifier un proxy, ajoutez l'option DevelopmentStorageProxyUri à la chaîne de connexion, puis définissez sa valeur sur l'URI du proxy. Voici par exemple une chaîne de connexion qui pointe vers l'émulateur de stockage et configure un proxy HTTP :

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

##Connexion à un compte de stockage dans Azure

Vous pouvez définir une chaîne de connexion sur un compte de stockage dans Azure de l'une des manières suivantes :

- Nous partons du principe que nous utilisons les points de terminaison par défaut pour les services de stockage. Il s'agit de l'option la plus simple pour la création d'une chaîne de connexion. Quand vous utilisez ce format de chaîne de connexion, vous devez 

- spécifier des points de terminaison explicites pour les services de stockage. Cette option vous permet de créer une chaîne de connexion plus complexe. Quand vous utilisez ce format de chaîne, vous pouvez spécifier des points de terminaison de service de stockage qui incluent un nom de domaine personnalisé ou réduire l'exposition des informations pour une chaîne de connexion basée sur une signature d'accès partagé.


> [AZURE.NOTE] Les services de stockage Azure prennent en charge HTTP et HTTPS, mais nous vous conseillons vivement d'utiliser HTTPS.

##Création d'une chaîne de connexion avec les points de terminaison par défaut

Pour créer une chaîne de connexion qui utilise les points de terminaison par défaut pour le service de stockage, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTP ou HTTPS, remplacez myAccountName par le nom de votre compte de stockage et remplacez myAccountKey par votre clé d'accès au compte :

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Par exemple, votre chaîne de connexion doit ressembler à l'exemple de chaîne de connexion suivant :

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

##Création d'une chaîne de connexion avec des points de terminaison explicites

Vous pouvez spécifier explicitement les points de terminaison du service dans votre chaîne de connexion dans les circonstances suivantes :

- Vous avez enregistré un nom de domaine personnalisé pour votre compte de stockage avec le service BLOB.

- Vous avez une signature d'accès partagé pour l'accès aux ressources de stockage.

###Spécification d'un point de terminaison d'objet blob avec un nom de domaine personnalisé 
Si vous avez enregistré un nom de domaine personnalisé utilisable avec le service BLOB, vous souhaiterez peut-être configurer explicitement le point de terminaison d'objet blob dans votre chaîne de connexion. La valeur du point de terminaison indiquée dans la chaîne de connexion est utilisée pour construire les URI de demande au service BLOB. Elle indique la forme de tous les URI renvoyés à votre code.  

Pour créer une chaîne de connexion qui spécifie des points de terminaison explicites, spécifiez le point de terminaison complet pour chaque service, y compris la spécification de protocole (HTTP ou HTTPS) à l'aide du format suivant :

    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

Quand vous spécifiez explicitement des points de terminaison de service, vous avez le choix entre deux options pour spécifier les informations d'identification. Vous pouvez spécifier le nom et la clé du compte (`AccountName=myAccountName;AccountKey=myAccountKey`), comme indiqué dans la section précédente, ou vous pouvez **spécifier une signature d'accès partagé**, comme indiqué dans la section Spécification de points de terminaison avec une signature d'accès partagé. Si vous spécifiez le nom et la clé du compte, le format de chaîne complet est le suivant :
    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

Vous pouvez spécifier des points de terminaison pour un objet blob, une table et une file d'attente dans une chaîne de connexion. Vous devez spécifier au moins un point de terminaison, mais vous n'avez pas besoin de spécifier les trois. Par exemple, si vous créez une chaîne de connexion pour un point de terminaison personnalisé d'objet blob, la spécification des points de terminaison de file d'attente et de table est facultative. Notez que si vous choisissez de ne pas indiquer les points de terminaison de file d'attente et de table à partir de la chaîne de connexion, vous ne serez pas en mesure d'accéder aux services de Table et de File d'attente à partir de votre code à l'aide de cette chaîne de connexion.

###Spécification de points de terminaison avec une signature d'accès partagé 
Vous pouvez créer une chaîne de connexion avec des points de terminaison explicites pour accéder aux ressources de stockage via une signature d'accès partagé.  Dans ce cas, vous pouvez spécifier la signature d'accès partagé dans le cadre de la chaîne de connexion, au lieu des informations d'identification de nom et de clé de compte. Le jeton de signature d'accès partagé encapsule des informations sur la ressource à laquelle vous devez accéder, la durée pendant laquelle elle est disponible et les autorisations accordées. Pour plus d'informations sur les signatures d'accès partagé, consultez [Délégation d'accès avec une signature d'accès partagé.](https://msdn.microsoft.com/library/ee395415.aspx).

Pour créer une chaîne de connexion incluant une signature d'accès partagé, spécifiez la chaîne au format suivant :

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

Le point de terminaison peut être le point de terminaison de service par défaut ou un point de terminaison personnalisé.  `base64Signature` correspond à la partie signature d'une signature d'accès partagé. La signature est un HMAC calculé sur une chaîne de signature et une clé valides à l'aide de l'algorithme SHA256, qui est ensuite encodé en Base64.

<!--HONumber=47-->
