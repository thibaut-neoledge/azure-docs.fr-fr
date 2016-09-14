<properties
   pageTitle="Exceptions FabricClient courantes générées | Microsoft Azure"
   description="Décrit les exceptions et les erreurs courantes qui peuvent être générées par les API FabricClient lors des opérations de gestion des applications et des clusters."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Exceptions et erreurs courantes lorsque vous travaillez avec les API FabricClient
Les API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permettent aux administrateurs de clusters et d'applications d’effectuer des tâches administratives au niveau d’une application, d’un service ou d’un cluster Service Fabric. Par exemple : déploiement d’une application, mise à niveau, suppression, vérification de l'intégrité d’un cluster, ou test d’un service. Les développeurs d'applications et les administrateurs de clusters peuvent utiliser les API FabricClient pour développer des outils permettant de gérer le cluster et les applications Service Fabric.

Il existe de nombreux types d'opérations qui peuvent être effectuées à l'aide de FabricClient. Chaque méthode peut générer des exceptions pour les erreurs dues à une saisie incorrecte, des erreurs d'exécution ou des problèmes d'infrastructure temporaires. Consultez la documentation de référence sur les API pour identifier les exceptions générées par une méthode spécifique. Quelques exceptions peuvent cependant être générées par de nombreuses autres API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Le tableau suivant répertorie les exceptions communes aux API FabricClient.

|Exception| Générée lorsque|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|L’objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) est dans un état fermé. Supprimez l’objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) que vous utilisez et instanciez un nouvel objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|L'opération a expiré. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) est renvoyée lorsque l'opération dépasse la valeur MaxOperationTimeout pour se terminer.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/fr-FR/library/system.unauthorizedaccessexception.aspx)|La vérification d'accès pour l'opération a échoué. E\_ACCESSDENIED est renvoyée.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Une erreur d'exécution s'est produite lors de l'opération. N’importe quelle méthode FabricClient peut potentiellement générer une exception [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx) ; la propriété [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indique la cause exacte de l'exception. Les codes d'erreur sont définis dans l’énumération [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx).|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|L'opération a échoué en raison d'une condition d'erreur transitoire quelconque. Par exemple, une opération peut échouer si un quorum de réplicas est temporairement inaccessible. Les exceptions temporaires correspondent à des opérations ayant échoué et qui peuvent être relancées.|

Certaines erreurs [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) courantes peuvent être retournées dans une exception [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx) :

|Error| Condition|
|---------|:-----------|
|CommunicationError|Une erreur de communication a provoqué l’échec de l'opération, retentez l'opération.|
|InvalidCredentialType|Le type d'informations d'identification n'est pas valide.|
|InvalidX509FindType|La valeur X509FindType n'est pas valide.|
|InvalidX509StoreLocation|L’emplacement du magasin X509 n'est pas valide.|
|InvalidX509StoreName|Le nom du magasin X509 n'est pas valide.|
|InvalidX509Thumbprint|La chaîne de l’empreinte de certificat X509 n'est pas valide.|
|InvalidProtectionLevel|Le niveau de protection n'est pas valide.|
|InvalidX509Store|Le magasin de certificats X509 ne peut pas être ouvert.|
|InvalidSubjectName|Le nom d'objet n'est pas valide.|
|InvalidAllowedCommonNameList|Le format de la chaîne de la liste de noms courants n'est pas valide. Ce doit être une liste séparée par des virgules.|

<!---HONumber=AcomDC_0831_2016-->