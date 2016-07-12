<properties
 pageTitle="Présentation d’Azure Scheduler | Microsoft Azure"
 description="Azure Scheduler vous permet de façon déclarative de décrire des actions à exécuter dans le cloud. Ensuite, il planifie et exécute ces actions automatiquement."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="06/30/2016"
 ms.author="krisragh"/>

# Présentation d’Azure Scheduler

Azure Scheduler vous permet de façon déclarative de décrire des actions à exécuter dans le cloud. Ensuite, il planifie et exécute ces actions automatiquement. Scheduler effectue cette opération à l’aide du [portail Azure](scheduler-get-started-portal.md), du code, de l’[API REST](https://msdn.microsoft.com/library/mt629143.aspx) ou d’Azure PowerShell.

Le scheduler crée, gère et appelle le travail planifié. Scheduler n’héberge pas de charge de travail et n’exécute pas de code. Il se contente d’_appeler_ du code qui est hébergé ailleurs, par exemple, dans Azure, localement ou auprès d’un autre fournisseur. Il effectue des appels via HTTP, HTTPS, une file d’attente de stockage, une file d’attente Service Bus ou une rubrique Service Bus.

Scheduler planifie les [tâches](scheduler-concepts-terms.md), conserve un historique des résultats de leur exécution, qui pourront faire l’objet d’une consultation, et planifie de façon déterministe et fiable les charges de travail à exécuter. Azure WebJobs (partie de la fonctionnalité d’applications Web dans le Service d’application Azure) et autres fonctionnalités de planification d’Azure utilisent le planificateur en arrière-plan. L'[API REST de Scheduler](https://msdn.microsoft.com/library/mt629143.aspx) permet de gérer la communication de ces actions. Ainsi, Scheduler prend en charge les [planifications complexes et la périodicité avancée facilement](scheduler-advanced-complexity.md).

Il existe plusieurs scénarios qui se prêtent à l’utilisation de Scheduler. Par exemple :

+ _Actions d’application périodiques_ : collecte régulière de données à partir de Twitter dans un flux.
+ _Maintenance quotidienne :_ nettoyages quotidiens des journaux en exécutant des sauvegardes et autres tâches de maintenance. Par exemple, un administrateur peut choisir de sauvegarder sa base de données toutes les nuits à 1 h 00 pendant neuf mois.

Scheduler vous permet de créer, de mettre à jour, de supprimer, d’afficher et de gérer des tâches et des [« ensembles de tâches »](scheduler-concepts-terms.md) par programmation, à l’aide de scripts et sur le portail.

## Voir aussi

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler](scheduler-advanced-complexity.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0706_2016-->