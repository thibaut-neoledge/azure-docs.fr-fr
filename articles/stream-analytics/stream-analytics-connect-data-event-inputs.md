<properties 
	pageTitle="Créer des entrées Stream Analytics | Microsoft Azure" 
	description="Découvrez comment vous connecter aux sources d’entrée et comment les configurer pour les solutions Stream Analytics."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Créer des entrées Stream Analytics

## Présentation des entrées Stream Analytics
---
Les entrées Stream Analytics sont définies en tant que connexion à une source de données. Comme les données sont envoyées à la source de données, elles sont utilisées par la tâche Stream Analytics et traitées en temps réel. Les entrées se divisent en deux groupes distincts, les entrées de flux de données et les entrées de données de référence. Stream Analytics propose une meilleure intégration aux sources d’entrée de hub d’événements et de stockage d’objets blob externes et internes à l’abonnement de tâche. Les formats de données pris en charge sont Avro, CSV et JSON.

## Entrées de flux de données
---
À la base, chaque définition de tâche Stream Analytics doit contenir au moins une source d’entrée de flux de données qui sera traitée et transformée par la tâche. Le stockage d’objets blob Azure et Azure Event Hubs sont pris en charge en tant que sources d’entrée de flux de données. Les sources d’entrée Azure Event Hubs sont utilisées pour collecter les flux d’événements à partir de plusieurs appareils et services. Les flux de données peuvent être, par exemple, des flux d’activités de médias sociaux, des informations boursières ou des données de capteurs.

Le stockage d’objets blob Azure peut être utilisé comme source d’entrée pour la réception de données en bloc. Il est important de noter que lors de l’utilisation d’objets blob Azure, les données sont au repos. Par conséquent, Stream Analytics interprète toutes les données contenues dans un objet blob comme ayant le même horodatage que celui de la création de l’objet blob, à moins que les enregistrements de l’objet blob ne contiennent des horodatages et que le mot clé TIMESTAMP BY ne soit utilisé.

## Entrées de données de référence
---
Stream Analytics prend également en charge un deuxième type de source d’entrée : les données de référence. Il s’agit de données auxiliaires qui sont généralement utilisées pour effectuer des corrélations et des recherches. Ces données sont généralement statiques ou rarement modifiées. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 50 Mo.

La prise en charge de l’actualisation des données de référence peut être activée en spécifiant un modèle de chemin d’accès dans la configuration d’entrée à l’aide des jetons {date} et {time}. Le travail charge l'objet blob correspondant en fonction de la date et de l'heure codées dans les noms d'objets blob selon le fuseau horaire UTC. Cette séquence d’objets blob de données de référence, avec date et heure encodées, est nécessaire pour garantir la cohérence des résultats. Par exemple, en cas de retard de traitement et de nécessité de recharger un fichier d’objets blob, on peut s’attendre à ce que le fichier se trouve au même emplacement sans être modifié, sinon, la sortie serait différente. Le seul scénario pris en charge consiste à ajouter de nouveaux objets blob avec les futures date et heure encodées dans le modèle de chemin d’accès.

Par exemple, si la tâche a une entrée de référence configurée dans le portail avec le modèle de chemin d’accès suivant : /sample/{date}/{heure}/products.csv où le format de date est « AAAA-MM-JJ » et le format de l’heure est « HH:mm », alors le travail récupère un fichier nommé /sample/2015-04-16/17:30/products.csv à 17:30 le 16 avril 2015 (UTC)


> [AZURE.NOTE]Actuellement, les tâches Stream Analytics ne recherchent les données de référence d’objets blob actualisées que lorsque l’heure coïncide avec celle encodée dans le nom de l’objet blob : par exemple, les tâches recherchent /sample/2015-04-16/17:30/products.csv entre 17:30 et 17:30:59.999999999 le 16 avril 2015, heure UTC. Quand il est 17:31, les tâches arrêtent de rechercher /sample/2015-04-16/17:30/products.csv et recherchent /sample/2015-04-16/17:31/products.csv.

