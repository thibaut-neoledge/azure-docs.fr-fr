<properties
   pageTitle="Configurer les paramètres de pare-feu et de proxy pour Operational Insights"
   description="En savoir plus sur les paramètres de proxy et de pare-feu que vous devez configurer pour le type d'agent que vous utilisez avec Operational Insights"
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
   ms.date="07/21/2015"
   ms.author="banders" />

# Configurer les paramètres de pare-feu et de proxy pour Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Les actions nécessaires pour configurer les paramètres de proxy et de pare-feu pour Operational Insights diffèrent selon que vous utilisez Operations Manager et ses agents ou des agents Microsoft Monitoring Agent qui se connectent directement aux serveurs. Consultez les sections suivantes en fonction du type d'agent que vous utilisez.

## Configurer les paramètres de pare-feu et de proxy avec Microsoft Monitoring Agent

Pour que Microsoft Monitoring Agent se connecte au service Operational Insights et s'enregistre auprès du service, il doit avoir accès au numéro de port de vos domaines et aux URL. Si vous utilisez un serveur proxy pour la communication entre l'agent et le service Operational Insights, vous devez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l'accès à Internet, vous devez configurer votre pare-feu pour autoriser l'accès à Operational Insights. Les tableaux suivants répertorient les ports dont Operational Insights a besoin.

