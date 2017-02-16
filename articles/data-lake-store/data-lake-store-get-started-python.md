---
title: "Prise en main d’Azure Data Lake Store à l’aide de Python | Microsoft Docs"
description: "Découvrez comment gérer le kit de développement logiciel (SDK) Python avec les comptes Data Lake Store et le système de fichiers."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: d8faeafc6d4c73c4c71d0bc1554b04302dffdc55
ms.openlocfilehash: 72186e03a1dc47f67b8f4cdcac55208a61c8e147


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Prise en main d’Azure Data Lake Store à l’aide de Python

> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Découvrez comment utiliser le Kit de développement logiciel (SDK) Python et Azure Data Lake Store pour effectuer des opérations de base comme créer des dossiers ou charger et télécharger des fichiers de données. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Composants requis

* **Python** Pour télécharger Python, accédez [ici](https://www.python.org/downloads/). Cet article utilise Python version 3.5.2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Data Lake Store avec Python, vous devez installer trois modules.

* Le module `azure-mgmt-resource`. Cela inclut les modules Azure pour Active Directory, etc.
* Le module `azure-mgmt-datalake-store`. Cela inclut les opérations de gestion du compte Azure Data Lake Store. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules de gestion Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* Le module `azure-datalake-store`. Cela inclut les opérations du système de fichiers Azure Data Lake Store. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules du système de fichiers Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Utilisez les commandes suivantes pour installer les modules.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Créer une application Python

1. Utilisez l’IDE de votre choix pour créer une application Python, par exemple, **mysample.py**.

2. Ajoutez les lignes suivantes pour importer les modules requis.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Enregistrez les modifications apportées à mysample.py.

## <a name="authentication"></a>Authentification

Dans cette section, nous aborderons les différentes méthodes permettant de s’authentifier auprès d’Azure AD. Voici les options disponibles :

* Authentification de l’utilisateur final
* Authentification de service à service
* Authentification multifacteur

Vous devez utiliser ces options d’authentification pour les modules de gestion des systèmes de fichiers et des comptes.

### <a name="end-user-authentication-for-account-management"></a>Authentification de l’utilisateur final pour la gestion du compte

Utilisez cette méthode pour l’authentification auprès d’Azure AD pour les opérations de gestion du compte (créer/supprimer un compte Data Lake Store, etc.). Vous devez fournir un nom d’utilisateur et un mot de passe d’utilisateur Azure AD. Notez que l’utilisateur ne doit pas être configuré pour l’authentification multifacteur.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Authentification de l’utilisateur final pour les opérations du système de fichiers

Utilisez cette méthode pour procéder à une authentification auprès d’Azure AD pour les opérations du système de fichiers (créer un dossier, charger un fichier, etc.). Utilisez-la avec une application **cliente native** Azure AD. L’utilisateur Azure AD pour lequel vous fournissez des informations d’identification ne doit pas être configuré pour l’authentification multifacteur.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>L’authentification de service à service avec clé secrète client pour al gestion de compte

Utilisez cette méthode pour l’authentification auprès d’Azure AD pour les opérations de gestion du compte (créer/supprimer un compte Data Lake Store, etc.). Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète client pour une application/un principal du service. Utilisez-le avec une « application web » Azure AD existante.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Authentification de service à service avec clé secrète client pour les opérations du système de fichiers

Utilisez cette méthode pour procéder à une authentification auprès d’Azure AD pour les opérations du système de fichiers (créer un dossier, charger un fichier, etc.). Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète client pour une application/un principal du service. Utilisez-le avec une « application web » Azure AD existante.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>Authentification multifacteur pour la gestion des comptes

Utilisez cette méthode pour l’authentification auprès d’Azure AD pour les opérations de gestion du compte (créer/supprimer un compte Data Lake Store, etc.). L’extrait de code suivant peut être utilisé pour authentifier votre application à l’aide de l’authentification multifacteur. Utilisez-le avec une « application web » Azure AD existante.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>Authentification multifacteur pour la gestion des systèmes de fichiers

Utilisez cette méthode pour procéder à une authentification auprès d’Azure AD pour les opérations du système de fichiers (créer un dossier, charger un fichier, etc.). L’extrait de code suivant peut être utilisé pour authentifier votre application à l’aide de l’authentification multifacteur. Utilisez-le avec une « application web » Azure AD existante.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Créer un groupe de ressources Azure

L’extrait de code suivant permet de créer un groupe de ressources Azure :

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>Créer des clients et un compte Data Lake Store

L’extrait de code suivant crée dans un premier temps le client du compte Data Lake Store. Il utilise l’objet client pour créer le compte Data Lake Store. Enfin, l’extrait de code crée un objet client de système de fichiers.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Répertorier les comptes Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>Créer un répertoire

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Charger un fichier


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Téléchargement d’un fichier

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Supprimer un répertoire

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>Voir aussi

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK Reference (Informations de référence sur le Kit de développement logiciel (SDK) .NET Azure Data Lake Store)](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST Reference (Informations de référence sur les API REST Data Lake Store)](https://msdn.microsoft.com/library/mt693424.aspx)



<!--HONumber=Jan17_HO2-->