Le seul cas où les objets blob de données de référence précédents sont pris en compte est lorsque la tâche démarre. À ce moment-là, la tâche recherche l’objet blob qui comprend dans son nom les date et heure les plus récemment encodées et dont la valeur est antérieure à l’heure de début de la tâche (l’objet blob de données de référence le plus récent avant le début de la tâche). Cela permet de garantir la présence d’un jeu de données de référence non vide au début de la tâche. En l’absence d’un tel jeu de données, la tâche échouera et affichera la notification de diagnostic suivante :

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## Création d’une entrée de flux de données
---
Pour créer une entrée de flux de données, accédez à l’onglet **Entrées** de la tâche Stream Analytics, puis cliquez sur **Ajouter une entrée** au bas de la page.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 Quand il crée une entrée de flux de données, l’utilisateur peut choisir entre l’option [**Hub d’événements**](Creating-an-Event-hub-input-data-stream) et l’option [**Stockage d’objets blob**] (Création d’un flux de données d’entrée de stockage d’objets blob). Sélectionnez l’option qui convient au type de flux de données à traiter.

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Création d’un flux de données d’entrée de hub d’événements
---
### Vue d’ensemble d’Event Hubs
Event Hubs est un service de réception d’événements hautement évolutif qui constitue le chemin d’entrée le plus répandu pour l’absorption des données Stream Analytics. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Event Hubs et Stream Analytics fournissent au client une solution complète pour des analyses en temps réel : Event Hubs permet aux clients d’alimenter Azure en événements en temps réel et les tâches de Stream Analytics peuvent les traiter en temps réel. Par exemple, les clients peuvent envoyer des clics web, des lectures de capteurs ou des journaux des événements en ligne sur Event Hubs et créer des travaux Stream Analytics pour utiliser Event Hubs comme flux de données d’entrée pour les opérations de filtrage, d’agrégation et de jointure en temps réel. Event Hubs peut être utilisé pour la sortie des données. Event Hubs peut notamment être utilisé en tant que sortie quand la sortie d’une tâche Stream Analytics est l’entrée d’une autre tâche de diffusion. Pour plus d’informations sur Event Hubs, consultez la documentation relative à [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs").

### Groupes de consommateurs
Chaque entrée d’une tâche Stream Analytics doit être configurée pour disposer de son propre groupe de consommateurs de hub d’événements. Quand une tâche contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plus d’un lecteur en aval. De ce fait, le nombre total de lecteurs d’un même groupe de consommateurs peut excéder la limite du hub d’événements de 5 lecteurs par groupe de consommateurs. Dans ce cas, la requête doit être divisée en plusieurs requêtes et les résultats intermédiaires routés par le biais de hubs d’événements supplémentaires. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d’informations, consultez le [Guide de programmation Event Hubs](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guide de programmation Event Hubs").

## Exemple de création d’une entrée de hub d’événements dans le portail Azure
---
Voici une procédure pas à pas pour configurer un hub d’événements en tant qu’entrée. Pour utiliser une entrée de hub d’événements, l’utilisateur doit disposer des informations suivantes sur le hub d’événements :

1. Alias d’entrée : un alias d’entrée avec un nom convivial fera l’objet d’une référence dans la requête de tâche.
2. Nom de l’espace de noms Service Bus. 
3. Nom du hub d’événements.
3. Nom de la stratégie du hub d’événements.
4. Facultatif : nom du groupe de consommateurs du hub d’événements.
	- Groupe de consommateurs qui absorbe les données du hub d’événements. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir du hub d’événements. Il est recommandé d’utiliser un groupe de consommateurs différent pour chaque tâche Stream Analytics.
5. Le format de sérialisation est utilisé pour le flux de données (Avro, CSV, JSON).

Sélectionnez d’abord **Ajouter une entrée** depuis la page Entrées de la tâche Stream Analytics.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Sélectionnez ensuite le hub d’événements comme entrée.

![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

Ensuite, entrez les informations dans les champs pour le hub d’événements, comme indiqué ci-dessous.

![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

Vérifiez que le format de sérialisation d’événements est correct pour le flux de données.

![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

Sélectionnez **Terminé**. L’entrée de flux de données du hub d’événements est maintenant créée.

## Création d’un flux de données d’entrée de stockage d’objets blob
---
Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage d’objets blob offre une solution peu coûteuse et évolutive. Ces données sont généralement considérées comme étant « au repos ». Un objet blob peut être utile pour une entrée de flux de données dans le cadre d’une analyse de fichiers journaux, où ces derniers seraient déjà capturés sur des systèmes et devraient être analysés, et que des données importantes devraient être extraites. Un objet blob pourrait également être utile dans le cadre d’une analyse de données au repos. Pour plus d’informations sur le stockage d’objets blob, consultez la documentation relative au [stockage d’objets blob](http://azure.microsoft.com/services/storage/blobs/).

Voici une procédure pas à pas pour configurer le stockage d’objets blob en tant qu’entrée. Les informations suivantes sont requises pour utiliser une entrée de stockage d’objets blob Azure :

1. Alias d’entrée : un alias d’entrée avec un nom convivial fera l’objet d’une référence dans la requête de tâche.
2. Si le compte de stockage est associé à un autre abonnement que celui de la tâche de diffusion en continu, le nom et la clé du compte de stockage seront nécessaires.
3. Nom du conteneur.
4. Préfixe du nom du fichier.
5. Format de sérialisation utilisé pour les données (Avro, CSV, JSON).

Sous l’onglet Entrées de la tâche Stream Analytics, cliquez sur **Ajouter une entrée**, puis sélectionnez l’option par défaut, **Flux de données**. ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Sélectionnez ensuite **Stockage d’objets blob**.

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

Entrez les informations dans les champs pour le compte de stockage, comme indiqué ci-dessous.

![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]Si vous cochez l’option Configurer les paramètres avancés, vous pourrez spécifier le modèle de préfixe de chemin d’accès pour la lecture des objets blob dans un chemin d’accès personnalisé. Si ce champ n’est pas spécifié, Stream Analytics lira tous les objets blob du conteneur.

![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

Choisissez ensuite le paramètre de sérialisation convenant aux données. Les options sont JSON, CSV et Avro.

![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

Sélectionnez **Terminé**. L’entrée de flux de données du stockage d’objets blob est maintenant créée.

## Création d’une entrée de données de référence de stockage d’objets blob
---
Vous pouvez profiter du stockage d’objets blob pour utiliser les fonctionnalités des données de référence.

Voici une procédure pas à pas pour configurer le stockage d’objets blob en tant qu’entrée de données de référence. Pour commencer, les informations suivantes sont requises :

1. Alias d’entrée : un alias d’entrée avec un nom convivial fera l’objet d’une référence dans la requête de tâche.
2. Si le compte de stockage est associé à un autre abonnement que celui de la tâche de diffusion en continu, le nom et la clé du compte de stockage seront nécessaires.
3. Nom du conteneur.
4. Préfixe du nom du fichier.
5. Format de sérialisation utilisé pour les données (CSV, JSON).
6. Modèle de chemin d’accès. Chemin d’accès de fichier utilisé pour localiser vos objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances de l’une des deux variables suivantes : {date} et {time}.


Sous l’onglet Entrées de la tâche Stream Analytics, cliquez sur **Ajouter une entrée**, puis sélectionnez l’option par défaut, **Données de référence**.

![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

Entrez les informations dans les champs pour le stockage d’objets blob et le compte de stockage, comme indiqué ci-dessous.

![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

Assurez-vous de faire défiler vers le bas pour spécifier le modèle de préfixe pour la hiérarchie des chemins d’accès qui contient l’objet blob, ainsi que le format des champs de date et d’heure.

![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

Choisissez le paramètre de sérialisation convenant aux données. Les options sont JSON, CSV et Avro.

![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

Sélectionnez **Terminé**. L’entrée de données de référence du stockage d’objets blob est maintenant créée.


## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-fr/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->