|**Ressource de l'agent**|**Ports**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |*.oms.opinsights.azure.com|Port 443|
|ods.systemcenteradvisor.com|Port 443|
|*.blob.core.windows.net/*|Port 443|

Vous pouvez utiliser la procédure suivante pour configurer les paramètres de proxy de Microsoft Monitoring Agent dans le Panneau de configuration. Vous devez utiliser la procédure pour chaque serveur. Si vous devez configurer plusieurs serveurs, utilisez un script pour automatiser ce processus. Dans ce cas, suivez la procédure *Pour configurer les paramètres de proxy de Microsoft Monitoring Agent à l'aide d'un script*.

### Pour configurer les paramètres de proxy de Microsoft Monitoring Agent dans le Panneau de configuration

1. Ouvrez le **Panneau de configuration**.

2. Ouvrez **Microsoft Monitoring Agent**.

3. Cliquez sur l'onglet **Paramètres de proxy**. ![Onglet Paramètres de proxy](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. Sélectionnez **Utiliser un serveur proxy** et tapez l'URL et le numéro de port, si nécessaire, comme indiqué dans l'exemple. Si votre serveur proxy requiert une authentification, tapez le nom d'utilisateur et le mot de passe pour accéder au serveur proxy.

Utilisez la procédure suivante pour créer un script PowerShell que vous pouvez exécuter pour définir les paramètres de proxy pour chaque agent qui se connecte directement aux serveurs.

### Pour configurer les paramètres de proxy de Microsoft Monitoring Agent à l'aide d'un script


- Copiez l'exemple suivant, mettez-le à jour avec les informations spécifiques à votre environnement, enregistrez-le avec l'extension de nom de fichier PS1, puis exécutez le script sur chaque ordinateur qui se connecte directement au service Operational Insights.


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Configurer les paramètres de pare-feu et de proxy avec Operations Manager

Pour qu'un groupe d'administration Operations Manager se connecte au service Operational Insights et s'y inscrive, il doit avoir accès aux numéros de ports de vos domaines et aux URL. Si vous utilisez un serveur proxy pour la communication entre le serveur d'administration Operations Manager et le service Operational Insights, vous devez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l'accès à Internet, vous devez configurer votre pare-feu pour autoriser l'accès à Operational Insights. Même si aucun serveur d'administration Operations Manager n'est placé derrière un serveur proxy, ses agents peuvent l’être. Dans ce cas, le serveur proxy devrait être configuré de la même manière que les agents afin d'activer et de permettre l’envoi des données de la solution Sécurité et gestion du journal au service Web Operational Insights.

Pour que les agents Operations Manager puissent communiquer avec le service Operational Insights, votre infrastructure Operations Manager (y compris les agents) doit disposer des paramètres appropriés pour le proxy et la version. Le paramètre de proxy d'agents est spécifié dans la console Operations Manager. La version devrait être une des versions suivantes :

- Operations Manager 2012 SP1 correctif cumulatif 7 ou version ultérieure
- Operations Manager 2012 R2 correctif cumulatif 3 ou version ultérieure


Les tableaux suivants répertorient les ports associés à ces tâches.

>[AZURE.NOTE]Certaines des ressources suivantes comportent la mention « Advisor ». Toutefois, les ressources répertoriées sont appelées à évoluer.

|**Ressource de l'agent**|**Ports**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |*.oms.opinsights.azure.com|Port 443|
|ods.systemcenteradvisor.com|Port 443|
|*.blob.core.windows.net/*|Port 443|

|**Ressource de serveur d'administration**|**Ports**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |service.systemcenteradvisor.com|Port 443| |scadvisor.accesscontrol.windows.net|Port 443| |scadvisorservice.accesscontrol.windows.net|Port 443| |*.blob.core.windows.net/*|Port 443|
|data.systemcenteradvisor.com|Port 443|
|ods.systemcenteradvisor.com|Port 443|
|*.systemcenteradvisor.com|Port 443|


|**Ressource de console Operations Manager et Operational Insights**|**Ports**|
|---|---|
|*.systemcenteradvisor.com|Port 80 et 443| |*.live.com|Ports 80 et 443|
|*.microsoftonline.com|Port 80 et 443| |login.windows.net|Port 80 et 443|


Utilisez les procédures suivantes pour inscrire votre groupe d'administration Operations Manager auprès du service Operational Insights. Si vous rencontrez des problèmes de communication entre le groupe d'administration et le service Operational Insights, utilisez les procédures de validation pour résoudre les problèmes de transmission de données au service Operational Insights.

### Pour demander des exceptions pour les points de terminaison du service Operational Insights

1. Utilisez les informations du premier tableau présenté précédemment pour vous assurer que les ressources nécessaires pour le serveur d'administration Operations Manager sont accessibles via tous les pare-feu éventuels.

2. Utilisez les informations du second tableau présenté précédemment pour vous assurer que les ressources nécessaires pour la console Operations dans Operations Manager et Operational Insights sont accessibles via tous les pare-feu éventuels.

3. Si vous utilisez un serveur proxy avec Internet Explorer, assurez-vous qu'il est configuré et qu'il fonctionne correctement. Pour vérifier, vous pouvez ouvrir une connexion web sécurisée (https), par exemple [https://bing.com](https://bing.com). Si la connexion web sécurisée ne fonctionne pas dans un navigateur, elle ne fonctionnera probablement pas dans la console d'administration Operations Manager avec les services web dans le cloud.

### Pour configurer le serveur proxy dans la console Operations Manager

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.

2. Développez **Operational Insights**, puis sélectionnez **Connexion à Operational Insights**. ![Connexion à Operations Manager Operational Insights](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. Dans la vue Connexion à Operational Insights, cliquez sur **Configurer le serveur proxy**. ![Connexion à Operations Manager Operational Insights, Configurer le serveur proxy](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. Dans l'Assistant Paramètres Operational Insights : serveur proxy, sélectionnez **Utiliser un serveur proxy pour accéder au service web Operational Insights**, puis tapez l'URL avec le numéro de port, par exemple, **http://myproxy:80**. ![Adresse du proxy Operations Manager Operational Insights](./media/operational-insights-proxy-firewall/proxy-om03.png)


### Pour spécifier les informations d'identification si le serveur proxy nécessite une authentification
 Les informations d'identification et les paramètres du serveur proxy doivent se propager aux ordinateurs gérés qui communiquent avec Operational Insights. Ces serveurs devraient se trouver dans le *groupe de serveurs de surveillance Microsoft System Center Advisor*. Les informations d'identification sont chiffrées dans le registre de chaque serveur du groupe.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.

2. Sous **Configuration d'identification**, sélectionnez **Profils**.

3. Ouvrez le profil **Proxy d'identification de System Center Advisor**. ![image du profil Proxy d'identification de System Center Advisor](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. Dans l'Assistant Profil d'identification, cliquez sur **Ajouter** pour utiliser un compte d'identification. Vous pouvez créer un compte d'identification ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour franchir le serveur proxy. ![image de l'Assistant Profil d'identification](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. Pour définir le compte à gérer, choisissez **Une classe, un groupe ou un objet sélectionné(e)** pour ouvrir la zone Recherche d'objets. ![image de l'Assistant Profil d'identification](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. Recherchez puis sélectionnez le **groupe de serveur de surveillance Microsoft System Center Advisor**. ![image de la zone Recherche d'objets](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. Cliquez sur **OK** pour fermer la zone Ajouter un compte d'identification ![image de l'Assistant Profil d'identification](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. Terminez l'Assistant et enregistrez les modifications. ![image de l'Assistant Profil d'identification](./media/operational-insights-proxy-firewall/proxyacct5.png)


### Pour valider que les packs d'administration Operational Insights sont téléchargés

- Si vous avez ajouté des solutions à l'aide d'Operational Insights, vous pouvez les afficher dans la console Operations Manager en tant que packs d'administration sous **Administration**. Recherchez *System Center Advisor* pour les retrouver rapidement. ![packs d'administration téléchargés](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- Vous pouvez également rechercher les packs d'administration Operational Insights en utilisant les commandes Windows PowerShell suivantes sur le serveur d'administration Operations Manager :

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### Pour valider qu'Operations Manager envoie des données au service Operational Insights

1. Sur le serveur d'administration Operations Manager, ouvrez l'Analyseur de performances (perfmon.exe), puis sélectionnez **Analyseur de performances**.

2. Cliquez sur **Ajouter**, puis sélectionnez **Groupes d'administration du service de contrôle d'intégrité**.

3. Ajoutez tous les compteurs commençant par **HTTP**. ![ajouter des compteurs](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. Si votre configuration Operations Manager est correcte, vous verrez une activité pour les compteurs d'administration du service de contrôle d'intégrité liés à des événements et autres éléments de données, en fonction des packs d'administration que vous avez ajoutés dans Operational Insights et de la stratégie de collecte de journaux configurée. ![Analyseur de performances indiquant une activité](./media/operational-insights-proxy-firewall/sendingdata2.png)

<!---HONumber=July15_HO4-->