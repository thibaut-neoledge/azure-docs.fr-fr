<properties
   pageTitle="Utilisation du connecteur Azure Machine Learning dans Logic Apps | Microsoft Azure App Service"
   description="Création et configuration du connecteur Azure Machine Learning et utilisation dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="jehollan"/>
   
# Vue d'ensemble
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Le connecteur Azure ML pour Logic Apps permet d’appeler des API Azure ML pour la notation par lots (service d’exécution de lots) et la reformation. Ces fonctionnalités associées aux déclencheurs d’application logique permettent de planifier des tâches de traitement par lots et de configurer la reformation planifiée de modèles.

 ![][1]
 
## Utilisation et ajout du connecteur Azure Machine Learning dans votre application logique
Pour commencer, créez une expérience dans Azure ML Studio, puis configurez et déployez un service Web. Vous pouvez ensuite utiliser l’URL et la clé de l’API de l’URL du billet BES qui se trouve sur la page d’aide d’extraction de lots ([procédure Machine Learning](../machine-learning/machine-learning-walkthrough-5-publish-web-service.md)).

Pour exécuter une tâche BES à l’aide du connecteur, ajoutez le connecteur Azure ML dans votre application logique. Entrez ensuite les informations requises (voir ci-dessous pour plus d'informations). Pour configurer la reformation, ajoutez un second connecteur Azure ML et renseignez les paramètres d’entrée (voir [Configuration d’un modèle pour la reformation](../machine-learning/machine-learning-retrain-models-programmatically.md).

## Exécution d’une tâche d'exécution de lots Azure ML
Le connecteur Azure ML propose les quatre options suivantes pour les tâches d’exécution de lots (BES) :
1.	Traitement par lots avec entrée et sortie : l’expérience dispose de modules d’entrée et de sortie du service Web.
2.	Traitement par lots sans entrée ni sortie : l’expérience ne dispose pas de modules d’entrée et de sortie du service Web (elle utilise par exemple des modules Lecteur et Enregistreur).
3.	Traitement par lots avec entrée uniquement : l’expérience dispose d’un module d’entrée du service Web, mais ne dispose d’aucun module de sortie du service Web (elle utilise par exemple un module Enregistreur).
4.	Traitement par lots avec sortie uniquement : l’expérience ne dispose d’aucun module d’entrée du service Web, mais dispose d’un module de sortie du service Web (elle utilise par exemple un module Lecteur) Notez que BES est une requête asynchrone et qu’il peut prendre du temps pour s’exécuter selon la taille de vos données et la complexité du modèle. Une fois la tâche terminée, le connecteur renvoie le résultat de sortie.

### Exécution d’une tâche d’exécution de lots : avec entrée et sortie
Si l’expérience Studio dispose de modules d’entrée et de sortie du service web, vous devez [fournir des informations sur le compte et l’emplacement des objets blob de stockage](../machine-learning/machine-learning-consume-web-services.md). En outre, vous pouvez inclure des paramètres Global (service web) s’ils sont définis dans votre expérience ([paramètres du service web Machine Learning](../machine-learning/machine-learning-web-service-parameters.md)).

![][2]

Cliquez sur les points de suspension pour afficher et masquer les champs du paramètre Global. Vous pouvez spécifier un ou plusieurs paramètres Global (service Web) dans une liste de champs et de valeurs séparés par une virgule.

![][3]

D’autres variations des tâches BES, telles qu’une tâche sans entrée ni sortie du service Web, sont également disponibles via le connecteur.

## Configuration de la reformation

Utilisez l’action Configurer la reformation pour configurer la reformation ponctuelle ou planifiée de votre modèle ML. À l’aide d’une tâche d’exécution de lots créée dans le connecteur, vous pouvez suivre les étapes pour la formation et la mise à jour d’un modèle de service Web. Dans ce flux de travail, vous devez utiliser le connecteur à deux reprises.
1.	Le premier connecteur est utilisé pour exécuter la tâche BES afin de reformer votre modèle et de renvoyer la sortie. La sortie de cette exécution présentera l'URL du nouveau modèle (.ilearner). Si vous l’avez défini comme tel dans votre expérience, elle peut également renvoyer l’URL de sortie du module Évaluer, qui est un fichier.csv. Dans l'étape suivante, vous pouvez utiliser les données de la sortie du module Évaluer pour décider s'il faut remplacer le modèle dans votre service Web (par exemple, si l’exactitude est supérieure à 0,85).
1.	Le second connecteur est utilisé pour configurer la reformation. Il utilise les paramètres de la sortie du premier connecteur pour vérifier éventuellement la condition du modèle de mise à jour et mettre à jour le service web avec le modèle récemment formé.
  *	Par exemple, vous pouvez accéder à la sortie de la tâche BES avec l’URL du modèle récemment formé en entrant `@{body('besconnector').Results.output2.FullURL}` dans le champ d’URL du modèle reformé. Cela suppose que la sortie du service web dans votre expérience de formation est appelée output2.
  *	Pour le nom de la ressource, utilisez le nom complet du modèle formé enregistré dans l’expérience prédictive, par exemple MyTrainedModel modèle [formé].
  *	Pour le champ Clé de résultat de l’évaluation, vous pouvez entrer les paramètres renvoyés dans la sortie du Module d’évaluation de l’expérience de formation (si vous l’avez incluse). Vous pouvez consulter la liste des paramètres disponibles en visualisant les résultats du module Évaluer dans l'expérience de formation dans Azure ML Studio. Dans le cas d’une expérience de classification, ces paramètres comprendraient l’exactitude, la précision, le rappel, le f-score, l’ASC, la perte de journaux moyenne et la perte de journaux de formation.

![][4]
 
### Reformation planifiée
 
À l’aide de déclencheurs d’application logique, vous pouvez configurer le connecteur afin qu’il s’exécute selon une planification. Cela peut permettre de reformer un modèle régulièrement au fur et à mesure que de nouvelles données arrivent. La tâche BES reforme le modèle et l'action Reformation met à jour le modèle une fois la reformation terminée.
 
![][5]
 
## Sortie du connecteur 
 
**BES** : une fois le traitement par lots terminé, la sortie du connecteur comprendra les informations suivantes pour chaque sortie du service web.
 
 ![][6]
 
Notez que ces informations ne seront pas disponibles si vous n'avez pas inclus de sortie du service Web (par exemple, si vous utilisez un module Enregistreur pour écrire dans une base de données à partir de l'expérience dans le Studio).

**Reformation** : une fois la reformation terminée, la sortie comprendra les informations suivantes.

![][7]

## Résumé

À l’aide du connecteur Azure ML pour Logic Apps, vous pouvez exécuter la notation par lots et la reformation des tâches à exécuter à la demande ou selon une planification périodique. L’association des deux actions permet de noter vos données ainsi que de reformer, d’évaluer et de mettre à jour votre modèle de service Web automatiquement, sans que vous n’ayez besoin d’écrire du code.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_0601_2016-->