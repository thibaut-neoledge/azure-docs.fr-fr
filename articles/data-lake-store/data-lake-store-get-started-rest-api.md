<properties 
   pageTitle="Prise en main d’Azure Data Lake Store avec les API REST| Microsoft Azure" 
   description="Utiliser les API REST de WebHDFS pour effectuer des opérations sur Data Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# Prise en main d’Azure Data Lake Store avec les API REST

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Dans cet article, vous allez découvrir comment utiliser les API REST de WebHDFS et de Data Lake Store pour gérer les comptes et effectuer certaines opérations de système de fichiers sur Azure Data Lake Store. Azure Data Lake Store expose ses propres API REST pour les opérations de gestion des comptes. Toutefois, comme Data Lake Store est compatible avec HDFS et l’écosystème Hadoop, il prend en charge l’utilisation des API REST de WebHDFS pour les opérations de système de fichiers.

>[AZURE.NOTE] Pour plus d’informations sur la prise en charge des API REST avec Data Lake Store, consultez [Azure Data Lake Store REST API Reference](https://msdn.microsoft.com/library/mt693424.aspx) (Informations de référence sur les API REST d’Azure Data Lake Store).

## Configuration requise

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Créez une application Azure Active Directory**. Consultez [Création de l’application Active Directory et du principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md). Une fois que vous avez créé l’application, récupérez les valeurs suivantes liées à l’application.
	- Obtenez l’ID client et l’ID de locataire associés à l’application.
	- Créer une clé d’authentification
	- Définir des autorisations déléguées

	Vous pouvez utiliser le lien ci-dessus pour obtenir des instructions sur la manière de récupérer ces valeurs.
- **Affectez l’application Azure Active Directory à un rôle**. Le rôle détermine le niveau de l’étendue pour laquelle vous souhaitez accorder des autorisations à l’application Azure Active Directory. Par exemple, vous pouvez affecter l’application au niveau de l’abonnement ou au niveau d’un groupe de ressources. Pour obtenir des instructions, consultez [Affecter l’application à un rôle](../resource-group-create-service-principal-portal.md#assign-application-to-role).
- [cURL](http://curl.haxx.se/). Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un compte Data Lake Store.

## Comment s’authentifier à l’aide d’Azure Active Directory ?

Vous avez le choix entre deux méthodes pour vous authentifier à l’aide d’Azure Active Directory :

* Avec la méthode **interactive**, l’application invite l’utilisateur à se connecter. Pour plus d’informations, consultez [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx).

* Avec la méthode **non interactive**, l’application fournit ses propres informations d’identification. Pour plus d’informations, consultez [Appels de service à service à l’aide d’informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

Cet article utilise la méthode **non interactive**. Avec cette méthode, vous devez émettre une demande POST, comme celle illustrée ci-dessous.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

La sortie de cette demande inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) que vous allez ensuite passer avec vos appels d’API REST. Enregistrez ce jeton d’authentification dans un fichier texte, car vous en aurez besoin plus loin dans cet article.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Créer un compte Data Lake Store

Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694078.aspx).

Utilisez la commande cURL suivante. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

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

	Vous devez obtenir un résultat semblable à ce qui suit :

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Lire les données d’un compte Azure Data Lake Store

Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lecture des données d’un compte Data Lake Store s’effectue en deux étapes.

* D’abord, envoyez une demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Cette opération retourne un emplacement où envoyer la demande GET suivante.
* Ensuite, envoyez la deuxième demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Cette opération affiche le contenu du fichier.

Toutefois, comme les paramètres d’entrée sont les mêmes pour la première et la deuxième étapes, vous pouvez utiliser le paramètre `-L` pour envoyer la première demande. L’utilisation de l’option `-L` permet essentiellement de combiner les deux demandes en une seule et d’indiquer à cURL de réexécuter la demande sur le nouvel emplacement. La sortie finale de tous les appels de la demande s’affiche, comme illustrée ci-dessous. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Le résultat doit ressembler à ce qui suit :

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

Le résultat doit ressembler à ce qui suit :

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Supprimer un fichier dans un compte Data Lake Store

Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilisez la commande cURL suivante pour supprimer un fichier. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Un résultat similaire à ce qui suit s’affiche normalement :

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Supprimer un compte Data Lake Store

Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694075.aspx).

Utilisez la commande cURL suivante pour supprimer un compte Data Lake Store. Remplacez **<yourstorename>** par le nom de votre Data Lake Store.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Un résultat similaire à ce qui suit s’affiche normalement :

	HTTP/1.1 200 OK
	...
	...

## Voir aussi

- [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->