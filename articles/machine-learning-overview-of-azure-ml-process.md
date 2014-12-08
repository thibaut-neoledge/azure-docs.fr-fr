<properties title="Azure Machine Learning API service operations" pageTitle="Opérations du service API Machine Learning| Azure" description="Creating and managing Azure Machine Learning web services" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


# Opérations du service API Azure Machine Learning 
En général, un projet Microsoft Azure Machine Learning (Azure ML) implique les étapes générales suivantes :  

1.	Récupération, analyse et préparation des données
2.	Création d'expériences d'apprentissage automatique utilisant plusieurs algorithmes ML
3.	Formation, test et génération d'un modèle formé
4.	Création d'un flux de travail opérationnel à l'aide du modèle formé et déploiement de ce flux en production
5.	Surveillance des performances du modèle et des mises à jour ultérieures  

>Le terme " expérience " est utilisé pour décrire un flux de travail interactif pouvant inclure la saisie et la manipulation de données, des instructeurs et des évaluateurs sous la forme d'un graphique non cyclique dirigé. Une fois le flux de travail publié sous forme de service web Azure, il n'est plus interactif. Cela signifie que pour le modifier, le modèle doit être mis à jour, puis republié pour mettre à jour le service web et son comportement.  

Les étapes 1 à 3 sont généralement réalisées par un spécialiste des données via de nombreuses itérations à la fin desquelles un modèle ML est proposé aux équipes d'ingénierie et des opérations afin de l'intégrer aux systèmes de production. Ainsi, le modèle ML pourra être utilisé en production.  

Le processus classique d'intégration et de déploiement du modèle ML dans un système de production peut prendre des semaines, voire des mois, en fonction du code utilisé pour établir les modèles (R, Python, C# ou Java), des considérations relatives à l'intégration dans la plateforme et à l'infrastructure, ainsi que de la planification du déploiement.  

Azure ML simplifie ce processus en faisant de la création du modèle et de l'évaluation une expérience simple et intuitive, puis en fournissant un processus simple pour déployer l'expérience sous forme de service web dans Azure. Ainsi, le délai total entre l'expérimentation du modèle et sa mise en production sous forme de service web est considérablement réduit.  

Ce document décrit les concepts et les étapes permettant de configurer un service web Azure ML à partir d'une expérience ML.  

# Présentation du processus Azure ML  #
Azure ML permet la création de services web à partir d'expériences ML définies dans Azure Machine Learning Studio (ML Studio). Un service web Azure ML peut être utilisé pour faire des prédictions en fonction de données véritables fournies en temps réel ou par lot.  
 
Le schéma suivant montre les étapes générales en deux parties : d'abord l'élaboration d'un modèle, puis sa publication sous forme de service web. Ce document se concentre sur le côté droit du schéma de la Figure 1 - Publication d'un service web d'évaluation, puis explique les concepts impliqués dans ce processus. 

![][1]  

Figure 1 : approvisionnement, élaboration et publication d'un service web d'évaluation  

# Services web Azure ML #
Un service web, dans le contexte de l'apprentissage automatique (Machine Learning, ML), est une interface logicielle qui fournit une communication entre des applications externes et un flux de travail d'apprentissage automatique. Il fournit une façon de communiquer avec un modèle d'évaluation en temps réel afin d'obtenir des résultats de prédiction et de les intégrer dans des applications clientes externes. Azure ML utilise Microsoft Azure pour le déploiement, l'hébergement et la gestion des services web Azure ML. Deux types de services peuvent être créés à l'aide d'Azure ML.  

## Service de requête-réponse (RRS, Request-Response Service) ##
Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et publiés à partir de l'environnement Expérimentation.   

