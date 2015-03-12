<properties 
	pageTitle="Configuration de IIS Express pour le test local de Service Mobile" 
	description="Apprenez à configurer IIS Express pour autoriser les connexions à un projet de service mobile local pour le test." 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="windows" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/20/2014" 
	ms.author="glenga"/>

# Configuration du serveur Web local pour autoriser les connexions à un service mobile local 

Azure Mobile Services vous permet de créer votre service mobile dans Visual Studio à l'aide d'un des langages .NET pris en charge, puis de le publier sur Azure. L'utilisation d'un service principal .NET vous permettra d'exécuter, de tester et de déboguer votre service mobile sur votre ordinateur local ou sur votre machine virtuelle avant de le publier sur Azure. 

Pour tester un service mobile localement avec des clients exécutés sur un émulateur, une machine virtuelle ou une station de travail séparée, vous devez configurer le serveur Web local et l'ordinateur hôte de manière à autoriser les connexions à l'adresse IP et au port de la station de travail. Cette rubrique montre comment configurer IIS Express pour activer les connexions à votre service mobile hébergé localement. 

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)] 



<!--HONumber=42-->
