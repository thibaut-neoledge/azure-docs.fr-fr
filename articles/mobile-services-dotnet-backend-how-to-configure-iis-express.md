<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Configuration du serveur Web local pour autoriser les connexions à un service mobile local

Azure Mobile Services vous permet de créer votre service mobile dans Visual Studio à l'aide d'un des langages .NET pris en charge, puis de le publier sur Azure. L'utilisation d'un service principal .NET vous permettra d'exécuter, de tester et de déboguer votre service mobile sur votre ordinateur local ou sur votre machine virtuelle avant de le publier sur Azure.

Pour tester un service mobile localement avec des clients exécutés sur un émulateur, une machine virtuelle ou une station de travail séparée, vous devez configurer le serveur Web local et l'ordinateur hôte de manière à autoriser les connexions à l'adresse IP et au port de la station de travail. Cette rubrique montre comment configurer IIS Express pour activer les connexions à votre service mobile hébergé localement.

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express][]]

  [mobile-services-how-to-configure-iis-express]: ../includes/mobile-services-how-to-configure-iis-express.md
