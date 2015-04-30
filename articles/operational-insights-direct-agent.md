<properties 
   pageTitle="connect-scom" 
   description="Connexion directe des ordinateurs à Operational Insights" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Connexion directe des ordinateurs à Operational Insights 

Vous pouvez connecter les ordinateurs directement à Operational Insights en installant l'agent Operational Insights sur chaque ordinateur à connecter. 


##Téléchargement, installation et configuration de l'agent
###Téléchargement du fichier de configuration de l'agent
1. Dans le **portail Operational Insights**, sur la page **Présentation**, cliquez sur **Serveurs et utilisation**.
1. Sous **Serveurs directement connectés**, cliquez sur **Configurer**.
1. À côté de **Ajouter des agents**, cliquez sur le lien de l'agent pour télécharger le fichier d'installation.
1. Dans la zone **Clé de l'espace de travail primaire**, sélectionnez la clé et copiez-la (CTRL + C).


### Installation de l'agent à l'aide du programme d'installation
1. Exécutez le programme d'installation pour installer l'agent sur un ordinateur que vous souhaitez gérer.
1. Sélectionnez **Connecter l'agent à Microsoft Azure Operational Insights**, puis cliquez sur **Suivant**.
1. Lorsque vous y êtes invité, entrez les informations que vous avez copiées à l'étape 4.
1. Lorsque vous avez terminé, l'**agent de gestion Microsoft** apparaît dans le **Panneau de configuration**.

### Installation de l'agent à l'aide de la ligne de commande
Modifiez, puis utilisez l'exemple suivant pour installer l'agent à l'aide de la ligne de commande.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Reconfiguration du Microsoft Monitoring Agent (facultatif)
Utilisez les informations suivantes pour permettre à un agent de communiquer directement avec le service Microsoft Azure Operational Insights. Une fois que vous avez configuré l'agent, il s'inscrira auprès du service de l'agent et obtiendra les informations de configuration nécessaires, ainsi que les packs d'administration contenant les informations du pack d'analyse décisionnelle.

Une fois que les données des ordinateurs analysés sont collectées par l'agent, le nombre d'ordinateurs analysés apparaîtra dans le portail Operational Insights sur la page Utilisation, sous **Agents directement connectés**. Vous pouvez afficher les données et les informations d'évaluation de tout ordinateur envoyant des données dans le portail Operational Insights.

Vous pouvez également désactiver l'agent, si nécessaire, ou l'autoriser à l'aide de la ligne de commande ou du script.

### Configuration de l'agent
- Après avoir installé l'agent **Microsoft Monitoring Agent**, ouvrez le **Panneau de configuration**.
- Ouvrez l'agent Microsoft Monitoring Agent, puis cliquez sur l'onglet Azure Operational Insights.
- Sélectionnez **Connexion à Azure Operational Insights**.
- Dans la zone **ID de l'espace de travail**, tapez l'ID de l'espace de travail fourni dans le portail Operational Insights.
- Dans la zone de Clé de compte, collez la **Clé de l'espace de travail primaire** que vous avez copiée lors de l'installation de l'agent, puis cliquez sur **OK**.

### Désactivation d'un agent
- Après avoir installé l'agent, ouvrez le **Panneau de configuration**.
- Ouvrez l'agent Microsoft Monitoring Agent, puis cliquez sur l'onglet **Azure Operational Insights**.
- Supprimez la connexion à **Azure Operational Insights**.

### Activation de l'agent à l'aide de la ligne de commande ou du script
Vous pouvez utiliser Windows PowerShell ou un script VB avec l'exemple suivant.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configuration des paramètres de pare-feu et de proxy (facultatif)
Si vous avez des serveurs proxy ou un pare-feu qui limitent l'accès à Internet dans votre environnement, vous devez suivre les procédures suivantes pour permettre à Operations Manager et/ou aux agents de communiquer avec le service Operational Insights 



- [Configuration des paramètres de pare-feu et de proxy (facultatif)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Contenu connexe

- [Billet de blog : connexion directe des serveurs à Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Billet de blog : activation d'Operational Insights pour les machines virtuelles Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)


<!--HONumber=52-->