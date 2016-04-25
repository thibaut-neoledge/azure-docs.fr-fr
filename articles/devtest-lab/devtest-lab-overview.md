<properties
	pageTitle="Qu’est-ce que le service DevTest Lab ? | Microsoft Azure"
	description="Découvrez comment un DevTest Lab peut faciliter la création, la gestion et le suivi des machines virtuelles de Microsoft Azure"
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
	ms.date="01/30/2016"
	ms.author="tarcher"/>

#Présentation de DevTest Lab

Les développeurs et testeurs cherchent à résoudre les problèmes de retards dans la création et la gestion de leurs environnements en accédant au cloud. Azure résout ces problèmes et permet le libre-service dans une nouvelle structure économique. Toutefois, les développeurs et testeurs doivent toujours consacrer beaucoup de temps à configurer leurs environnements en libre-service. En outre, les décideurs ne savent pas précisément comment exploiter le cloud afin d’optimiser leurs économies tout en allégeant le fardeau administratif.

Azure DevTest Lab est un service permettant aux développeurs et testeurs de créer rapidement des environnements dans Azure tout en réduisant le temps perdu et les coûts. Vous pouvez tester la dernière version de votre application en approvisionnant rapidement des environnements Windows et Linux à l’aide d’artefacts et de modèles réutilisables. DevTest facilite l’intégration de votre pipeline de déploiement pour approvisionner des environnements à la demande. Faites évoluer votre test de charge de travail en approvisionnant plusieurs agents de test et créez des environnements pré-approvisionnés pour des formations et des démonstrations.

##Pourquoi utiliser DevTest Lab ?

DevTest Lab offre les avantages suivants lors de la création, de la configuration et de la gestion des environnements de test et de développement dans le cloud :

###Libre-service convivial

DevTest Lab facilite le contrôle des coûts en vous permettant de définir des stratégies pour votre laboratoire, comme le nombre de machines virtuelles par utilisateur et par laboratoire. DevTest Lab vous permet également de créer des stratégies pour arrêter et démarrer automatiquement vos machines virtuelles.

###Vos applications sont prêtes pour le test en un clin d’œil

DevTest Lab vous permet de créer des environnements pré-approvisionnés dotés de tous les éléments dont votre équipe a besoin pour commencer à développer et à tester des applications. Il vous suffit de revendiquer les environnements où la dernière version fonctionnelle de votre application est installée et de commencer à travailler directement. Vous pouvez également utiliser des conteneurs pour créer plus rapidement et facilement des environnements.

###Créez, réutilisez

Capturez et partagez les artefacts et modèles d’environnement au sein de votre équipe ou organisation, tout cela dans le contrôle du code source, pour créer facilement des environnements de développement et de test.

###Intégré à votre chaîne d’outils existante

Tirez parti des plug-ins prêts à l’emploi ou de notre API pour approvisionner des environnement de développement/test directement à partir de votre outil d’intégration continue préféré, d’un environnement de développement intégré ou d’un pipeline de mise en production automatisé. Vous pouvez également utiliser notre outil en ligne de commande complet.

##Concepts du service DevTest Lab

La liste suivante présente les définitions et les concepts clés de DevTest Lab :

Les **artefacts** sont utilisés pour déployer et configurer votre application après l’approvisionnement d’une machine virtuelle. Les artefacts peuvent être :

- des outils que vous voulez installer sur la machine virtuelle, tels que des agents, Fiddler, Visual Studio ;
- des actions que vous souhaitez exécuter sur la machine virtuelle, telles que le clonage d’un référentiel ;
- des applications que vous voulez tester.

Les artefacts sont des fichiers JSON basés sur Azure Resource Manager (ARM) qui contiennent des instructions pour effectuer le déploiement et appliquer la configuration. Pour obtenir plus d’informations sur ARM, consultez la rubrique [Présentation d’Azure Resource Manager](../resource-group-overview.md).

Les **référentiels d’artefact** sont des référentiels Git dans lesquels les artefacts sont archivés. Vous pouvez ajouter un même référentiel d’artefact à plusieurs laboratoires de votre organisation pour les réutiliser et les partager.

La **base** est une image de machine virtuelle dont tous les outils et paramètres sont préinstallés et configurés pour créer rapidement une machine virtuelle. Vous pouvez approvisionner une machine virtuelle en sélectionnant une base existante, puis en ajoutant un artefact pour installer l’agent de test. Vous pouvez ensuite enregistrer la machine virtuelle approvisionnée comme base. Il est donc possible d’utiliser la base sans avoir à réinstaller l’agent de test pour chaque approvisionnement de votre machine virtuelle.

Les **plafonds** constituent un mécanisme permettant de réduire le gaspillage dans votre laboratoire. Par exemple, vous pouvez définir un plafond pour limiter le nombre de machines virtuelles qui peuvent être créées par un utilisateur ou dans un laboratoire.

Les **stratégies** vous aident à contrôler les coûts dans votre laboratoire. Par exemple, vous pouvez créer une stratégie pour arrêter automatiquement les machines virtuelles selon une planification définie.

##Étapes suivantes

Pour commencer à utiliser DevTest Lab, suivez le didacticiel étape par étape [Création d’une instance du service Azure DevTest Lab](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0413_2016-->