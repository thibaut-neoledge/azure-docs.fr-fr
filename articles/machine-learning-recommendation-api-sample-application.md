<properties title="Common operations in the Machine Learning Recommendations API" pageTitle="Opérations courantes dans l'API de Machine Learning Recommendations | Azure" description="Azure ML Recommendation Sample Application" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="jaymathe" /> 


# Opérations courantes dans l'API de Machine Learning Recommendations

##Objectif

Ce document illustre l'utilisation de certaines des API d'Azure ML Recommendation, via une version d'évaluation de l'application.

Cette application n'est pas conçue pour inclure toutes les fonctionnalités ou pour utiliser toutes les API, mais simplement pour présenter quelques-unes des opérations courantes à effectuer lorsque vous souhaitez vous familiariser avec le service de recommandations d'Azure ML. 

##Présentation de Recommendation

Recommendation via le service de recommandations d'Azure ML est activé lorsque vous créez un modèle de recommandation basé sur les données suivantes :

* Un référentiel de l'élément que vous souhaitez recommander, autrement dit, un catalogue
* Les données représentant l'utilisation des éléments par session/utilisateur (Vous pouvez progressivement acquérir ces données via l'acquisition de données ; ne fait pas partie de la version d'évaluation de l'application.)

Une fois qu'un modèle de recommandation a été généré, vous pouvez l'utiliser pour prédire des éléments à un utilisateur selon l'ensemble d'éléments (ou un élément unique) qu'il a sélectionné.

Afin de permettre le scénario ci-dessus, procédez comme suit avec Azure ML Recommendation :

* Créez un modèle : il s'agit d'un conteneur logique pour les données (de catalogue et d'utilisation) et les modèles de prévision. Chaque conteneur de modèle est identifié par un identifiant unique attribué au moment de sa création. Cet identifiant, appelé identifiant de modèle, est utilisé par la plupart des API. 
* Téléchargez un catalogue : une fois qu'un conteneur de modèle est créé, vous pouvez lui associer un catalogue.

Remarque : Les étapes ci-dessus (" Créez un modèle " et " Téléchargez un catalogue ") sont généralement effectuées une fois pendant le cycle de vie du modèle.

* Téléchargez l'utilisation : pour ajouter des données d'utilisation au conteneur du modèle.
* Générez le modèle de recommandation : une fois que vous avez suffisamment de données, vous pouvez générer un modèle de recommandation. Cette opération utilise des algorithmes d'apprentissage automatique très avancés pour créer un modèle de recommandation. Chaque génération est associée à un identifiant unique. Vous devez conserver cet identifiant, car il est nécessaire au fonctionnement de certaines API.
* Surveillez le processus de génération : la génération d'un modèle de recommandation est une opération asynchrone et peut prendre de quelques minutes à plusieurs heures selon la quantité de données (de catalogue et d'utilisation) et les paramètres de génération. Vous devez donc surveiller la génération. Un modèle de recommandation est créé uniquement si sa génération associée est réalisée avec succès.
* (Facultatif) Choisissez la génération active d'un modèle de recommandation. Cette étape est nécessaire uniquement si vous avez associé plusieurs modèles de recommandation à votre conteneur de modèle. Toute demande d'obtention de recommandation sans indiquer la génération active (modèle de recommandation actif) sera automatiquement redirigée par le système vers la génération active par défaut. 

Remarque : une génération active (modèle de recommandation) est prête pour la production et est conçue pour une charge de production contrairement à un modèle de recommandation non actif qui reste dans un environnement similaire à celui d'un test (parfois appelé intermédiaire).

* Obtenez la recommandation : une fois que vous disposez d'un modèle de recommandation, vous pouvez déclencher la recommandation pour un seul élément ou pour une liste d'éléments de votre choix. 

En général, l'appel de la fonction Obtenez une recommandation dure un certain temps. En attendant, vous pouvez rediriger les données d'utilisation vers le système de recommandation d'Azure ML qui les ajoutera au conteneur de modèle spécifié. Une fois que vous avez suffisamment de données d'utilisation, vous pouvez déclencher une nouvelle génération de modèle de recommandation pour utiliser les données les plus récentes. 

##Configuration requise

* Visual studio 2013
* Accès à Internet 

##Solution de la version d'évaluation de l'application Azure ML

La solution contient le code source, les fichiers d'utilisation et de catalogue en version d'évaluation, ainsi que des directives pour télécharger les packages nuget requis pour la compilation.

##API utilisée

L'application utilise uniquement un petit sous-ensemble de la fonctionnalité de recommandation d'Azure ML via un sous-ensemble de l'API disponible. Les API suivantes sont illustrées dans l'application :

* Créez un modèle : créez le conteneur logique pour les modèles de recommandation et les données. Un modèle est identifié par un nom. Un utilisateur ne peut pas créer deux fois un modèle portant le même nom.
* Téléchargez le fichier de catalogue : pour télécharger les données de catalogue.
* Téléchargez le fichier d'utilisation : pour télécharger les données d'utilisation.
* Déclenchez la génération : pour créer un modèle de recommandation.
* Surveillez l'exécution de la génération : surveillez l'état d'une génération de modèle de recommandation.
* Choisissez un modèle de recommandation généré : pour indiquer quel modèle de recommandation utiliser par défaut pour un certain conteneur de modèle. Cette étape est nécessaire uniquement si vous avez plusieurs modèles de recommandation et si vous souhaitez configurer une génération non active comme génération active.
* Obtenez la recommandation : pour récupérer un élément recommandé en fonction d'un élément unique ou d'un ensemble d'éléments. 

Pour une description complète de l'API, consultez la documentation de Microsoft Azure Marketplace. 

Remarque : un modèle peut posséder plusieurs générations au fil du temps (pas simultanément). Chaque génération a été créée avec le même catalogue ou un catalogue mis à jour et des données d'utilisation supplémentaires.

## Pièges courants

* Vous devez fournir votre nom d'utilisateur et votre clé de compte principal Microsoft Azure Marketplace comme ligne de commande pour exécuter la version d'évaluation.
* La version d'évaluation de l'application ne peut pas exécuter des étapes consécutives : le flux de l'application prend en charge la création, le téléchargement, la surveillance de la génération et l'obtention de la recommandation d'un nom de modèle prédéfini. Par conséquent, elle échouera à exécuter des étapes consécutives si vous ne modifiez pas le nom du modèle entre les appels.
* Recommendation peut ne pas fournir de données : la version d'évaluation de l'application utilise de très petits catalogue et fichier d'utilisation, par conséquent, le modèle de recommandation créé n'est pas très important. Certains éléments du catalogue n'ont donc aucun élément recommandé.

## Clause d'exclusion de responsabilité
La version d'évaluation de l'application n'est pas conçue pour être exécutée pour la production, les données fournies dans le catalogue et le fichier d'utilisation ne sont pas nombreuses et ne fournissent pas un modèle de recommandation éloquent. Elles sont seulement fournies à titre de démonstration. 

## Informations juridiques
Ce document est fourni " en l'état ". Les informations et illustrations présentées dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiées sans notification préalable. 
Certains exemples sont fournis à titre indicatif uniquement et sont fictifs. Aucune association ou connexion réelle n'est voulue et ne devrait être inférée. 
Ce document ne vous accorde aucun droit légal à la propriété intellectuelle pour un produit Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à titre de référence. 
(c) 2014 Microsoft. Tous droits réservés. 

