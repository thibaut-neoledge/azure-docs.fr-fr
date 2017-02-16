---
title: "Prise en main d’Azure Data Lake Analytics à l’aide de Python | Microsoft Docs"
description: "Découvrez comment utiliser Python pour créer un compte Data Lake Store, et soumettez des travaux. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Didacticiel : prise en main d’Azure Data Lake Analytics à l’aide de Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser Python pour créer des comptes Azure Data Lake Analytics, définir des travaux Data Lake Analytics dans [U-SQL](data-lake-analytics-u-sql-get-started.md) et envoyer des travaux à des comptes Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous développez un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l'aide d'autres outils pris en charge, cliquez sur les onglets en haut de cette section.

##<a name="prerequisites"></a>Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : authentification de l’utilisateur final ou authentification de service à service. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).
- **[Python](https://www.python.org/downloads/)**. Vous devez utiliser la version 64 bits. Cet article utilise Python version 3.5.2.


## <a name="install-azure-python-sdk"></a>Installer le Kit de développement logiciel (SDK) Python

Pour utiliser Data Lake Store avec Python, vous devez installer trois modules.

Le module azure-mgmt-datalake-store inclut les opérations de gestion du compte Azure Data Lake Store. Le module azure-mgmt-resource inclut d’autres modules Azure pour Active Directory, etc. Le module azure-datalake-store inclut les opérations du système de fichiers d’Azure Data Lake Store. Le module azure-datalake-analytics inclut les opérations d’Azure Data Lake Analytics. Utilisez les commandes suivantes pour installer les modules.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Créer une application Python

1. Utilisez l’IDE de votre choix pour créer une application Python, par exemple, mysample.py.

2. Ajoutez les lignes suivantes pour importer les modules requis.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Enregistrez les modifications apportées au fichier de l’application Python.

## <a name="authentication"></a>Authentification

Utilisez l’une des méthodes suivantes pour l’authentification :

### <a name="end-user-authentication-for-account-management"></a>Authentification de l’utilisateur final pour la gestion du compte

Utilisez cette méthode pour l’authentification auprès d’Azure AD pour les opérations de gestion du compte (créer/supprimer un compte Data Lake Store, etc.). Vous devez fournir un nom d’utilisateur et un mot de passe d’utilisateur Azure AD. Le compte d’utilisateur ne peut pas être configuré pour l’authentification multifacteur et le compte ne peut pas être un compte Microsoft/Live ID, par exemple @outlook.com, et @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>L’authentification de service à service avec clé secrète client pour al gestion de compte

Utilisez cette méthode pour l’authentification auprès d’Azure AD pour les opérations de gestion du compte (créer/supprimer un compte Data Lake Store, etc.). Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète client pour une application/un principal du service. Utilisez-le avec une « application web » Azure AD existante.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Créer un groupe Resource Management

L’extrait de code suivant permet de créer un groupe de ressources Azure :

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Créer un compte Data Lake Store

Chaque compte Data Lake Analytics nécessite un compte Data Lake Store. Pour des instructions, consultez [Créer un compte Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Envoyer des travaux Analytique Data Lake

Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Étapes suivantes

- Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO4-->


