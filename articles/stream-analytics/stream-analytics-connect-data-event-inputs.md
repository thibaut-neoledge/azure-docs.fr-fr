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
	ms.date="08/13/2015" 
	ms.author="jeffstok"/>

# Créer des entrées Stream Analytics

## Présentation des entrées Stream Analytics
---
Les entrées Stream Analytics sont définies en tant que connexion à une source de données. Stream Analytics propose une intégration de pointe aux sources azure de hub d'événements et de stockage d'objets blob externes et internes à l'abonnement de tâche. Comme les données sont envoyées à la source de données, elles sont utilisées par la tâche Stream Analytics et traitées en temps réel. Les entrées sont divisées en deux types distincts : les entrées de flux de données et les entrées de données de référence.

## Entrées de flux de données
---
Les tâches Stream Analytics doivent contenir au moins une entrée de flux de données qui sera traitée et transformée par la tâche. Le stockage d’objets blob Azure et Azure Event Hubs sont pris en charge en tant que sources d’entrée de flux de données. Les hubs d'événements Azure sont utilisés pour collecter des flux d'événements à partir de plusieurs appareils et services, comme par exemple les flux d'activités sur les réseaux sociaux, des informations boursière ou des données de capteurs. Le stockage d’objets blob Azure peut être utilisé comme source d’entrée pour la réception de données en bloc.

## Entrées de données de référence
---
Stream Analytics prend en charge un second type d'entrée : les données de référence. Il s’agit de données auxiliaires qui sont généralement utilisées pour effectuer des corrélations et des recherches. Ces données sont généralement statiques ou rarement modifiées. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 50 Mo.

## Création d’un flux de données d’entrée de hub d’événements
---
### Vue d’ensemble d’Event Hubs
Les Event Hubs constituent un service de réception d'événements hautement évolutif et la méthode d'absorption de données dans une tâche Stream Analytics la plus répandue. Event Hubs et Stream Analytics fournissent au client une solution complète pour des analyses en temps réel : Event Hubs permet aux clients d’alimenter Azure en événements en temps réel et les tâches de Stream Analytics peuvent les traiter en temps réel. Par exemple, les clients peuvent envoyer des clics web, des lectures de capteurs ou des journaux des événements en ligne sur Event Hubs et créer des travaux Stream Analytics pour utiliser Event Hubs comme flux de données d’entrée pour les opérations de filtrage, d’agrégation et de jointure en temps réel. Event Hubs peut être utilisé pour la sortie des données. Pour plus d'informations sur Event Hubs, consultez la documentation relative aux [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs").

