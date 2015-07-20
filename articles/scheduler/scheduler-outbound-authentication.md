<properties 
 pageTitle="Authentification sortante de Scheduler" 
 description="" 
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
 ms.date="05/12/2015" 
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

## Exemple de requête et de réponse pour l'authentification ClientCertificate

L'exemple de requête suivant effectue une requête PUT qui incorpore l'authentification `ClientCertificate`. La requête est la suivante :


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Une fois que cette requête est envoyée, la réponse est la suivante :

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
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

## Exemple de requête et de réponse pour l'authentification de base

L'exemple de requête suivant effectue une requête PUT qui incorpore l'authentification `Basic`. La requête est la suivante :

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Une fois que cette requête est envoyée, la réponse est la suivante :

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Corps de la requête pour l'authentification ActiveDirectoryOAuth

Lorsque vous ajoutez l'authentification à l'aide du modèle `ActiveDirectoryOAuth`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Obligatoire. Type d'authentification. Pour l'authentification ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`.|
|_locataire_ |Obligatoire. L'identifiant du locataire est un ID qui est utilisé pour identifier le locataire Active Directory.|
|_audience_ |Obligatoire. Cette option est définie sur https://management.core.windows.net/.|.
|_clientId_ |Obligatoire. Indiquer l'identifiant client pour l'application Azure AD.|
|_secret_ |Obligatoire. Secret du client qui demande le jeton.|

## Corps de la réponse pour l'authentification ActiveDirectoryOAuth

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Type d'authentification. Pour l'authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`.|
|_locataire_ |L'identifiant du locataire est utilisé pour identifier le locataire Active Directory.|
|_audience_ |Cette option est définie sur https://management.core.windows.net/.|.
|_clientId_ |L'identifiant client pour l'application Azure AD.|

## Exemple de requête et de réponse pour l'authentification ActiveDirectoryOAuth

L'exemple de requête suivant effectue une requête PUT qui incorpore l'authentification `ActiveDirectoryOAuth`. La requête est la suivante :

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Une fois que cette requête est envoyée, la réponse est la suivante :

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Voir aussi
 
 [Présentation d'Azure Scheduler](scheduler-intro.md)
 
 [Concepts, terminologie et hiérarchie d'entités de Scheduler](scheduler-concepts-terms.md)
 
 [Prise en main de Scheduler dans le portail de gestion](scheduler-get-started-portal.md)
 
 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)
 
 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler](scheduler-advanced-complexity.md)
 
 [Informations de référence sur l'API REST de Scheduler](https://msdn.microsoft.com/library/dn528946)
 
 [Informations de référence sur les applets de commande PowerShell de Scheduler](scheduler-powershell-reference.md)
 
 [Haute disponibilité et fiabilité de Scheduler](scheduler-high-availability-reliability.md)
 
 [Limites, valeurs par défaut et codes d'erreur de Scheduler](scheduler-limits-defaults-errors.md)
 
  

<!---HONumber=July15_HO2-->