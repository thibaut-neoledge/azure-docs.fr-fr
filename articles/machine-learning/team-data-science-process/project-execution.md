---
title: "Réalisation de projets de science des données - Azure | Microsoft Docs"
description: "Explique comment un scientifique des données peut réaliser un projet de science des données de manière traçable et collaborative, avec gestion de versions."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>Réalisation de projets science des données

Ce document explique comment un scientifique des données peut réaliser, au sein d’une équipe, un projet de science des données d’une manière systématique, collaborative et avec gestion de versions, à l’aide du [processus TDSP](overview.md) (Team Data Science Process). TDSP est un framework élaboré par Microsoft qui propose une suite structurée d’activités visant à exécuter les solutions cloud d’analytique prédictive avec efficacité. Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md). 

Cette rubrique fournit des instructions pour les procédures suivantes : 

1. Utiliser la **planification sprint** pour les éléments du projet.<br> Si vous ne connaissez pas la planification sprint, vous pouvez obtenir des informations générales et détaillées [ici](https://en.wikipedia.org/wiki/Sprint_(software_development) "ici"). 
2. **Ajouter des éléments de travail** à une planification sprint.
3. **Lier les éléments de travail avec des activités de codage** suivies à l’aide de Git.
4. Effectuer une **revue de code**. 


>[AZURE.NOTE] À travers les instructions suivantes, nous décrivons les étapes nécessaires à la configuration d’un environnement d’équipe TDSP à l’aide de Visual Studio Team Services (VSTS). Nous indiquons comment accomplir ces tâches avec VSTS, car c’est de cette façon que nous implémentons TDSP chez Microsoft. Les éléments (3) et (4) de la liste précédente font partie des avantages que présente l’utilisation de VSTS. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable d’équipe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui sera différente. Par exemple, l’élément de la section 6 (**Lier un élément de travail à une branche Git**) peut ne pas être aussi simple qu’avec VSTS.

La figure suivante montre le flux de travail classique de planification sprint, de codage et de contrôle de code source impliqué dans l’implémentation d’un projet de science des données :

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologie 

Dans le framework de planification sprint TDSP, il existe quatre types **d’éléments de travail** fréquemment utilisés : **Caractéristique**, **Récit**, **Tâche** et **Bogue**. Chaque projet d’équipe met à jour un backlog de tous les éléments de travail. Il n’existe pas de backlog au niveau du dépôt Git, sous les projets d’équipe. Voici leurs définitions :

- **Caractéristique** : une caractéristique correspond à un engagement de projet. Les différents engagements pris auprès d’un client sont considérés comme des caractéristiques distinctes. De même, il est préférable de considérer les différentes phases d’un projet client comme des caractéristiques distinctes. Si vous choisissez un format tel que ***NomClient-NomEngagement*** pour nommer vos caractéristiques, vous pourrez facilement connaître le contexte du projet ou de l’engagement grâce au nom qu’il porte.
- **Récit** : les récits sont des éléments de travail qui sont nécessaires pour mener une caractéristique (un projet) de bout en bout. Voici quelques exemples de récits :
    - Obtention de données 
    - Exploration de données 
    - Génération de caractéristiques
    - Création de modèles
    - Utilisation de modèles 
    - Nouvel apprentissage de modèles
- **Tâche** : les tâches sont des éléments de travail de code ou de document pouvant être assignés, ou autres activités devant être effectuées pour mener à bien un récit. Par exemple, les tâches du récit *Obtention de données* pourraient être les suivantes :
    -  Obtention d’informations d’identification SQL Server 
    -  Chargement de données dans SQL Data Warehouse 
- **Bogue** : les bogues désignent généralement les corrections qui doivent être apportées à du code ou à un document existant au moment de terminer une tâche. Si le bogue est dû à l’absence d’étapes ou de tâches, il peut passer au statut de récit ou de tâche, selon le cas. 

>[AZURE.NOTE] Les concepts de caractéristiques, de récits, de tâches et de bogues ont été empruntés à la gestion de code logiciel (SCM) qui est utilisée en science des données. Leurs définitions peuvent varier légèrement de celles données par la gestion de code logiciel.

##  2. <a name='SprintPlanning-2'></a>Planification sprint 

La planification sprint est utile pour la définition des priorités d’un projet, ainsi que pour la planification et l’allocation des ressources. De nombreux scientifiques des données sont engagés dans plusieurs projets à la fois, qui peuvent durer des mois. Les projets évoluent souvent à des rythmes différents. Sur le serveur VSTS, vous pouvez facilement créer, gérer et effectuer le suivi des éléments de travail de votre projet d’équipe, ainsi qu’effectuer une planification sprint en vue de garantir le bon déroulement du projet. 

Pour obtenir des instructions relatives à la planification sprint dans VSTS, cliquez sur [ce lien](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning). 


##  3. <a name='AddFeature-3'></a>Ajouter une caractéristique  

Une fois que vous avez créé le dépôt de votre projet sous un projet d’équipe, accédez à la page **Vue d’ensemble** de l’équipe, puis cliquez sur **Gérer le travail**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Pour inclure une caractéristique dans le backlog, cliquez sur **Backlogs** --> **Caractéristiques** --> **Nouveau**, tapez le **Titre** de la caractéristique (il s’agit généralement du nom de votre projet), puis cliquez sur **Ajouter**.

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Double-cliquez sur la caractéristique que vous venez de créer. Ajoutez les descriptions, affectez les membres de l’équipe à la caractéristique, puis définissez les paramètres de planification. 

Vous pouvez également lier cette caractéristique au dépôt du projet. Sous la section **Développement**, cliquez sur **Ajouter un lien**. Une fois les modifications apportées à la caractéristique, cliquez sur **Enregistrer et fermer**.


##  4. <a name='AddStoryunderfeature-4'></a>Ajouter un récit sous une caractéristique 

Les récits peuvent être ajoutés sous une caractéristique pour décrire les principales étapes nécessaires pour achever le projet (ou caractéristique). Pour ajouter un nouveau récit, cliquez sur le symbole **+** situé à gauche de la caractéristique, dans la vue Backlog.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

Vous pouvez modifier les détails du récit, tels que son état, sa description, ses commentaires, sa planification et sa priorité, dans la fenêtre contextuelle.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Vous pouvez lier ce récit à un dépôt existant en cliquant sur **+ Ajouter un lien** sous **Développement**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Ajouter une tâche à un récit 

Les tâches sont des étapes détaillées qui sont nécessaires à la réalisation d’un récit. Une fois que toutes les tâches d’un récit sont terminées, le récit doit également être terminé. 

Pour ajouter une tâche à un récit, cliquez sur le symbole **+** en regard de l’élément de récit, sélectionnez **Tâche**, puis renseignez les informations détaillées de cette tâche dans la fenêtre contextuelle.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

Une fois que vous avez créé les caractéristiques, les récits et les tâches, vous pouvez les afficher dans la vue **Backlog** ou **Tableau** pour connaître leur état.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Lier un élément de travail à une branche Git 

VSTS fournit un moyen pratique de connecter un élément de travail (un récit ou une tâche) à une branche Git. En effet, il vous permet de lier votre récit ou votre tâche directement au code associé. 

Pour connecter un élément de travail à une nouvelle branche, double-cliquez sur un élément de travail, puis, dans la fenêtre contextuelle, cliquez sur **Créer une branche** sous **+ Ajouter un lien**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Fournissez les informations concernant cette nouvelle branche, telles que son nom ou son dépôt Git de base. Le dépôt Git choisi doit être celui situé sous le projet d’équipe auquel appartient l’élément de travail. La branche de base peut être la branche master ou une autre branche existante.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Il est préférable de créer une branche Git pour chaque élément de travail d’un récit. Ensuite, pour chaque élément de travail de tâche, vous devez créer une branche basée sur la branche du récit. Lorsque plusieurs personnes travaillent sur les différents récits d’un même projet ou les différentes tâches d’un même récit, il est utile d’organiser les branches sous la forme d’une hiérarchie correspondant aux relations entre le récit et les tâches. Le nombre de conflits peut être réduit lorsque chaque membre d’équipe travaille sur une branche différente et lorsque chaque membre travaille sur du code (ou autre artefact) différent en cas de partage d’une branche. 

L’illustration suivante montre la stratégie de création de branche recommandée pour TDSP. Vous n’aurez peut-être pas besoin d’autant de branches, en particulier si seulement une ou deux personnes travaillent à un projet, ou si une seule personne est chargée de l’ensemble des tâches d’un récit. Toutefois, le fait de séparer la branche de développement de la branche master est toujours conseillé. Cela peut éviter à la branche de publication d’être interrompue par les activités de développement. Pour obtenir une description plus complète du modèle de branche Git, consultez [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/).

![12](./media/project-execution/project-execution-12-git-branches.png)

Pour basculer vers la branche sur laquelle vous souhaitez travailler, exécutez la commande suivante dans une invite de commandes (Windows ou Linux). 

    git checkout <branch name>

Si vous remplacez *<branch name\>* par **master**, vous allez revenir à la branche **maître**. Une fois que vous avez basculé vers la branche de travail, vous pouvez commencer à travailler sur l’élément de travail, en développant les artefacts du code ou de la documentation nécessaires à la réalisation de l’élément. 

Vous pouvez également lier un élément de travail à une branche existante. Dans la page **Détail** d’un élément de travail, au lieu de cliquer sur **Créer une branche**, cliquez sur **+ Ajouter un lien**. Ensuite, sélectionnez la branche à laquelle vous souhaitez lier l’élément de travail. 

![13.](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

Vous pouvez également créer une branche à l’aide des commandes Bash Git. Si <base branch name\> est manquant, <new branch name\> est basé sur la branche _maître_. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Travailler sur une branche et valider les modifications 

Maintenant, supposons que vous apportiez des modifications à la branche *data\_ingestion* de l’élément de travail, comme l’ajout d’un fichier R à la branche de votre ordinateur local. Vous pouvez valider le fichier R ajouté à la branche de cet élément de travail, du moment que vous vous trouvez dans cette branche dans l’interpréteur de commandes Git et que vous utilisez les commandes Git suivantes :

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Créer une demande de tirage (pull request) dans VSTS 

Lorsque vous êtes prêt, après quelques validations et envois (push), à fusionner la branche actuelle avec la branche de base, vous pouvez envoyer une **demande de tirage (pull request)** sur le serveur VSTS. 

Dans la page principale de votre projet d’équipe, cliquez sur **CODE**. Sélectionnez la branche à fusionner et le nom du dépôt Git avec lequel vous souhaitez fusionner la branche. Ensuite, cliquez sur **Demandes de tirage (Pull requests)**, puis cliquez sur **Nouvelle demande de tirage (Pull request)** pour créer une revue de demande de tirage avant que le travail de la branche ne soit fusionné avec sa branche de base.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Ajoutez une description pour la demande de tirage, ajoutez des réviseurs, puis envoyez la demande.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Réviser et fusionner 

Lorsque des demandes de tirage sont créées, vos réviseurs reçoivent une notification par e-mail leur demandant de réviser les demandes. Les réviseurs doivent vérifier si les modifications apportées fonctionnent, et les tester avec le demandeur, si possible. Selon le résultat de leur évaluation, les réviseurs peuvent approuver ou rejeter la demande de tirage. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Une fois la revue terminée, pour fusionner la branche de travail avec la branche de base, cliquez sur le bouton **Terminer**. Vous pouvez choisir de supprimer la branche de travail une fois celle-ci fusionnée. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Dans le coin supérieur gauche, vérifiez que la demande est marquée comme **Terminée**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Lorsque vous revenez au dépôt sous **CODE**, vous êtes informé que vous venez de basculer vers la branche master.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

Vous pouvez également utiliser les commandes Git pour fusionner votre branche de travail avec la branche de base, et supprimer la branche de travail après la fusion :

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Utilitaire d’exploration interactive des données, d’analyse et de création de rapports (IDEAR)

Cet utilitaire R Markdown constitue un outil souple et interactif qui permet d’évaluer et d’explorer les jeux de données. Les utilisateurs peuvent rapidement générer des rapports à partir d’un jeu de données avec un minimum de codage. Ils peuvent exporter les résultats de l’exploration qui s’affichent dans l’outil interactif vers un rapport final, que vous pouvez remettre aux clients ou utiliser pour prendre des décisions concernant les variables à inclure dans l’étape suivante de modélisation.

Pour l’instant, l’outil fonctionne uniquement sur les trames de données en mémoire. Un fichier .yaml est nécessaire pour spécifier les paramètres du jeu de données à explorer. Pour plus d’informations, consultez [IDEAR dans Utilitaires de science des données TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Utilitaire de modélisation et de création de rapports de référence

Cet utilitaire est un outil personnalisable et semi-automatique qui permet de créer des modèles à l’aide d’un balayage hyperparamétrique, et de comparer la précision de ces modèles. 

Cet utilitaire de création de modèles est un fichier R Markdown qui peut être exécuté pour produire une sortie HTML autonome comprenant une table des matières qui facilite la navigation entre ses différentes sections. Trois algorithmes sont exécutés lorsque le fichier Markdown est exécuté (compilé) : la régression régularisée qui utilise le package glmnet, une forêt aléatoire qui utilise le package randomForest, et des arbres de boosting qui utilisent le package xgboost. Chacun de ces algorithmes génère un modèle formé. La précision de ces modèles est ensuite comparée et les tracés d’importance relative des caractéristiques sont créés. Deux utilitaires sont actuellement disponibles : un pour la classification binaire et l’autre pour la régression. Leurs principales différences se situent au niveau de la manière dont sont spécifiés les paramètres de contrôle et les métriques de précision pour ces tâches d’apprentissage automatique. 

Un fichier Yaml permet de spécifier :

- L’entrée de données (une source SQL ou un fichier de données R) 
- La proportion de données utilisée pour l’apprentissage et celle utilisée pour le test
- Les algorithmes à exécuter 
- Les paramètres de contrôle choisis pour l’optimisation des modèles :
    - validation croisée 
    - amorçage
    - plis de validation croisée
- Les jeux d’hyperparamètres de chaque algorithme. 

Le nombre d’algorithmes, le nombre de plis pour l’optimisation, les hyperparamètres et le nombre de jeux d’hyperparamètres à balayer peuvent également être modifiés dans le fichier Yaml pour exécuter les modèles plus rapidement. Par exemple, ils peuvent être exécutés avec un nombre inférieur de plis de validation croisée ou un nombre inférieur de jeux de paramètres. Il peuvent également être exécutés avec un plus grand nombre de plis de validation croisée ou un plus grand nombre de jeux de paramètres, si cela est garanti.

Pour plus d’informations, consultez [Utilitaire de modélisation et de création de rapports automatisées dans Utilitaires de science des données TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Suivi de l’avancement des projets à l’aide des tableaux de bord Power BI

Les responsables de groupe, d’équipe et de projet de science des données doivent suivre l’avancement de leurs projets, les tâches qui ont été effectuées et par qui, et les tâches qu’il reste à accomplir. Si vous utilisez VSTS, vous pouvez générer des tableaux de bord Power BI pour suivre les activités et les éléments de travail associés à un dépôt Git. Pour plus d’informations sur la connexion de Power BI à Visual Studio Team Services, consultez [Connecter Power BI à Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Pour savoir comment créer des tableaux de bord Power BI et des rapports pour effectuer le suivi des activités et des éléments de travail du dépôt Git après la connexion des données VSTS à Power BI, consultez [Créer des tableaux de bord et des rapports Power BI](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Voici deux exemples simples de tableaux de bord destinés à effectuer le suivi des activités et des éléments de travail dans Git. Dans le premier exemple de tableau de bord, les activités de validation Git sont répertoriées par utilisateur, par date et par dépôt. Vous pouvez facilement segmenter les activités pour n’afficher que celles qui vous intéressent.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

Dans le deuxième exemple de tableau de bord, les éléments de travail (récits et tâches) de différentes itérations sont présentés. Ils sont regroupés par personne responsable et par niveau de priorité, et leur état est signalé par une couleur.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Étapes suivantes

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours d’apprentissage [Avec Azure ML](http://aka.ms/datascienceprocess).