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


>[AZURE.NOTE] Comme Log Analytics inclut une fonctionnalité Recherche de journal, vous n’avez pas besoin d’installer une solution pour l’activer. Toutefois, vous pouvez obtenir des visualisations de données, des recherches suggérées et des informations en ajoutant des solutions à partir de la galerie de solutions.

Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service OMS. Le traitement par le service OMS prend généralement quelques minutes à une heure. Une fois les données traitées par le service, vous pouvez les consulter dans OMS.

Vous pouvez facilement supprimer une solution quand vous n'en avez plus besoin. Quand vous supprimez une solution, ses données ne sont pas envoyées au service OMS, ce qui peut réduire la quantité de données utilisées par rapport à votre quota quotidien (si vous en avez un).


## Solutions prises en charge par Microsoft Monitoring Agent

Pour le moment, les serveurs qui sont connectés directement à OMS à l'aide de Microsoft Monitoring Agent peuvent utiliser la plupart des solutions disponibles, notamment :

- Évaluation d'Active Directory
- Gestion des alertes (sans alertes SCOM)
- Logiciel anti-programme malveillant
- Suivi des modifications
- Sécurité
- Évaluation de SQL
- Mises à jour du système

Les solutions suivantes ne sont toutefois *pas* prises en charge par Microsoft Monitoring Agent et requièrent l’agent System Center Operations Manager (SCOM).

- Gestion des alertes (avec alertes SCOM)
- Gestion de la capacité
- Évaluation de la configuration

Reportez-vous à la rubrique [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md) pour plus d’informations sur la connexion de l’agent SCOM à Log Analytics.

### Pour ajouter une solution à l’aide de la galerie de solutions

1. Dans la page de présentation d’OMS, cliquez sur la vignette **Galerie des solutions**. ![galerie de solutions](./media/log-analytics-add-solutions/sol-gallery.png)
2. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'OMS. Cliquez sur le nom de la solution que vous souhaitez ajouter à OMS.
3. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Add**.
4. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d'aperçu dans OMS. Vous pouvez commencer à l'utiliser une fois que le service OMS a traité vos données.

## Pour configurer des solutions
1. Vous devrez configurer certaines solutions. Par exemple, vous devrez configurer Automation, Azure Site Recovery et Backup avant de pouvoir les utiliser.
2. Pour chacune de ces solutions, cliquez sur sa vignette dans la page Vue d’ensemble. ![configurer la solution](./media/log-analytics-add-solutions/configure-additional.png)
3. Configurez la solution avec les informations nécessaires, puis cliquez sur **Enregistrer**. ![configurer la solution](./media/log-analytics-add-solutions/configure.png)

### Pour supprimer une solution à l’aide de la galerie de solutions

1. Dans la page de présentation d’OMS, cliquez sur la vignette **Paramètres**.
2. Dans la page Paramètres, sous l’onglet Solutions, cliquez sur **Supprimer** pour la solution que vous souhaitez supprimer.
3. Dans la boîte de dialogue de confirmation, cliquez sur **Oui** pour supprimer la solution.

## Détails sur la collecte des données pour les fonctionnalités et les solutions OMS

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte pour les fonctionnalités et les solutions OMS.

|type de données| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
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
|Données de communication|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Non](./media/log-analytics-add-solutions/oms-bullet-red.png)| chaque minute|

## Solutions et fonctionnalités Log Analytics en version préliminaire

En exécutant un service et en suivant des pratiques de développement, nous pouvons collaborer avec des clients pour développer des solutions et des fonctionnalités.

Pendant la phase de la version préliminaire privée, nous accordons à un petit groupe de clients un accès à une implémentation anticipée de la fonctionnalité ou de la solution pour obtenir des commentaires et apporter des améliorations. Cette implémentation anticipée a des fonctionnalités et des capacités opérationnelles minimales.

Notre objectif est d’effectuer des expérimentations rapides pour trouver ce qui fonctionne et ce qui ne fonctionne pas. Nous ne quittons ce processus que quand, au vu des commentaires des clients utilisant la version préliminaire privée, nous savons que nous pouvons passer à une version préliminaire publique.

Pendant la phase de la version préliminaire publique, nous mettons la fonctionnalité ou la solution à la disposition de tous les utilisateurs pour obtenir d’autres commentaires et valider nos mise à l’échelle et efficacité. Pendant cette phase :

- Les fonctionnalités en version préliminaire apparaissent sous l’onglet Paramètres et peuvent être activées par tout utilisateur.
- Les solutions en version préliminaire peuvent être ajoutées par le biais de la galerie ou à l’aide d’un script publié

### Que dois-je savoir sur les fonctionnalités et solutions en version préliminaire ?

