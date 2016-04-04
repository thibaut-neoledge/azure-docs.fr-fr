<properties
   pageTitle="Guide relatif aux travaux en arrière-plan | Microsoft Azure"
   description="Conseils portant sur l’exécution de tâches en arrière-plan indépendamment de l’interface utilisateur."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Guide relatif aux travaux en arrière-plan

![](media/best-practices-background-jobs/pnp-logo.png)


## Vue d’ensemble

Plusieurs types d’applications requièrent des tâches en arrière-plan, qui s’exécutent indépendamment de l’interface utilisateur. Les exemples incluent des traitements par lots, des tâches de traitement intensif et des processus exécutés sur le long terme, comme les workflows. Les travaux en arrière-plan peuvent être exécutés sans nécessiter l’intervention de l’utilisateur ; l’application peut démarrer la tâche et continuer à traiter les demandes interactives des utilisateurs. Ce processus peut favoriser la réduction de la charge sur l’interface utilisateur de l’application, ce qui peut améliorer la disponibilité et limiter les temps de réponse interactifs.

Par exemple, si une application doit générer des miniatures d’images téléchargées par les utilisateurs, elle peut effectuer ce travail en arrière-plan et enregistrer ces miniatures dans le stockage une fois la tâche terminée sans que l’utilisateur ait besoin d’attendre la fin de cette exécution. De même, un utilisateur qui initie une commande peut lancer un flux de travail en arrière-plan pour traiter cette dernière, tandis que l’interface utilisateur permet à l’utilisateur de continuer à parcourir l’application web. Une fois le travail en arrière-plan terminé, le système peut mettre à jour les commandes stockées et envoyer un e-mail à l’utilisateur, afin de confirmer la commande.

Lorsque vous envisagez d’implémenter une tâche en tant que travail en arrière-plan, vous devez en premier lieu savoir si cette opération peut s’exécuter sans interaction utilisateur et sans que l’interface utilisateur doive attendre qu’elle se termine. Ainsi, il peut être malavisé d’exécuter en arrière-plan un travail obligeant l’interface à attendre la fin de son exécution.

## Types de travaux en arrière-plan

Les travaux en arrière-plan présentent généralement une ou plusieurs des caractéristiques suivantes :

- Il peut s’agir de tâches consommant énormément de ressources CPU, comme des calculs mathématiques, des analyses de modèles structurels, etc.
- Il peut s’agir de tâches utilisant un grand nombre d’E/S, comme une suite de transactions de stockage ou des opérations d’indexation des fichiers.
- Il peut s’agir de tâches de traitement par lots, comme les opérations de traitement planifiées et les mises à jour de données exécutées la nuit.
- Il peut s’agir de workflows exécutés sur le long terme, comme les flux d’exécution de commandes ou de déploiement des services et des systèmes.
- Il peut s’agir d’actions de traitement des données sensibles, cette tâche étant déplacée vers un emplacement plus sécurisé à des fins de traitement. Par exemple, peut-être voulez-vous éviter de traiter des données sensibles par le biais d’une application web et préférez-vous utiliser un modèle de type [opérateur de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx) pour transférer les données à un processus en arrière-plan isolé, qui peut accéder au stockage protégé.

## Déclencheurs

Vous pouvez lancer un travail en arrière-plan de différentes manières. En effet, ces tâches appartiennent toutes à l’une des catégories suivantes :

