<properties 
	pageTitle="Guide des fonctionnalités d'Operational Insights" 
	description="Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Guide des fonctionnalités d'Operational Insights</h1>

Ce guide vise à mieux vous faire comprendre les problèmes qu'Operational Insights peut aider à résoudre, ce que l'environnement d'Operational Insights comprend et comment vous pouvez créer un compte Operational Insights et vous connecter au service.

<h2 id="whatisaad">Présentation d'Operational Insights</h2>

Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté.

Grâce à Operational Insights, vous pouvez transformer les données machine en intelligence opérationnelle. Plus précisément, vous pouvez :

- Gérer la capacité de votre infrastructure de serveurs
- Mettre à jour les serveurs avec des mises à jour système
- Comprendre les relations entre les données dans les fichiers journaux des serveurs
- Protéger les serveurs contre les programmes malveillants
- Gérer les risques de sécurité avec une garantie de sécurité
- Effectuer le suivi des modifications de configuration sur vos serveurs
- Optimiser SQL Server
- Effectuer des recherches dans les journaux d'événements et IIS
- Installer des agents sur les machines virtuelles dans IaaS via Microsoft Azure
- Utiliser Operational Insights sans Operations Manager  

<h2 id="">Environnement Operational Insights</h2>

L'environnement Operational Insights est composé des éléments suivants :

- espaces de travail hébergés dans Microsoft Azure qui sont des conteneurs pour les comptes Azure,
- service web Operational Insights, qui est hébergé dans le cloud,
- agents distincts qui se connectent directement au service web
- ou service joint à System Center Operations Manager, mais non requis.




Si vous avez utilisé la version précédente d'Operational Insights, appelée System Center Advisor, le logiciel Advisor est peut-être installé dans votre environnement local. Toutefois, le logiciel Advisor n'est pas pris en charge avec Operational Insights.

L'utilisation du logiciel Operational Insights en tant que service Operations Manager implique un ou plusieurs groupes d'administration et au moins un agent par groupe. Les agents Operations Manager collectent des données de vos serveurs et les analysent à l'aide de packs d'analyse décisionnelle (semblables à des packs d'administration dans System Center Operations Manager). Les données analysées sont envoyées régulièrement d'Operations Manager au service web Operational Insights au moyen d'un serveur proxy, en ignorant les autres serveurs, de sorte qu'aucune charge supplémentaire ne leur soit appliquée.

De même, les agents installés sur des ordinateurs individuels peuvent se connecter directement au service web pour envoyer les données collectées à traiter.

Les données de chaque pack d'analyse décisionnelle sont analysées et présentées sur le portail Operational Insights. Vous pouvez afficher toutes les alertes, ainsi que les conseils de correction, les évaluations de configuration, les problèmes de capacité d'infrastructure, l'état de mise à jour système, les avertissements anti-programme malveillant et les données consignées qui leur sont associés. Vous pouvez également effectuer des recherches détaillées.

<h2 id="">Création d'un compte Operational Insights et connexion</h2>

Utilisez les informations suivantes pour configurer un compte à utiliser avec Microsoft Azure Operational Insights et connectez-vous à Operational Insights.



<h3>Obtention d'un compte professionnel ou Microsoft</h3>

Si vous souhaitez créer un compte professionnel, inscrivez-vous dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">Compte professionnel Microsoft</a>. Ensuite, vous devez accorder l'accès à votre compte professionnel pour Operational Insights dans <a href="http://aka.ms/Mr1dtz" target="_blank">Windows Azure Active Directory</a>.


Si vous souhaitez obtenir un compte Microsoft (précédemment appelé " Windows Live ID "), inscrivez-vous dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">Inscription - Compte Microsoft</a>. L'accès est accordé automatiquement à Operational Insights avec un compte Microsoft.


<h3>Connexion à Operational Insights</h3>

1. Accédez à <a href="preview.opinsights.azure.com" target="_blank">Microsoft Azure Operational Insights</a>  et choisissez Se connecter en haut de la page.
2. Sélectionnez **Compte Microsoft** ou **Compte professionnel** , puis **connectez-vous** avec vos informations d'identification.
3. Si vous y êtes invité, créez un compte Operational Insights à associer à vos informations de connexion.
4. Sélectionnez **Essayer la version d'évaluation** lorsque vous êtes invité à sélectionner la version d'Operational Insights que vous souhaitez utiliser.
5. Si vous y êtes invité, entrez votre code d'invitation et cliquez sur **Appliquer** pour accéder à la version d'évaluation. Si vous n'avez pas de code d'invitation, au bas de l'écran, entrez les informations requises pour en obtenir un.

<!--HONumber=45--> 
 