- API REST : RRS est un service web RESTFul. 
-	Interface du service : l'interface du service web RRS est définie dans le cadre de la configuration de l'expérience à l'aide des ports d'entrée/de sortie de l'expérience Azure ML Studio.
-	Étapes du développement : étant donné qu'il fait partie du flux de travail Azure ML, le service RRS est d'abord généré dans l'environnement intermédiaire, où il peut être testé. Lorsqu'il est considéré comme terminé et prêt pour la production, il est déployé dans l'environnement de production. 
-	Déploiement dans Azure : le résultat du déploiement de RRS est un point de terminaison du service web Azure.
-	Paramètres d'interface : la requête envoyée à un service RRS est en fait constituée des données nécessaires devant être évaluées à l'aide de l'expérience définie dans Studio. La réponse est le résultat de la prédiction du modèle. 
-	Valeurs de réponse : RRS accepte une seule ligne de paramètres en entrée et génère une seule ligne en sortie. La ligne obtenue en sortie peut contenir plusieurs colonnes.   

## Service d'exécution de lots (BES, Batch Execution Service) ##
Le service d'exécution de lots (BES, Batch Execution Service) est un service d'évaluation asynchrone d'enregistrements de données. La saisie de données pour le service BES est similaire à celle utilisée dans le service RRS. La différence principale réside dans le fait que le service BES lit un lot d'enregistrements à partir de diverses sources telles que des objets blob, des tables dans Azure, SQL Azure, HDInsight (requêtes Hive) et des sources HTTP.  Les résultats de l'évaluation sont envoyés vers un fichier dans le stockage d'objets blob Azure et les données du point de terminaison de stockage sont renvoyées dans la réponse.  

Le service BES fournit également des interfaces pour obtenir le statut du processus d'évaluation exécuté et pour annuler la requête. Il est également capable d'exécuter des packages de modèles avec de grandes quantités de données.  

-	API REST : le service BES est un service web RESTFul.
-	Interface du service : l'interface du service web BES, similaire à celle du service RRS, est définie dans le cadre de la configuration du modèle à l'aide des ports d'entrée/de sortie de l'expérience Azure ML Studio.
-	Étapes du développement : étant donné qu'il fait partie du flux de travail permettant de créer BES, il est d'abord généré dans l'environnement intermédiaire, où il peut être testé. Lorsqu'il est terminé et prêt pour la production, il est déployé dans l'environnement de production. 
-	Déploiement dans Azure : le résultat du déploiement de BES est un point de terminaison du service web Azure.
-	Paramètres d'interface : la requête pour un service BES est l'URL d'un fichier dans l'objet blob Azure ou une entrée SAP des enregistrements à évaluer. La réponse est écrite dans l'objet blob Azure et l'URL du point de terminaison du stockage des réponses est renvoyée.  

# Publication d'un service web Azure ML #
Azure ML Studio fournit une application sur navigateur permettant de créer et d'exécuter facilement des expériences d'apprentissage automatique dans une interface utilisateur graphique à l'aide de différentes sources de données, de modules de manipulation et de validation de données et d'algorithmes ML. Les expériences dans ML Studio sont élaborées sous forme de graphique non cyclique dirigé de modules de traitement de données.  

Une fois l'expérience configurée et exécutée correctement pour être formée sur les données, elle peut être enregistrée sous forme de modèle formé et utilisée pour l'évaluation. Le modèle formé est alors utilisé dans l'expérience d'évaluation ou dans un flux de travail, puis publié sous forme de service web Azure.  

## Expérience d'apprentissage ##
Une expérience peut inclure plusieurs modules pour le chargement et la manipulation des données, l'application d'algorithmes d'apprentissage automatique et l'évaluation des résultats. Un modèle formé utilise le jeu de données d'apprentissage et un algorithme d'apprentissage pour prédire une réponse.  

Une fois que le modèle a terminé son exécution, un modèle formé peut être enregistré sous forme de composant réutilisable pour évaluer les jeux de données et les requêtes tests.

![][2]  

Figure 2 : exemple montrant l'enregistrement d'un modèle formé lors de l'expérience

Le modèle formé enregistré est alors disponible dans la section des modèles formés de l'application.

![][3]  

Figure 3 : section des modèles formés montrant la liste des modèles

## Expérience d'évaluation ##
Une expérience d'évaluation génère des prédictions à l'aide du modèle formé et des exemples de données.  

La figure 1 ci-dessus montre l'utilisation d'un module Noter le modèle dans l'expérience. Dans Studio, il fait partie des modules d'apprentissage automatique.

![][4]  

Figure 4 : Noter le modèle 

