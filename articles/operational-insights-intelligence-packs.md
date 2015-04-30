<properties 
	pageTitle="Packs d'analyse décisionnelle Operational Insights" 
	description="Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté." 
	services="operational-insights" 
	documentationCenter="n/a" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="operational-insights" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="alfran"/>

# Packs d'analyse décisionnelle

Les packs d'analyse décisionnelle sont une collection de **règles de logique**, **de visualisation** et **d'acquisition des données** qui répondent aux problèmes principaux actuels que rencontrent les clients. Les packs d'analyse décisionnelle utilisent la fonction de recherche Operational Insights, pour vous proposer des mesures cernant un domaine problématique en particulier. Elles permettent des analyses plus poussées afin de vous aider à étudier et à résoudre plus rapidement les problèmes opérationnels, de collecter et de corréler les différents types de données machine et vous aident à être proactif avec des activités comme la planification de capacité, le rapport d'état des correctifs et l'audit de sécurité. 

Ce guide vous présente les différents packs d'analyse décisionnelle disponibles et leur utilisation.

>[AZURE.NOTE] Pour plus d'informations sur l' *ajout* de packs d'analyse décisionnelle, consultez [Ajout de packs d'analyse décisionnelle](operational-insights-add-intelligence-pack.md)

## SQL Assessment

Le pack d'analyse décisionnelle SQL Assessment évalue le risque et l'intégrité de vos environnements SQL Server à intervalles réguliers. Par défaut, il analyse les systèmes SQL chaque semaine et les informations sont présentées sous la forme d'un cumul mensuel. Il fournit une liste hiérarchisée de recommandations adaptées à vos déploiements. Ces recommandations sont classées en six domaines, ce qui permet à vous et à votre équipe :

- **de comprendre** rapidement **les risques et l'intégrité** de vos environnements
- d'**agir** facilement pour réduire les risques et améliorer l'intégrité
- **de hiérarchiser** votre travail et d'être plus **productif**

