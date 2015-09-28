<properties
   pageTitle="Résolution des problèmes à l’aide d’Operational Insights"
   description="En savoir plus sur la résolution des problèmes à l’aide d’Operational Insights"
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
   ms.date="09/10/2015"
   ms.author="banders" />

# Résolution des problèmes à l’aide d’Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez utiliser les informations des sections suivantes pour vous aider à résoudre les problèmes. Si le problème que vous rencontrez n’est pas présenté dans cet article, vous pouvez consultez le [blog de l’équipe Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Résolution de problèmes d’autorisations avec l’évaluation de SQL
Le service Operations Management Suite (OMS) utilise les groupes de gestion de Microsoft Monitoring Agent et d’Operations Manager pour collecter des données et les envoyer au service OMS. Certaines charges de travail, telles que SQL Server, nécessitent des privilèges spécifiques de la charge de travail pour effectuer la collecte de données dans un contexte de sécurité différent, tel qu’un compte de domaine. Si votre Microsoft Monitoring Agent est connecté via System Center Operations Manager et que vous rencontrez des problèmes d’autorisations lors de la collecte de données, vous devez fournir des informations d’identification en configurant un compte d’identification.

Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser ce compte Windows en tant que compte d’identification.

### Configuration du compte d’identification SQL dans la console Operations

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d’identification**, cliquez sur **Profils**, puis ouvrez le **profil d’identification de Microsoft System Center Advisor**.

3. Dans la page **Comptes d’identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d’identification Windows contenant les informations d’identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.

    >[AZURE.NOTE]Le type de compte d’identification doit être Windows. Le compte d’identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.

5. Cliquez sur **Enregistrer**.