### Comparaison du service de requête-réponse (RRS) et du service d'exécution de lots (BES) ###
Lors de l'élaboration d'expériences d'évaluation qui seront publiées sous forme de services web, un des services ou les deux seront sélectionnés en fonction du scénario d'évaluation. Si la requête d'évaluation implique l'évaluation d'un seul enregistrement, par exemple une requête pour déterminer si le client A va changer de transporteur (prédiction d'attrition du client), elle peut être évaluée en temps réel et créée sous forme de service web RRS. Ce service renverra le résultat du modèle de prédiction en temps réel. Dans l'exemple ci-dessus, il pourra s'agit d'une réponse positive ou négative.  

Pour des opérations d'évaluation dans lesquelles de nombreux enregistrements doivent être évalués lors d'une seule requête (par exemple si un lot d'enregistrements contenant les données client est envoyé au service pour être évalué), le service BES est le plus approprié. Dans ce cas, la requête sera une requête asynchrone dans laquelle tous les enregistrements seront traités et enregistrés dans un objet blob Azure avant qu'une réponse ne soit renvoyée une fois le traitement terminé.  
  
### Utilisation du modèle formé ###
Pour configurer l'expérience d'évaluation, le modèle formé (" Adult Income Predictor " de la figure 3) est ajouté à l'expérience. Les autres modules utilisés pour former le modèle peuvent maintenant être supprimés. Le flux de travail final ressemble alors à la figure 5 ci-dessous. 

### Ports d'entrée et de sortie ###
Après avoir configuré l'expérience avec le modèle formé (voir la section ci-dessus pour plus de détails) et évalué l'expérience mise à jour, les ports d'entrée et de sortie doivent être configurés. Cela permet d'indiquer les points d'entrée et de sortie des données dans le modèle de prédiction, ainsi que le résultat de cette dernière. Les ports agiront comme des définitions d'interface pour le service web publié. Le port d'entrée du module Noter le modèle peut être configuré comme expliqué ci-dessous en cliquant avec le bouton droit sur le point d'entrée.

![][5]  

Figure 5 : configuration du port d'entrée pour l'évaluation 

De même, le port de sortie du module Noter le modèle peut être configuré comme indiqué ci-dessous.

![][6]  

Figure 6 : configuration du port de sortie  

## Publication du service ##
Après avoir configuré les ports et exécuté l'expérience, le modèle peut être publié sous forme de service web. La première étape consiste à publier le service dans l'environnement intermédiaire et à le tester à cet endroit afin de s'assurer qu'il renvoie les résultats attendus avant d'indiquer qu'il est prêt pour le déploiement en production.  

### Publication dans l'environnement intermédiaire ###
Cliquez sur l'icône de publication du service web pour déployer le service web dans l'environnement intermédiaire. 

![][7]  

Figure 7 : bouton de publication du service web  

Après avoir publié le modèle sous forme de service web dans l'environnement intermédiaire, il peut être testé à l'aide des paramètres d'entrée et indiqué comme étant prêt à être déployé en production. Le tableau de bord indique le lien pour le test. 

![][8]  

Figure 8 : tableau de bord du service web

Cliquez sur le lien du test et indiquez les paramètres d'évaluation. Le service web peut alors être testé dans l'environnement intermédiaire. La requête test est évaluée à l'aide du modèle et basée sur les données fournies. Le résultat de l'évaluation est renvoyé. 

### Publication en production ###
La publication d'un service web en production le rendra disponible pour d'autres applications qui l'utiliseront à des fins de prédiction et d'évaluation. À la fin du déploiement dans l'environnement intermédiaire et une fois le test réussi, le service web est indiqué comme étant prêt pour un déploiement dans l'environnement de production. 

![][9]  

Figure 9 : marquage du service web comme étant prêt à être déployé en production

Cela n'exécute pas le déploiement, mais génère une notification à l'attention de l'utilisateur. Les autorisations de déploiement appropriées y sont indiquées pour pouvoir déployer le service en production.

![][10]  

Figure 10 : notification de déploiement et option de déploiement en production

## Appel du service web ##
#### RRS ####
Le service web RRS est un point de terminaison REST qui peut être appelé par d'autres applications clientes à l'aide de divers langages de programmation. La page d'aide de l'API fournit un lien vers l'exemple de code pour appeler les nouveaux exemples de service web en C#, R et Python.

![][11]  

Figure 11 : exemple de code pour appeler RRS  

## Expériences sans évaluation ##
En plus d'élaborer des services web d'évaluation, des expériences peuvent être créées pour exécuter d'autres tâches, comme l'extraction et la transformation de données. Dans ce cas, le service web n'exécute pas les opérations d'apprentissage automatique. Il utilise les fonctionnalités de manipulation de données d'Azure ML Studio pour lire à partir de différentes sources de données, convertir les types de données ou filtrer et appliquer des données et des manipulations mathématiques.   

### Publication d'un service web sans évaluation ###
La procédure de publication d'un service web sans évaluation est similaire à celle du service d'évaluation décrite ci-dessus. La différence principale réside dans le fait que le port de sortie n'est pas défini dans le module Noter le modèle.

# Mise à jour d'un service publié #
Un service web publié peut avoir besoin d'être mis à jour pour diverses raisons, comme la mise à jour des données d'apprentissage, la modification d'un schéma de données d'apprentissage et d'évaluation, l'amélioration d'algorithmes ou toute autre modification apportée au modèle ML d'origine. Ces modifications influent sur le modèle formé et sur les résultats de l'évaluation. Elles nécessitent également la publication d'un service web mis à jour.

![][12]  

Figure 12 : modification du modèle et publication du service web d'évaluation mis à jour  

## Mise à jour du modèle formé ##
Les modifications apportées à l'expérience d'apprentissage requièrent un nouvel apprentissage du modèle formé. Pour ce faire, le modèle publié doit être modifié. L'exemple ci-dessous montre le flux de travail d'évaluation de la figure 5 ci-dessus après la suppression du modèle formé existant.

![][13]  

Figure 13 : modèle formé supprimé du flux de travail  

L'étape suivante consiste à ajouter les modules nécessaires pour diviser les données en segments d'apprentissage et de test, appliquer l'algorithme d'apprentissage, former le modèle et évaluer les données d'apprentissage et les résultats. Remarque : ces étapes peuvent varier en fonction des autres modifications nécessaires à l'expérience, par exemple la nécessité de l'ajout d'un algorithme d'apprentissage différent (figure 14).  

Une fois les nouveaux modules ajoutés, ils doivent être configurés comme il se doit avant que l'expérience puisse être réexécutée. Par exemple, le cercle rouge dans l'exemple ci-dessous indique que la colonne d'étiquette n'a pas été configurée pour le modèle formé.

![][14]  

Figure 14 : modules ajoutés pour former à nouveau le modèle.  

Cliquez sur le modèle formé et configurez le nom de la colonne d'étiquette pour résoudre le problème. 

![][15]  

Figure 15 : sélection de la colonne " income " en tant qu'étiquette  

## Enregistrement du modèle formé mis à jour ##
Une fois tous les nouveaux modules configurés comme il se doit, l'expérience doit être enregistrée et réexécutée correctement. Le modèle formé peut alors être enregistré (comme illustré dans la figure 2 ci-dessus). La différence réside dans le fait que, dans ce cas, la case doit être cochée afin de mettre à jour le modèle formé existant. 

![][16]  

Figure 16 : mise à jour du modèle formé existant

## Publication du service mis à jour ##
Une fois le modèle formé mis à jour, les étapes décrites dans la section ci-dessus relatives à la publication d'un **service web Azure ML** seront répétées :  

-	Utilisation du modèle formé (maintenant à jour) dans l'expérience d'évaluation
-	Configuration des ports d'entrée/de sortie
-	Publication dans l'environnement intermédiaire
-	Publication dans l'environnement de production  

Une fois l'expérience mise à jour et le nouveau modèle formé créé et évalué, cliquez sur le bouton de publication du service web pour publier le service. Le nouveau service créé remplace le service existant.


<!--Image references-->
[1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
[2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
[3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
[4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
[5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
[6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
[7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
[8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
[9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
[10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
[11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
[12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
[13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
[14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
[15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
[16]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png

<!--Link references-->