Nous sommes motivés par les nouvelles fonctionnalités et solutions, et à l’idée de travailler avec vous pour les développer.

Toutefois, les fonctionnalités et solutions en version préliminaire ne s’adressent pas à tout le monde ; ainsi, avant de demander à participer à une version préliminaire privée ou d’activer une version préliminaire publique, soyez certain d’être prêt à utiliser quelque chose qui est en cours de développement.

Quand vous activez une fonctionnalité en version préliminaire par le biais du portail, un avertissement apparaît, vous rappelant que la fonctionnalité est disponible en version préliminaire.

#### Pour les versions préliminaires *privées* et *publiques*

Les points suivants s’appliquent aux versions préliminaires publiques et privées :

- Les choses ne marchent pas toujours correctement. 
  - Les problèmes vont d’une gêne mineure à quelque chose qui ne fonctionne pas du tout.
- La version préliminaire peut avoir un impact négatif sur vos systèmes et environnement.
  - Malgré tous nos efforts, des événements inattendus peuvent parfois affecter les systèmes que vous utilisez avec OMS.
- Une perte ou altération de données peut se produire.
- Nous pouvons vous demander de collecter des journaux de diagnostic ou d’autres données pour nous aider à résoudre les problèmes.
- La fonctionnalité ou solution peut être supprimée (temporairement ou définitivement).
  - Les connaissances acquises pendant la phase de la version préliminaire peuvent nous amener à ne pas publier la fonctionnalité ou solution.
- Les versions préliminaires peuvent ne pas fonctionner ou ne pas avoir été testées avec toutes les configurations, et nous pouvons limiter les éléments suivants :
  - Les systèmes d’exploitation utilisables (par exemple, une fonctionnalité peut s’appliquer uniquement à Linux en version préliminaire)
  - Le type d’agent (MMA, SCOM) utilisable (par exemple, une fonctionnalité peut ne pas fonctionner avec SCOM en version préliminaire)  
- Les solutions et fonctionnalités en version préliminaire ne sont pas couvertes par le Contrat de niveau de service.
- L’utilisation des fonctionnalités en version préliminaire occasionne des frais d’utilisation.
- Des fonctionnalités ou fonctions nécessaires pour la fonctionnalité ou solution peuvent faire défaut ou être incomplètes.
- Les fonctionnalités et solutions peuvent ne pas être disponibles dans toutes les régions.
- Les fonctionnalités et solutions peuvent ne pas être localisées.
- Les fonctionnalités et solutions peuvent n’être utilisables que par un nombre limité de clients ou d’appareils.
- Vous pouvez être amené à utiliser des scripts pour effectuer une configuration et pour activer la solution ou fonctionnalité.
- L’interface utilisateur (IU) est inachevée et peut changer d’un jour à l’autre.
- Les versions préliminaires publiques peuvent ne pas être appropriées pour vos systèmes de production ou critiques.

#### Pour la version préliminaire *privée*

Outre les points ci-dessus, ce qui suit est propre aux versions préliminaires privées :

- Vous êtes supposé nous faire part de vos commentaires sur votre expérience pour que nous puissions améliorer la fonctionnalité ou solution.
- Nous pouvons vous contacter pour recueillir vos commentaires au moyen d’enquêtes, par téléphone ou par e-mail.
- Les choses ne marchent pas toujours correctement.
- Nous pouvons imposer un accord de confidentialité pour la participation ou inclure du contenu confidentiel.
  - Avant de vous exprimer dans un billet de blog ou un tweet, ou de communiquer par un autre moyen avec des tiers, contactez le directeur de programmes responsable de la version préliminaire pour connaître les restrictions qui s’appliquent à la divulgation d’informations.
- N’exécutez pas la fonctionnalité ou solution sur des systèmes de production ou critiques.


### Comment puis-je accéder aux fonctionnalités et solutions privées en version préliminaire ?

La nature de la version préliminaire privée détermine la forme de l’invitation.

- En répondant à l’enquête client mensuelle et en nous autorisant à vous suivre, vous augmentez vos chances d’être invité à une version préliminaire privée.
- Votre équipe des comptes Microsoft peut vous désigner.
- Vous pouvez vous inscrire en fonction des détails publiés sur twitter ([msopsmgmt](https://twitter.com/msopsmgmt)). 
- Vous pouvez vous inscrire en fonction des détails partagés à l’occasion d’événements communautaires : recherchez-nous dans les rencontres, conférences et communautés en ligne. 


## Étapes suivantes

- [Recherchez dans les journaux](log-analytics-log-searches.md) les informations détaillées collectées par les solutions.

<!---HONumber=AcomDC_0518_2016-->