6. Modifiez puis exécutez l’exemple T-SQL suivant sur chaque instance de SQL Server afin d’accorder les autorisations minimales requises pour que le compte d’identification puisse effectuer l’évaluation de SQL. Toutefois, vous n’avez pas besoin de faire cela si un compte d’identification fait déjà partie du rôle de serveur sysadmin sur les instances de SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$\_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version Get-SCOMManagementPack | where {$\_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version ```

>[AZURE.NOTE]Si vous dépannez la solution de capacité, vérifiez *le nombre* de packs d’administration contenant le nom « capacity » dont vous disposez : il existe deux packs d’administration avec le même nom d’affichage (mais dont l’ID interne est différent) dans le même ensemble de packs d’administration. Si l’un des deux n’est pas importé (souvent en raison de l’absence d’une dépendance VMM), l’autre de l’est pas non plus et l’opération n’est pas relancée.

Vous devez voir les trois packs suivants liés à la « capacité » : - Pack d’analyse décisionnelle de Microsoft System Center Capacity Advisor - Pack d’analyse décisionnelle de Microsoft System Center Capacity Advisor - Données de stockage de Microsoft System Center Capacity Advisor

Si vous voyez uniquement un ou deux d'entre eux, mais pas les trois, supprimez-les et attendez 5 à 10 minutes pour qu'Operations Manager les télécharge et les importe à nouveau : vérifiez les journaux des événements pour les erreurs pendant cette période.

### Procédure 2 : vérification du téléchargement des solutions appropriées sur votre agent direct
>[AZURE.NOTE]Si vous utilisez uniquement Operations Manager, vous pouvez ignorer cette procédure.

Dans l’agent direct, vous devez voir la stratégie de collecte de la solution, mise en cache sous **C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procédure 3 : vérification de l'envoi des données au service Advisor (ou de tentatives d'envoi)
Vous pouvez effectuer la procédure suivante sur l'ordinateur agent direct ou sur le serveur d'administration Operations Manager, selon que vous utilisez des agents connectés directement ou Operations Manager :

1. - Ouvrez l'« Analyseur de performances »
2. - Sélectionnez « Groupes d'administration du service de contrôle d'intégrité »
3. - Ajoutez tous les compteurs commençant par « HTTP ».

Si la configuration est correcte, ces compteurs doivent montrer des signes d'activité, lorsque des événements et d'autres éléments de données (selon les solutions embarquées dans le portail et la stratégie de collecte de journaux configurée) sont téléchargés. Ces compteurs ne sont pas nécessairement actifs en permanence : ils peuvent avoir une activité faible ou inexistante parce que vous ne disposez pas de beaucoup de solutions embarquées ou que votre stratégie de collecte de données n'est pas très développée.

### Procédure 4 : recherche d'erreurs sur les journaux des événements du serveur d'administration ou de l'agent direct
Pour finir, si tout ce qui est décrit ci-dessus échoue, et que le service ne reçoit toujours pas de données, vérifiez si des erreurs figurent dans l’**Observateur d’événements**.

Ouvrez **Observateur d’événements** -> **Application et services** -> **Operations Manager** et filtrez par source d’événements : **Advisor**, **Modules du service de contrôle d’intégrité**, **Service de contrôle d’intégrité** et **Connecteur de service** (ce dernier s’applique à l’agent direct uniquement).

La plupart de ces événements sont similaires dans Operations Manager et sur l'agent direct et les étapes de dépannage également. La seule différence entre Operations Manager et l'agent direct est le processus d'inscription (GUI dans Operations Manager, combinaison d'ID et de clé d'espace de travail dans l'agent direct), mais, après l'inscription initiale, le service échange et utilise des certificats, et tout ce qui concerne la communication avec le service est identique.

Par conséquent, beaucoup de ces événements s'appliquent aux deux types d'infrastructure de rapports. Voici une liste des événements courants que vous pouvez voir.

### Événements issus de la source « Modules du service de contrôle d'intégrité »
##### ID d'événement 2138
Cela signifie que votre proxy requiert une authentification. Veuillez suivre les étapes de [Configuration des serveurs proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### ID d'événement 2137
Operations Manager ne peut pas lire le certificat d'authentification. Exécuter à nouveau l'Assistant d'inscription d'Advisor permet de réparer les comptes de certificats/d'identification

##### ID d'événement 2132
Signifie **Non autorisé**. Il peut s'agir d'un problème lié au certificat et/ou à l'inscription auprès du service. Essayez de réexécuter l'Assistant d'inscription qui réparera les certificats et les comptes d'identification. En outre, vérifiez que le proxy a été défini pour autoriser les exclusions. Pour plus d’informations, voir [Configuration des serveurs proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### ID d'événement 2129
Il s'agit d'une connexion ayant échoué en raison de l'échec de la négociation SSL. Vérifiez que vos systèmes sont configurés pour utiliser TLS et non SSL. Il peut y avoir des paramètres SSL étranges relatifs au chiffrement sur ce serveur, soit dans les options **Avancées** d’Internet Explorer, soit dans le Registre Windows de la clé

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### ID d'événement 2127
Code d'erreur de l'échec d'envoi de données. Si cela se produit uniquement de temps en temps, ce peut n'être qu'une erreur passagère. Surveillez-le, afin de connaître la fréquence à laquelle il se produit. S'il apparaît très souvent (toutes les 10 minutes environ, pendant une période prolongée), il pourrait s'agir d'un vrai problème : vérifiez votre configuration réseau, les paramètres de proxy décrits ci-dessus et réexécutez l'Assistant d'inscription. Mais s'il ne se produit que sporadiquement (c'est-à-dire quelques fois par jour), cela ne devrait pas poser de problèmes, étant donné que les données sont mises en file d'attente et retransmises. Cette fréquence est généralement le signe d'un simple délai d'expiration réseau.

Certains des codes d'erreur HTTP ont des significations particulières, par exemple :

- la PREMIÈRE fois qu'un agent direct MMA ou qu'un serveur de gestion tente d'envoyer des données à notre service, il obtiendra une erreur 500 avec un code d'erreur interne 404, 404 signifiant introuvable. Cela indique que la zone de stockage que nous allons utiliser pour votre nouvel espace de travail n'est pas encore tout à fait prête : elle est en cours d'approvisionnement. Lors de la nouvelle tentative, elle sera toutefois prête et le flux s'exécutera (dans des conditions normales).
- 403 peut indiquer un problème d'autorisation/d'informations d'identification et ainsi de suite.

##### ID d'événement 2128
Échec de la résolution du nom DNS. Votre serveur ne peut pas résoudre notre adresse Internet à laquelle il doit envoyer des données. Il peut s'agir de paramètres de résolution DNS erronés sur votre ordinateur, de paramètres de proxy incorrects ou d'un problème DNS (temporaire) de votre fournisseur. Tout comme l'événement précédent, la fréquence (constamment ou de temps en temps) peut indiquer s'il s'agit d'un vrai problème ou non.

##### ID d'événement 2130
Délai d'expiration. Tout comme l'événement précédent, la fréquence (constamment ou de temps en temps) peut indiquer s'il s'agit d'un vrai problème ou non.

### Événements issus de la source « Service de contrôle d'intégrité »
##### ID d'événement 4511
Impossible de charger le module « System.PublishDataToEndPoint » – fichier introuvable. Échec de l'initialisation d'un module de type « System.PublishDataToEndPoint » (CLSID « {D407D659-65E4-4476-BF40-924E56841465} ») avec le code d'erreur indiquant : « Le système ne trouve pas le fichier spécifié ». Cette erreur indique que vous avez d'anciennes DLL sur votre ordinateur, qui ne contiennent pas les modules requis. La solution consiste à mettre à jour vos serveurs d'administration avec le dernier correctif cumulatif.

##### ID d'événement 4502
Module bloqué. Si vous voyez ce message pour les flux de travail avec des noms tels que **CollectInstanceSpace** ou **CollectTypeSpace**, cela peut signifier que le serveur rencontre des problèmes d’envoi des données de configuration. La fréquence (constamment ou de temps en temps) peut indiquer s'il s'agit d'un vrai problème ou non. S'il se produit plus qu'une fois par heure, c'est un problème. Si cette opération échoue seulement une ou deux fois par jour, il sera en mesure de récupérer. Selon la façon dont le module échoue (la description offre plus de détails), il peut s'agir d'un problème local (c'est-à-dire de collecte pour la base de données) ou d'un problème d'envoi vers le cloud. Vérifiez vos paramètres de réseau et de proxy. En dernier recours, redémarrez HealthService.

##### ID d'événement 4501
Module « System.PublishDataToEndPoint » bloqué. Un module de type « System.PublishDataToEndPoint » a signalé une erreur 87L qui était en cours d'exécution dans le cadre de la règle « Microsoft.SystemCenter.CollectAlertChangeDataToCloud », en cours d'exécution pour l'instance « Groupe d’administration Operations Manager » avec l’ID « {6B1D1BE8-EBB4-B425-08DC-2385C5930B04} » dans le groupe d'administration « SCOMTEST ». Vous ne devriez *pas* voir ce message précisément avec ce flux de travail, ce module et cette erreur. Ce [bogue a été *corrigé*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). S'il s'affiche à nouveau, signalez-le via le canal de support Microsoft de votre choix.


### Événements issus de la source « Connecteur du service »
##### ID d'événement 4002
Le service a renvoyé le code d'état HTTP 403 en réponse à une requête. Contactez l'administrateur de service pour vérifier l'intégrité du service. La requête sera retentée ultérieurement. Vous pouvez obtenir une erreur 403 pendant la phase initiale d’enregistrement de l’agent, vous verrez une URL telle que https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest. Le code d’erreur 403 signifie « interdit » : cette erreur est généralement due à un WorkspaceId ou une clé mal copié ou à l’horloge qui n’est pas synchronisée sur l’ordinateur. Essayez de la synchroniser avec une source de temps fiable et d'utiliser la vérification de la connectivité de l'application du Panneau de configuration pour l'Agent de surveillance de Microsoft pour vérifier que vous disposez de l'ID d'espace de travail et de la clé appropriés.


### Procédure 5 : recherche de vos agents pour l’envoi de données et leur l'indexation des données dans le portail
Effectuez une vérification dans le portail OpInsights, à partir de la page Vue d’ensemble et accédez à la petite vignette **Serveurs et utilisation** : vous verrez si les groupes d’administration (et leurs agents) et les agents directs rapportent des données dans la recherche de journaux. Le nombre d'agents sur la vignette est dérivé des données : si les ordinateurs n'émettent pas de rapports durant 2 semaines, ils disparaissent.

Les détails vous présentent la recherche de journaux et affichent l'horodateur des dernières données indexées pour chaque ordinateur. À partir de là, vous pouvez explorer les données. Selon la quantité de collecte de données configurée et les solutions, la planification et la vitesse de téléchargement des données peuvent varier.

Cette page comporte également des informations de mesure (cela n'utilise pas l'index de recherche, mais le système de facturation, actualisé toutes les deux heures) concernant les quantités de données envoyées au service par solution.

<!---HONumber=Sept15_HO3-->