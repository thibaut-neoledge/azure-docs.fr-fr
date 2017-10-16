---
title: "Script Python pour récupérer des données à partir d’Azure Log Analytics | Microsoft Docs"
description: "L’API Recherche de journal Log Analytics permet aux clients d’API REST de récupérer des données à partir d’un espace de travail Log Analytics.  Cet article fournit un exemple de script Python utilisant l’API Recherche de journal."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: bwren
ms.openlocfilehash: 56d7c6dc648a01e7b0efc167cb65c94bac5468ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Récupérer des données à partir de Log Analytics via un script Python
[L’API Recherche de journal Log Analytics](log-analytics-log-search-api.md) permet aux clients d’API REST de récupérer des données à partir d’un espace de travail Log Analytics.  Cet article présente un exemple de script Python qui utilise l’API Recherche de journal Log Analytics.  

## <a name="authentication"></a>Authentification
Ce script utilise un principal de service dans Azure Active Directory pour s’authentifier auprès de l’espace de travail.  Les principaux de service permettent à une application cliente de demander à ce que le service authentifie un compte même si le client n’a pas le nom du compte. Avant d’exécuter ce script, vous devez créer un principal de service à l’aide du processus indiqué dans [Utiliser le portail pour créer une application et un principal de service Azure Active Directory pouvant accéder aux ressources](../azure-resource-manager/resource-group-create-service-principal-portal.md).  Vous devrez fournir l’ID d’application, l’ID de locataire et la clé d’authentification pour le script. 

> [!NOTE]
> Lorsque vous [créez un compte Azure Automation](../automation/automation-create-standalone-account.md), un principal de service adapté à l’utilisation de ce script est créé.  Si vous disposez déjà d’un principal de service créé par Azure Automation, vous devez être en mesure de l’utiliser plutôt que d’en créer un nouveau, bien que vous deviez [créer une clé d’authentification](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) s’il n’y en a pas d’existante.

## <a name="script"></a>Script
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [l’API Recherche de journal Log Analytics](log-analytics-log-search-api.md).