<properties
	pageTitle="Ajouter des solutions Log Analytics à partir de la galerie de solutions | Microsoft Azure"
	description="Les solutions Log Analytics représentent une collection de règles logiques, de visualisation et d'acquisition des données qui fournissent des mesures cernant un domaine problématique en particulier."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Ajouter des solutions Log Analytics à partir de la galerie de solutions

Les solutions Log Analytics représentent une collection de règles **logiques**, de **visualisation** et **d'acquisition des données** qui fournissent des mesures cernant un domaine problématique en particulier. Cet article répertorie les solutions prises en charge par Log Analytics et vous indique comment les ajouter et les supprimer à l'aide de la galerie de solutions.

Les solutions vous offrent des informations plus approfondies pour :
- étudier et résoudre plus rapidement les problèmes opérationnels
- collecter et corréler les différents types de données machine
- vous aider à être proactif avec des activités comme la planification de capacité, le rapport d’état des correctifs et l’audit de sécurité.


>[AZURE.NOTE] Comme OMS inclut une fonctionnalité Recherche de journal de base, vous n'avez pas besoin d'installer une solution pour l'activer. Toutefois, vous pouvez obtenir des fonctionnalités supplémentaires en y ajoutant des solutions à partir de la page Galerie de solutions.

Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service OMS. Le traitement des données par le service OMS peut prendre de quelques minutes à plusieurs heures. Une fois les données traitées par le service, vous pouvez les consulter dans OMS.

Vous pouvez facilement supprimer une solution quand vous n'en avez plus besoin. Quand vous supprimez une solution, ses données ne sont pas envoyées au service OMS, ce qui peut réduire la quantité de données utilisées par rapport à votre quota quotidien (si vous en avez un).


## Solutions prises en charge par Microsoft Monitoring Agent

Pour le moment, les serveurs qui sont connectés directement à OMS à l'aide de Microsoft Monitoring Agent peuvent utiliser la plupart des solutions disponibles, notamment :

- Mises à jour du système
- Logiciel anti-programme malveillant
- Suivi des modifications
- Évaluation de SQL
- Évaluation d'Active Directory
- Gestion des alertes (sans alertes SCOM)

Les solutions suivantes ne sont toutefois *pas* prises en charge par Microsoft Monitoring Agent et requièrent l’agent System Center Operations Manager (SCOM).

- Gestion de la capacité
- Gestion des alertes (avec alertes SCOM)
- Évaluation de la configuration

Reportez-vous à la rubrique [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md) pour plus d'informations sur la connexion de l'agent SCOM à Log Analytics.

### Pour ajouter une solution à l’aide de la galerie de solutions

1. Dans la page d'aperçu d'OMS, cliquez sur la vignette **Galerie des solutions**. ![galerie de solutions](./media/log-analytics-add-solutions/sol-gallery.png)
2. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'OMS. Cliquez sur le nom de la solution que vous souhaitez ajouter à OMS.
3. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Add**.
4. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d'aperçu dans OMS. Vous pouvez commencer à l'utiliser une fois que le service OMS a traité vos données.

## Pour configurer des solutions
1. Vous devrez configurer certaines solutions. Par exemple, vous devrez configurer Automation, Azure Site Recovery et Backup avant de pouvoir les utiliser.
2. Pour chacune de ces solutions, cliquez sur sa vignette sur la page Vue d’ensemble. ![configurer la solution](./media/log-analytics-add-solutions/configure-additional.png)
3. Configurez la solution avec les informations nécessaires, puis cliquez sur **Enregistrer**. ![configurer la solution](./media/log-analytics-add-solutions/configure.png)

### Pour supprimer une solution à l’aide de la galerie de solutions

1. Dans la page Vue d’ensemble d'OMS, cliquez sur la vignette **Paramètres**.
2. Dans la page Paramètres, sous l’onglet Solutions, cliquez sur **Supprimer** pour la solution que vous souhaitez supprimer.
3. Dans la boîte de dialogue de confirmation, cliquez sur **Oui** pour supprimer la solution.

## Détails sur la collecte des données pour les fonctionnalités et les solutions OMS

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte pour les fonctionnalités et les solutions OMS.

|type de données| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l'agent SCOM envoyées via un groupe d'administration | fréquence de collecte |
|---|---|---|---|---|---|---|---|
|Évaluation d'AD|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 jours|
|État de la réplication AD|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 jours|
|Alertes (Nagios)|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|à l'arrivée|
|Alertes (Zabbix)|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minute|
|Alertes (Operations Manager)|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutes|
|Logiciel anti-programme malveillant|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| Toutes les heures|
|Gestion de la capacité|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| Toutes les heures|
|Suivi des modifications|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| Toutes les heures|
|Suivi des modifications|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|Toutes les heures|
|Évaluation de la configuration (Advisor hérité)|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| deux fois par jour|
|ETW|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Journaux IIS|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Groupes de sécurité réseau|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutes|
|Office 365|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|sur notification|
|Compteurs de performance|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|comme prévu, minimum de 10 secondes|
|Compteurs de performance|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|comme prévu, minimum de 10 secondes|
|Service Fabric|Windows|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Évaluation de SQL|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 jours|
|SurfaceHub|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|à l'arrivée|
|Syslog|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|depuis Azure Storage : 10 minutes ; à partir de l’agent : à l’arrivée|
|Mises à jour du système|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| au moins 2 fois par jour et 15 minutes après l'installation d'une mise à jour|
|Journaux des événements de sécurité Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)| pour Azure Storage : 10 minutes ; pour l'agent : à l'arrivée|
|Journaux du pare-feu Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)| à l'arrivée|
|Journaux d’événements Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| pour Azure Storage : 1 minutes ; pour l'agent : à l'arrivée|
|Données de câble|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)| chaque minute|


## Étapes suivantes

- [Recherchez dans les journaux](log-analytics-log-searches.md) les informations détaillées collectées par les solutions.

<!---HONumber=AcomDC_0504_2016-->