---
title: "Créer une image Docker pour la gestion des modèles dans Azure Machine Learning | Microsoft Docs"
description: "Ce document décrit les étapes à suivre pour créer une image Docker pour votre service web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4c4391cecaf10428b5d4cacf3b39e6a08d417053
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Informations de référence sur l’API de compte de gestion des services Azure Machine Learning

Pour plus d’informations sur la configuration de l’environnement de déploiement, consultez [Configuration d’un compte de gestion des modèles](model-management-configuration.md).

L’API Gestion des modèles Azure Machine Learning implémente les opérations suivantes :

- Inscription de modèles
- Création de manifestes
- Création d’images Docker
- Création de services web

Vous pouvez utiliser cette image pour créer un service web localement ou sur un cluster ACS distant ou dans un autre environnement compatible avec Docker.

## <a name="prerequisites"></a>Prérequis
Veillez à parcourir les étapes d’installation dans le document [Guide de démarrage rapide d’installation et de création](quickstart-installation.md).

Avant de poursuivre, vous devez disposer des éléments suivants :
1. Compte de gestion des modèles provisionné
2. Environnement créé pour le déploiement et la gestion des modèles
3. Modèle d’apprentissage automatique

### <a name="aad-token"></a>Jeton AAD
Quand vous utilisez l’interface CLI, connectez-vous à l’aide de `az login`. L’interface CLI utilise votre jeton AAD à partir du fichier .azure. Si vous voulez utiliser les API, voici les possibilités qui s’offrent à vous :

##### <a name="acquiring-the-aad-token-manually"></a>Acquisition du jeton AAD manuellement
Vous pouvez utiliser `az login` et obtenir le dernier jeton à partir du fichier .azure dans votre répertoire de base.

##### <a name="acquiring-the-aad-token-programmatically"></a>Acquisition du jeton AAD par programmation
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Une fois que vous avez créé le principal du service, enregistrez la sortie. Maintenant, vous pouvez vous en servir pour obtenir le jeton auprès d’AAD :

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Le jeton est placé dans l’en-tête d’autorisation pour les appels d’API. Vous trouverez plus de détails à la suite.


## <a name="register-model"></a>Inscrire un modèle

À l’étape d’inscription de modèle, votre modèle d’apprentissage automatique est inscrit auprès du compte de gestion des modèles Azure que vous avez créé. Cette inscription permet le suivi des modèles et de leurs versions qui sont assignées au moment de l’inscription. L’utilisateur indique le nom du modèle. L’inscription ultérieure de modèles sous le même nom génère une nouvelle version et un nouvel ID.

### <a name="request"></a>Demande

| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Description
Inscrit un modèle

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| model | corps | Charge utile utilisée pour inscrire un modèle | Oui | [Model](#model) |


### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | OK. L’inscription du modèle a abouti | [Modèle](#model) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Interroger la liste des modèles dans un compte
### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Description
Interroge la liste des modèles dans un compte. La liste de résultats peut être filtrée à partir d’une balise et d’un nom. Si aucun filtre n’est passé, la requête répertorie tous les modèles dans le compte donné. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| name | requête | Nom d’objet | Non | chaîne |
| tag | requête | Balise Model | Non | chaîne |
| count | requête | Nombre d’éléments à récupérer dans une page | Non | chaîne |
| $skipToken | Jeton de continuation pour récupérer la page suivante | Non | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Succès | [PaginatedModelList](#paginatedmodellist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obtenir les détails du modèle
### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Description
Obtient le modèle par ID

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’objet | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Succès | [Modèle](#model) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Inscrire un manifeste auprès du modèle inscrit et de toutes les dépendances

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Description
Inscrit un manifeste auprès du modèle inscrit et de toutes ses dépendances

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| manifestRequest | corps | Charge utile utilisée pour inscrire un manifeste | Oui | [Manifest](#manifest) |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | L’inscription du manifeste a abouti | [Manifest](#manifest) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Interroger la liste des manifestes dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Description
Interroge la liste des manifestes dans un compte. La liste de résultats peut être filtrée en utilisant l’ID de modèle et le nom de manifeste. Si aucun filtre n’est passé, la requête répertorie tous les manifestes dans le compte donné. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| modelId | requête | ID du modèle | Non | chaîne |
| manifestName | requête | Nom du manifeste | Non | chaîne |
| count | requête | Nombre d’éléments à récupérer dans une page | Non | chaîne |
| $skipToken | requête | Jeton de continuation pour récupérer la page suivante | Non | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obtenir les détails du manifeste

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Description
Obtient le manifeste par ID

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’objet | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [Manifest](#manifest) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Créer une image

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Description
Crée une image en tant qu’image Docker dans l’enregistrement de contrôle d’accès (ACR)

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| imageRequest | corps | Charge utile utilisée pour créer une image | Oui | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Réponses
| Code | Description | En-têtes | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET vous indiquera l’état de la tâche de création d’image. | Operation-Location |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Interroger la liste d’images dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Description
Interroge la liste d’images dans un compte. La liste de résultats peut être filtrée en utilisant l’ID et le nom du manifeste. Si aucun filtre n’est passé, la requête répertorie toutes les images dans le compte donné. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| manifestId | requête | ID du manifeste | Non | chaîne |
| manifestName | requête | Nom du manifeste | Non | chaîne |
| count | requête | Nombre d’éléments à récupérer dans une page | Non | chaîne |
| $skipToken | requête | Jeton de continuation pour récupérer la page suivante | Non | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [PaginatedImageList](#paginatedimagelist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Afficher les détails de l’image

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Description
Obtient l’image par ID

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’image | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [Image](#image) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Créer un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Description
Crée un service à partir d’une image

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| serviceRequest | corps | Charge utile utilisée pour créer un service | Oui | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Réponses
| Code | Description | En-têtes | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET indiquera l’état de la tâche de création de service. | Operation-Location |
| 409 | Un service existe déjà sous le nom spécifié. |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Interroger la liste de services dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Description
Interroge la liste de services dans un compte. La liste de résultats peut être filtrée à partir du nom/ID du modèle, du nom/ID du manifeste, de l’ID de l’image, du nom du service ou de l’ID de ressource Compute Machine Learning. Si aucun filtre n’est passé, la requête répertorie tous les services dans le compte. La liste retournée est paginée et le nombre d’éléments dans chaque page est un paramètre facultatif.

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| serviceName | requête | Nom du service | Non | chaîne |
| modelId | requête | Nom du modèle | Non | chaîne |
| modelName | requête | ID du modèle | Non | chaîne |
| manifestId | requête | ID du manifeste | Non | requête |
| manifestName | requête | Nom du manifeste | Non | chaîne |
| imageId | requête | ID de l’image | Non | chaîne |
| computeResourceId | requête | ID de ressource Compute Machine Learning | Non | chaîne |
| count | requête | Nombre d’éléments à récupérer dans une page | Non | chaîne |
| $skipToken | requête | Jeton de continuation pour récupérer la page suivante | Non | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [PaginatedServiceList](#paginatedservicelist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obtenir les détails du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Obtient le service par ID

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’objet | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [ServiceResponse](#serviceresponse) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Mettre à jour un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Met à jour un service existant

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’objet | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| serviceUpdateRequest | corps | Charge utile utilisée pour mettre à jour un service existant | Oui |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Réponses
| Code | Description | En-têtes | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de l’emplacement de l’opération asynchrone. Un appel GET indiquera l’état de la tâche de mise à jour de service. | Operation-Location |
| 404 | Le service associé à l’ID spécifié n’existe pas |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Supprimer un service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| SUPPRIMER |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Description
Supprime un service

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’objet | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi |  |
| 204 | Le service associé à l’ID spécifié n’existe pas |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obtenir les clés du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Description
Obtient les clés du service

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID du service | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [AuthKeys](#authkeys)
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Régénérer les clés du service

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Description
Régénère les clés du service, puis les retourne

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID du service | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| regenerateKeyRequest | corps | Charge utile utilisée pour mettre à jour un service existant | Oui | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [AuthKeys](#authkeys)
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Interroger la liste des déploiements dans un compte

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Description
Interroger la liste des déploiements dans un compte La liste de résultats peut être filtrée en utilisant l’ID du service, qui retourne uniquement les déploiements créés pour le service en question. Si aucun filtre n’est passé, la requête répertorie tous les déploiements dans le compte donné.

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |
| serviceId | requête | ID du service | Non | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [DeploymentList](#deploymentlist) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obtenir les détails relatifs au déploiement

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Description
Obtient le déploiement par ID

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID du déploiement | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [Déploiement](#deployment) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obtenir les détails de l’opération

### <a name="request"></a>Demande
| Méthode | URI de demande |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Description
Obtient l’état de l’opération asynchrone par ID d’opération

### <a name="parameters"></a>Paramètres
| Nom | Emplacement | Description | Requis | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | chemin | ID d’abonnement Azure | Oui | chaîne |
| resourceGroupName | chemin | Nom du groupe de ressources dans lequel se trouve le compte de gestion des modèles | Oui | chaîne |
| accountName | chemin | Nom du compte de gestion des modèles | Oui | chaîne |
| id | chemin | ID de l’opération | Oui | chaîne |
| api-version | requête | Version de l’API du fournisseur de ressources Microsoft.Machine.Learning à utiliser | Oui | chaîne |
| Authorization | en-tête | Jeton d’autorisation, qui doit prendre la forme « Bearer XXXXXX » | Oui | chaîne |

### <a name="responses"></a>Réponses
| Code | Description | Schéma |
|--------------------|--------------------|--------------------|
| 200 | réussi | [OperationStatus](#asyncoperationstatus) |
| default | Réponse d’erreur décrivant la raison de l’échec de l’opération | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Définitions

<a name="asset"></a>
### <a name="asset"></a>Asset
Objet de ressource qui sera nécessaire pendant la création d’images Docker


|Nom|Description|Schéma|
|---|---|---|
|**id**  <br>*facultatif*|ID de la ressource|chaîne|
|**mimeType**  <br>*facultatif*|Type MIME du contenu du modèle. Pour plus d’informations sur le type MIME, accédez à https://www.iana.org/assignments/media-types/media-types.xhtml|chaîne|
|**unpack**  <br>*facultatif*|Indique l’emplacement où le contenu doit être décompressé pendant la création d’images Docker|booléen|
|**url**  <br>*facultatif*|URL de l’emplacement de la ressource|chaîne|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
État de l’opération asynchrone

*Type* : enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
État de l’opération


|Nom|Description|Schéma|
|---|---|---|
|**createdTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) de l’opération asynchrone|chaîne (date-heure)|
|**endTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de fin (UTC) de l’opération asynchrone|chaîne (date-heure)|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facultatif*|ID de l’opération asynchrone|chaîne|
|**operationType**  <br>*facultatif*|Type de l’opération asynchrone|enum (Image, Service)|
|**resourceLocation**  <br>*facultatif*|Ressource créée/mise à jour par l’opération asynchrone|chaîne|
|**state**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Clés d’authentification d’un service


|Nom|Description|Schéma|
|---|---|---|
|**primaryKey**  <br>*facultatif*|Clé primaire|chaîne|
|**secondaryKey**  <br>*facultatif*|Clé secondaire|chaîne|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Paramètres pour l’outil de mise à l’échelle automatique


|Nom|Description|Schéma|
|---|---|---|
|**autoscaleEnabled**  <br>*facultatif*|Active ou désactive l’outil de mise à l’échelle automatique|booléen|
|**maxReplicas**  <br>*facultatif*|Nombre maximal de réplicas pod prévus pour la montée en puissance.  <br>**Valeur minimale** : `1`|entier|
|**minReplicas**  <br>*facultatif*|Nombre minimal de réplicas pod prévus pour la descente en puissance.  <br>**Valeur minimale** : `0`|entier|
|**refreshPeriodInSeconds**  <br>*facultatif*|Durée d’actualisation pour le déclencheur de la mise à l’échelle automatique.  <br>**Valeur minimale** : `1`|entier|
|**targetUtilization**  <br>*facultatif*|Pourcentage d’utilisation à partir duquel la mise à l’échelle se déclenche.  <br>**Valeur minimale** : `0`  <br>**Valeur maximale** : `100`|entier|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Ressource Compute Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**id**  <br>*facultatif*|ID de la ressource|chaîne|
|**type**  <br>*facultatif*|Type de ressource|enum (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuration de la réservation de ressources pour le conteneur du cluster


|Nom|Description|Schéma|
|---|---|---|
|**cpu**  <br>*facultatif*|Spécifie la réservation du processeur. Format pour Kubernetes : https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu|chaîne|
|**memory**  <br>*facultatif*|Spécifie la réservation de mémoire. Format pour Kubernetes : https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory|chaîne|


<a name="deployment"></a>
### <a name="deployment"></a>Deployment
Instance d’un déploiement Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du déploiement|chaîne (date-heure)|
|**expiredAt**  <br>*facultatif*  <br>*en lecture seule*|Heure d’expiration (UTC) du déploiement|chaîne (date-heure)|
|**id**  <br>*facultatif*|ID du déploiement|chaîne|
|**imageId**  <br>*facultatif*|ID d’image associé à ce déploiement|chaîne|
|**serviceName**  <br>*facultatif*|Nom du service|chaîne|
|**statut**  <br>*facultatif*|État du déploiement actuel|chaîne|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Tableau d’objets de déploiement.

*Type* : tableau < [Deployment](#deployment) >


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Détail de l’erreur du service de gestion des modèles.


|Nom|Description|Schéma|
|---|---|---|
|**code**  <br>*obligatoire*|code d’erreur|chaîne|
|**message**  <br>*obligatoire*|message d’erreur|chaîne|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Objet d’erreur du service de gestion des modèles.


|Nom|Description|Schéma|
|---|---|---|
|**code**  <br>*obligatoire*|code d’erreur|chaîne|
|**details**  <br>*facultatif*|Tableau d’objets de détail d’erreur.|tableau < [ErrorDetail](#errordetail) >|
|**message**  <br>*obligatoire*|Message d’erreur|chaîne|
|**statusCode**  <br>*facultatif*|Code d'état HTTP|entier|


<a name="image"></a>
### <a name="image"></a>Image
Image Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**computeResourceId**  <br>*facultatif*|ID de l’environnement créé dans la ressource Compute Machine Learning|chaîne|
|**createdTime**  <br>*facultatif*|Heure de création (UTC) de l’image|chaîne (date-heure)|
|**creationState**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*facultatif*|Texte de description de l’image|chaîne|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facultatif*|ID de l’image|chaîne|
|**imageBuildLogUri**  <br>*facultatif*|URI des journaux chargés à partir de la génération de l’image|chaîne|
|**imageLocation**  <br>*facultatif*|Chaîne d’emplacement Azure Container Registry pour l’image créée|chaîne|
|**imageType**  <br>*facultatif*||[ImageType](#imagetype)|
|**manifest**  <br>*facultatif*||[Manifest](#manifest)|
|**name**  <br>*facultatif*|Nom de l’image|chaîne|
|**version**  <br>*facultatif*|Version de l’image définie par le service de gestion des modèles|entier|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Demande de création d’une image Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**computeResourceId**  <br>*obligatoire*|ID de l’environnement créé dans la ressource Compute Machine Learning|chaîne|
|**description**  <br>*facultatif*|Texte de description de l’image|chaîne|
|**imageType**  <br>*obligatoire*||[ImageType](#imagetype)|
|**manifestId**  <br>*obligatoire*|ID du manifeste à partir duquel l’image sera créée|chaîne|
|**name**  <br>*obligatoire*|Nom de l’image|chaîne|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Spécifie le type de l’image

*Type* : enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Manifeste Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**assets**  <br>*obligatoire*|Liste de ressources|tableau < [Asset](#asset) >|
|**createdTime**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du manifeste|chaîne (date-heure)|
|**description**  <br>*facultatif*|Texte de description du manifeste|chaîne|
|**driverProgram**  <br>*obligatoire*|Programme pilote du manifeste|chaîne|
|**id**  <br>*facultatif*|ID du manifeste|chaîne|
|**modelIds**  <br>*facultatif*|Liste d’ID des modèles inscrits. La demande échoue si l’un des modèles inclus n’est pas inscrit|tableau < string >|
|**modelType**  <br>*facultatif*|Indique que les modèles sont déjà inscrits auprès du service de gestion des modèles|enum (Registered)|
|**name**  <br>*obligatoire*|Nom du manifeste|chaîne|
|**targetRuntime**  <br>*obligatoire*||[TargetRuntime](#targetruntime)|
|**version**  <br>*facultatif*  <br>*en lecture seule*|Version du manifeste affectée par le service de gestion des modèles|entier|
|**webserviceType**  <br>*facultatif*|Spécifie le type souhaité du service web qui sera créé à partir du manifeste|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Model
Instance d’un modèle Azure Machine Learning


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*  <br>*en lecture seule*|Heure de création (UTC) du modèle|chaîne (date-heure)|
|**description**  <br>*facultatif*|Texte de description du modèle|chaîne|
|**id**  <br>*facultatif*  <br>*en lecture seule*|ID du modèle|chaîne|
|**mimeType**  <br>*obligatoire*|Type MIME du contenu du modèle. Pour plus d’informations sur le type MIME, accédez à https://www.iana.org/assignments/media-types/media-types.xhtml|chaîne|
|**name**  <br>*obligatoire*|Nom du modèle|chaîne|
|**balises**  <br>*facultatif*|Liste de balises du modèle|tableau < string >|
|**unpack**  <br>*facultatif*|Indique si le modèle doit être décompressé pendant la création d’images Docker.|booléen|
|**url**  <br>*obligatoire*|URL du modèle. En général, une URL SAS prend place ici.|chaîne|
|**version**  <br>*facultatif*  <br>*en lecture seule*|Version du modèle affectée par le service de gestion des modèles|entier|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informations sur la collecte de données du modèle


|Nom|Description|Schéma|
|---|---|---|
|**eventHubEnabled**  <br>*facultatif*|Active le concentrateur d’événements pour un service|booléen|
|**storageEnabled**  <br>*facultatif*|Active le stockage pour un service|booléen|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Liste paginée d’images


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|chaîne|
|**value**  <br>*facultatif*|Tableau d’objets de modèle|tableau < [Image](#image) >|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Liste paginée de manifestes.


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|chaîne|
|**value**  <br>*facultatif*|Tableau d’objets de manifeste.|tableau < [Manifest](#manifest) >|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Liste paginée de modèles.


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|chaîne|
|**value**  <br>*facultatif*|Tableau d’objets de modèle.|tableau < [Model](#model) >|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Liste paginée de services.


|Nom|Description|Schéma|
|---|---|---|
|**nextLink**  <br>*facultatif*|Lien de continuation (URI absolu) vers la page suivante de résultats de la liste.|chaîne|
|**value**  <br>*facultatif*|Tableau d’objets de service.|tableau < [ServiceResponse](#serviceresponse) >|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Demande de création d’un service


|Nom|Description|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service|booléen|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatoire*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obligatoire*|Image pour créer le service|chaîne|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|entier|
|**name**  <br>*obligatoire*|Nom du service|chaîne|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|entier|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Demande de régénération de clé pour un service


|Nom|Description|Schéma|
|---|---|---|
|**keyType**  <br>*facultatif*|Spécifie la clé à régénérer|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
État détaillé du service


|Nom|Description|Schéma|
|---|---|---|
|**createdAt**  <br>*facultatif*|Heure de création (UTC) du service|chaîne (date-heure)|
|**id**  <br>*facultatif*|ID du service|chaîne|
|**image**  <br>*facultatif*||[Image](#image)|
|**manifest**  <br>*facultatif*||[Manifest](#manifest)|
|**models**  <br>*facultatif*|Liste de modèles|tableau < [Model](#model) >|
|**name**  <br>*facultatif*|Nom du service|chaîne|
|**scoringUri**  <br>*facultatif*|URI pour la notation du service|chaîne|
|**state**  <br>*facultatif*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*facultatif*|Heure (UTC) de la dernière mise à jour|chaîne (date-heure)|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service|booléen|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatoire*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|entier|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|entier|
|**error**  <br>*facultatif*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Demande de mise à jour d’un service


|Nom|Description|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*facultatif*|Active les insights d’application pour un service|booléen|
|**autoScaler**  <br>*facultatif*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*facultatif*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facultatif*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*facultatif*|Image pour créer le service|chaîne|
|**maxConcurrentRequestsPerContainer**  <br>*facultatif*|Nombre maximal de demandes simultanées  <br>**Valeur minimale** : `1`|entier|
|**numReplicas**  <br>*facultatif*|Nombre de réplicas pod s’exécutant à un moment donné. Ne peut pas être spécifié si Autoscaler est activé.  <br>**Valeur minimale** : `0`|entier|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Type du runtime cible.


|Nom|Description|Schéma|
|---|---|---|
|**properties**  <br>*obligatoire*||mappage < chaîne, chaîne >|
|**runtimeType**  <br>*obligatoire*|Spécifie le runtime|enum (SparkPython, Python)|


