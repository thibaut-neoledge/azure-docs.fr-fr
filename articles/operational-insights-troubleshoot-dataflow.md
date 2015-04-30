<properties 
	pageTitle="Operational Insights - Dépannage des agents ou du flux de données d'Operations Manager" 
	description="Découvrez comment dépanner les problèmes liés aux agents connectés directement et au flux de données d'Operations Manager vers Azure Operational Insights" 
	services="operational-insights" 
	documentationCenter="" 
	authors="dani3l3" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/23/2015" 
	ms.author="dmuscett"/>


#Résoudre les problèmes des agents ou du flux de données d'Operations Manager vers Operational Insights
L'ensemble de procédures suivantes est conçu comme un guide pour vous aider à dépanner vos agents connectés directement ou les déploiements d'Operations Manager configurés pour envoyer des données à Azure Operational Insights.

##Procédure 1 : Vérification du téléchargement des packs d'administration appropriés sur votre environnement Operations Manager
*Remarque : Si vous utilisez uniquement un agent direct, vous pouvez passer à la procédure suivante.*

Selon les packs d'analyse décisionnelle que vous avez activés à partir du portail OpInsights vous verrez plus ou moins de packs d'administration. Recherchez le mot clé " Advisor " ou " Intelligence " dans leur nom. 
Vous pouvez vérifier ces packs d'administration à l'aide de OpsMgr PowerShell :

      Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
      Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
    