SQL Assessment requiert l'exécution de .NET 4 sur chaque agent. Il prend en charge les éditions Standard, Developer et Enterprise de SQL Server, toutes les versions actuellement prises en charge.
[En savoir plus sur MSDN](https://msdn.microsoft.com/library/azure/dn873958.aspx)


## Évaluation de la configuration

L'évaluation de la configuration vous fournit des informations détaillées sur l'état actuel de votre infrastructure de serveur. 

Contrairement aux packs d'analyse décisionnelle, l'évaluation de la configuration est disponible lorsque vous commencez à utiliser Operational Insights avec System Center Operations Manager. Elle n'est pas disponible si vous utilisez uniquement des agents connectés directement.

Les informations de configuration sont collectées depuis les serveurs analysés, puis sont envoyées au service Operational Insights dans le cloud à des fin de traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. Les données traitées pour les serveurs sont présentées pour les domaines suivants :

- **Alertes :** affiche les alertes liées à la configuration et proactives qui ont été déclenchées pour vos serveurs analysés. Elles sont produites par les règles créées par l'organisation Microsoft Customer and Support (CSS) avec les meilleures pratiques du secteur
- **Recommandations de la base de connaissances :** affiche les articles de la Base de connaissances Microsoft recommandés pour les charges de travail dans votre infrastructure : ils sont suggérés automatiquement en fonction de votre configuration, via l'utilisation de l'apprentissage automatique
- **Serveurs et charges de travail analysés :** affiche les serveurs et les charges de travail analysés par Operational Insights
- **Capture instantanée actuelle :** affiche les informations les plus récentes sur les serveurs ayant envoyé des données au service Operational Insights
- **Historique des modifications :** affiche la liste des modifications de configuration apportées à vos serveurs analysés

> [AZURE.IMPORTANT] L'évaluation de la configuration ne peut pas être activée pour les ordinateurs gérés par **Operations Manager**.  Pour plus d'informations, consultez [Connexion d'Operations Manager](operational-insights-connect-scom.md)


## Évaluation des logiciels malveillants
En cas de protection insuffisante, les serveurs comprenant des menaces actives et une protection insuffisante figurent sur la page **Programmes malveillants**. En utilisant les informations de la page **Programmes malveillants**, vous pouvez développer un plan pour protéger les serveurs en ayant besoin.

> [AZURE.IMPORTANT] L'évaluation des logiciels malveillants prend actuellement uniquement en charge les clients en temps réel de Windows Defender et System Center Endpoint Protection (SCEP). Si aucun de ces clients n'est trouvé, elle utilise des données de l'outil de suppression de logiciels malveillants et marque le serveur comme n'ayant pas de protection en temps réel. Vous pouvez en savoir plus [ici](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Nous surveillons également [un problème avec les ordinateurs sous Windows 7 et Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## Gestion des alertes

Avec le pack d'analyse décisionnelle de Gestion des alertes, vous pouvez afficher vos alertes **Operations Manager** sur tous vos serveurs. Cet outil peut vous aider à trier facilement les alertes et à identifier les causes premières  dans votre environnement de manière très rapide et fluide.

Vous pouvez obtenir des détails sur des scénarios clés, notamment :

- Le nombre d'alertes déclenchées dans un intervalle de temps spécifié
- Les principales sources avec le plus grand nombre d'alertes actives déclenchées dans un intervalle de temps spécifié.
- Les principales alertes actives critiques et d'avertissement déclenchées dans un intervalle de temps spécifié.
- La possibilité d'effectuer des recherches dans toutes les alertes et d'afficher chaque alerte en détail

> [AZURE.IMPORTANT] La gestion des alertes peut uniquement être activée lorsqu'Operational Insights est utilisé conjointement à **System Center Operations Manager**. Cela n'envoie pas de données depuis les agents, mais synchronise simplement les alertes Operations Manager sur le cloud pour vous permettre d'effectuer le tri dans Operational Insights et d'utiliser la recherche. Pour plus d'informations, consultez [Connexion d'Operations Manager](operational-insights-connect-scom.md)


## Planification de la capacité

Vous pouvez utiliser le pack d'analyse décisionnelle de gestion de la capacité dans Microsoft Azure Operational Insights pour vous aider à comprendre la capacité de votre infrastructure serveur. Le pack d'analyse décisionnelle lit les compteurs de performance sur le serveur analysé et envoie les données d'utilisation au service Operational Insights dans le cloud pour le traitement. La logique est appliquée aux données d'utilisation et le service cloud enregistre les données. Au fil du temps, des modèles d'utilisation sont identifiés et la capacité est projetée, en fonction de la consommation actuelle.

Par exemple, une projection peut identifier le moment où de la mémoire supplémentaire ou des cœurs de processeur supplémentaires sont nécessaires pour un serveur. Dans cet exemple, la projection peut indiquer que dans 30 jours, le serveur aura besoin de mémoire supplémentaire. Cela peut vous aider à planifier une mise à niveau de la mémoire au cours de la fenêtre de maintenance suivante du serveur, qui peut avoir lieu toutes les deux semaines.

>[AZURE.IMPORTANT] La gestion de la capacité ne peut être activée que lorsqu'Operational Insights est utilisé conjointement avec **System Center Operations Manager** et vous devez également activer le connecteur Operations Manager avec Virtual Machine Manager (VMM). Pour plus d'informations sur la connexion des systèmes, consultez [Connexion de VMM avec Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Pour plus d'informations sur l'utilisation du pack d'analyse décisionnelle de gestion de capacité, consultez :

- [Utilisation de la page de calcul](https://msdn.microsoft.com/library/azure/dn873965.aspx)
- [Utilisation de la page de stockage en attachement direct](https://msdn.microsoft.com/library/azure/dn873954.aspx) 


## Suivi des modifications

Vous pouvez utiliser le pack d'analyse décisionnelle de suivi des modifications de configuration pour vous aider à identifier facilement les logiciels et les modifications des services Windows qui se produisent dans votre environnement : l'identification de ces modifications de configuration peut vous aider à identifier les problèmes opérationnels. En utilisant les informations de la page de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur. Vous pouvez afficher les modifications apportées à votre infrastructure, puis étudier en détail les informations pour les catégories suivantes :

- Modifications selon le type de configuration pour les logiciels et services Windows
- Modifications logicielles des applications et mises à jour pour chaque serveur
- Nombre total de modifications logicielles pour chaque application
- Modifications de services Windows pour chaque serveur


## Évaluation des mises à jour du système

Vous pouvez utiliser le pack d'analyse décisionnelle des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure Si des mises à jour manquantes sont trouvées, elles sont affichées sur la page **Mises à jour**. Vous pouvez utiliser la page **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin.

La page Mises à jour présente les catégories suivantes :

- Les serveurs avec des mises à jour manquantes
- Les serveurs qui n'ont pas été mis à jour récemment
- Les mises à jour qui doivent être appliquées à des serveurs spécifiques
- Le type de mises à jour manquantes

Vous pouvez cliquer sur n'importe quelle vignette ou n'importe quel élément pour en afficher les détails dans la page **Recherche**, afin d'obtenir plus d'informations sur la mise à jour manquante.  Les résultats de recherche comprennent les informations suivantes :

- Serveur
- Titre de la mise à jour
- ID de la base de connaissances
- Produit concerné par la mise à jour
- Gravité de la mise à jour
- Date de publication

Les résultats de recherche de serveur comprennent les informations suivantes :

- Nom du serveur
- Nom de version de système d'exploitation
- Méthode d'activation de la mise à jour automatique
- Jours écoulés depuis la dernière mise à jour
- Version de l'agent Windows Update







<!--HONumber=52-->