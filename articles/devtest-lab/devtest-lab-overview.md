<properties
	pageTitle="Qu’est-ce que DevTest Labs ? | Microsoft Azure"
	description="Découvrez comment DevTest Labs peut faciliter la création, la gestion et la surveillance des machines virtuelles Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

#Qu’est-ce que DevTest Labs ?

Les développeurs et testeurs cherchent à résoudre les problèmes de retards dans la création et la gestion de leurs environnements en accédant au cloud. Azure résout ces problèmes et permet le libre-service dans une nouvelle structure économique. Toutefois, les développeurs et testeurs doivent toujours consacrer beaucoup de temps à configurer leurs environnements en libre-service. En outre, les décideurs ne savent pas précisément comment exploiter le cloud afin d’optimiser leurs économies tout en allégeant le fardeau administratif.

Azure DevTest Labs est un service permettant aux développeurs et aux testeurs de créer rapidement des environnements dans Azure tout en réduisant le temps perdu et les coûts. Vous pouvez tester la dernière version de votre application en approvisionnant rapidement des environnements Windows et Linux à l’aide d’artefacts et de modèles réutilisables. DevTest Labs facilite l’intégration de votre pipeline de déploiement pour approvisionner des environnements à la demande. Faites évoluer votre test de charge de travail en approvisionnant plusieurs agents de test et créez des environnements pré-approvisionnés pour des formations et des démonstrations.

##Pourquoi utiliser DevTest Labs ?

DevTest Labs offre les avantages suivants lors de la création, de la configuration et de la gestion des environnements de test et de développement dans le cloud :

###Libre-service convivial

DevTest Labs facilite le contrôle des coûts en vous permettant de définir des stratégies pour votre laboratoire, comme le nombre de machines virtuelles par utilisateur et par laboratoire. DevTest Labs vous permet également de créer des stratégies pour arrêter et démarrer automatiquement vos machines virtuelles.

###Vos applications sont prêtes pour le test en un clin d’œil

DevTest Labs vous permet de créer des environnements pré-approvisionnés avec tous les éléments dont votre équipe a besoin pour commencer à développer et à tester des applications. Il vous suffit de revendiquer les environnements où la dernière version fonctionnelle de votre application est installée et de commencer à travailler directement. Vous pouvez également utiliser des conteneurs pour créer plus rapidement et facilement des environnements.

###Créez, réutilisez

Capturez et partagez les artefacts et modèles d’environnement au sein de votre équipe ou organisation, tout cela dans le contrôle du code source, pour créer facilement des environnements de développement et de test.

###Intégré à votre chaîne d’outils existante

Tirez parti des plug-ins prêts à l’emploi ou de notre API pour approvisionner des environnement de développement/test directement à partir de votre outil d’intégration continue préféré, d’un environnement de développement intégré ou d’un pipeline de mise en production automatisé. Vous pouvez également utiliser notre outil en ligne de commande complet.

##Concepts de DevTest Labs

La liste suivante présente les définitions et concepts principaux de DevTest Labs :

Les **artefacts** sont utilisés pour déployer et configurer votre application après l’approvisionnement d’une machine virtuelle. Les artefacts peuvent être :

- des outils que vous voulez installer sur la machine virtuelle, tels que des agents, Fiddler, Visual Studio ;
- des actions que vous souhaitez exécuter sur la machine virtuelle, telles que le clonage d’un dépôt ;
- des applications que vous voulez tester.

Les artefacts sont des fichiers JSON basés sur Azure Resource Manager (ARM) qui contiennent des instructions pour effectuer le déploiement et appliquer la configuration. Pour obtenir plus d’informations sur ARM, consultez la rubrique [Présentation d’Azure Resource Manager](../resource-group-overview.md).

Les **dépôts d’artefact** sont des dépôts Git dans lesquels les artefacts sont archivés. Vous pouvez ajouter un même dépôt d’artefact à plusieurs laboratoires de votre organisation pour les réutiliser et les partager.

La **base** est une image de machine virtuelle dont tous les outils et paramètres sont préinstallés et configurés pour créer rapidement une machine virtuelle. Vous pouvez approvisionner une machine virtuelle en sélectionnant une base existante, puis en ajoutant un artefact pour installer l’agent de test. Vous pouvez ensuite enregistrer la machine virtuelle approvisionnée comme base. Il est donc possible d’utiliser la base sans avoir à réinstaller l’agent de test pour chaque approvisionnement de votre machine virtuelle.

Par rapport aux images de base, les **formules** offrent en plus un mécanisme pour l’approvisionnement rapide de machines virtuelles. Dans DevTest Labs, une formule est une liste de valeurs de propriétés par défaut utilisée pour créer une machine virtuelle de laboratoire. Avec les formules, les machines virtuelles ayant le même ensemble de propriétés, comme l’image de base, la taille de machine virtuelle, le réseau virtuel et les artefacts, peuvent être créées sans avoir à spécifier ces propriétés à chaque fois. Quand vous créez une machine virtuelle à partir d’une formule, les valeurs par défaut peuvent être utilisées telles quelles ou modifiées.

Les **plafonds** constituent un mécanisme permettant de réduire le gaspillage dans votre laboratoire. Par exemple, vous pouvez définir un plafond pour limiter le nombre de machines virtuelles qui peuvent être créées par un utilisateur ou dans un laboratoire.

Les **stratégies** vous aident à contrôler les coûts dans votre laboratoire. Par exemple, vous pouvez créer une stratégie pour arrêter automatiquement les machines virtuelles selon une planification définie.

##Étapes suivantes

Pour prendre en main DevTest Labs, suivez le didacticiel étape par étape [Créer un laboratoire dans DevTest Labs](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0518_2016-->