<properties
	pageTitle="Comment faire évoluer un modèle d’apprentissage automatique d’une simple expérience en service web opérationnel | Microsoft Azure"
	description="Vue d’ensemble de la façon dont votre modèle Azure Machine Learning peut passer du stade de l’expérience de développement à celui d’un service web opérationnel."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="garye"/>


# Comment faire évoluer un modèle d’apprentissage automatique d’une simple expérience en service web opérationnel

Dans Azure Machine Learning Studio, une ***expérience*** est une zone de dessin qui vous permet de développer, exécuter, tester et effectuer une itération de manière interactive au cours de la création d’un modèle d’analyse prédictive. Vous disposez d’un large éventail de modules que vous pouvez utiliser pour importer des données dans votre expérience, manipuler les données, former un modèle à l’aide d’algorithmes d’apprentissage automatique, noter le modèle, évaluer les résultats et générer les valeurs finales.

Une fois que vous êtes satisfait de votre expérience, vous pouvez la déployer en tant que ***service web Azure*** afin de permettre aux utilisateurs d’envoyer de nouvelles données et de recevoir les résultats en retour.

Dans cet article, nous allons vous donner une vue d’ensemble de la façon dont votre modèle d’apprentissage automatique peut passer du stade de l’expérience de développement à celui d’un service web opérationnel.

