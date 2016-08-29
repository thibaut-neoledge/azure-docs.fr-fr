<properties
 pageTitle="Authentification sortante de Scheduler"
 description="Authentification sortante de Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="krisragh"/>

# Authentification sortante de Scheduler

Les travaux de Scheduler peuvent avoir besoin de faire appel à des services qui requièrent une authentification. De cette façon, un service appelé peut déterminer si le travail de Scheduler peut accéder à ses ressources. Certains de ces services incluent d'autres services Azure, Salesforce.com, Facebook et des sites Web personnalisés sécurisés.

## Ajout et suppression de l'authentification

L'ajout de l'authentification à un travail de Scheduler est simple : il suffit d'ajouter un élément enfant JSON `authentication` à l'élément `request` lors de la création ou de la mise à jour d'un travail. Les secrets transmis au service de Scheduler dans une requête PUT, PATCH ou POST, dans le cadre de l'objet `authentication`, ne sont jamais retournés dans les réponses. Dans les réponses, les informations secrètes ont la valeur null ou peuvent disposer d'un jeton public qui représente l'entité authentifiée.

Pour supprimer l'authentification, exécutez PUT ou PATCH de façon explicite sur le travail, définissant l'objet `authentication` sur null. Vous ne verrez pas de propriétés d'authentification en réponse.

Actuellement, les seuls types d'authentification pris en charge sont le modèle `ClientCertificate` (pour utiliser les certificats client SSL/TLS), le modèle `Basic` (pour l'authentification de base) et le modèle `ActiveDirectoryOAuth` (pour l'authentification Active Directory OAuth).

## Corps de la requête pour l'authentification ClientCertificate

Lorsque vous ajoutez l'authentification à l'aide du modèle `ClientCertificate`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément|Description|
|:---|:---|
|_authentification (élément parent)_|Objet d'authentification pour l'utilisation d'un certificat client SSL.|
|_type_|Obligatoire. Type d'authentification. Pour les certificats client SSL, la valeur doit être `ClientCertificate`.|
|_pfx_|Obligatoire. Contenu codé en base64 du fichier PFX.|
|_mot de passe_|Obligatoire. Mot de passe pour accéder au fichier PFX.|


## Corps de la réponse pour l'authentification ClientCertificate

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'utilisation d'un certificat client SSL.|
|_type_ |Type d'authentification. Pour les certificats client SSL, la valeur est `ClientCertificate`.|
|_certificateThumbprint_ |L'empreinte numérique du certificat.|
|_certificateSubjectName_ |Le nom unique du sujet du certificat.|
|_certificateExpiration_ |La date d’expiration du certificat.|

## Exemple de requête REST pour l’authentification ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
		"headers": {
          "x-ms-version": "2013-03-01"
        },
		"authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Exemple de réponse REST pour l’authentification ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: azure.microsoft.com/-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "azure.microsoft.com/.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## Corps de la requête pour l'authentification de base

Lorsque vous ajoutez l'authentification à l'aide du modèle `Basic`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification de base.|
|_type_ |Obligatoire. Type d'authentification. Pour l'authentification de base, la valeur doit être `Basic`.|
|_nom d'utilisateur_ |Obligatoire. Nom d'utilisateur à authentifier.|
|_mot de passe_ |Obligatoire. Mot de passe à authentifier.|

## Corps de la réponse pour l'authentification de base

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification de base.|
|_type_ |Type d'authentification. Pour l'authentification de base, la valeur est `Basic`.|
|_nom d'utilisateur_ |Le nom d'utilisateur authentifié.|

## Exemple de requête REST pour l’authentification de base

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
		"headers": {
          "x-ms-version": "2013-03-01"
        },
		"authentication": {
          "type": "basic",
		  "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Exemple de réponse REST pour l’authentification de base

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: azure.microsoft.com/-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"azure.microsoft.com/.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## Corps de la requête pour l'authentification ActiveDirectoryOAuth

Lorsque vous ajoutez l'authentification à l'aide du modèle `ActiveDirectoryOAuth`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Obligatoire. Type d'authentification. Pour l'authentification ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`.|
|_locataire_ |Obligatoire. L’identificateur de client pour le client Azure AD.|
|_audience_ |Obligatoire. Cette option est définie sur https://management.core.windows.net/.|.
|_clientId_ |Obligatoire. Indiquer l'identifiant client pour l'application Azure AD.|
|_secret_ |Obligatoire. Secret du client qui demande le jeton.|

### Déterminer votre identificateur de client

Vous pouvez trouver l’identificateur de client pour le client Azure AD en exécutant `Get-AzureAccount` dans Azure PowerShell.

## Corps de la réponse pour l'authentification ActiveDirectoryOAuth

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Type d'authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`.|
|_locataire_ |L’identificateur de client pour le client Azure AD. |
|_audience_ |Cette option est définie sur https://management.core.windows.net/.|
|_clientId_ |L'identifiant client pour l'application Azure AD.|

## Exemple de requête REST pour l’authentification ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
		"headers": {
          "x-ms-version": "2013-03-01"
        },
		"authentication": {
          "tenant":"azure.microsoft.com/.onazure.microsoft.com/.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Exemple de réponse REST pour l’authentification ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: azure.microsoft.com/-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/azure.microsoft.com/.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"azure.microsoft.com/.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"azure.microsoft.com/.onazure.microsoft.com/.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.azure.microsoft.com/.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0817_2016-->