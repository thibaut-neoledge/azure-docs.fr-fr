<properties 
	pageTitle="Créer des sorties Stream Analytics | Microsoft Azure" 
	description="Découvrez comment vous connecter aux cibles de sortie et comment les configurer pour les solutions Stream Analytics." 
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
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# Créer des sorties Stream Analytics

## Présentation des sorties Stream Analytics ##
---
Quand vous créez une tâche Stream Analytics, vous devez penser à la façon dont elle sera consommée. De quelle façon les consommateurs de la transformation de données voient-ils les résultats de la tâche Stream Analytics ? Quels outils utiliseront-ils pour analyser la sortie ? La rétention et l’entreposage des données sont-ils requis ?

Azure Stream Analytics fournit sept méthodes différentes pour stocker et afficher les sorties de tâche : les bases de données SQL, le stockage d’objets blob, les hubs d’événements, les files d’attente Service Bus, les rubriques Service Bus, Power BI et le stockage de tables. Cela permet à la fois de faciliter l’affichage des sorties de tâche et de rendre plus flexibles la consommation et le stockage des sorties de tâche pour l’entreposage de données et autres utilisations.

## Utilisation d’une base de données SQL comme sortie ##
---
Vous pouvez utiliser une base de données SQL comme sortie pour les données relationnelles et pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Pour plus d’informations sur les bases de données SQL Azure, consultez [Bases de données SQL Azure](http://azure.microsoft.com/services/sql-database/).

### Paramètres ###

Pour utiliser une base de données SQL, vous devez disposer des informations suivantes concernant la base de données SQL :

1. Nom du serveur
2. Nom de la base de données
3. Combinaison nom d’utilisateur/mot de passe valide.
4. Nom de la table de sortie. Cette table doit déjà exister, sinon la tâche ne sera pas créée.

### Ajout d’une base de données SQL comme sortie ###

![graphic1][graphic1]

Accédez à l’onglet Sorties de la tâche, cliquez sur la commande **Ajouter une sortie**, puis cliquez sur Suivant.

![graphic2][graphic2]

Choisissez **Base de données SQL** comme sortie.

![graphic3][graphic3]

Sur la page suivante, entrez les informations de la base de données. L’alias de sortie est un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. Si une seule sortie existe, l’alias par défaut est « output ».

![graphic4][graphic4]

Si la base de données existe dans le même abonnement que celui de la tâche Stream Analytics, l’option Utiliser la base de données SQL de l’abonnement actuel est disponible. Sélectionnez ensuite la base de données dans la liste déroulante.

![graphic5][graphic5]

Cliquez sur Suivant pour ajouter cette sortie. Deux opérations doivent démarrer, la première étant d’ajouter la sortie.

![graphic6][graphic6]

La seconde est un test de la connexion. Azure Stream Analytics tente de se connecter à cette base de données SQL et de vérifier que les informations d’identification saisies sont correctes et que la table est accessible.

## Utilisation du stockage d’objets blob comme sortie ##
---
Pour une présentation du stockage d’objets blob Azure et de son utilisation, consultez la rubrique [Utilisation du stockage d’objets blob](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Paramètres ###

Pour utiliser une sortie de stockage d’objets blob, vous devez disposer des informations suivantes :

1. Si le compte de stockage est associé à un autre abonnement que celui de la tâche de diffusion en continu, vous devrez renseigner le nom et la clé du compte de stockage dans les champs qui s’afficheront.
2. Nom du conteneur.
3. Préfixe du nom du fichier.
4. Format de sérialisation utilisé pour les données (Avro, CSV, JSON).

### Ajout du stockage d’objets blob comme sortie ###

Sélectionnez la sortie du stockage d’objets blob.

![graphic20][graphic20]

Fournissez les détails comme indiqué ci-dessous :

![graphic21][graphic21]

## Utilisation d’un hub d’événements comme sortie ##
---
### Vue d'ensemble ###
 
Event Hubs est un service de réception d’événements hautement évolutif qui constitue le chemin d’entrée le plus répandu pour l’absorption des données Stream Analytics. Sa gestion robuste d’un nombre élevé d’événements le rend idéal pour les sorties de tâche. Les hubs d’événements peuvent être utilisés en tant que sortie quand la sortie d’une tâche Stream Analytics est l’entrée d’une autre tâche de diffusion. Pour plus d’informations sur Event Hubs, accédez au portail [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs").
 
### Paramètres ###

Quelques paramètres doivent être configurés pour les flux de données de hub d’événements.

1. Espace de noms Service Bus : espace de noms Service Bus du hub d’événements. Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. Quand vous créez un hub d’événements, vous créez en même temps un espace de noms Service Bus. 
2. Nom du hub d’événements : nom donné au hub d’événements. C’est le nom que vous avez spécifié lors de la création du hub d’événements. 
3. Nom de la stratégie du hub d’événements : nom de la stratégie d’accès partagé au hub d’événements. La stratégie d’accès partagé peut être configurée pour un hub d’événements sous l’onglet Configurer. Chaque stratégie d’accès partagé reçoit un nom, les autorisations que vous aurez définies, ainsi que les clés d’accès qui auront été générées.
4. Nom de la clé de la stratégie du hub d’événements : clé principale ou secondaire de la stratégie d’accès partagé au hub d’événements.  
5. Colonne de clé de partition : paramètre facultatif pour les sorties du hub d’événements. Cette colonne contient la clé de partition pour la sortie du hub d’événements.

### Ajout d’un hub d’événements comme sortie ###

1. En haut de la page, cliquez sur **Sortie**, puis sur**Ajouter une sortie**. Sélectionnez les hubs d’événements comme option de sortie, puis cliquez sur le bouton droit au bas de la fenêtre.

    ![graphic38][graphic38]

2. Fournissez les informations requises dans les champs de sortie. Une fois terminé, cliquez sur le bouton droit au bas de la fenêtre pour continuer.

    ![graphic36][graphic36]

3. Vérifiez que le format de sérialisation d’événements, le type d’encodage et le format possèdent les valeurs appropriées, puis cochez la case pour terminer l’action.

    ![graphic37][graphic37]

## Utilisation de Power BI comme sortie ##
---
### Vue d’ensemble ###
Power BI peut être utilisé comme sortie d’une tâche Stream Analytics pour fournir une expérience de visualisation riche aux utilisateurs de Stream Analytics. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par les métriques. Pour plus d’informations sur Power BI, consultez le site [Power BI](https://powerbi.microsoft.com/).

### Paramètres ###

Quelques paramètres sont nécessaires pour configurer les sorties Power BI.

1. Alias de sortie : tout alias de sortie portant un nom convivial et auquel il est facile de faire référence. Cet alias de sortie est particulièrement utile si vous décidez d’avoir plusieurs sorties pour votre tâche. Dans ce cas, votre requête fera référence à cet alias. Par exemple, utilisons la valeur d’alias de sortie « OutPbi ».
2. Nom du jeu de données : entrez le nom du jeu de données qui doit être utilisé pour la sortie Power BI. Par exemple, « pbidemo ».
2. Nom de la table : fournissez un nom de table sous le jeu de données de la sortie Power BI. Par exemple, « pbidemo ». **Actuellement, la sortie Power BI des travaux Stream Analytics peut avoir seulement une table dans un jeu de données.**

### Ajout de Power BI comme sortie ###

1.  En haut de la page, cliquez sur **Sortie**, puis sur**Ajouter une sortie**. Sélectionnez Power BI comme option de sortie.

    ![graphic22][graphic22]

2.  Un écran semblable à celui-ci s’affiche.

    ![graphic23][graphic23]

3.  Dans cette étape, indiquez le compte professionnel ou scolaire servant à autoriser la sortie Power BI. Si vous n’êtes pas déjà inscrit à Power BI, choisissez **Inscrivez-vous**.
4.  Un écran semblable à celui-ci s’affiche.

    ![graphic24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Cliquez sur **OK**, puis **Tester la connexion**. La configuration de sortie est terminée.


## Utilisation du stockage de tables Azure comme sortie ##
---
Le stockage de tables offre un stockage hautement disponible et massivement évolutif, de telle sorte que votre application puisse être mise à l’échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables est le magasin de clés/attributs NoSQL de Microsoft, qui peut être utilisé pour les données structurées avec moins de contraintes au niveau du schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace. Pour plus d’informations sur le stockage de tables Azure, consultez [Stockage de tables Azure](./articles/storage/storage-introduction.md).

### Paramètres ###

Pour utiliser un stockage de tables Azure, vous devez disposer des informations suivantes :

1. Nom du compte de stockage (si le stockage est associé à un autre abonnement que celui de votre tâche de diffusion).
2. Clé du compte de stockage (si le stockage est associé à un autre abonnement que celui de votre tâche de diffusion).
3. Nom de table de sortie (sera créé s’il n’existe pas).
4. Clé de partition (obligatoire).
5. Clé de ligne

Pour une meilleure conception de la clé de partition et de la clé de ligne, veuillez vous référer à l’article ci-dessous [Conception d’une stratégie de partitionnement évolutive pour Azure Table Storage](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Ajout du stockage de tables Azure comme sortie ###

![graphic11][graphic11]

Accédez à l’onglet Sorties de la tâche, cliquez sur la commande **Ajouter une sortie**, puis cliquez sur Suivant.

![graphic12][graphic12]

Choisissez **Stockage de tables** comme sortie.

![graphic13][graphic13]

Dans la page suivante, entrez les informations de la table Azure. L’alias de sortie est le nom que vous pouvez utiliser dans votre requête pour diriger la sortie de cette requête vers cette table.

![graphic14][graphic14]

![graphic15][graphic15]

La taille du lot correspond au nombre d’enregistrements d’une opération de traitement par lot. En général, la valeur par défaut est suffisante pour la plupart des tâches. Pour plus d’informations sur la modification de ce paramètre, consultez les [spécifications des opérations de traitement par lot des tables](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx).

Si un compte de stockage Azure existe dans le même abonnement que celui utilisé pour créer la tâche, sélectionnez « Utiliser le compte de stockage de l’abonnement actuel », puis choisissez le compte de stockage dans la liste déroulante.

Cliquez sur Suivant pour ajouter cette sortie. Deux opérations doivent démarrer, la première étant d’ajouter la sortie.

![graphic16][graphic16]

La seconde est un test de la connexion. Azure Stream Analytics tente de se connecter à ce compte de stockage et de vérifier que les informations d’identification saisies sont correctes.

## Files d'attente Service Bus ##
---
### Présentation des concepts associés aux files d’attente Service Bus ###
Les files d’attente Service Bus permettent de livrer des messages selon le principe du premier entré, premier sorti (FIFO), à un ou plusieurs destinataires concurrents. En général, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

Pour plus d’informations sur les files d’attente Service Bus, consultez [Files d’attente, rubriques et abonnements Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx "Files d'attente, rubriques et abonnements Service Bus") et [Présentation des files d’attente Service Bus](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Présentation des files d’attente Service Bus").

### Paramètres ###

Pour utiliser une sortie de file d’attente Service Bus, vous devez disposer des informations suivantes :

1. Alias de sortie : tout alias de sortie portant un nom convivial et auquel il est facile de faire référence. Cet alias de sortie est particulièrement utile si vous décidez d’avoir plusieurs sorties pour votre tâche. Dans ce cas, la requête de tâche fera référence à cet alias.
2. Espace de noms et nom Service Bus.
3. Nom de la file d’attente : les files d’attente sont des entités de messagerie qui sont similaires aux rubriques et aux hubs d’événements. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Quand une file d’attente est créée, elle reçoit également un nom.
4. Format de sérialisation utilisé pour les données (Avro, CSV, JSON).

### Ajout des files d’attente Service Bus comme sortie ###

![graphic31][graphic31]

Fournissez les informations requises, comme illustré ci-dessous, puis sélectionnez Suivant.

![graphic32][graphic32]

Vérifiez que le format et la sérialisation des données sont corrects.

![graphic33][graphic33]

## Rubriques Service Bus ##
---
### Présentation des concepts associés aux rubriques Service Bus ###
Les files d’attente Service Bus offrent une communication de type un-à-un entre l’expéditeur et le destinataire, alors que les rubriques Service Bus offrent une communication de type un-à-plusieurs.

Pour plus d’informations sur les files d’attente Service Bus, consultez [Files d’attente, rubriques et abonnements Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx "Files d'attente, rubriques et abonnements Service Bus") et [Présentation des rubriques Service Bus](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Présentation des rubriques Service Bus").

### Paramètres ###

Pour utiliser une sortie de rubrique Service Bus, vous devez disposer des informations suivantes :

1. Alias de sortie : tout alias de sortie portant un nom convivial et auquel il est facile de faire référence. Cet alias de sortie est particulièrement utile si vous décidez d’avoir plusieurs sorties pour votre tâche. Dans ce cas, votre requête fera référence à cet alias.
2. Espace de noms et nom Service Bus.
3. Nom de la rubrique : les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux hubs d’événements. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Quand une rubrique est créée, elle reçoit également un nom. Les messages envoyés à une rubrique ne seront pas disponibles si un abonnement n’a pas été créé. Assurez-vous donc que la rubrique comprend un ou plusieurs abonnements.
4. Format de sérialisation utilisé pour les données (Avro, CSV, JSON).

### Ajout des rubriques Service Bus comme sortie ###

Sélectionnez les rubriques Service Bus comme option de sortie.

![graphic34][graphic34]

Fournissez les informations requises, comme illustré ci-dessous, puis sélectionnez Suivant.

![graphic35][graphic35]

Vérifiez que le format et la sérialisation des données sont corrects.

![graphic33][graphic33]


## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Nov15_HO3-->