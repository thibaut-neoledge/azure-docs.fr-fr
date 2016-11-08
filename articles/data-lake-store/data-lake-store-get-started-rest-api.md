---
title: Prise en main d’Azure Data Lake Store avec les API REST| Microsoft Docs
description: Utiliser les API REST de WebHDFS pour effectuer des opérations sur Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme

---
# Prise en main d’Azure Data Lake Store avec les API REST
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> 
> 

Dans cet article, vous allez découvrir comment utiliser les API REST de WebHDFS et de Data Lake Store pour gérer les comptes et effectuer certaines opérations de système de fichiers sur Azure Data Lake Store. Azure Data Lake Store expose ses propres API REST pour les opérations de gestion des comptes. Toutefois, comme Data Lake Store est compatible avec HDFS et l’écosystème Hadoop, il prend en charge l’utilisation des API REST de WebHDFS pour les opérations de système de fichiers.

> [!NOTE]
> Pour plus d’informations sur la prise en charge des API REST avec Data Lake Store, consultez [Azure Data Lake Store REST API Reference](https://msdn.microsoft.com/library/mt693424.aspx) (Informations de référence sur les API REST d’Azure Data Lake Store).
> 
> 

## Composants requis
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).
* [cURL](http://curl.haxx.se/). Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un compte Data Lake Store.

## Comment s’authentifier à l’aide d’Azure Active Directory ?
Vous avez le choix entre deux méthodes pour vous authentifier à l’aide d’Azure Active Directory :

### Authentification de l’utilisateur final (interactive)
Dans ce scénario, l’application invite l’utilisateur à se connecter. Toutes les opérations sont effectuées dans le contexte de l’utilisateur. Pour l’authentification interactive, procédez comme suit.

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

### Authentification de service à service (non interactive)
Dans ce scénario, l’application fournit ses propres informations d’identification pour effectuer les opérations. Avec cette méthode, vous devez émettre une demande POST, comme celle illustrée ci-dessous.

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

La sortie de cette demande inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) que vous allez ensuite passer avec vos appels d’API REST. Enregistrez ce jeton d’authentification dans un fichier texte, car vous en aurez besoin plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la méthode **non interactive**. Pour plus d’informations sur l’authentification non interactive (appels de service à service), consultez [Appels de service à service à l’aide d’informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## Créer un compte Data Lake Store
Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694078.aspx).

Utilisez la commande cURL suivante. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Dans la commande ci-dessus, remplacez <`REDACTED`> par le jeton d’autorisation que vous avez récupéré précédemment. La charge utile de la demande pour cette commande est contenue dans le fichier **input.json** fourni pour le paramètre `-d` ci-dessus. Le contenu du fichier input.json ressemble à ceci :

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## Créer des dossiers dans un compte Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilisez la commande cURL suivante. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Dans la commande ci-dessus, remplacez <`REDACTED`> par le jeton d’autorisation que vous avez récupéré précédemment. Cette commande crée un répertoire appelé **mytempdir** sous le dossier racine de votre compte Data Lake Store.

Si l’opération s’est déroulée correctement, vous devez voir une réponse similaire à celle-ci :

    {"boolean":true}

## Afficher la liste des dossiers dans un compte Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilisez la commande cURL suivante. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Dans la commande ci-dessus, remplacez <`REDACTED`> par le jeton d’autorisation que vous avez récupéré précédemment.

Si l’opération s’est déroulée correctement, vous devez voir une réponse similaire à celle-ci :

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## Charger des données dans un compte Azure Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Le chargement de données à l’aide de l’API REST de WebHDFS s’effectue en deux étapes, comme expliqué ci-dessous.

1. Envoyez une demande HTTP PUT sans envoyer les données du fichier à charger. Dans la commande suivante, remplacez **<yourstorename>** par le nom de votre Data Lake Store.
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    La sortie de cette commande contient une URL de redirection temporaire, comme celle-ci :
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. Vous devez maintenant envoyer une autre demande HTTP PUT sur l’URL indiquée pour la propriété **Location** dans la réponse. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    Vous devez obtenir un résultat semblable à ce qui suit :
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## Lire les données d’un compte Azure Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lecture des données d’un compte Data Lake Store s’effectue en deux étapes.

* D’abord, envoyez une demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Cette opération retourne un emplacement où envoyer la demande GET suivante.
* Ensuite, envoyez la deuxième demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Cette opération affiche le contenu du fichier.

Toutefois, comme les paramètres d’entrée sont les mêmes pour la première et la deuxième étape, vous pouvez utiliser le paramètre `-L` pour envoyer la première demande. L’utilisation de l’option `-L` permet essentiellement de combiner les deux demandes en une seule et d’indiquer à cURL de réexécuter la demande sur le nouvel emplacement. La sortie finale de tous les appels de la demande s’affiche, comme illustrée ci-dessous. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Le résultat doit ressembler à ce qui suit :

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## Renommer un fichier dans un compte Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilisez la commande cURL suivante pour renommer un fichier. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Le résultat doit ressembler à ce qui suit :

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## Supprimer un fichier dans un compte Data Lake Store
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilisez la commande cURL suivante pour supprimer un fichier. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Un résultat similaire à ce qui suit s’affiche normalement :

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## Supprimer un compte Data Lake Store
Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694075.aspx).

Utilisez la commande cURL suivante pour supprimer un compte Data Lake Store. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Un résultat similaire à ce qui suit s’affiche normalement :

    HTTP/1.1 200 OK
    ...
    ...

## Voir aussi
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

<!---HONumber=AcomDC_1005_2016-->