Remarque : Si vous dépannez le pack d'analyse décisionnelle de capacité, vérifiez le nombre de packs d'administration contenant le nom 'capacity' dont vous disposez : il existe deux packs d'administration avec le même nom d'affichage (mais dont l'ID interne est différent) dans le même ensemble de packs d'administration. Si l'un des deux n'est pas importé (souvent en raison de l'absence d'une dépendance VMM), l'autre pack d'administration n'est pas importé et l'opération n'est pas relancée.

Vous devez voir les trois packs d'administration suivants liés à 'capacité'
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Storage Data 

Si vous voyez uniquement un ou deux d'entre eux, mais pas les trois, supprimez-les et attendez 5 à 10 minutes pour qu'Operations Manager les télécharge et les importe à nouveau : vérifiez les journaux des événements pour les erreurs pendant cette période.

##Procédure 2 : Vérification du téléchargement des packs d'analyse décisionnelle appropriés sur votre agent direct
*Remarque : Si vous utilisez uniquement Operations Manager, vous pouvez ignorer cette procédure.*

Dans l'agent direct, vous devez voir la procédure de collecte des données des packs d'analyse décisionnelle, mise en cache sous **C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs**


##Procédure 3 : Vérification de l'envoi des données au service Advisor (ou de tentatives d'envoi)
Vous pouvez effectuer la procédure suivante sur l'ordinateur agent direct ou sur le serveur d'administration Operations Manager, selon que vous utilisez des agents connectés directement ou Operations Manager :

1. - Ouvrez l''Analyseur de performances' 
1. - Sélectionnez 'Groupes d'administration du service de contrôle d'intégrité'
1. - Ajoutez tous les compteurs commençant par 'HTTP'

Si la configuration est correcte, ces compteurs doivent montrer des signes d'activité, lorsque des événements et d'autres éléments de données (selon les packs d'analyse décisionnelle du portail et la stratégie de collecte de journaux configurée) sont téléchargés. Ces compteurs ne sont pas nécessairement 'actifs' en permanence : ils peuvent avoir une activité faible ou inexistante parce que vous n'avez pas beaucoup de packs d'analyse décisionnelle ou que votre stratégie de collecte de données n'est pas très développée. 

##Procédure 4 : Recherche d'erreurs sur les journaux des événements du serveur d'administration ou de l'agent direct 
Pour finir, si tous les éléments ci-dessus échouent, mais que le service ne reçoit toujours pas de données, vérifiez si vous trouvez des erreurs dans **l'Observateur d'événements**.

Ouvrez **l'Observateur d'événements** -> **Application et Services** -> **Operations Manager** et filtrez selon les sources d'événements : **Advisor**, **Modules du service de contrôle d'intégrité**, **HealthService** et **Connecteur de service** (ce dernier ne s'applique qu'à l'agent direct). 

La plupart de ces événements sont similaires dans Operations Manager et sur l'agent direct et les étapes de dépannage également. 
La seule différence entre Operations Manager et l'agent direct est le processus d'inscription (GUI dans Operations Manager, combinaison d'ID et de clé d'espace de travail dans l'agent direct), mais, après l'inscription initiale, le service échange et utilise des certificats, et tout ce qui concerne la communication avec le service est identique.

Par conséquent, beaucoup de ces événements s'appliquent aux deux types d'infrastructure de rapports. Voici une liste des événements courants que vous pouvez voir.

###Événements ayant pour source 'Modules du service de contrôle d'intégrité'
#####ID d'événement 2138
Cela signifie que votre proxy requiert une authentification. Suivez les étapes de [Configuration des serveurs proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

#####ID d'événement 2137
Operations Manager ne peut pas lire le certificat d'authentification. Exécuter à nouveau l'Assistant d'inscription d'Advisor permet de réparer les comptes de certificats/d'identification

#####ID d'événement 2132
Signifie **Non autorisé**. Il peut s'agir d'un problème lié au certificat et/ou à l'inscription auprès du service. Essayez de réexécuter l'Assistant d'inscription qui réparera les certificats et les comptes d'identification. En outre, vérifiez que le proxy a été défini pour autoriser les exclusions. Pour plus d'informations, consultez la page [Configuration des serveurs proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

#####ID d'événement 2129
Il s'agit d'une connexion ayant échoué en raison de l'échec de la négociation SSL. Vérifiez que vos systèmes sont configurés pour utiliser TLS et non SSL. Il peut y avoir des paramètres SSL étranges relatifs au chiffrement sur ce serveur, soit dans les options **Avancées** d'Internet Explorer, soit dans le Registre Windows de la clé 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

#####ID d'événement 2127
Code d'erreur de l'échec d'envoi de données. Si cela se produit uniquement de temps en temps, ce peut n'être qu'une erreur passagère. Surveillez-le, afin de connaître la fréquence à laquelle il se produit. S'il apparaît très souvent (toutes les 10 minutes environ, pendant une période prolongée), il pourrait s'agir d'un vrai problème : vérifiez votre configuration réseau, les paramètres de proxy décrits ci-dessus et réexécutez l'Assistant d'inscription. Mais s'il ne se produit que sporadiquement (c'est-à-dire quelques fois par jour), cela ne devrait pas poser de problèmes, étant donné que les données sont mises en file d'attente et retransmises. Cette fréquence est généralement le signe d'un simple délai d'expiration réseau.
 
Certains des codes d'erreur HTTP ont des significations particulières, par exemple : 

- la PREMIÈRE fois qu'un agent direct MMA ou qu'un serveur de gestion tente d'envoyer des données à notre service, il obtiendra une erreur 500 avec un code d'erreur interne 404, 404 signifiant introuvable. Cela indique que la zone de stockage que nous allons utiliser pour votre nouvel espace de travail n'est pas encore tout à fait prête : elle est en cours d'approvisionnement. Lors de la nouvelle tentative, elle sera toutefois prête et le flux s'exécutera (dans des conditions normales).
- 403 peut indiquer un problème d'autorisation/d'informations d'identification et ainsi de suite. 

#####ID d'événement 2128
Échec de la résolution du nom DNS. Votre serveur ne peut pas résoudre notre adresse Internet à laquelle il doit envoyer des données. Il peut s'agir de paramètres de résolution DNS erronés sur votre ordinateur, de paramètres de proxy incorrects ou d'un problème DNS (temporaire) de votre fournisseur. Tout comme l'événement précédent, la fréquence (constamment ou 'de temps en temps') peut indiquer s'il s'agit d'un vrai problème ou non.

#####ID d'événement 2130
Délai d'expiration. Tout comme l'événement précédent, la fréquence (constamment ou de temps en temps) peut indiquer s'il s'agit d'un vrai problème ou non.

### Événements ayant pour source 'HealthService'
#####ID d'événement 4511
Impossible de charger le module "System.PublishDataToEndPoint" : fichier introuvable. L'initialisation d'un module de type "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") a échoué avec le code d'erreur Le fichier spécifié est introuvable.  
Cette erreur indique que vous avez d'anciennes DLL sur votre ordinateur, qui ne contiennent pas les modules requis. La solution consiste à mettre à jour vos serveurs d'administration avec le dernier correctif cumulatif.

#####ID d'événement 4502
Module bloqué. Si vous voyez ce message pour les flux de travail avec des noms comme **CollectInstanceSpace** ou **CollectTypeSpace**, cela peut signifier que le serveur rencontre des problèmes d'envoi des données de configuration. La fréquence (constamment ou de temps en temps) peut indiquer s'il s'agit d'un vrai problème ou non. S'il se produit plus qu'une fois par heure, c'est un problème. Si cette opération échoue seulement une ou deux fois par jour, il sera en mesure de récupérer. Selon la façon dont le module échoue (la description offre plus de détails), il peut s'agir d'un problème local (c'est-à-dire de collecte pour la base de données) ou d'un problème d'envoi vers le cloud. Vérifiez vos paramètres de réseau et de proxy. En dernier recours, redémarrez HealthService.

#####ID d'événement 4501
Module " System.PublishDataToEndPoint " bloqué. Un module de type " System.PublishDataToEndPoint " a signalé une erreur 87L qui était en cours d'exécution dans le cadre de la règle " Microsoft.SystemCenter.CollectAlertChangeDataToCloud ", en cours d'exécution pour l'instance " Groupe d'administration Operations Manager " avec id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" dans le groupe d'administration " SCOMTEST ". 
Vous ne devez PLUS voir ce message avec ce workflow, module et erreur précis. Ce [bogue  *a été corrigé*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). S'il s'affiche à nouveau, signalez-le via le canal de support Microsoft de votre choix.


### Événements ayant pour source 'Service Connector'
#####ID d'événement 4002
Le service a renvoyé le code d'état HTTP 403 en réponse à une requête.  Contactez l'administrateur de service pour vérifier l'intégrité du service. La requête sera retentée ultérieurement. Vous pouvez obtenir une erreur 403 pendant la phase initiale de l'inscription de l'agent. Vous verrez une URL semblable à https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest
Le code d'erreur 403 signifie " interdit " : il sindique généralement qu'un WorkspaceId ou une clé ont été mal copiés, ou encore que l'horloge n'est pas synchronisée sur l'ordinateur. Essayez de la synchroniser avec une source de temps fiable et d'utiliser la vérification de la connectivité de l'application du Panneau de configuration pour l'Agent de surveillance de Microsoft pour vérifier que vous disposez de l'ID d'espace de travail et de la clé appropriés. 





##Procédure 5 : Recherche d'envoi de données de vos agents et de l'indexation des données dans le portail
Effectuez une vérification dans le portail OpInsights, à partir de la page Vue d'ensemble accédez à la petite vignette **Serveurs et Utilisation** : vous verrez si les groupes d'administration (et leurs agents) et les agents directs rapportent des données dans la recherche. Le nombre d'agents sur la vignette est dérivé des données : si les ordinateurs n'émettent pas de rapports durant 2 semaines, ils disparaissent.

Les détails vous présentent la recherche et affichent l'horodateur des dernières données indexées pour chaque ordinateur. À partir de là, vous pouvez explorer les données. Selon la quantité de collecte de données configurée et les packs d'analyse décisionnelle, la planification et la vitesse de téléchargement de données peuvent varier.

Cette page comporte également des informations de mesure (cela n'utilise pas l'index de recherche mais le système de facturation, actualisé toutes les deux heures) concernant les quantités de données envoyées au service par pack d'analyse décisionnelle.

<!--HONumber=52-->