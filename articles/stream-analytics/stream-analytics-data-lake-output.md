<properties
	pageTitle="Sortie de Stream Analytics Data Lake Store| Microsoft Azure"
	description="Configuration de l’authentification et de l’autorisation d’Azure Data Lake Store dans un travail Stream Analytics"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="03/18/2016"
	ms.author="jeffstok"
/>

# Sortie de Stream Analytics Data Lake Store

Les travaux Stream Analytics prennent en charge plusieurs méthodes de sortie, dont [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store est un référentiel d'entreprise à très grande échelle pour les charges de travail d'analyse du Big Data. Data Lake Store vous permet de stocker des données de toute taille, de tout type et de toute vitesse d’ingestion en vue d’une analyse opérationnelle et exploratoire. Cet article aborde les autorisations, la configuration et le renouvellement d’autorisations d’Azure Data Lake Store dans le portail Azure Classic Stream Analytics.

> [AZURE.NOTE] À l’heure actuelle, la création et la configuration des sorties Data Lake Store sont prises en charge **uniquement** dans le portail Azure Classic.

## Autoriser un compte Data Lake Store

1.  Quand vous sélectionnez Data Lake Store comme sortie dans le portail de gestion Azure, vous êtes invité à autoriser l’utilisation de votre Data Lake Store existant ou à demander l’accès à la version préliminaire de Data Lake Store au moyen du portail Azure Classic.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.jpg)

2.  Si vous avez déjà accès à Data Lake Store, cliquez sur Autoriser maintenant. Pendant un bref instant, une page s’affiche avec le message suivant : « Redirection de l’autorisation... ». La page se ferme automatiquement pour laisser place à la page permettant de configurer la sortie Data Lake Store.

Si vous n’êtes pas inscrit à la version préliminaire de Data Lake Store, cliquez sur le lien « Inscrivez-vous maintenant » pour faire une demande d’inscription ou suivez les [instructions de prise en main](../data-lake-store/data-lake-store-get-started-portal.md).

## Configurer les propriétés de sortie de Data Lake Store

Une fois le compte Data Lake Store authentifié, vous pouvez configurer les propriétés de votre sortie Data Lake Store. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour configurer votre sortie Data Lake Store.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers Data Lake Store.</td>
</tr>
<tr>
<td>Compte Data Lake Store</td>
<td>Nom du compte de stockage où vous envoyez votre sortie. Vous voyez la liste déroulante des comptes Data Lake Store auxquels ont accès les utilisateurs connectés au portail.</td>
</tr>
<tr>
<td>Modèle de préfixe de chemin [<I>facultatif</I>]</td>
<td>Chemin de fichier utilisé pour écrire vos fichiers dans le compte Data Lake Store spécifié. <BR>{date}, {time}<BR>Exemple 1 : dossier1/journaux/{date}/{heure}<BR>Exemple 2 : dossier1/journaux/{date}</td>
</tr>
<tr>
<td>Format de la date [<I>facultatif</I>]</td>
<td>Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple&#160;: JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [<I>facultatif</I>]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est&#160;HH.</td>
</tr>
<tr>
<td>Format de sérialisation de l’événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Encodage</td>
<td>S’il s’agit du format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format d’encodage actuellement pris en charge.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge&#160;: virgule, point-virgule, espace, tabulation et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. «&#160;Séparé par une ligne&#160;» spécifie que la sortie sera mise en forme avec chaque objet JSON séparé par une nouvelle ligne. «&#160;Tableau&#160;» spécifie que la sortie sera mise en forme en tant que tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

## Renouveler une autorisation Data Lake Store

Il existe actuellement une limitation selon laquelle le jeton d’authentification doit être actualisé manuellement tous les 90 jours pour tous les travaux impliquant une sortie Data Lake Store. Vous devez également réauthentifier votre compte Data Lake Store si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail. L’absence de sortie du travail et une erreur d’authentification de l’utilisateur dans les journaux des opérations constituent des symptômes de ce problème.

Pour le résoudre, arrêtez le travail en cours d’exécution et accédez à votre sortie Data Lake Store. Cliquez sur le lien « Renouveler l’autorisation ». Pendant un bref instant, vous voyez une page indiquant « Redirection de l’autorisation... ». La page se ferme automatiquement et si l’opération réussit, vous voyez le message suivant : « L’autorisation a été correctement renouvelée ». Vous devrez ensuite cliquer sur Enregistrer au bas de la page, puis redémarrer votre travail à partir de l’heure du dernier arrêt pour éviter toute perte de données.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.jpg)

<!---HONumber=AcomDC_0323_2016-->