---
title: "Prise en main d’Azure Data Lake Analytics à l’aide d’API REST| Microsoft Docs"
description: "Utiliser les API REST de WebHDFS pour effectuer des opérations sur Data Lake Analytics"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 332d7af2539eea8890745005104ac5b0921c2b7f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Prise en main d’Azure Data Lake Analytics à l’aide d’API REST
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser les API REST WebHDFS et les API REST Data Lake Analytique pour gérer les comptes, les travaux et le catalogue Data Lake Analytics. 

## <a name="prerequisites"></a>Composants requis
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Analytics auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Analytics à l’aide d’Azure Active Directory).
* [cURL](http://curl.haxx.se/). Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un compte Data Lake Analytics.

## <a name="authenticate-with-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory
Il existe deux méthodes d’authentification avec Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Authentification de l’utilisateur final (interactive)
Avec cette méthode, l’application invite l’utilisateur à se connecter. Toutes les opérations sont effectuées dans le contexte de l’utilisateur. 

Pour l’authentification interactive, procédez comme suit :

1. Dans votre application, redirigez l’utilisateur vers l’URL suivante :
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > L’URI \<REDIRECT-URI> doit être codée pour être utilisée dans une URL. Par conséquent, pour https://localhost, utilisez `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Pour les besoins de ce didacticiel, vous pouvez remplacer les valeurs d’espace réservé de l’URL ci-dessus et la coller dans la barre d’adresse d’un navigateur web. Vous serez redirigé pour vous authentifier à l’aide de vos informations de connexion Azure. Lorsque vous êtes connecté, la réponse s’affiche dans la barre d’adresse du navigateur. La réponse présente le format suivant :
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Capturez le code d’autorisation de la réponse. Pour ce didacticiel, vous pouvez copier le code d’autorisation de la barre d’adresse du navigateur web et le transmettre dans la demande POST au point de terminaison de jeton, comme indiqué ci-dessous :
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Dans ce cas, il n’est pas nécessaire de coder l’URI \<REDIRECT-URI>.
   > 
   > 
3. La réponse est un objet JSON contenant un jeton d’accès (par exemple, `"access_token": "<ACCESS_TOKEN>"`) et un jeton d’actualisation (par exemple, `"refresh_token": "<REFRESH_TOKEN>"`). Votre application utilise le jeton d’accès pour accéder à Azure Data Lake Store et le jeton d’actualisation pour obtenir un autre jeton d’accès lorsque l’un d’eux expire.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Lorsque le jeton d’accès arrive à expiration, vous pouvez demander un nouveau jeton d’accès à l’aide du jeton d’actualisation, comme indiqué ci-dessous :
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Pour plus d’informations sur l’authentification utilisateur interactive, consultez [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Authentification de service à service (non interactive)
Avec cette méthode, l’application fournit ses propres informations d’identification pour effectuer les opérations. Avec cette méthode, vous devez émettre une demande POST, comme celle illustrée ci-dessous : 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

La sortie de cette demande inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) que vous allez ensuite passer avec vos appels d’API REST. Enregistrez ce jeton d’authentification dans un fichier texte, car vous en aurez besoin plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la méthode **non interactive** . Pour plus d’informations sur l’authentification non interactive (appels de service à service), consultez [Appels de service à service à l’aide d’informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics
Vous devez créer un groupe de ressources Azure et un compte Data Lake Store avant de pouvoir créer un compte Data Lake Analytics.  Consultez [Créer un compte Data Lake Store](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

La commande Curl suivante montre comment créer un compte :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Remplacez \<`REDACTED`\> par le jeton d’autorisation, \<`AzureSubscriptionID`\> par votre ID d’abonnement \<`AzureResourceGroupName`\> par un nom de groupe de ressources Azure existant, et \<`NewAzureDataLakeAnalyticsAccountName`\> par le nom d’un nouveau compte Data Lake Analytics. La charge utile de la demande pour cette commande est contenue dans le fichier **CreateDatalakeAnalyticsAccountRequest.json** fourni pour le paramètre `-d` ci-dessus. Le contenu du fichier input.json ressemble à ceci :

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Énumérer les comptes Data Lake Analytics d’un abonnement
La commande Curl suivante montre comment répertorier les comptes dans un abonnement :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Remplacez \<`REDACTED`\> par le jeton d’autorisation, \<`AzureSubscriptionID`\> par votre ID d’abonnement. Le résultat ressemble à ce qui suit :

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtenir des informations sur un compte Data Lake Analytics
La commande Curl suivante montre comment obtenir des informations sur un compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Remplacez \<`REDACTED`\> par le jeton d’autorisation, \<`AzureSubscriptionID`\> par votre ID d’abonnement \<`AzureResourceGroupName`\> par un nom de groupe de ressources Azure existant, et \<`DataLakeAnalyticsAccountName`\> par le nom d’un compte Data Lake Analytics existant. Le résultat ressemble à ce qui suit :

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Répertorier les magasins Data Lake d’un compte Data Lake Analytics
La commande Curl suivante montre comment répertorier les magasins Data Lake d’un compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Remplacez \<`REDACTED`\> par le jeton d’autorisation, \<`AzureSubscriptionID`\> par votre ID d’abonnement \<`AzureResourceGroupName`\> par un nom de groupe de ressources Azure existant, et \<`DataLakeAnalyticsAccountName`\> par le nom d’un compte Data Lake Analytics existant. Le résultat ressemble à ce qui suit :

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Envoyer des travaux U-SQL
La commande Curl suivante montre comment envoyer un travail U-SQL :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Remplacez \<`REDACTED`\> par le jeton d’autorisation, \<`DataLakeAnalyticsAccountName`\> par le nom d’un compte Data Lake Analytics existant. La charge utile de la demande pour cette commande est contenue dans le fichier **SubmitADLAJob.json** fourni pour le paramètre `-d` ci-dessus. Le contenu du fichier input.json ressemble à ceci :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

Le résultat ressemble à ce qui suit :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Répertorier des travaux U-SQL
La commande Curl suivante montre comment répertorier des travaux U-SQL :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Remplacez \<`REDACTED`\> par le jeton d’autorisation et \<`DataLakeAnalyticsAccountName`\> par le nom d’un compte Data Lake Analytics existant. 

Le résultat ressemble à ce qui suit :

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obtenir les éléments du catalogue
La commande Curl suivante montre comment obtenir les bases de données à partir du catalogue :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

Le résultat ressemble à ce qui suit :

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Voir aussi
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour obtenir une vue d'ensemble de Data Lake Analytics, consultez [Vue d'ensemble de Data Lake Analytics Azure](data-lake-analytics-overview.md).
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.


