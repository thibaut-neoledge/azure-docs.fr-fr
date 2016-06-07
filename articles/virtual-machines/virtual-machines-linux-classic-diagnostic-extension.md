
<properties
		pageTitle="Analyse d’une machine virtuelle Linux avec une extension de machine virtuelle | Microsoft Azure"
		description="Découvrez comment utiliser l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux."
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux

## Introduction

L’extension de diagnostic Linux aide l’utilisateur à surveiller les machines virtuelles Linux s’exécutant sur Microsoft Azure, avec les fonctionnalités suivantes :

- Collecte et charge les données de performances, de diagnostic et de syslog du système des machines virtuelles Linux dans la table de stockage de l’utilisateur.
- Permet à utilisateur de personnaliser les métriques des données collectées et chargées.
- Permet à l’utilisateur de charger les fichiers journaux spécifiés dans la table de stockage désignée.

Pour la version 2.0, les données comprennent :

- Toutes les journalisations de Rsyslog de Linux, y compris les journaux système, de sécurité et des applications.
- Toutes les données système spécifiées dans ce [document](https://scx.codeplex.com/wikipage?title=xplatproviders").
- Les fichiers journaux spécifiés par l’utilisateur.

Notez que cette extension fonctionne avec les modèles de déploiement Classic et Resource Manager.


## Comment activer l’extension
L’extension peut être activée via le [portail Azure](https://ms.portal.azure.com/#), Azure PowerShell ou des scripts de l’interface de ligne de commande CLI Azure.

Pour afficher et configurer les données système et de performances directement à partir du portail Azure, suivez ces [étapes](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ « URL du blog Windows »/).


Cet article est centré sur l’activation et la configuration de l’extension via des commandes de l’interface de ligne de commande Azure. Ceci vous permet de lire et d’afficher directement les données de la table de stockage. Sachez que les méthodes de configuration décrites ci-dessous ne fonctionnent pas pour le portail Azure. Pour afficher et configurer le système et des données de performance directement depuis le portail Azure, cette extension doit être activée via le portail Azure, comme indiqué dans le paragraphe précédent.


## Composants requis
- Agent Microsoft Azure Linux version 2.0.6 ou ultérieure. Notez que la plupart des images de la galerie Linux de machines virtuelles Azure comprennent la version 2.0.6 ou ultérieure. Vous pouvez exécuter **WAAgent -version** pour vérifier la version installée dans la machine virtuelle. Si la machine virtuelle exécute une version antérieure à 2.0.6, vous pouvez suivre ces [instructions](https://github.com/Azure/WALinuxAgent "instructions") pour la mettre à jour.
- [Interface de ligne de commande Azure](../xplat-cli-install.md). Suivez [ce guide](../xplat-cli-install.md) pour configurer l’environnement de l’interface de ligne de commande CLI Azure sur votre machine. Une fois que l’interface de ligne de commande CLI Azure est installée, vous pouvez utiliser la commande **azure** de votre interface de ligne de commande (invite de ligne de commande, Terminal, Bash) afin d’accéder aux commandes de l’interface de ligne de commande CLI Azure. Par exemple, exécutez **azure vm extension set --help** pour l’utilisation détaillée, exécutez **azure login** pour vous connecter à Azure, ou exécutez **azure vm list** afin de répertorier l’ensemble des machines virtuelles que vous possédez dans Microsoft Azure.
- Un compte de stockage dédié au stockage des données. Vous devez disposer d’un nom de compte de stockage et d’une clé d’accès préalablement créés pour charger les données dans votre stockage.


## Utiliser la commande de l’interface de ligne de commande Azure afin d’activer l’extension de diagnostic Linux

###  Scénario 1 Activer l’extension avec le jeu de données par défaut
Pour la version 2.0 et ultérieure, les données collectées par défaut comprennent :

- Toutes les informations de Rsyslog (y compris les journaux système, de sécurité et des applications)  
- Un ensemble principal de données système de base. Notez que le jeu de données complet est décrit dans ce [document](https://scx.codeplex.com/wikipage?title=xplatproviders). Si vous voulez activer des données supplémentaires, poursuivez avec les étapes des scénarios 2 et 3.

Étape 1. Créez un fichier nommé PrivateConfig.json avec le contenu suivant.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Scénario 2 Personnaliser les métriques de surveillance des performances  
Cette section décrit comment personnaliser le tableau des données de diagnostic et de performance.

Étape 1. Créez un fichier nommé PrivateConfig.json à l’aide du contenu comme décrit par le scénario 1 ci-dessus. Créez également un fichier nommé PrivateConfig.json comme dans l’exemple suivant. Spécifiez les données spécifiques que vous souhaitez collecter.

Pour tous les fournisseurs et les variables pris en charge, consultez ce [document](https://scx.codeplex.com/wikipage?title=xplatproviders). Vous pouvez avoir plusieurs requêtes et les stocker dans plusieurs tables en ajoutant d’autres requêtes dans le script.

Par défaut, les données de Rsyslog sont toujours collectées.

	{
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions ’2.*’ --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


###   Scénario 3 Charger vos propres fichiers journaux
Cette section décrit comment collecter et charger des fichiers journaux spécifiques dans votre compte de stockage. Vous devez spécifier le chemin d’accès à votre fichier journal, ainsi que le nom de la table où stocker votre journal. Vous pouvez avoir plusieurs fichiers journaux en ajoutant plusieurs entrées fichier/table au script.

Étape 1. Créez un fichier nommé PrivateConfig.json à l’aide du contenu décrit dans le scénario 1. Créez un autre fichier nommé PublicConfig.json avec le contenu suivant.

	{
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions ’2.*’ --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


###   Scénario 4 Empêcher l’extension de collecter des journaux
Cette section décrit comment empêcher l’extension de collecter des journaux. Notez que le processus de l’agent d’analyse sera toujours exécuté, même si cette reconfiguration est effectuée. Par conséquent, si vous voulez arrêter complètement le processus de l’agent d’analyse, vous devez désinstaller l’extension. À l’avenir, nous ajouterons peut-être une propriété de configuration permettant de simplement désactiver l’extension (en arrêtant ainsi entièrement le processus de l’agent d’analyse), sans avoir à désinstaller l’intégralité de l’extension.

Étape 1. Créez un fichier nommé PrivateConfig.json à l’aide du contenu décrit dans le scénario 1. Créez un autre fichier nommé PublicConfig.json avec le contenu suivant.

	{
     	"perfCfg":[],
     	"enableSyslog":"false"
	}


Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions ’2.*’ --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


## Passer en revue vos données
Les données de performance et de diagnostic sont stockées dans une table Azure Storage. Consultez [cet article](../storage/storage-ruby-how-to-use-table-storage.md) pour découvrir comment accéder aux données de la table de stockage à l’aide de scripts de l’interface de ligne de commande CLI Azure.

Vous pouvez aussi utiliser les outils d’interface utilisateur suivants pour accéder aux données :

1.	Utilisez l’Explorateur de serveurs de Visual Studio. Accédez à votre compte de stockage. Après environ 5 minutes d’exécution de la machine virtuelle, quatre tables par défaut doivent s’afficher : « LinuxCpu », « LinuxDisk », « LinuxMemory » et « Linuxsyslog ». Double-cliquez sur le nom de la table pour afficher les données.
2.	Utilisez l’[Explorateur du stockage Azure](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer") pour accéder aux données.

![image](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

Si vous avez activé les éléments fileCfg ou perfCfg spécifiés dans les scénarios 2 ou 3, vous pouvez utiliser les outils précédents afin d’afficher les données non présentes par défaut.



## Problèmes connus
- Pour la version 2.0, les informations de Rsyslog et le fichier journal spécifié par l’utilisateur sont accessibles seulement via un script.

<!---HONumber=AcomDC_0525_2016-->