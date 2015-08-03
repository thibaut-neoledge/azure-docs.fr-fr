<properties
	pageTitle="Connexion directe des ordinateurs à Operational Insights"
	description="Vous pouvez connecter les ordinateurs directement à Operational Insights en installant l’agent Operational Insights sur chaque ordinateur à connecter."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# Connexion directe des ordinateurs à Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez connecter les ordinateurs directement à Operational Insights en installant l’agent Operational Insights sur chaque ordinateur à connecter.

> [AZURE.TIP]Pour les machines virtuelles s’exécutant dans Azure, installez l'agent en suivant les étapes de la rubrique [Analyse des données des serveurs dans Microsoft Azure](operational-insights-analyze-data-azure.md)

## Téléchargement et installation de l’agent
Utilisez les procédures suivantes pour télécharger et installer l'agent Operational Insights.

### Pour télécharger le fichier de configuration de l’agent
1. Dans le portail Operational Insights, sur la page **Vue d’ensemble**, cliquez sur **Paramètres**. Cliquez sur l’onglet **Sources connectées**, situé en haut de la page. ![settings page](./media/operational-insights-direct-agent/direct-agent01.png)
2. Sous **Associer directement les serveurs (64 bits)**, cliquez sur le bouton Télécharger l'agent pour télécharger le fichier d'installation.
3. À droite de l’**ID de l'espace de travail**, cliquez sur l'icône de copie et collez l’ID dans le Bloc-notes.
4. À droite de la **clé primaire**, cliquez sur l'icône de copie et collez l’ID dans le Bloc-notes. ![settings page](./media/operational-insights-direct-agent/direct-agent02.png)

### Installation de l’agent à l’aide du programme d’installation
1. Exécutez le programme d’installation pour installer l’agent sur un ordinateur que vous souhaitez gérer.
2. Sélectionnez **Connecter l’agent à Microsoft Azure Operational Insights**, puis cliquez sur **Suivant**.
3. Lorsque vous y êtes invité, saisissez l’**ID de l'espace de travail** et la **clé primaire** que vous avez copiés dans le Bloc-notes lors de la procédure précédente.

4. Cliquez sur **Next**. L'agent vérifie qu'il peut se connecter à Operation Insights.
5. Lorsque vous avez terminé, l’**agent de gestion Microsoft** apparaît dans le **Panneau de configuration**.

### Installation de l’agent à l’aide de la ligne de commande
- Modifiez, puis utilisez l’exemple suivant pour installer l’agent à l’aide de la ligne de commande. ```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Configuration de l’agent Microsoft Monitoring Agent (facultatif)
Utilisez les informations suivantes pour permettre à un agent de communiquer directement avec le service Microsoft Azure Operational Insights. Une fois que vous avez configuré l’agent, il s’inscrira auprès du service de l’agent et obtiendra les informations de configuration nécessaires, ainsi que les packs d’administration contenant les informations de la solution.

Une fois que les données des ordinateurs analysés sont collectées par l’agent, le nombre d’ordinateurs analysés apparaîtra dans le portail Operational Insights dans l’onglet **Sources connectées**, dans **Paramètres**, sous **Associer directement les serveurs (64 bits)**. Vous pouvez afficher les données et les informations d’évaluation de tout ordinateur envoyant des données dans le portail Operational Insights.

Vous pouvez également désactiver l’agent, si nécessaire, ou l’autoriser à l’aide de la ligne de commande ou du script.

### Configuration de l’agent
1. Après avoir installé l’**agent Microsoft Monitoring Agent**, ouvrez le **Panneau de configuration**.
2. Ouvrez l’agent Microsoft Monitoring Agent, puis cliquez sur l’onglet Azure Operational Insights.
3. Sélectionnez **Connexion à Azure Operational Insights**.
4. Dans la zone **ID de l’espace de travail**, collez l’ID de l’espace de travail dans le portail Operational Insights.
5. Dans la zone **Clé du compte**, collez la **clé primaire** dans le portail Operational Insights, puis cliquez sur **OK**.

### Désactivation d’un agent
1. Après avoir installé l’agent, ouvrez le **Panneau de configuration**.
2. Ouvrez l’agent Microsoft Monitoring Agent, puis cliquez sur l’onglet **Azure Operational Insights**.
3. Supprimez la **connexion à Azure Operational Insights**.

### Activation de l’agent à l’aide de la ligne de commande ou du script
- Vous pouvez utiliser Windows PowerShell ou un script VB avec l’exemple suivant.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Configuration des paramètres de pare-feu et de proxy (facultatif)
Si vous avez des serveurs proxy ou un pare-feu qui limitent l’accès à Internet dans votre environnement, vous devez suivre les procédures suivantes pour permettre à Operations Manager ou aux agents de communiquer avec le service Operational Insights.

- [Configuration des paramètres de pare-feu et de proxy (facultatif)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO4-->