>[AZURE.NOTE] Il existe d’autres manières de développer et déployer des modèles d’apprentissage automatique, mais cet article se concentre uniquement sur l’utilisation de Machine Learning Studio. Pour plus d’informations sur la manière de créer un service web prédictif avec R, consultez le blog relatif au [développement et au déploiement d’applications web prédictives à l’aide de RStudio et d’Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (en anglais).

Bien qu’Azure Machine Learning Studio soit principalement conçu pour vous aider à développer et déployer un *modèle d’analyse prédictive*, il est possible de l’utiliser pour développer une expérience qui n’inclut pas de modèle d’analyse prédictive. Une expérience peut, par exemple, simplement injecter des données et les manipuler avant de générer les résultats. À l’instar d’une expérience d’analyse prédictive, vous pouvez déployer cette expérience non prédictive en tant que service web. Ce processus sera toutefois plus simple car l’expérience n’est pas apparentée à l’apprentissage ou à la notation d’un modèle d’apprentissage automatique. Si cela ne représente pas l’utilisation classique de Studio, nous allons l’inclure dans cette discussion afin de pouvoir vous expliquer de manière exhaustive le mode de fonctionnement de Studio.

## Développement et déploiement d’un service web prédictif

Voici les étapes effectuées pour une solution classique lorsque vous développez et déployez à l’aide de Machine Learning Studio :

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figure 1 : Étapes d’un modèle d’analyse prédictive classique*

### Expérience de formation

L’***expérience de formation*** correspond à la zone d’expérience initiale dans Machine Learning Studio. L’expérience de formation vous permet de développer, tester, itérer et former un modèle d’apprentissage automatique. Vous pouvez même former plusieurs modèles simultanément en recherchant la meilleure solution possible ; mais une fois que vous aurez terminé votre expérience, vous devrez sélectionner un seul modèle formé et supprimer le reste de l’expérience. Pour obtenir un exemple de développement d’une expérience d’analyse prédictive, consultez [Guide pas à pas : développer une solution d’analyse prédictive pour l’évaluation des risques de crédit dans Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Expérience prédictive

Une fois que vous avez formé un modèle dans votre expérience d’apprentissage, cliquez sur **Configurer le service web** dans Machine Learning Studio. Studio va alors commencer à convertir votre expérience de formation en ***expérience prédictive***. L’objectif de l’expérience prédictive consiste à utiliser votre modèle formé afin d’évaluer les nouvelles données, ce dans le but de devenir opérationnel sous la forme d’un service web Azure.

Cette conversion s’effectue automatiquement de la manière suivante :

-   Conversion de l’ensemble des modules utilisés pour l’apprentissage en un seul module, enregistré comme modèle d’apprentissage

-   Suppression de tous les modules superflus non liés à la notation

-   Ajout des ports d’entrée et de sortie qui seront utilisés par le service web

Vous pouvez apporter des modifications supplémentaires pour accélérer le déploiement de votre expérience prédictive en tant que service web. Par exemple, si vous souhaitez que le service web génère uniquement un sous-ensemble de résultats, vous pouvez ajouter un module de filtrage avant le port de sortie.

Dans ce processus de conversion, l’expérience de formation n’est pas supprimée. Une fois le processus terminé, deux onglets s’afficheront dans Studio : le premier pour l’expérience de formation, le second pour l’expérience prédictive. Ainsi, avant de déployer votre service web, vous pouvez apporter des modifications à l’expérience de formation et reconstruire l’expérience prédictive. Vous pouvez également enregistrer une copie de l’expérience de formation pour démarrer une autre ligne de l’expérimentation.

>[AZURE.NOTE] Lorsque vous cliquez sur **Configurer le service web**, vous démarrez un processus automatique permettant de convertir votre expérience de formation en expérience prédictive. Ce processus fonctionne bien dans la plupart des cas. Mais si vous avez défini une expérience de formation complexe (par exemple, si vous avez plusieurs chemins d’accès pour des formations que vous souhaitez relier entre elles), vous préférerez peut-être effectuer cette conversion manuellement. Pour plus d’informations sur le fonctionnement de ce processus de conversion, consultez [Conversion d’une expérience de formation Machine Learning en expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### Service web

Dès que vous pensez que votre expérience prédictive est terminée, cliquez sur **Déployer le service web** pour configurer votre modèle en le déployant en tant que ***service web Azure***. Les utilisateurs peuvent maintenant envoyer des données à votre modèle à l’aide de l’API REST du service web et recevoir les résultats en retour. Pour plus d’informations sur la procédure à suivre, consultez [Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-web-services.md).

Une fois le service web déployé, l’expérience prédictive et le service web restent connectés, et vous pouvez basculer de l’un à l’autre :

|***À partir de cette page...***|***Cliquez sur...***|***pour ouvrir cette page...***|
| ------------------- | --------------- | ---------------------- |
|zone de dessin de l’expérience Studio|**Accéder au service web**|configuration du service web dans Studio|
|configuration du service web dans Studio|**Afficher les données les plus récentes**|zone de dessin de l’expérience Studio|
|configuration du service web dans Studio|**Gérer les points de terminaison…**|gestion des points de terminaison dans le portail Azure Classic|
|gestion des points de terminaison dans le portail Azure Classic|**Modifier dans Studio**|zone de dessin de l’expérience Studio|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*Figure 2 : Liens entre une expérience et un service web*

## Scénario non classique : création d’un service web non prédictif

Si votre expérience ne forme pas un modèle d’analyse prédictive, vous n’avez pas besoin de créer une expérience de formation et une expérience d’évaluation ; vous ne disposez que d’une seule expérience, que vous pouvez déployer en tant que service web (Machine Learning Studio détecte si votre expérience contient ou non un modèle prédictif en analysant les modules que vous avez utilisés).

Une fois que vous avez parcouru votre expérience et que vous la jugez satisfaisante :

1.  Cliquez sur **Configurer le service web** : les nœuds d’entrée et de sortie sont automatiquement ajoutés

2.  Cliquez sur **Exécuter**

3.  Cliquez sur **Déployer le service web**

Votre service web est désormais déployé, et vous pouvez y accéder et le gérer de la même manière qu’un service web prédictif.

## Les boutons du service web

Dans Machine Learning Studio, les boutons du service web (**Configurer le service web** et **Déployer le service web**) changent de nom et de fonction selon votre progression dans le processus de développement.

**L’expérience contient un modèle prédictif**

Si l’expérience forme et évalue un modèle prédictif, les boutons du service web exécutent les fonctions suivantes :

|**Type d’expérience**|**Bouton**|**Résultat**|
| -------------------- | -------- | -------------- |
|Expérience en cours de développement|**Configurer le service web**|Propose deux options|
|&nbsp;|- **Service web prédictif**|Convertit l’expérience en une expérience de formation et une expérience de notation|
|&nbsp;|- **Reformation du service web**|Convertit l’expérience en expérience de reformation (voir la section « Mise à jour » ci-dessous)|
|Expérience d'apprentissage|**Configurer le service web**|Propose deux options|
|&nbsp;|- **Mettre à jour l’expérience prédictive**|Met à jour l’expérience prédictive associée en tenant compte des modifications que vous apportées à l’expérience de formation|
|&nbsp;|- **Reformation du service web**|Convertit l’expérience de formation en expérience de reformation (voir la section « Mise à jour » ci-dessous)|
|&nbsp;|-*ou*- **Déployer le service web**|Si vous avez configuré l’expérience de reformation pour un déploiement, cette option permet de la déployer en tant que service web|
|Expérience prédictive|**Déployer le service web**|Déploie l’expérience prédictive sous la forme d’un service web|

**L’expérience *ne* contient pas de modèle prédictif**

Si l’expérience n’implique ni la formation ni la notation d’un modèle prédictif, les boutons du service web sont bien plus simples :

|**Type d’expérience**|**Bouton**|**Résultat**|
| -------------------- | -------- | -------------- |
|Expérience en cours de développement|**Configurer le service web**|Prépare l’expérience en vue de son déploiement en tant que service web|
|Expérience préparée pour le déploiement|**Déployer le service web**|Déploie l’expérience en tant que service web, ouvre la page de configuration du service web|

## Mise à jour de votre service web

Maintenant que vous avez déployé votre expérience en tant que service web, que se passe-t-il si vous avez une mise à jour à effectuer ?

Tout dépend de ce que vous devez mettre à jour :

**Vous souhaitez modifier l’entrée ou la sortie, ou vous souhaitez modifier la façon dont le service web manipule les données**

Si vous ne modifiez pas le modèle, mais que vous changez simplement la façon dont le service web gère les données, vous pouvez modifier l’expérience prédictive, puis cliquez de nouveau sur **Déployer le service web**. Le service web sera arrêté et l’expérience prédictive mise à jour déployée avant le redémarrage du service web.

Voici un exemple : supposons que votre expérience prédictive retourne toute la ligne de données d’entrée avec le résultat prédit. Vous pouvez décider que le service web se contente simplement de retourner le résultat. Vous pouvez donc ajouter un module **Colonnes de projet** dans l’expérience prédictive, juste avant le port de sortie, pour exclure les colonnes qui ne correspondent pas au résultat. Lorsque vous cliquez de nouveau sur **Déployer le service web**, le service web est mis à jour.

**Vous souhaitez reformer le modèle avec de nouvelles données**

Si vous souhaitez conserver votre modèle d’apprentissage automatique, mais en le reformant avec de nouvelles données, vous avez deux possibilités :

1.  **Reformer le modèle pendant l’exécution du service web** : si vous souhaitez reformer votre modèle pendant l’exécution du service web prédictif, vous pouvez procéder en apportant quelques modifications à l’expérience de formation pour en faire une ***expérience de reformation***, puis le déployer en tant que ***service web* de reformation**. Pour connaître la procédure à suivre, consultez la page [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md).

2.  **Revenir à l’expérience de formation d’origine et utiliser d’autres données de formation pour développer votre modèle** : votre expérience prédictive est liée au service web, mais l’expérience de formation n’est pas directement liée de cette façon. Si vous modifiez l’expérience de formation d’origine, cliquez sur **Configurer le service web**. Vous obtiendrez alors une *nouvelle* expérience prédictive qui, une fois déployée, créera un *nouveau* service web. L’opération ne se résume pas à mettre simplement à jour le service web d’origine.

    Par conséquent, si vous devez modifier l’expérience de formation, ouvrez-la et cliquez sur **Enregistrer sous** pour effectuer une copie. Cette précaution garantira l’intégrité de l’expérience d’apprentissage d’origine, de l’expérience prédictive et du service web. Vous pouvez désormais créer un service web avec vos modifications. Une fois que vous avez déployé le nouveau service web, vous pouvez alors décider d’arrêter le service web précédent ou de le maintenir en exécution simultanée.

**Vous souhaitez former un modèle différent**

Si vous souhaitez apporter des modifications à votre expérience prédictive d’origine (par exemple, sélection d’un autre algorithme d’apprentissage automatique, test d’une méthode de formation différente, etc.), vous devez suivre la deuxième procédure décrite ci-dessus pour la reformation de votre modèle : ouvrez l’expérience de formation, cliquez sur **Enregistrer sous** pour effectuer une copie, puis commencez à développer votre modèle, à créer l’expérience prédictive et à déployer le service web. Cela créera un nouveau service web non lié à l’original. Vous pouvez alors décider du ou des services qui continueront à fonctionner.

## Pour aller plus loin

Pour plus d’informations sur ce processus, consultez les articles suivants :

-   conversion de l’expérience : [Conversion d’une expérience de formation Machine Learning en expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   déploiement du service web : [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)

-   reformation du modèle : [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md)

Pour obtenir des exemples de l’ensemble du processus, consultez :

-   [Didacticiel sur l'apprentissage automatique : création de votre première expérience dans Azure Machine Learning Studio](machine-learning-create-experiment.md)

-   [Guide pas à pas : développer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

<!---HONumber=AcomDC_0128_2016-->