### Groupes de consommateurs
Chaque entrée Event Hub Stream Analytics doit être configurée de manière à disposer de son propre groupe de consommateurs. Quand une tâche contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plus d'un lecteur en aval, ce qui impacte le total de lecteurs d'un même groupe de consommateurs. Pour éviter de dépasser la limite d'Event Hub de 5 lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche Stream Analytics. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d'informations, consultez le [Guide de programmation Event Hubs](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guide de programmation Event Hubs").

## Création d'un flux de données d'entrée Event Hub
---
### Ajout d'un Event Hub comme une entrée de flux de données  ###

1. Dans l'onglet Entrées de la tâche Stream Analytics, cliquez sur **AJOUTER UNE ENTRÉE** et sélectionnez l'option par défaut, **Flux de données**, puis cliquez avec le bouton droit.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Sélectionnez ensuite **Concentrateur d'événements**.

    ![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. Renseignez ou sélectionnez les champs suivants, puis cliquez sur le bouton droit :

    - **Alias d'entrée** : un nom convivial qui servira dans la requête de travail à faire référence à cette entrée  
    - **Espace de noms Service Bus** : un espace de noms Service Bus est un conteneur pour un jeu d'entités de messagerie. En créant un concentrateur d’événements, vous avez également créé un espace de noms Service Bus.  
    - **Concentrateur d'événements** : nom de votre concentrateur d'événements.  
    - **Nom de la stratégie du hub d'événements** : la stratégie d'accès partagé, qui peut être créée dans l'onglet Configuration du hub d'événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès.  
    - **Groupe de consommateurs du hub d'événements** (en option) : groupe de consommateurs qui absorbe les données du hub d'événements. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir du hub d’événements. Il est recommandé d’utiliser un groupe de consommateurs différent pour chaque tâche Stream Analytics.  

    ![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. Spécifiez les paramètres suivants :

    - **Format de sérialisation de l'événement** : pour s'assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données entrantes.  
    - **Encodage** : UTF-8 est le seul format de codage actuellement pris en charge.  

    ![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. Cliquez sur le bouton de vérification pour terminer cet Assistant et vérifier que Stream Analytics peut se connecter au hub d'événements.

## Création d'une entrée de flux de données de stockage d'objets blob
---
Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage d’objets blob offre une solution peu coûteuse et évolutive. Les données dans le stockage d'objets Blob sont généralement considérées comme « au repos », mais elles peuvent être traitées comme un flux de données par Stream Analytics. Le traitement du journal est un scénario courant pour les entrées de stockage d'objets Blob avec Stream Analytics dans lequel la télémétrie est capturée à partir d'un système et doit être analysée et traitée pour extraire des données pertinentes. Il est important de noter que l'horodatage par défaut des événements de stockage d'objets Blob dans Stream Analytics est l'horodatage de la création de l'objet blob. Pour traiter les données en tant que flux à l'aide d'un horodatage dans la charge utile d'événement, vous devez utiliser le mot clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx). Pour plus d'informations sur le stockage d'objets blob, consultez la documentation relative au [stockage d'objets blob](http://azure.microsoft.com/services/storage/blobs/).

### Ajout d'un stockage d'objets blob en tant qu'entrée de flux de données  ###

1. Dans l'onglet Entrées de la tâche Stream Analytics, cliquez sur **AJOUTER UNE ENTRÉE** et sélectionnez l'option par défaut, **Flux de données**, puis cliquez avec le bouton droit.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Sélectionnez **Stockage d'objets blob**, puis cliquez avec le bouton droit.

    ![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. Renseignez ou sélectionnez les champs suivants :

    - **Alias d'entrée** : un nom convivial qui servira dans la requête de travail à faire référence à cette entrée  
    - **Compte de stockage** : si le compte de stockage est associé à un autre abonnement que celui de la tâche de diffusion en continu, le nom et la clé du compte de stockage seront nécessaires.  
    - **Conteneur de stockage** : les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d'objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.  

    ![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. Cochez l'option **Configurer les paramètres avancés** pour configurer le modèle de préfixe de chemin d'accès pour la lecture d'objets blob dans un chemin d'accès personnalisé. Si ce champ n’est pas spécifié, Stream Analytics lira tous les objets blob du conteneur.

    ![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. Choisissez les paramètres suivants :

    - **Format de sérialisation de l'événement** : pour s'assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données entrantes.  
    - **Encodage** : UTF-8 est le seul format de codage actuellement pris en charge.  


    ![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. Cliquez sur le bouton de vérification pour terminer cet Assistant et vérifier que Stream Analytics peut se connecter au compte de stockage d'objets blob.

## Création de données de référence de stockage d'objets blob
---
Le stockage d'objets blob peut être utilisé pour définir les données de référence pour une tâche Stream Analytics. Il s'agit de données statiques ou à variation lente qui sont utilisées pour effectuer des recherches ou la corrélation des données. La prise en charge de l'actualisation des données de référence peut être activée en spécifiant un modèle de chemin d'accès dans la configuration d'entrée à l'aide des jetons {date} et {time}. Stream Analytics met à jour des définitions de données de référence en fonction de ce modèle de chemin d'accès. Par exemple, un modèle `"/sample/{date}/{time}/products.csv"` avec un format de date « AAAA-MM-JJ » et un format d'heure « HH:mm » indique à Stream Analytics de récupérer l'objet blob mis à jour `"/sample/2015-04-16/17:30/products.csv"` à 17h30 le 16 avril 2015 (UTC).

> [AZURE.NOTE]Actuellement, les tâches Stream Analytics ne recherchent les données de référence d’objets blob actualisées que lorsque l’heure coïncide avec celle encodée dans le nom de l’objet blob : par exemple, les tâches recherchent /sample/2015-04-16/17:30/products.csv entre 17:30 et 17:30:59.999999999 le 16 avril 2015, heure UTC. Quand il est 17:31, les tâches arrêtent de rechercher /sample/2015-04-16/17:30/products.csv et recherchent /sample/2015-04-16/17:31/products.csv.

Le seul cas où les objets blob de données de référence précédents sont pris en compte est lorsque la tâche démarre. À ce moment-là, la tâche recherche l’objet blob qui comprend dans son nom les date et heure les plus récemment encodées et dont la valeur est antérieure à l’heure de début de la tâche (l’objet blob de données de référence le plus récent avant le début de la tâche). Cela permet de garantir la présence d’un jeu de données de référence non vide au début de la tâche. En l’absence d’un tel jeu de données, la tâche échouera et affichera la notification de diagnostic suivante :

### Ajout d'un stockage d'objets blob comme données de référence  ###

1. Dans l'onglet Entrées de la tâche Stream Analytics, cliquez sur **AJOUTER UNE ENTRÉE**, puis sélectionnez l'option **Données de référence** et cliquez sur le bouton droit.

    ![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	Renseignez ou sélectionnez les champs suivants :

    - **Alias d'entrée** : un nom convivial qui servira dans la requête de travail à faire référence à cette entrée  
    - **Compte de stockage** : si le compte de stockage est associé à un autre abonnement que celui de la tâche de diffusion en continu, le nom et la clé du compte de stockage seront nécessaires.  
    - **Conteneur de stockage** : les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d'objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.  
    - **Modèle de chemin d'accès** : chemin d'accès du fichier utilisé pour localiser vos objets blob dans le conteneur spécifié. Dans le chemin d'accès, vous pouvez choisir de spécifier une ou plusieurs instances de l'une des 2 variables suivantes : {date}, {time}.  

    ![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. Choisissez les paramètres suivants :

    - **Format de sérialisation de l'événement** : pour s'assurer que vos requêtes fonctionnent comme prévu, Stream Analytics a besoin de connaître le format de sérialisation (JSON, CSV ou Avro) que vous utilisez pour les flux de données entrantes.  
    - **Encodage** : UTF-8 est le seul format de codage actuellement pris en charge.  

    ![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	Cliquez sur le bouton de vérification pour terminer cet Assistant et vérifier que Stream Analytics peut se connecter au compte de stockage d'objets blob.

    ![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO7-->