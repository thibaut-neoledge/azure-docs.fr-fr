<properties 
   pageTitle="Considérations relatives à Operations Manager avec Operational Insights"
   description="Si vous utilisez Microsoft Azure Operational Insights avec Operations Manager, votre configuration repose sur une distribution de groupes d'administration et d'agents d'Operations Manager pour collecter et envoyer des données vers le service Operational Insights pour l'analyse"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Considérations relatives à Operations Manager avec Operational Insights

Si vous utilisez Microsoft Azure Operational Insights avec Operations Manager, votre configuration repose sur une distribution de groupes d'administration et d'agents d'Operations Manager pour collecter et envoyer des données vers le service Operational Insights pour l'analyse. Toutefois, si vous utilisez des agents qui se connectent directement au service Web, Operations Manager est inutile. Prenez en compte les éléments suivants lorsque vous utilisez Operational Insights avec Operations Manager.

## Configuration requise et fonctions logicielles d'Operational Insights

Operational Insights se compose d'un service Web dans le cloud et de deux agents qui se connectent directement au service Web ou aux agents et groupes d'administration Operations Manager qui gèrent les ordinateurs de votre environnement.

Avant de sélectionner les groupes d'administration (pour gérer les agents et envoyer des données vers le service Operational Insights) et les agents d'Operations Manager (pour collecter des données), assurez-vous de bien comprendre les éléments suivants :

- L'agent Operations Manager est installé sur tout serveur à partir duquel vous souhaitez collecter et analyser des données.

- Le groupe d'administration Operations Manager transfère les données des agents au service Web Operational Insights. Il n'analyse pas les données.

- Le groupe d'administration Operations Manager doit avoir accès à Internet pour télécharger les données vers le service Web.

- Pour de meilleurs résultats, n'installez pas le serveur d'administration Operations Manager sur un ordinateur qui est également un contrôleur de domaine.

- Un agent Operations Manager doit avoir une connexion réseau au groupe d'administration Operations Manager afin de transférer les données.

Operational Insights peut utiliser le service de contrôle d'intégrité de System Center pour collecter et analyser les données. Operations Manager dépend du service de contrôle d'intégrité de System Center. Lorsque vous affichez les programmes installés sur votre serveur, vous verrez le logiciel d'agent de System Center Operations Manager, notamment dans Ajout/Suppression de programmes. Ne supprimez pas ce dernier, car Operational Insights en dépend. Si vous supprimez le logiciel de l'agent Operations Manager, Operational Insights ne fonctionnera plus.

## Coexistence avec Operations Manager

Lorsque vous utilisez Operations Manager, Operational Insights est uniquement pris en charge avec l'agent Operations Manager dans System Center Operations Manager 2012 R2 ou System Center Operations Manager 2012 SP1. Il n'est pas pris en charge avec les versions précédentes de System Center Operations Manager. Étant donné que l'agent Operations Manager est utilisé pour collecter des données, il utilise des informations d'identification spécifiques (comptes d'action ou comptes d'identification) pour prendre en charge certaines charges de travail analysées, comme SharePoint 2012.

## Operational Insights et SQL Server 2012

Lorsque vous utilisez Operations Manager, le service de contrôle d'intégrité de System Center s'exécute sous le compte système Local. Dans les versions de SQL Server antérieures à SQL Server 2008 R2, le compte Système local était activé par défaut et était membre du rôle serveur d'administrateur système. Dans SQL Server 2012, la connexion de Système local ne fait pas partie du rôle serveur d'administrateur système. Par conséquent, lorsque vous utilisez Operational Insights, il ne peut pas analyser complètement l'instance de SQL Server 2012 et toutes les règles ne peuvent pas générer des alertes.

## Connectivité du réseau interne et Internet

Lorsque vous utilisez des agents qui se connectent directement auprès du service Web, les agents doivent accéder à Internet pour envoyer des données au service Web et recevoir des informations de configuration mises à jour à partir du service Web.

Lorsque vous utilisez Operations Manager, le serveur d'administration doit accéder à Internet pour envoyer des données au service Web et recevoir les informations de configuration mises à jour à partir du service Web. Toutefois, vos agents n'ont pas besoin d'accéder à Internet. Si vous avez des agents Operations Manager sur des serveurs qui ne sont pas connectés à Internet, vous pouvez utiliser le service Web, s'ils peuvent communiquer avec un serveur d'administration connecté à Internet.

## Gestion des clusters

L'agent Operations Manager est pris en charge sur les ordinateurs exécutant Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008 configurés comme faisant partie d'un cluster de basculement Windows. Vous pouvez afficher les clusters dans le portail Operational Insights. Dans la page Serveurs, les clusters sont identifiés en tant que TYPE=CLUSTER (et non TYPE=AGENT, qui est l'identification des ordinateurs physiques).

Les règles de découverte et de configuration s'exécutent sur les nœuds actifs et passifs du cluster, mais toutes les alertes générées sur les nœuds passifs sont ignorées. Si un nœud passe de passif à actif, les alertes de ce nœud sont affichées automatiquement, sans intervention de votre part.

Certaines alertes peuvent être générées à deux reprises, en fonction de la règle générant l'alerte. Par exemple, une règle qui détecte un pilote défectueux en examinant le système d'exploitation génère des alertes pour le serveur physique et pour le cluster.

L'analyse de la configuration des nœuds passifs n'est pas prise en charge.

Operational Insights ne gère pas le regroupement ou la liaison des ordinateurs exécutant Windows Server qui font partie du même cluster de basculement.

## Mise à l'échelle de votre environnement Operational Insights

Lorsque vous planifiez votre déploiement Operational Insights (en particulier lorsque vous analysez le nombre d'agents d'Operations Manager qui doivent transférer des données via un seul groupe d'administration), prenez en compte la capacité de ce serveur en termes d'espace de fichier.

Examinez les variables suivantes :

- Nombre d'agents par groupe d'administration

- La taille moyenne des données transférées de l'agent au groupe d'administration par jour. Par défaut, chaque agent télécharge des fichiers CAB sur le groupe d'administration deux fois par jour. La taille des fichiers CAB dépend de la configuration du serveur (par exemple le nombre de moteurs SQL Server et de bases de données) et de l'intégrité du serveur (comme le nombre d'alertes générées). Dans la plupart des cas, la taille de téléchargement quotidien est généralement inférieure à 100 Ko.

- La période d'archivage pour conserver les données dans le groupe d'administration (la valeur par défaut est de 5 jours)

Par exemple, si vous supposez une taille de téléchargement quotidienne de 100 Ko par agent et la période d'archivage par défaut, vous aurez besoin du stockage suivant pour le groupe d'administration :

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Nombre d'agents</b></td>
		<td><b>Espace requis estimé pour le groupe d'administration</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2.5 Mo (5 agents x 100 Ko de données/jour x 5 jours = 2500 Ko)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 Mo (50 agents x 100 Ko de données/jour x 5 jours = 25 000 Ko)</td>
    </tr>

    </tbody>
    </table>

## Emplacements géographiques

Si vous souhaitez analyser des données à partir de serveurs dans divers emplacements géographiques, envisagez de disposer d'un groupe d'administration par emplacement. Cela peut améliorer les performances de transfert de données de l'agent au groupe d'administration.


<!--HONumber=52-->