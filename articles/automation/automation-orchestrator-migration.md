<properties
   pageTitle="Migration des Runbooks et des packs d'intégration à partir d'Orchestrator"
   description="Décrit comment migrer des Runbooks et des packs d'intégration de System Center Orchestrator vers Azure Automation."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/22/2015"
   ms.author="bwren" />


# Migration d'Orchestrator vers Azure Automation

Dans [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx), les Runbooks sont basés sur les activités de packs d'intégration qui sont spécifiquement écrits pour Orchestrator, tandis que dans Azure Automation, ils sont basés sur les workflows Windows PowerShell. Les Runbooks graphiques dans Azure Automation ont une apparence semblable aux Runbooks Orchestrator, avec leurs activités représentant les applets de commande PowerShell, les Runbooks enfants et les ressources.

Le [Kit de migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclut des outils destinés à vous aider à convertir des Runbooks Orchestrator en Runbooks Azure Automation. En plus de convertir les Runbooks eux-mêmes, vous devez convertir les packs d'intégration avec les activités qu'ils utilisent en modules d'intégration à l'aide d'applets de commande Windows PowerShell.

Voici le processus de base pour la conversion de Runbooks Orchestrator en Runbooks Azure Automation. Chacune de ces étapes est décrite en détail dans les sections ci-dessous.

1.  Téléchargez le [Kit de migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) qui contient les outils et modules décrits dans cet article.
2.  Installez le [module d'activités Standard](#standard-activities-module) dans Azure Automation. Cela inclut les versions converties des activités Orchestrator standard qui peuvent être utilisées par les Runbooks convertis.
2.  Installez les [modules d'intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans Azure Automation pour les packs d'intégration utilisés par vos Runbooks.
3.  Convertissez les packs d'intégration personnalisés et tiers à l'aide du [convertisseur de packs d'intégration](#integration-pack-converter), puis installez-les dans Azure Automation.
4.  Recréez manuellement les ressources globales d'Orchestrator dans Azure Automation, car il n'existe aucune méthode automatisée pour effectuer cette migration.
5.  Convertissez les Runbooks Orchestrator à l'aide du [convertisseur de Runbooks](#runbook-converter-coming-soon) (bientôt disponible), puis installez-les dans Azure Automation.
6.  Configurez un [Runbook Worker hybride](#hybrid-runbook-worker) dans votre centre de données local pour exécuter les Runbooks convertis.

## Service Management Automation

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) stocke et exécute des Runbooks dans votre centre de données local comme Orchestrator, et utilise les mêmes modules d'intégration qu'Azure Automation. Lorsqu'il est disponible, le [convertisseur de Runbooks](#runbook-converter-coming-soon) convertit les Runbooks Orchestrator en Runbooks graphiques, lesquels ne sont néanmoins pas pris en charge dans SMA. Vous pouvez toujours installer le [module d'activités Standard](#standard-activities-module) et les [modules d'intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans SMA, mais vous devez [ réécrire vos Runbooks](http://technet.microsoft.com/library/dn469262.aspx) manuellement.

## Runbook Worker hybride

Dans Orchestrator, les Runbooks sont stockés sur un serveur de base de données et s'exécutent sur des serveurs de Runbooks, les deux se trouvant dans votre centre de données local. Dans Azure Automation, les Runbooks sont stockés dans le cloud Azure et peuvent s'exécuter dans votre centre de données locales à l'aide d'un [Runbook Worker hybride](automation-hybrid-runbook-worker.md). C'est ainsi que vous exécuterez généralement les Runbooks convertis à partir d'Orchestrator, car ils sont conçus pour s'exécuter sur des serveurs locaux.

## Convertisseur de packs d'intégration

Le convertisseur de packs d'intégration convertit les packs d'intégration créés à l'aide d'Orchestrator Integration Toolkit (OIT) en modules d'intégration basés sur Windows PowerShell et qui peuvent être importés dans Azure Automation ou dans Service Management Automation.

Lorsque vous exécutez le convertisseur de packs d'intégration, un Assistant vous permettant de sélectionner un fichier de pack d'intégration (.oip) s'affiche. Cet Assistant répertorie ensuite les activités incluses dans ce pack d'intégration et vous permet de sélectionner celles qui seront migrées. Une fois l'Assistant terminé, il crée un module qui inclut une applet de commande correspondante pour chacune des activités du pack d'intégration d'origine.


### Paramètres

Toutes les propriétés d'une activité du pack d'intégration sont converties en paramètres de l'applet de commande correspondante dans le module d'intégration. Les applets de commande Windows PowerShell possèdent un ensemble de [paramètres communs](http://technet.microsoft.com/library/hh847884.aspx) qui peuvent être utilisés avec toutes les applets de commande. Par exemple, le paramètre -Verbose entraîne l'affichage, par une applet de commande, des informations détaillées relatives à son fonctionnement. Aucune applet de commande ne peut avoir un paramètre portant le même nom qu'un paramètre commun. Si une activité a une propriété portant le même nom qu'un paramètre commun, l'Assistant vous invite à fournir un autre nom pour le paramètre.

### Activités d'analyse

Dans Orchestrator, les Runbooks d'analyse commencent par une [activité d'analyse](http://technet.microsoft.com/library/hh403827.aspx) et s'exécutent en permanence, attendant d'être appelés par un événement particulier. Azure Automation ne prend pas en charge les Runbooks d'analyse. Les activités d'analyse se trouvant dans le pack d'intégration ne seront donc pas converties. Au lieu de cela, une applet de commande réservée est créée dans le module d'intégration pour l'activité d'analyse. Cette applet de commande n'a aucune fonctionnalité, mais elle permet l'installation de tout Runbook converti qui l'utilise. Ce Runbook ne pourra pas s'exécuter dans Azure Automation, mais il peut être installé afin que l'utilisateur puisse le modifier.

### Packs d'intégration ne pouvant pas être convertis

Les packs d'intégration qui n'ont pas été créés avec OIT, notamment ceux créés par Microsoft, ne peuvent pas être convertis avec cet outil. Les packs d'intégration fournis par Microsoft ont été convertis en modules d'intégration afin qu'ils puissent être installés dans Azure Automation ou dans Service Management Automation.


## Module d'activités standard

Orchestrator inclut un ensemble d'[activités standard](http://technet.microsoft.com/library/hh403832.aspx) qui ne sont pas incluses dans un pack d'intégration, mais qui sont utilisées par de nombreux Runbooks. Le module d'activités standard est un module d'intégration qui inclut une applet de commande équivalente pour chacune de ces activités. Vous devez installer le module d'intégration dans Azure Automation avant d'importer des Runbooks convertis qui utilisent une activité standard.

En plus de prendre en charge les Runbooks convertis, les applets de commande du module d'activités standard peuvent être utilisées par une personne ayant une bonne connaissance d'Orchestrator pour créer de nouveaux Runbooks dans Azure Automation. Alors que les fonctionnalités de toutes les activités standard peuvent être effectuées à l'aide d'applets de commande, il est possible qu'elles fonctionnent différemment. Les applets de commande du module d'activités standard converties fonctionnent de la même manière que leurs activités correspondantes et utilisent les mêmes paramètres. Cela peut aider l'auteur des Runbooks Orchestrator existants dans sa transition vers les Runbooks Azure Automation.

## Modules d'intégration de System Center Orchestrator
Microsoft fournit des [packs d'intégration](http://technet.microsoft.com/library/hh295851.aspx) destinés à la création des Runbooks afin d'automatiser les composants de System Center et d'autres produits. Actuellement, lorsque vous téléchargez certains de ces packs d'intégration à partir de [TechNet](http://www.microsoft.com/download/details.aspx?id=39622), ils ne peuvent pas être convertis à l'aide du convertisseur de packs d'intégration en raison d'un problème connu qui sera résolu avec la version RC du Kit de migration de System Center Orchestrator. Les [modules d'intégration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all) incluent des versions converties de ces packs d'intégration qui peuvent être importées dans Azure Automation et Service Management Automation avant cette version.

## Convertisseur de Runbooks (bientôt disponible)

Cet outil convertira les Runbooks Orchestrator en Runbooks graphiques qui peuvent être importés dans Azure Automation. Vous trouverez plus de détails sur cet outil ici quand il sera disponible.

## Articles connexes

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Runbook Worker hybride](automation-hybrid-runbook-worker.md)
- [Activités standard d'Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=July15_HO3-->