- [**Déclencheurs pilotés par des événements**](#event-driven-triggers). La tâche est lancée suite à un événement, généralement une action effectuée par un utilisateur ou une étape d’un workflow.
- [**Déclencheurs pilotés par des planifications**](#schedule-driven-triggers). La tâche est appelée sur la base d’une planification basée sur un minuteur. Il peut s’agir d’une planification périodique ou d’un appel unique, prévu pour une date ultérieure.

### Déclencheurs pilotés par des événements

Lors d’un appel piloté par un événement, un déclencheur est utilisé pour démarrer la tâche en arrière-plan. Voici quelques exemples d’utilisation de déclencheurs pilotés par des événements :

- L’interface utilisateur, ou une autre tâche, place un message dans une file d’attente. Ce message contient des données relatives à une action qui a eu lieu, comme une commande passée par un utilisateur. La tâche en arrière-plan est à l’écoute sur cette file d’attente et détecte l’arrivée d’un nouveau message. Elle le lit et utilise les données qu’il inclut en tant qu’entrée pour le travail en arrière-plan.
- L’interface utilisateur, ou l’autre tâche, enregistre ou met à jour une valeur dans le stockage. La tâche en arrière-plan surveille le stockage et détecte les modifications éventuelles. Elle lit les données et les utilise en tant qu’entrée pour le travail en arrière-plan.
- L’interface utilisateur, ou l’autre tâche, envoie une demande à un point de terminaison, tel qu’un URI HTTPS ou une API exposée en tant que service web. Elle transmet les données requises pour terminer la tâche en arrière-plan dans le cadre de la demande. Le point de terminaison ou le service web appelle la tâche en arrière-plan, qui utilise les données en tant qu’entrée.

Il existe différents exemples de tâches adaptés à un appel piloté par un événement : le traitement d’images, les workflows, l’envoi d’informations à des services distants, le déploiement de nouveaux utilisateurs dans des applications multi-locataires, l’envoi d’e-mails, etc.

### Déclencheurs pilotés par des planifications

Lors d’un appel piloté par une planification, un minuteur est utilisé pour démarrer la tâche en arrière-plan. Voici quelques exemples d’utilisation de déclencheurs pilotés par des planifications :

- Un minuteur exécuté en local, au sein de l’application ou dans le cadre du système d’exploitation de l’application, appelle régulièrement une tâche en arrière-plan.
- Un minuteur en cours d’exécution dans une autre application, ou un service de minuteur comme Azure Scheduler, envoie régulièrement une demande à un service web ou à une API. Le service web ou l’API appelle la tâche en arrière-plan.
- Une application ou un processus distincts démarrent un minuteur, qui déclenche l’appel de la tâche en arrière-plan après un certain délai, ou à une heure spécifique.

Il existe différents exemples de tâches adaptés à un appel piloté par une planification : routines de traitement par lots (mise à jour de listes de produits associées pour les utilisateurs, sur la base de leur comportement récent, par exemple), tâches de traitement des données de routine (mise à jour d’index ou génération de résultats cumulés, analyse de données pour des rapports quotidiens, nettoyage des données de rétention, vérification de la cohérence des données, etc.)

Si vous utilisez une tâche pilotée par une planification qui doit s’exécuter en tant qu’instance unique, tenez compte de ce qui suit :

- Si l’instance de calcul qui exécute le planificateur (par exemple, un ordinateur virtuel utilisant des tâches Windows planifiées) est mise à l’échelle, vous verrez s’exécuter plusieurs instances du planificateur, qui peuvent démarrer plusieurs instances de la tâche.
- Si les tâches s’exécutent plus longtemps que la période séparant deux événements du planificateur, le planificateur peut démarrer une autre instance de la tâche alors que l’instance précédente est toujours en cours d’exécution.

## Renvoi de résultats

Les travaux en arrière-plan s’exécutent de façon asynchrone dans le cadre d’un processus distinct, voire à un autre emplacement, à partir de l’interface utilisateur ou du processus qui a appelé la tâche en arrière-plan. Dans l’idéal, les tâches en arrière-plan sont des opérations de type « Fire and Forget » ; la progression de leur exécution n’a aucun impact sur l’interface utilisateur ou le processus appelant. Cela signifie que le processus appelant n’est pas contraint d’attendre la fin d’une tâche et ne peut pas, de ce fait, détecter automatiquement son achèvement. Si vous avez besoin qu’une tâche en arrière-plan communique avec la tâche appelante pour indiquer sa progression ou sa fin, vous devez implémenter un mécanisme à cette fin. Voici quelques exemples :

- Écriture de la valeur de l’indicateur d’état dans l’espace de stockage accessible à l’interface utilisateur ou à la tache appelante, qui peut surveiller ou vérifier cette valeur, le cas échéant. Les autres données que la tâche en arrière-plan doit renvoyer à la tâche appelante peuvent être placées dans le même espace de stockage.
- Établissement d’une file d’attente de réponse, que l’interface utilisateur ou la tâche appelante écoute. La tâche en arrière-plan peut envoyer des messages à la file d’attente, indiquant l’état et l’exécution des opérations. Les données que la tâche en arrière-plan doit renvoyer à la tâche appelante peuvent être placées dans ces messages. Si vous utilisez Microsoft Azure Service Bus, vous pouvez utiliser les propriétés **ReplyTo** et **CorrelationId** pour implémenter cette fonctionnalité. Pour en savoir plus, voir [Correlation in Service Bus Brokered Messaging](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exposition d’une API ou d’un point de terminaison à partir de la tâche en arrière-plan, à laquelle l’interface utilisateur ou la tâche appelante peut accéder pour obtenir des informations sur l’état. Les données que la tâche en arrière-plan doit renvoyer à la tâche appelante peuvent être incluses dans la réponse.
- Rappel de la tâche en arrière-plan vers l’interface utilisateur ou la tâche appelante, via une API, pour indiquer l’état à différents points prédéfinis ou lors de l’achèvement. Cette action peut être effectuée via des événements déclenchés en local, ou via un mécanisme de publication ou d’abonnement. Les données que la tâche en arrière-plan doit renvoyer à la tâche appelante peuvent être incluses dans la charge utile de l’événement ou de la demande.

## Environnement d’hébergement

Vous pouvez héberger des tâches en arrière-plan à l’aide de différents services de plateforme Microsoft Azure :

- [**Azure Web Apps et WebJobs**](#azure-web-apps-and-webjobs). Vous pouvez utiliser WebJobs pour exécuter des tâches personnalisées basées sur différents types de scripts ou de programmes exécutables dans le contexte de l’application web.
- [**Rôles de travail et web Microsoft Azure Cloud Services**](#azure-cloud-services-web-and-worker-roles). Vous pouvez écrire du code dans un rôle qui s’exécute en tant que tâche en arrière-plan.
- [**Azure Virtual Machines**](#azure-virtual-machines). Si vous disposez d’un service Windows ou que vous souhaitez utiliser le Planificateur de tâches de Windows, une pratique courante consiste à héberger les tâches en arrière-plan dans une machine virtuelle dédiée.

Les sections suivantes décrivent chacune de ces options plus en détail, en ajoutant des informations qui vous permettront de choisir l’action la plus appropriée.

### Azure Web Apps et WebJobs

Vous pouvez utiliser Azure WebJobs pour exécuter des tâches personnalisées en tant que tâches en arrière-plan dans une application web Azure. WebJobs permet une exécution dans le contexte de votre application web en tant que processus continu ou en réponse à un événement déclencheur d’Azure Scheduler ou à des facteurs externes, tels que les modifications apportées aux objets blob de stockage et aux files d’attente de messages. Les travaux peuvent être démarrés et arrêtés à la demande, et arrêtés normalement. Si une tâche web exécutée en continu échoue, elle est redémarrée automatiquement. Les actions liées aux nouvelles tentatives et aux erreurs sont configurables.

Lorsque vous configurez une tâche web, tenez compte de ce qui suit :

- Si vous souhaitez que le travail réponde à un déclencheur piloté par un événement, affectez-lui la valeur **Exécuter en continu**. Le script ou programme est stocké dans le dossier nommé site/wwwroot/app\_data/jobs/continuous.
- Si vous souhaitez que le travail réponde à un déclencheur piloté par une planification, affectez-lui la valeur **Exécuter selon une planification**. Le script ou programme est stocké dans le dossier nommé site/wwwroot/app\_data/jobs/triggered.
- Si vous choisissez l’option **Exécuter à la demande** lorsque vous configurez un travail, ce dernier exécute le même code qu’avec l’option **Exécuter selon une planification**.

Azure WebJobs s’exécute dans le bac à sable (sandbox) de l’application web, ce qui signifie qu’il peut accéder aux variables d’environnement et partager des informations telles que les chaînes de connexion avec l’application web. Le travail peut accéder à l’identificateur unique de l’ordinateur qui exécute le travail. La chaîne de connexion **AzureWebJobsStorage** fournit l’accès aux files d’attente de stockage Azure, aux objets blob et aux tables de données des applications, ainsi qu’à Service Bus pour la messagerie et les communications. La chaîne de connexion **AzureWebJobsDashboard** fournit l’accès aux fichiers journaux d’actions des tâches.

Les tâches web Azure présentent les caractéristiques suivantes :

- **Sécurité** : les tâches web sont protégées par les informations d’identification sur le déploiement de l’application web.
- **Types de fichiers pris en charge** : les tâches web peuvent être définies au moyen de scripts de commande (.cmd), de fichiers de commandes (.bat), de scripts PowerShell (.ps1), de scripts Shell bash (.sh), de scripts PHP (.php), de scripts Python (.py), de code JavaScript (.js) et de programmes exécutables (.exe, .jar, etc.).
- **Déploiement** : les scripts et exécutables peuvent être déployés à l’aide du portail Microsoft Azure, créés et déployés à l’aide du complément [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) pour Visual Studio, ou [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs), via le [Kit de développement logiciel (SDK) des tâches web Microsoft Azure](websites-dotnet-webjobs-sdk-get-started.md), ou via une opération de copie directe aux emplacements suivants :
  - Pour une exécution déclenchée : site/wwwroot/app\_data/jobs/triggered/{nom\_travail}
  - Pour une exécution en continu : site/wwwroot/app\_data/jobs/continuous/{nom\_travail}
- **Journalisation** : la commande Console.Out est traitée (marquée) comme étant de type « INFO », alors que la commande « Console.Error » présente le type « ERROR ». Les informations de surveillance et de diagnostic sont accessibles via le portail Azure. Quant aux fichiers journaux, ils peuvent être téléchargés à partir du site. Ces éléments sont sauvegardés aux emplacements suivants :
  - Pour une exécution déclenchée : Vfs/data/jobs/continuous/jobName
  - Pour une exécution en continu : Vfs/data/jobs/triggered/jobName
- **Configuration** : vous pouvez configurer des tâches web au moyen du portail, de l’API REST et de PowerShell. Un fichier de configuration nommé settings.job, situé dans le même répertoire racine que le script de travail, peut être utilisé pour fournir des informations sur la configuration d’un travail. Par exemple :
  - { "stopping\_wait\_time": 60 }
  - { "is\_singleton": true }

### Considérations

- Par défaut, WebJobs est mis à l’échelle par rapport à l’application web. Toutefois, vous pouvez configurer les travaux afin qu’ils s’exécutent sur une instance unique, en définissant la propriété de configuration **is\_singleton** sur la valeur true. Les tâches web d’une instance unique sont utiles pour les tâches que vous ne voulez pas mettre à l’échelle ou exécuter en tant qu’instances multiples simultanées (réindexation, analyse des données et tâches similaires).
- Pour réduire l’impact des tâches sur les performances de l’application web, envisagez la création d’une instance de l’application web Azure vide dans un nouveau plan de service d’application, afin d’héberger les tâches web qui s’exécutent depuis longtemps ou utilisent un grand nombre de ressources.

### Plus d’informations

- La section [Ressources Azure WebJobs](websites-webjobs-resources.md) répertorie de nombreuses ressources, éléments à télécharger et exemples utiles pour les tâches web.

## Rôles de travail et web Microsoft Azure Cloud Services

Les tâches en arrière-plan peuvent être exécutées dans un rôle web ou dans un rôle de travail distinct. La sélection du rôle de travail ou du rôle web doit reposer sur les exigences en termes d’évolutivité et d’élasticité, la durée de vie des tâches, la cadence de publication, la sécurité, la tolérance de panne, les conflits, la complexité et l’architecture logique. Pour en savoir plus, voir [Modèle de consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx).

Vous pouvez implémenter des tâches en arrière-plan dans un rôle Cloud Services de différentes manières :

- Créez une implémentation de la classe **RoleEntryPoint** dans le rôle et utilisez ses méthodes pour exécuter des tâches en arrière-plan. Les tâches s’exécutent dans le contexte du fichier WaIISHost.exe. Elles peuvent utiliser la méthode **GetSetting** de la classe **CloudConfigurationManager** pour charger les paramètres de configuration. Pour en savoir plus, voir [Cycle de vie (Cloud Services)](#lifecycle-cloud-services).
- Utilisez les tâches de démarrage pour exécuter des tâches en arrière-plan lorsque l’application démarre. Pour forcer les tâches à poursuivre leur exécution en arrière-plan, définissez la propriété **taskType** sur **background** (si vous ne le faites pas, le processus de démarrage de l’application s’arrête et attendra la fin de la tâche). Pour en savoir plus, voir [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md).
- Utilisez le Kit de développement logiciel (SDK) des tâches web pour implémenter des tâches en arrière-plan en tant que tâches web, initiées sous la forme d’une tâche de démarrage. Pour en savoir plus, voir [Prise en main du Kit de développement logiciel (SDK) Azure WebJobs](websites-dotnet-webjobs-sdk-get-started.md).
- Utilisez une tâche de démarrage pour installer un service Windows qui exécute une ou plusieurs tâches en arrière-plan. Vous devez définir la propriété **taskType** sur **background**, afin que le service s’exécute en arrière-plan. Pour en savoir plus, voir [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md).

### Exécution de tâches en arrière-plan dans le rôle web

Le principal avantage lié à l’exécution des tâches en arrière-plan dans le rôle web est la réduction des coûts d’hébergement, car il n’est pas nécessaire de déployer des rôles supplémentaires.

### Exécution de tâches en arrière-plan dans le rôle de travail

Le fait d’exécuter des tâches en arrière-plan dans un rôle de travail présente plusieurs avantages :

- Cela vous permet de gérer la mise à l’échelle de chaque type de rôle, de manière séparée. Par exemple, vous devrez peut-être disposer d’un plus grand nombre d’instances du rôle web pour gérer la charge actuelle, mais d’un nombre moins important d’instances du rôle de travail qui exécute les tâches en arrière-plan. La mise à l’échelle séparée des instances de calcul de la tâche en arrière-plan par rapport aux rôles de l’interface utilisateur permet de réduire les coûts d’hébergement, tout en assurant des performances acceptables.
- Cela permet de décharger la charge de traitement des tâches en arrière-plan du rôle web. Le rôle web qui fournit l’interface utilisateur peut rester réactif, ce qui peut se traduire par un nombre moins important d’instances requises pour gérer un volume donné de demandes de la part des utilisateurs.
- Cela vous permet d’implémenter la séparation des intérêts. Chaque type de rôle peut implémenter un ensemble spécifique de tâches clairement définies et associées. Ainsi, la conception et la gestion du code sont plus simples, car l’interdépendance entre le code et les fonctionnalités est moins appuyée entre les rôles.
- Cela peut faciliter l’isolation des données et processus sensibles. Par exemple, les rôles web qui implémentent l’interface utilisateur n’ont pas nécessairement besoin d’un accès aux données gérées et contrôlées par un rôle de travail. Cela peut permettre de renforcer la sécurité, notamment lorsque vous utilisez un modèle tel que le [modèle d’opérateur de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx).  

### Considérations

Tenez compte des points suivants lorsque vous choisissez la méthode et l’emplacement de déploiement des tâches en arrière-plan via l’utilisation des rôles de travail et web de Cloud Services :

- L’hébergement des tâches en arrière-plan dans un rôle web existant peut permettre d’éviter les coûts liés à l’exécution d’un rôle de travail distinct pour ces seules tâches. Toutefois, cela risque également d’affecter les performances et la disponibilité de l’application en cas de conflit portant sur le traitement et d’autres ressources. L’utilisation d’un rôle de travail distinct protège le rôle web des effets de l’exécution de tâches en arrière-plan exécutées depuis longtemps ou utilisant un grand nombre de ressources.
- Si vous hébergez des tâches en arrière-plan à l’aide de la classe **RoleEntryPoint**, vous pouvez facilement la déplacer vers un autre rôle. Ainsi, si vous créez la classe dans un rôle web, puis décidez que vous devez exécuter les tâches dans un rôle de travail, vous pouvez déplacer l’implémentation de la classe **RoleEntryPoint** vers le rôle de travail.
- Les tâches de démarrage sont conçues pour exécuter un programme ou un script. Le déploiement d’un travail en arrière-plan en tant que programme exécutable peut s’avérer plus difficile, surtout s’il requiert également le déploiement d’assemblys dépendants. Peut-être est-il plus simple de déployer et d’utiliser un script pour définir un travail en arrière-plan lorsque vous utilisez des tâches de démarrage.
- Les exceptions qui provoquent l’échec d’une tâche en arrière-plan ont un impact différent selon leur mode d’hébergement :
  - Si vous utilisez la méthode reposant sur la classe **RoleEntryPoint**, l’échec d’une tâche entraîne le redémarrage du rôle ; la tâche redémarre automatiquement. Cela peut affecter la disponibilité de l’application. Pour éviter ce problème, veillez à inclure une fonction robuste de gestion des exceptions dans la classe **RoleEntryPoint** et dans toutes les tâches en arrière-plan. Utilisez du code pour redémarrer des tâches ayant échoué lorsque cela s’applique, et lancez l’exception pour redémarrer le rôle uniquement si vous ne pouvez effectuer une récupération normale suite à l’échec dans votre code.
  - Si vous utilisez des tâches de démarrage, vous êtes chargé de gérer l’exécution des tâches et de vérifier si elles échouent.
- Dans ce cas, les opérations de gestion et de surveillance des tâches de démarrage s’avèrent plus complexes qu’au moyen de la classe **RoleEntryPoint**. Toutefois, le Kit de développement logiciel (SDK) des tâches web Azure inclut un tableau de bord pour faciliter la gestion de tâches web que vous lancez via des tâches de démarrage.

### Plus d’informations

- [Modèle de consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx)
- [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)

## Azure Virtual Machines

Vous pouvez implémenter les tâches en arrière-plan de manière à empêcher leur déploiement sur Azure Web Apps ou Cloud Services, sauf si ce n’est pas pertinent. Voici quelques exemples typiques : services Windows et utilitaires et programmes exécutables tiers. Il est également possible d’inclure des programmes écrits pour un environnement d’exécution différent de celui qui héberge l’application, par exemple un programme Unix ou Linux que vous souhaitez exécuter à partir d’une application Windows ou .NET. Vous avez le choix entre plusieurs systèmes d’exploitation pour une machine virtuelle Azure. Vous pouvez ensuite exécuter votre service ou programme exécutable sur cette machine virtuelle.

Pour savoir quand utiliser Virtual Machines, consultez [Comparaison entre Azure App Service, Azure Cloud Services et Azure Virtual Machines](choose-web-site-cloud-service-vm.md). Pour en savoir plus sur les options des machines virtuelles, voir [Tailles des machines virtuelles et des services cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Pour en savoir plus sur les systèmes d’exploitation et images préconstruites disponibles pour les machines virtuelles, consultez la [galerie dédiée à Azure Virtual Machines](https://azure.microsoft.com/gallery/virtual-machines/).

Pour lancer la tâche en arrière-plan dans un ordinateur virtuel distinct, vous disposez d’un éventail d’options :

- Vous pouvez exécuter la tâche à la demande directement à partir de votre application, en envoyant une demande à un point de terminaison exposé par la tâche, puis en transmettant les données requises par la tâche. Le point de terminaison appelle la tâche.
- Vous pouvez configurer la tâche afin qu’elle s’exécute selon une planification précise, à l’aide d’un planificateur ou un d’un minuteur proposé par le système d’exploitation choisi. Par exemple, sur Windows, vous pouvez utiliser le Planificateur de tâches Windows pour exécuter des scripts et des tâches. Sinon, si le système SQL Server est installé sur la machine virtuelle, vous pouvez utiliser SQL Server Agent pour exécuter ces scripts et ces tâches.
- Vous pouvez utiliser Microsoft Azure Scheduler pour lancer la tâche en ajoutant un message à une file d’attente sur laquelle la tâche est à l’écoute, ou en envoyant une requête à une API exposée par la tâche.

Consultez la section relative aux [déclencheurs](#triggers) (ci-dessus) pour en savoir plus sur le lancement de tâches en arrière-plan.

### Considérations

Lorsque vous envisagez de déployer des tâches en arrière-plan sur une machine virtuelle Azure, tenez compte des points suivants :

- L’hébergement de tâches en arrière-plan sur une machine virtuelle Azure distincte vous permet une certaine flexibilité et vous offre un contrôle précis sur l’initiation, l’exécution, la planification et l’allocation des ressources. Toutefois, cela augmente le coût d’exécution si un ordinateur virtuel doit être déployé uniquement pour exécuter des tâches en arrière-plan.
- Il n’existe aucun mécanisme pour effectuer le suivi des tâches sur le portail Azure, ni aucune fonction de redémarrage automatisé des tâches ayant échoué. Toutefois, vous pouvez surveiller l’état de base de la machine virtuelle et le gérer au moyen des [appels de commande Microsoft Azure Service Management](http://msdn.microsoft.com/library/azure/dn495240.aspx). Toutefois, il n’existe aucune fonction permettant de contrôler les processus et threads dans les nœuds de calcul. En règle générale, l’utilisation d’une machine virtuelle nécessite des efforts supplémentaires pour implémenter un mécanisme afin de collecter des données d’instrumentation dans la tâche et de rassembler les données du système d’exploitation sur l’ordinateur virtuel. Pour ce faire, vous pouvez essayer le composant [System Center Management Pack pour Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Vous pouvez envisager de créer des sondes de surveillance exposées via des points de terminaison HTTP. Le code de ces sondes peut effectuer des contrôles d’intégrité, collecter des statistiques et des informations opérationnelles ou rassembler les informations sur les erreurs et les renvoyer à une application de gestion. Pour en savoir plus, voir [Modèle de surveillance de point de terminaison d’intégrité](http://msdn.microsoft.com/library/dn589789.aspx).

### Plus d’informations

- [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) sur Azure
- [FAQ sur les machines virtuelles Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## Remarques relatives à la conception

Certains facteurs essentiels doivent être pris en compte lors de la conception de tâches en arrière-plan. Les sections ci-après traitent du partitionnement, des conflits et de la coordination.

## Partitionnement

Si vous décidez d’inclure des tâches en arrière-plan dans une instance de calcul existante (par exemple, une application web, un rôle web, un rôle de travail existant ou une machine virtuelle), vous devez déterminer l’effet de cette opération sur les attributs de qualité de l’instance de calcul et de la tâche en arrière-plan elle-même. Ces facteurs vous aideront à décider s’il faut colocaliser les tâches avec l’instance de calcul existante, ou les séparer dans une instance de calcul distincte :

- **Disponibilité** : il se peut que les tâches en arrière-plan ne doivent pas nécessairement présenter le même niveau de disponibilité que d’autres parties de l’application, notamment l’interface utilisateur et d’autres parties directement impliquées par l’interaction de l’utilisateur. Par ailleurs, les tâches en arrière-plan seront peut-être plus tolérantes envers l’échec d’une nouvelle tentative de connexion ou un problème de latence, voire d’autres facteurs affectant la disponibilité, car les opérations peuvent être mises en file d’attente. Toutefois, la capacité doit être suffisante pour empêcher la sauvegarde de demandes susceptibles de bloquer les files d’attente et d’affecter l’application dans son ensemble.
- **Évolutivité** : les tâches en arrière-plan sont susceptibles de présenter des exigences différentes en termes d’évolutivité par rapport à l’interface utilisateur et aux éléments interactifs de l’application. Il peut être nécessaire de mettre l’interface utilisateur à l’échelle, afin qu’elle puisse gérer les pics de demande ; l’exécution de tâches en arrière-plan en attente pourrait alors être effectuée pendant les périodes moins actives et ce, par un nombre moins important d’instances de calcul.
- **Résilience** : l’échec d’une instance de calcul qui se contente d’héberger des tâches en arrière-plan peut ne pas affecter l’application dans son ensemble de manière irrémédiable s’il est possible de mettre en file d’attente ou de reporter les demandes relatives à ces tâches, jusqu’à ce que la tâche soit de nouveau disponible. Si l’instance de calcul et/ou les tâches peuvent être redémarrées pendant un intervalle approprié, les utilisateurs de l’application ne seront peut-être pas affectés.
- **Sécurité** : les tâches en arrière-plan peuvent présenter des restrictions ou des exigences différentes en termes de sécurité par rapport à l’interface utilisateur ou à d’autres éléments de l’application. En utilisant une instance de calcul distincte, vous pouvez spécifier un environnement de sécurité différent pour les tâches. Vous pouvez également utiliser des modèles comme celui de l’opérateur de contrôle d’appels pour isoler les instances de calcul en arrière-plan de l’interface utilisateur, afin d’optimiser la sécurité et la séparation.
- **Performances** : vous pouvez choisir le type d’instance de calcul des tâches en arrière-plan de façon qu’il réponde précisément aux exigences de ces tâches en termes de performances. Cela peut se traduire par l’utilisation d’une option de calcul moins coûteuse si les tâches ne nécessitent pas les mêmes fonctions de traitement que l’interface utilisateur, ou d’une instance plus volumineuse s’il leur faut davantage de capacité et de ressources.
- **Gestion** : les tâches en arrière-plan peuvent présenter un rythme de développement et de déploiement différent de celui du code de l’application principale ou de l’interface utilisateur. Le fait de déployer une instance de calcul distincte peut permettre de simplifier les mises à jour et le contrôle de version.
- **Coût** : l’ajout d’instances de calcul pour exécuter des tâches en arrière-plan augmente les coûts d’hébergement. Vous devez déterminer soigneusement l’équilibre entre l’ajout de capacité additionnelle et ces coûts supplémentaires.

Pour en savoir plus, voir [Modèle de choix de l’instance responsable](http://msdn.microsoft.com/library/dn568104.aspx) et [Modèle de consommateur concurrent](http://msdn.microsoft.com/library/dn568101.aspx).

## Conflits

Si vous disposez de plusieurs instances d’un travail en arrière-plan, il est possible qu’elles soient en concurrence pour l’accès aux ressources et aux services (bases de données et stockage, par exemple). Cet accès simultané peut provoquer des conflits de ressources, ce qui peut compromettre la disponibilité des services et l’intégrité des données dans le stockage. Vous pouvez résoudre un conflit de ressources en utilisant une approche de verrouillage pessimiste, afin d’empêcher les instances concurrentes d’une tâche d’accéder simultanément à un service ou de corrompre les données.

Vous pouvez aussi définir des tâches en arrière-plan en tant que singletons, afin qu’une seule instance s’exécute à la fois. Toutefois, si vous optez pour cette solution, vous supprimez les avantages que peut offrir une configuration à plusieurs instances en termes de fiabilité et de performances, notamment si l’interface utilisateur peut fournir suffisamment d’efforts pour garantir l’activité d’au moins une tâche en arrière-plan. Il est vital de s’assurer que la tâche en arrière-plan peut redémarrer automatiquement et présente une capacité suffisante pour répondre aux pics de demande. Vous pouvez obtenir ce résultat en allouant à une instance de calcul un nombre de ressources suffisant, et/ou en implémentant un mécanisme de mise en file d’attente capable de stocker les demandes à des fins d’exécution ultérieure (lorsque la demande baisse).

## Coordination

Les tâches en arrière-plan peuvent être complexes et nécessiter le lancement de plusieurs tâches individuelles pour produire un résultat ou satisfaire toutes les exigences. Dans ces scénarios, on divise souvent la tâche en sous-tâches ou étapes discrètes de plus petite taille, qui peuvent être exécutées par plusieurs consommateurs. Les travaux impliquant plusieurs étapes peuvent s’avérer efficaces et plus flexibles, car les étapes individuelles peuvent être utilisées dans plusieurs travaux. Il est également aisé d’ajouter, de supprimer ou de modifier l’ordre de ces étapes.

La coordination de plusieurs étapes ou tâches peut s’avérer délicate. Toutefois, vous pouvez vous reposer sur trois modèles courants pour effectuer l’implémentation d’une solution :

- **Décomposition d’une tâche en plusieurs étapes réutilisables**. Une application peut être contrainte d’exécuter diverses tâches de complexité variable sur les informations qu’elle traite. Il existe une approche directe, mais quelque peu rigide, permettant d’implémenter cette application : l’exécution de ce traitement sous la forme d’un module monolithique. Toutefois, cette approche est susceptible de réduire les opportunités de refactorisation, d’optimisation ou de réutilisation du code, si certaines parties du même processus de traitement doivent être utilisées à un autre emplacement de l’application. Pour en savoir plus, voir [Modèle des canaux et filtres](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gestion de l’exécution des étapes d’une tâche**. Une application peut effectuer des tâches comprenant un certain nombre d’étapes, dont certaines peuvent appeler des services à distance ou accéder à des ressources distantes. Les étapes individuelles peuvent être indépendantes les unes des autres, mais elles restent orchestrées par la logique d’application qui implémente la tâche. Pour en savoir plus, voir [Modèle de superviseur de l’agent du planificateur](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gestion de la récupération des étapes en échec d’une tâche**. Une application peut avoir besoin d’annuler le travail effectué par une série d’étapes, qui définissent une opération cohérente au final, si l’une de ces étapes échoue. Pour en savoir plus, voir [Modèle de transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx).

## Cycle de vie (Cloud Services)

 Si vous décidez d’implémenter des travaux en arrière-plan pour les applications Cloud Services utilisant des rôles web ou de travail en recourant à la classe **RoleEntryPoint**, vous devez être capable d’appréhender le cycle de vie de cette classe, afin de pouvoir l’utiliser correctement.

Les rôles web et de travail passent par un ensemble de phases distinctes lorsqu’elles démarrent, s’exécutent et s’arrêtent. La classe **RoleEntryPoint** expose une série d’événements qui indiquent que ces phases sont en cours. Vous les utilisez pour initialiser, exécuter et arrêter vos tâches en arrière-plan. Voici le cycle complet :

- Azure charge l’assembly de rôle et effectue une recherche portant sur une classe dérivée de la classe **RoleEntryPoint** dans cet assembly.
- S’il la trouve, il appelle la méthode **RoleEntryPoint.OnStart()**. Vous substituez cette méthode pour initialiser vos tâches en arrière-plan.
- Une fois la méthode **OnStart** exécutée, Azure appelle la méthode **Application\_Start()** dans le fichier global de l’application, s’il est présent (par exemple : le fichier Global.asax dans un rôle web exécutant ASP.NET).
- Microsoft Azure appelle la méthode **RoleEntryPoint.Run()** sur un nouveau thread au premier plan qui s’exécute en parallèle avec la méthode **OnStart()**. Vous substituez cette méthode pour démarrer vos tâches en arrière-plan.
- Lorsque la méthode Run termine son exécution, Azure commence par appeler la méthode **Application\_End()** dans le fichier global de l’application (s’il est présent), puis appelle la méthode **RoleEntryPoint.OnStop()**. Vous substituez la méthode **OnStop** pour arrêter vos tâches en arrière-plan, nettoyer les ressources, supprimer les objets et fermer les connexions que les tâches sont susceptibles d’avoir utilisées.
- Le processus hôte du rôle de travail Azure est arrêté. À ce stade, ce rôle est recyclé et redémarre.

Pour en savoir plus et consulter un exemple d’utilisation des méthodes de la classe **RoleEntryPoint**, voir [Modèle de consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx).

## Considérations

Lorsque vous planifiez la façon dont vous allez exécuter les tâches en arrière-plan dans un rôle web ou de travail, tenez compte des points suivants :

- L’implémentation par défaut de la méthode **Run** dans la classe **RoleEntryPoint** contient un appel à l’élément **Thread.Sleep(Timeout.Infinite)** qui permet au rôle de rester actif indéfiniment. Si vous substituez la méthode **Run** (qui est généralement nécessaire pour exécuter des tâches en arrière-plan), vous ne devez pas autoriser votre code à quitter cette méthode, sauf si vous souhaitez recycler l’instance du rôle.
- Une implémentation classique de la méthode **Run** inclut du code permettant de démarrer chacune des tâches en arrière-plan, ainsi qu’une construction en boucle qui vérifie périodiquement l’état de toutes les tâches en arrière-plan. Il peut redémarrer toute tâche en échec ou détecter la présence éventuelle de jetons d’annulation qui signalent les travaux dont l’exécution est terminée.
- Si une tâche en arrière-plan lève une exception non gérée, cette tâche doit être recyclée alors que toutes les autres tâches en arrière-plan du rôle doivent être autorisées à se poursuivre. Toutefois, si l’exception est provoquée par l’altération d’objets en dehors de la tâche, tels que le stockage partagé, cette exception doit être gérée par votre classe **RoleEntryPoint**, toutes les tâches doivent être annulées et la méthode **Run** doit être autorisée à se terminer. Ensuite, Microsoft Azure redémarre le rôle.
- Utilisez la méthode **OnStop** pour suspendre ou arrêter les tâches en arrière-plan et nettoyer les ressources. Cela peut impliquer l’arrêt des tâches de longue durée ou à plusieurs étapes ; il est essentiel de savoir comment y parvenir sans entraîner d’incohérence des données. Si une instance de rôle s’arrête pour une raison quelconque (autre qu’un arrêt initié par l’utilisateur), le code exécutant la méthode **OnStop** doit se terminer dans les cinq minutes. Le cas échéant, son arrêt sera forcé. Vérifiez que votre code peut s’exécuter dans les temps ou peut accepter d’être arrêté avant la fin de son exécution.  
- L’équilibreur de charge de Microsoft Azure commence à diriger le trafic vers l’instance de rôle lorsque la méthode **RoleEntryPoint.OnStart** renvoie la valeur true. Par conséquent, vous pouvez envisager de placer l’ensemble de votre code d’initialisation dans la méthode **OnStart**, afin que les instances de rôle qui ne s’initialisent pas correctement ne reçoivent aucun trafic.
- Vous pouvez utiliser des tâches de démarrage en plus des méthodes de la classe **RoleEntryPoint**. Vous devez utiliser des tâches de démarrage pour initialiser les paramètres que vous devez modifier dans l’équilibreur de charge Microsoft Azure, car ces tâches seront exécutées avant que le rôle ne reçoive une demande. Pour en savoir plus, voir [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md).
- Si une tâche de démarrage inclut une erreur, cela peut obliger le rôle à redémarrer continuellement. Ainsi, il se peut que vous ne puissiez pas effectuer un échange d’adresses IP virtuelles vers une version intermédiaire, car l’échange nécessite un accès exclusif au rôle, ce qui est impossible lorsque ce dernier redémarre. Pour résoudre ce problème :
	-  Ajoutez le code suivant au début des méthodes **OnStart** et **Run**, dans votre rôle :

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Ajoutez la définition du paramètre **Freeze** en tant que valeur booléenne dans les fichiers ServiceDefinition.csdef et ServiceConfiguration.cscfg du rôle, puis définissez-la sur la valeur **false**. Si le rôle entre dans un mode de redémarrage répété, vous pouvez affecter à ce paramètre la valeur **true** pour bloquer l’exécution du rôle et permettre son échange avec une version précédente.

## Remarques relatives à la résilience

Les tâches en arrière-plan doivent être assez résilientes pour pouvoir fournir des services fiables à l’application. Lors de la planification et de la conception de tâches en arrière-plan, tenez compte des points suivants :

- Les tâches en arrière-plan doivent être en mesure de gérer correctement le redémarrage d’un rôle ou d’un service, sans corrompre les données ni introduire d’incohérences dans l’application. Pour les tâches de longue durée ou à plusieurs étapes, vous pouvez envisager d’utiliser des _points de contrôle_, en enregistrant l’état des travaux dans le stockage persistant ou en tant que messages dans une file d’attente, le cas échéant. Ainsi, vous pouvez conserver les informations d’état dans un message placé dans une file d’attente, et mettre à jour ces informations de manière incrémentielle en parallèle avec la progression de la tâche, afin que cette dernière puisse être traitée depuis le dernier point de contrôle, au lieu de reprendre l’exécution depuis le début. Lorsque vous utilisez des files d’attente Azure Service Bus, vous pouvez recourir à des sessions de messages pour activer le même scénario. Les sessions vous permettent d’enregistrer et de récupérer l’état de traitement de l’application (à l’aide des méthodes [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx)). Pour en savoir plus sur la conception de processus et workflows à plusieurs étapes fiables, voir [Modèle de superviseur de l’agent du planificateur](http://msdn.microsoft.com/library/dn589780.aspx).
- Lorsque vous utilisez des rôles web ou de travail pour héberger plusieurs tâches en arrière-plan, concevez la substitution de la méthode **Run** de manière à effectuer le suivi des tâches interrompues ou en échec, puis redémarrez-les. Si cette opération n’est pas pratique, et si vous utilisez un rôle de travail, obligez ce dernier à redémarrer en quittant la méthode **Run**.
- Lorsque vous utilisez des files d’attente pour communiquer avec les tâches en arrière-plan, ces files peuvent jouer le rôle de mémoire tampon pour stocker les demandes envoyées aux tâches lorsque l’application est soumise à une charge supérieure à la moyenne. Ainsi, les tâches peuvent se mettre au même niveau que l’interface utilisateur lorsque l’activité baisse. Cela signifie également que le recyclage du rôle ne bloquera pas l’interface utilisateur. Pour en savoir plus, voir [Modèle de nivellement de la charge basé sur une file d’attente](http://msdn.microsoft.com/library/dn589783.aspx). Si certaines tâches sont plus importantes que d’autres, vous pouvez envisager d’implémenter le [modèle de file d’attente prioritaire](http://msdn.microsoft.com/library/dn589794.aspx) pour vous assurer que ces tâches s’exécutent avant les autres.
- Les tâches en arrière-plan initiées par, ou qui traitent les messages doivent être conçues pour gérer les incohérences, comme les messages qui arrivent à destination, mais dans le désordre, ou les messages qui entraînent une erreur répétée, souvent appelés _messages incohérents_, et les messages livrés plusieurs fois. Tenez compte des éléments suivants :
  - Les messages qui doivent être traités selon un ordre spécifique, comme ceux qui modifient les données sur la base de leur valeur existante (par exemple, en ajoutant une valeur à une valeur existante), risquent de ne pas arriver dans le même ordre que lors de leur envoi. Par ailleurs, ils peuvent être gérés par différentes instances d’une tâche en arrière-plan et selon un ordre différent, étant donné les différences de charge sur chaque instance. Les messages devant être traités selon un ordre spécifique doivent inclure un numéro, une clé ou un indicateur de séquence quelconque, que les tâches en arrière-plan peuvent utiliser pour garantir leur traitement dans l’ordre adéquat. Si vous utilisez Microsoft Azure Service Bus, vous pouvez utiliser des sessions de messagerie afin de garantir un ordre de livraison adéquat. Toutefois, la conception d’un processus ne tenant pas compte de l’ordre des messages peut s’avérer plus efficace, lorsque c’est possible.
  - En règle générale, une tâche en arrière-plan obtient un aperçu des messages de la file d’attente, ce qui les masque aux autres consommateurs de messages pendant un temps, puis supprime les messages une fois leur traitement effectué avec succès. Si une tâche en arrière-plan échoue lors du traitement d’un message, ce dernier réapparaît dans la file d’attente après expiration du délai d’attente de l’aperçu. Il sera traité par une autre instance de la tâche ou lors du prochain cycle de traitement de cette instance. Si ce message provoque une erreur répétée dans le consommateur, il bloque la tâche, la file d’attente, voire l’application elle-même lorsque la file d’attente est saturée. Pour cette raison, il est essentiel de détecter et de supprimer les messages incohérents de la file d’attente. Si vous utilisez Microsoft Azure Service Bus, vous pouvez déplacer les messages entraînant une erreur (manuellement ou automatiquement) vers une file d’attente de lettres mortes associée.
  - Les files d’attente bénéficient nécessairement d’_au moins un_ mécanisme de livraison, mais ils peuvent délivrer le même message plusieurs fois. En outre, si une tâche en arrière-plan échoue après avoir traité un message, mais avant de l’avoir supprimé de la file d’attente, ce message pourra à nouveau être traité. Les tâches en arrière-plan doivent être idempotentes : cela signifie que le traitement d’un même message plusieurs fois n’entraîne aucune erreur ni aucune incohérence dans les données de l’application. Par nature, certaines opérations sont idempotentes (comme la définition d’une valeur stockée sur une nouvelle valeur spécifique). Toutefois, si, par exemple, vous ajoutez une valeur à une valeur stockée existante sans vérifier que cette dernière n’a pas été modifiée depuis le moment où le message a été envoyé pour la première fois, des incohérences apparaîtront. Les files d’attente Microsoft Azure Service Bus peuvent être configurées pour supprimer automatiquement les messages en double.
  - Certains systèmes de messagerie, comme les files d’attente de stockage Microsoft Azure et les files Azure Service Bus, prennent en charge une propriété « de-queue count », qui indique le nombre de lectures d’un message depuis la file d’attente. Cette propriété peut être utile lors du traitement de messages incohérents et répétés. Pour en savoir plus, voir [Primer de messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx) et [Modèles d’idempotence](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Remarques relatives à la mise à l’échelle et aux performances

Les tâches en arrière-plan doivent offrir des performances suffisantes pour éviter de bloquer l’application ou entraîner des incohérences, suite à une opération en retard lorsque le système est soumis à une charge. En général, vous améliorez les performances lorsque vous mettez à l’échelle les instances de calcul qui hébergent les tâches en arrière-plan. Lors de la planification et de la conception de tâches en arrière-plan, tenez compte des points suivants en matière de performances et d’évolutivité :

- Azure prend en charge la mise à l’échelle automatique (montée en charge et descente en puissance) en fonction de la demande et de la charge actuelles, ou selon une planification définie au préalable, aussi bien pour Web Apps, les rôles de travail et web Cloud Services et les déploiements hébergés par les machines virtuelles. Cette fonctionnalité permet de garantir que l’application dans son ensemble dispose de fonctionnalités suffisamment performantes, tout en réduisant les coûts d’exécution.
- Si les tâches en arrière-plan présentent des performances différentes de celles d’autres éléments de l’application Cloud Services (par exemple, l’interface utilisateur ou des composants comme la couche d’accès aux données), l’hébergement des tâches en arrière-plan dans un rôle de travail distinct permet aux rôles des tâches en arrière-plan et de l’interface utilisateur d’être mis à l’échelle de manière indépendante, afin de gérer la charge. Si plusieurs tâches en arrière-plan présentent des performances entièrement différentes les unes des autres, vous pouvez envisager de les répartir dans des rôles de travail distincts et de mettre chaque type de rôle à l’échelle de manière indépendante. Notez toutefois que cette opération peut s’avérer plus coûteuse que la combinaison de l’ensemble des tâches dans un nombre moins important de rôles.
- Une simple mise à l’échelle des rôles peut s’avérer insuffisante pour empêcher une baisse des performances en cas de charge élevée. Peut-être devrez-vous également mettre les files d’attente de stockage et d’autres ressources à l’échelle, afin de vous assurer qu’un point unique de la chaîne de traitement dans son ensemble ne deviendra pas un goulot d’étranglement. Tenez également compte d’autres limitations comme le débit maximal du stockage et d’autres services sur lesquels reposent l’application et les tâches en arrière-plan.
- La conception des tâches en arrière-plan doit prévoir une mise à l’échelle. Ainsi, elles doivent être en mesure de détecter, de manière dynamique, le nombre de files d’attente de stockage utilisées, afin de pouvoir écouter ou envoyer les messages à la file d’attente appropriée.
- Par défaut, les tâches web sont mises à l’échelle en fonction de l’instance d’application web Azure associée. Cependant, si vous souhaitez qu’une tâche web s’exécute uniquement en tant qu’instance unique, vous pouvez créer un fichier Settings.job contenant les données JSON : **{ "is\_singleton": true }**. Cela oblige Azure à exécuter une seule instance de la tâche web, même s’il existe plusieurs instances de l’application web associée. Cela peut être utile pour les tâches planifiées qui doivent s’exécuter en tant qu’instances uniques.

## Modèles associés

- [Primer de messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx)
- [Recommandations en matière de mise à l’échelle automatique](http://msdn.microsoft.com/library/dn589774.aspx)
- [Modèle de transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx)
- [Modèle des consommateurs récurrents](http://msdn.microsoft.com/library/dn568101.aspx)
- [Recommandations en matière de partitionnement du calcul](http://msdn.microsoft.com/library/dn589773.aspx)
- [Modèle de consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx)
- [Modèle d’opérateur de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx)
- [Modèle de choix de l’instance responsable](http://msdn.microsoft.com/library/dn568104.aspx)
- [Modèle des canaux et filtres](http://msdn.microsoft.com/library/dn568100.aspx)
- [Modèle de file d’attente prioritaire](http://msdn.microsoft.com/library/dn589794.aspx)
- [Modèle de nivellement de la charge basé sur une file d’attente](http://msdn.microsoft.com/library/dn589783.aspx)
- [Modèle de superviseur de l’agent du planificateur](http://msdn.microsoft.com/library/dn589780.aspx)

## Plus d’informations

- [Mise à l’échelle d’applications Azure avec des rôles de travail](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Exécution de tâches en arrière-plan](http://msdn.microsoft.com/library/ff803365.aspx)
- [Azure Role Startup Life Cycle](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (billet de blog)
- [Azure Cloud Services Role Lifecycle](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vidéo)
- [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)
- [Files d’attente Azure et files d’attente Service Bus - comparaison et différences](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Procédure : activer les diagnostics dans un service cloud](cloud-services-dotnet-diagnostics.md)

<!---HONumber=AcomDC_0323_2016-->