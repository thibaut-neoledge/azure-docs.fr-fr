<properties 
	pageTitle="Définition de sorties | Microsoft Azure" 
	description="Présentation des sorties Stream Analytics" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Présentation des sorties Stream Analytics

Lorsque vous créez une tâche Stream Analytics, vous devez penser à la façon dont sa sortie sera consommée. De quelle façon les consommateurs de la transformation de données voient-ils les résultats de la tâche Stream Analytics ? Quels outils utiliseront-ils pour analyser la sortie ? La rétention et l’entreposage des données sont-ils requis ?

Pour permettre un éventail de modèles d’application, Azure Stream Analytics fournit sept méthodes différentes pour stocker et afficher les sorties de tâche : les bases de données SQL, le stockage d’objets blob, Event Hubs, les files d’attente Service Bus, les rubriques Service Bus, Power BI et le stockage de tables sont des sorties prises en charge. Cela permet à la fois de faciliter l’affichage des sorties de tâche et de rendre plus flexibles la consommation et le stockage des sorties de tâche pour l’entreposage de données et autres utilisations.


## Base de données SQL ##

Vous pouvez utiliser une [base de données SQL Azure](http://azure.microsoft.com/services/sql-database/) comme sortie pour les données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Les tâches Stream Analytics écriront dans une table existante d’une base de données SQL Azure. Notez que le schéma de table doit correspondre exactement aux champs et aux types de sortie de votre tâche. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de base de données SQL.

| Nom de la propriété | Description |
|---------------|-------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données | Nom de la base de données où vous envoyez votre sortie |
| Nom du serveur | Nom du serveur de base de données SQL |
| Nom d’utilisateur | Nom de l’utilisateur qui a accès en écriture à la base de données |
| Mot de passe | Mot de passe de connexion à la base de données |
| Table | Nom de la table où la sortie sera écrite. Le nom de la table est sensible à la casse et le schéma de cette table doit correspondre exactement au nombre de champs et aux types générés par la sortie de votre tâche. |

## Stockage d'objets blob ##

Le stockage d’objets blob offre une solution peu coûteuse et évolutive pour stocker de grandes quantités de données non structurées dans le cloud. Pour une présentation du stockage d’objets blob Azure et de son utilisation, consultez [Utilisation du stockage d’objets blob](./articles/storage-dotnet-how-to-use-blobs.md).

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie d’objets blob.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Nom du compte de stockage où vous envoyez votre sortie.</td>
</tr>
<tr>
<td>Clé du compte de stockage</td>
<td>Clé secrète associée au compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.</td>
</tr>
<tr>
<td>Séquence d’octets préfixe du chemin d’accès [facultatif]</td>
<td>Chemin d’accès de fichier utilisé pour écrire vos objets blob dans le conteneur spécifié.<BR>Dans le chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances de l’une des 2&#160;variables suivantes pour spécifier la fréquence d’écriture des objets blob&#160;:<BR>{date}, {time}<BR>Exemple&#160;1&#160;: cluster1/logs/{date}/{time}<BR>Exemple&#160;2&#160;: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Format de la date [facultatif]</td>
<td>Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple&#160;: JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [facultatif]</td>
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

## Concentrateur d'événements

[Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un service de réception d’événements de publication/d’abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde. Un Event Hub peut être utilisé en tant que sortie quand la sortie d’une tâche Stream Analytics est l’entrée d’une autre tâche de diffusion.

Quelques paramètres sont requis pour configurer les flux de données Event Hub en tant que sortie.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet Event Hub. |
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Concentrateur d'événements | Nom de votre sortie Event Hub |
| Nom de la stratégie de l’Event Hub | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configurer de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès |
| Clé de la stratégie de l’Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Colonne de clé de partition [facultatif] | Cette colonne contient la clé de partition pour la sortie du hub d’événements. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format | Applicable uniquement pour le type JSON. « Séparé par une ligne » spécifie que la sortie sera mise en forme avec chaque objet JSON séparé par une nouvelle ligne. « Tableau » spécifie que la sortie sera mise en forme en tant que tableau d’objets JSON. |
## Power BI

[Power BI](https://powerbi.microsoft.com/) peut être utilisé comme sortie d’une tâche Stream Analytics pour fournir une expérience de visualisation riche aux utilisateurs de Stream Analytics. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par les métriques.

> [AZURE.NOTE]À ce stade, la création et la configuration des sorties Power BI ne sont pas prises en charge dans le portail Azure en version préliminaire.

### Autorisation d’un compte Power BI

1.	Lorsque Power BI est sélectionné en tant que sortie dans le portail de gestion Azure, vous êtes invité à autoriser un utilisateur de Power BI existant ou à créer un compte Power BI.  

    ![Autoriser un utilisateur de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	Créez un compte si vous n’en avez pas déjà un, puis cliquez sur Autoriser maintenant. Un écran semblable à celui-ci s’affiche.

    ![Compte Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	Dans cette étape, indiquez le compte professionnel ou scolaire servant à autoriser la sortie Power BI. Si vous n’êtes pas déjà inscrit à Power BI, choisissez S’inscrire maintenant. Le compte professionnel ou scolaire que vous utilisez pour Power BI peut être différent du compte d’abonnement Azure avec lequel vous êtes actuellement connecté.

### Configuration des propriétés de sortie Power BI

Une fois le compte Power BI authentifié, vous pouvez configurer les propriétés pour votre sortie Power BI. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour configurer votre sortie Power BI.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette sortie Power BI. |
| Nom du jeu de données | Fournissez un nom de jeu de données qui doit être utilisé pour la sortie Power BI |
| Nom de la table | Fournissez un nom de table sous le jeu de données de la sortie Power BI. Actuellement, la sortie Power BI des tâches Stream Analytics peut avoir une table uniquement dans un jeu de données. |
| Nom du groupe | Pour activer le partage de données avec d’autres utilisateurs de Power BI, écrivez des données dans les groupes. Vous pouvez sélectionner des groupes à l’intérieur de votre compte Power BI ou choisir « Mon espace de travail » si vous ne souhaitez pas écrire dans un groupe. La mise à jour d’un groupe existant nécessite le renouvellement de l’authentification Power BI. |

> [AZURE.NOTE]Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table seront automatiquement remplis au démarrage de la tâche et lorsque celle-ci se mettra à extraire des sorties dans Power BI. Notez que si la requête de tâche ne génère aucun résultat, le jeu de données et la table ne seront pas créés. De même, veuillez noter que si Power BI dispose déjà d’un jeu de données et d’une table portant le même nom que celui saisi dans la tâche Stream Analytics, alors ces données seront écrasées.

### Renouvellement de l’autorisation Power BI

Il existe une limitation temporaire selon laquelle le jeton d'authentification doit être actualisé manuellement tous les 90 jours pour toutes les tâches avec une sortie Power BI. Vous devrez également authentifier de nouveau votre compte Power BI si son mot de passe a été modifié depuis la création ou la dernière authentification de votre tâche. Un symptôme de ce problème est l’absence de sortie de la tâche et une « erreur d’authentification de l’utilisateur » dans les journaux des opérations :

  ![Erreur de jeton d’actualisation Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI. Cliquez sur le lien « Renouveler l’autorisation », puis redémarrez votre tâche depuis l’heure du dernier arrêt pour éviter de perdre des données.

  ![Renouvellement de l’autorisation Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Stockage de tables

Le [stockage de tables Azure](./articles/storage-introduction.md) offre un stockage hautement disponible et massivement évolutif, de telle sorte qu’application puisse être mise à l’échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables est le magasin de clés/attributs NoSQL de Microsoft, qui peut être utilisé pour les données structurées avec moins de contraintes au niveau du schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage de tables. |
| Compte de stockage | Nom du compte de stockage où vous envoyez votre sortie. |
| Clé du compte de stockage | Clé d’accès associée au compte de stockage. |
| Nom de la table | Nom de la table. La table sera créée s’il n’existe pas. |
| Partition Key | Nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table donnée qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut atteindre 1 Ko. |
| Row Key | Nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition donnée. Elle constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut atteindre 1 Ko. |
| Taille du lot | Nombre d’enregistrements d’une opération par lot. En général, la valeur par défaut est suffisante pour la plupart des tâches. Pour plus d’informations sur la modification de ce paramètre, consultez [Spécifications des opérations par lot des tables](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |

## Files d'attente Service Bus

Les [files d’attente Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) permettent de livrer des messages selon le principe du premier entré, premier sorti (FIFO), à un ou plusieurs destinataires concurrents. En général, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

La table ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de file d’attente.

| Nom de la propriété | Description |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette file d’attente Service Bus. |
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. |
| Nom de la file d’attente | Nom de la file d’attente Service Bus. |
| Nom de la stratégie de file d’attente | Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la file d’attente. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de file d’attente | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format | Applicable uniquement pour le type JSON. « Séparé par une ligne » spécifie que la sortie sera mise en forme avec chaque objet JSON séparé par une nouvelle ligne. « Tableau » spécifie que la sortie sera mise en forme en tant que tableau d’objets JSON. |

## Rubriques Service Bus

Les files d’attente Service Bus offrent une communication de type un-à-un entre l’expéditeur et le destinataire, alors que les [rubriques Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une communication de type un-à-plusieurs.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette rubrique Service Bus. |
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Nom de la rubrique | Les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux Event Hubs. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Quand une rubrique est créée, elle reçoit également un nom. Les messages envoyés à une rubrique ne seront pas disponibles si un abonnement n’a pas été créé. Assurez-vous donc que la rubrique comprend un ou plusieurs abonnements |
| Nom de la stratégie de rubrique | Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la rubrique. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès |
| Clé de la stratégie de rubrique | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | S’il s’agit du format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |

## Base de données de documents

[DocumentDB azure](http://azure.microsoft.com/services/documentdb/) est un service de base de données de documents NoSQL entièrement géré qui permet d’utiliser des données de requêtes et de transactions sans schéma, offre des performances prévisibles et fiables et permet un développement rapide.

Le tableau ci-dessous répertorie les noms et les descriptions des propriétés pour la création d’une sortie DocumentDB.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Nom du compte</td>
<td>Nom du compte DocumentDB. Cela peut également être le point de terminaison du compte.</td>
</tr>
<tr>
<td>Clé du compte</td>
<td>Clé d’accès partagé du compte DocumentDB.</td>
</tr>
<tr>
<td>Base de données</td>
<td>Nom de la base de données DocumentDB.</td>
</tr>
<tr>
<td>Modèle de nom de collection</td>
<td>Modèle de nom de collection des collections à utiliser. Le format de nom de collection peut être construit à l’aide du jeton facultatif {partition}, où les partitions commencent à&#160;0.<BR>Par exemple, Voici les entrées valides&#160;:<BR>MyCollection{partition}<BR>MyCollection<BR>Notez que les collections doivent exister avant que le travail d’analyse de flux de données soit démarré et ne sont pas créées automatiquement.</td>
</tr>
<tr>
<td>Partition Key</td>
<td>Nom du champ dans les événements de sortie utilisé pour spécifier la clé de partitionnement de sortie sur les collections.</td>
</tr>
<tr>
<td>ID du document</td>
<td>Nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur laquelle sont basées les opérations d’insertion ou de mise à jour.</td>
</tr>
</tbody>
</table>


## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes
Stream Analytics, un service géré d’analyse de diffusion en continu des données à partir de l’Internet des objets vous a été présenté. Pour en savoir plus sur ce service, consultez les rubriques suivantes :

- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Oct15_HO1-->