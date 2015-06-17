<properties 
	pageTitle="Tableau de bord Power BI de Stream Analytics | Azure" 
	description="Découvrez comment remplir un tableau de bord Power BI avec les données d’un travail Stream Analytics." 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>
	
#Azure Stream Analytics & Power BI : tableau de bord en direct sur l’analyse en temps réel des flux de données

Un des usages courants d’Azure Stream Analytics consiste à analyser des volumes élevés de flux de données en temps réel et obtenir l’analyse dans un tableau de bord en direct (qui se met à jour en temps réel sans que les utilisateurs n’aient à réactualiser le navigateur). [Microsoft Power BI](https://powerbi.com/) est parfait pour générer un tableau de bord en temps réel sans délai. [Voici une vidéo d’exemple pour illustrer le scénario](https://www.youtube.com/watch?v=SGUpT-a99MA). Dans cet article, découvrez comment utiliser Power BI comme sortie pour votre travail Azure Stream Analytics. Remarque - Bien qu’Azure Stream Analytics est disponible, la sortie Power BI est au stade de fonctionnalité en version préliminaire.

##Configuration requise

* Un compte Microsoft Azure utilisant un ID d’organisation (Power BI fonctionne avec un ID d’organisation uniquement. L’ID d’organisation est l’adresse de messagerie professionnelle, par exemple xyz@mycompany.com. Les adresses de messagerie personnelles comme xyz@hotmail.com ne sont pas des ID d’organisation. [Plus d’informations sur les ID d’organisation](https://www.arin.net/resources/request/org.html) ).
* Un flux d’entrée pour alimenter un travail ASA (Azure Stream Analytics) en flux de données. À ce stade, ASA accepte les entrées depuis un concentrateur d’événements Azure ou un magasin d’objets blob Azure.  

##Création d’un travail Azure Stream Analytics

Dans le [portail Azure](https://manage.windowsazure.com), cliquez sur **Nouveau, Services de données, Stream Analytics, Création rapide**.

Spécifiez les valeurs suivantes, puis cliquez sur **Créer un travail Stream Analytics** :

* **Nom du travail** - entrez un nom pour le travail. Par exemple, **DeviceTemperatures**.
* **Région** - sélectionnez la région où vous souhaitez exécuter le travail. Envisagez de placer le travail et le concentrateur d’événements dans la même région afin d’améliorer les performances et d’éviter le coût d’un transfert de données entre différentes régions.
* **Compte de stockage** - choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.

Dans le volet gauche, cliquez sur **Stream Analytics** pour afficher une liste des travaux Stream Analytics.

![graphic1][graphic1]

> [AZURE.TIP]Le nouveau travail est affiché avec l’état **Non démarré**. Notez que le bouton **Démarrer** situé en bas de la page est désactivé. Cela est dû au fait qu’avant de pouvoir démarrer le travail, vous devez configurer son entrée, sa sortie, sa requête, etc.

##Spécification d'une entrée de travail

Pour ce didacticiel, nous supposons que vous utilisez EventHub comme entrée avec la sérialisation JSON et l’encodage utf-8.

* Cliquez sur le nom du travail.
* En haut de la page, cliquez sur **Entrées**, puis sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera le long d’une procédure de configuration de votre entrée.
*	Sélectionnez **Flux de données**, puis cliquez avec le bouton droit.
*	Sélectionnez **Concentrateur d’événements**, puis cliquez avec le bouton droit.
*	Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :
  *	**Alias d’entrée** - entrez un nom convivial pour cette entrée de travail. Notez que vous utiliserez ce nom dans la requête par la suite.
  * **Concentrateur d’événements** - si le concentrateur d’événements que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, sélectionnez l’espace de noms dans lequel est situé le concentrateur d’événements.
*	Si votre concentrateur d’événements est situé dans un autre abonnement, sélectionnez **Utiliser le concentrateur d’événements à partir d’un autre abonnement** et entrez manuellement l’**espace de noms Service Bus**, le **nom du concentrateur d’événements**, le **nom de la stratégie du concentrateur d’événements**, la **clé de stratégie du concentrateur d’événements** et le **nombre de partitions du concentrateur d’événements**.

> [AZURE.NOTE]Cet exemple utilise le nombre de partitions par défaut : 16.

* **Nom du concentrateur d’événements** - sélectionnez le nom du concentrateur d’événements Azure que vous possédez.
* **Nom de la stratégie du concentrateur d’événements** - sélectionnez la stratégie du concentrateur d’événements pour le concentrateur d’événements que vous utilisez. Assurez-vous que cette stratégie a des autorisations de gestion.
*	**Groupe de consommateurs du concentrateur d’événements** - vous pouvez laisser ce champ vide ou spécifier un groupe de consommateurs présent sur votre concentrateur d’événements. Notez que chaque groupe de consommateurs d’un concentrateur d’événements peut avoir seulement 5 lecteurs à la fois. Décidez en conséquence du groupe de consommateurs approprié pour votre travail. Si vous laissez le champ vide, le groupe de consommateurs par défaut est utilisé.

*	Cliquez avec le bouton droit.
*	Spécifiez les valeurs suivantes :
  *	**Format du sérialiseur d’événements** - JSON
  *	**Encodage** - UTF8
*	Cliquez sur la coche pour ajouter cette source et vérifier que Stream Analytics peut se connecter au concentrateur d’événements.

##Ajouter une sortie Power BI

1.  En haut de la page, cliquez sur **Sortie**, puis sur**Ajouter une sortie**. Power BI est répertorié comme option de sortie.

![graphic2][graphic2]

> [AZURE.NOTE]Remarque : la sortie Power BI est disponible uniquement pour les comptes Azure utilisant des ID d’organisation. Si vous n’utilisez pas un ID d’organisation pour votre compte Azure (par exemple, votre ID Windows Live/compte personnel Microsoft), l’option de sortie Power BI ne s’affiche pas.

2.  Sélectionnez **Power BI**, puis cliquez avec le bouton droit.
3.  Un écran de ce type s’affiche :

![graphic3][graphic3]

4.  Dans cette étape, vous devez veiller à utiliser le même ID d’organisation que pour votre travail ASA. À ce stade, la sortie Power BI doit utiliser le même ID d’organisation que votre travail ASA. Si vous avez déjà un compte Power BI utilisant le même ID d’organisation, sélectionnez « Autoriser maintenant ». Dans le cas contraire, cliquez sur « Inscrivez-vous » et utilisez le même ID d’organisation que votre compte Azure en vous inscrivant à Power BI. [Voici un bon blog décrivant les détails de l’inscription à Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).
5.  Ensuite, un écran de ce type s’affiche :

![graphic4][graphic4]

Fournissez les valeurs suivantes :

* **Alias de sortie** - Vous pouvez saisir n’importe quel alias de sortie auquel il vous sera facile de vous référer. Cet alias de sortie est particulièrement utile si vous décidez d’avoir des sorties multiples pour votre travail. Dans ce cas, vous devez faire référence à cette sortie dans votre requête. Par exemple, utilisons la valeur d’alias de sortie = « OutPbi ».
* **Nom du jeu de données** - Fournissez un nom de jeu de données que vous souhaitez donner à votre sortie de Power BI. Par exemple, utilisons « pbidemo ».
*	**Nom de la table** - Fournissez un nom de table dans le jeu de données de la sortie de Power BI. Appelons-la par exemple « pbidemo ». Actuellement, la sortie Power BI des travaux ASA peut avoir seulement une table dans un jeu de données.

>	[AZURE.NOTE] Note - You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your ASA job and the job starts pumping output into Power BI. If your ASA job query doesn’t return any results, the dataset and table will not be created.

*	Cliquez sur **OK**, puis **Tester la connexion** . Votre configuration de sortie est terminée.

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this ASA job, the existing data will be overwritten.


##Écriture d’une requête

Accédez à l’onglet **Requête** de votre travail. Écrivez la requête dont vous souhaitez obtenir la sortie dans votre Power BI. Par exemple, il peut s’agir de la requête SQL suivante :

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
Démarrez votre travail. Assurez-vous que votre concentrateur d’événements reçoit des événements et que votre requête génère les résultats attendus. Si votre requête renvoie 0 ligne, le jeu de données et les tables de Power BI ne sont pas créés automatiquement.

##Créez le tableau de bord dans Power BI

Accédez à [Powerbi.com](https://powerbi.com) et connectez-vous à l’aide de votre ID d’organisation. Si la requête du travail ASA génère des résultats, vous voyez que votre jeu de données est déjà créé :

![graphic5][graphic5]

Pour créer le tableau de bord, cliquez sur l’option Tableaux de bord et créez un tableau de bord.

![graphic6][graphic6]

Dans cet exemple, nous l’appellerons « Tableau de bord de démonstration ».

Cliquez maintenant sur le jeu de données créé par votre travail ASA (pbidemo dans notre exemple). Vous accédez alors à une page pour créer un graphique sur ce jeu de données. Voici un des exemples de rapports que vous pouvez créer :

Sélectionnez les champs Σ temp et heure. Ils vont automatiquement dans Valeur et Axe sur le graphique :

![graphic7][graphic7]

Avec cela, vous obtenez automatiquement un graphique comme celui-ci :

![graphic8][graphic8]

Dans la section valeur, cliquez sur la liste déroulante pour temp et sélectionnez **moyenne** pour la température. Dans le graphique, cliquez sur **visualisation** et choisissez **graphique en courbes** :

![graphic9][graphic9]

Vous obtenez un graphique en courbes de la moyenne dans le temps. En utilisant l’option d’épinglage comme montré ci-dessous, vous pouvez épingler ce rapport au tableau de bord que vous avez créé précédemment :

![graphic10][graphic10]

Lorsque vous affichez le tableau de bord avec ce rapport épinglé, vous constatez que celui-ci est mis à jour en temps réel. Essayez de modifier les données de vos événements (par exemple, le pic de température). L’effet apparaît alors en temps réel sur votre tableau de bord.

Notez que ce didacticiel ne vous a montré la création que d’un seul type de graphique pour un jeu de données. Toutefois, les possibilités de Power BI sont illimitées. Pour un autre exemple de tableau de bord Power BI, visionnez la vidéo de [mise en route avec Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Vous pouvez également consulter la page [Tableaux de bord dans Power BI en version préliminaire](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview) pour en savoir plus sur la création des tableaux de bord avec Power BI.

## Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png

<!--HONumber=52-->
 