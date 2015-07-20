<properties
 pageTitle="Présentation d'Azure Scheduler"
 description=""
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
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# Présentation d'Azure Scheduler

Azure Scheduler vous permet de façon déclarative de décrire des actions à exécuter dans le cloud. Ensuite, il planifie et exécute ces actions automatiquement. Azure Scheduler effectue cette opération à l'aide du [portail Azure](scheduler-get-started-portal.md), de code, de l'[API REST](https://msdn.microsoft.com/library/dn528946) ou de PowerShell.

Azure Scheduler gère, planifie et appelle le travail planifié. Azure Scheduler n'héberge pas de charge de travail et n'exécute pas de code. Il se contente d'_appeler_ du code qui est hébergé ailleurs, par exemple, dans Azure, localement ou auprès d'un autre fournisseur. Il effectue l'appel via HTTP, HTTPS ou une file d'attente de stockage.

Azure Scheduler planifie les tâches, conserve un historique des résultats de leur exécution, qui pourront faire l'objet d'une interrogation, et planifie de façon déterministe et fiable les charges de travail à exécuter. Les scripts planifiés Azure Mobile Services, les tâches web Azure Web Apps et d'autres fonctionnalités de planification Azure utilisent Azure Scheduler en arrière-plan. L'[API REST de Scheduler](https://msdn.microsoft.com/library/dn528946) permet de gérer la communication de ces actions. Ainsi, Scheduler prend en charge les [planifications complexes et la périodicité avancée facilement](scheduler-advanced-complexity.md).

Il existe plusieurs scénarios qui se prêtent à l'utilisation d'Azure Scheduler. Par exemple :

+ _Actions d'application périodiques_ : collecte régulière de données à partir de Twitter et intégration de celles-ci au flux.
+ _Maintenance quotidienne_ : analyse quotidienne des journaux, exécution de sauvegardes et autres tâches de maintenance. Par exemple, un administrateur peut choisir de sauvegarder sa base de données à 1h00 tous les jours pendant les 9 mois à venir.

Scheduler vous permet de créer, de mettre à jour, de supprimer, d'afficher et de gérer des [« ensembles de tâches » et des « tâches »](scheduler-concepts-terms.md) par programmation, à l'aide de scripts et sur le portail.

## Voir aussi

 [Concepts, terminologie et hiérarchie d'entités de Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail de gestion](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler](scheduler-advanced-complexity.md)

 [Informations de référence sur l'API REST de Scheduler](https://msdn.microsoft.com/library/dn528946)

 [Informations de référence sur les applets de commande PowerShell de Scheduler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité de Scheduler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d'erreur de Scheduler](scheduler-limits-defaults-errors.md)

 [Authentification sortante de Scheduler](scheduler-outbound-authentication.md)
 

<!---HONumber=July15_HO2-->