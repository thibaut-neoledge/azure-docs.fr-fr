
<properties
		pageTitle="Analyse d’une machine virtuelle Linux avec une extension de machine virtuelle | Microsoft Azure"
		description="Découvrez comment utiliser l’extension de diagnostic Linux pour surveiller les données de performances et de diagnostic d’une machine virtuelle Linux dans Azure."
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

L’extension de diagnostic Linux aide l’utilisateur à surveiller les machines virtuelles Linux s’exécutant sur Microsoft Azure. Elle présente les fonctionnalités suivantes :

- Elle collecte et télécharge les informations sur les performances du système depuis la machine virtuelle Linux vers la table de stockage de l’utilisateur, y compris les informations de diagnostic et de syslog.
- Elle permet aux utilisateurs de personnaliser les mesures de données collectées et chargées.
- Elle permet aux utilisateurs de télécharger les fichiers journaux spécifiés dans la table de stockage désignée.

Dans la version 2.0, les données comprennent les éléments suivants :

- tous les journaux Rsyslog de Linux, y compris les journaux système, de sécurité et d’applications ;
- toutes les données système qui sont spécifiées sur le site [System Center Cross Platform Solutions (Solutions multiplateforme pour System Center)](https://scx.codeplex.com/wikipage?title=xplatproviders) ;
- les fichiers journaux spécifiés par l’utilisateur.

Cette extension fonctionne avec les modèles de déploiement Classic et Resource Manager.


## Activer l’extension
Vous pouvez activer cette extension via le [portail Azure](https://portal.azure.com/#), Azure PowerShell ou les scripts de la CLI Azure.

Pour afficher et configurer les données sur les performances et système directement à partir du portail Azure, suivez [ces étapes du blog Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "URL du blog Windows"/).


Cet article se concentre sur la procédure permettant d’activer et de configurer l’extension à l’aide des commandes de la CLI Azure. Cela vous permet de lire et d’afficher les données directement à partir de la table de stockage.

Sachez que les méthodes de configuration décrites ici ne fonctionnent pas pour le portail Azure. Pour afficher et configurer les données sur les performances et système directement depuis le portail Azure, vous devez activer cette extension via le portail.


## Composants requis
- **Agent Microsoft Azure Linux version 2.0.6 ou plus**. Notez que la plupart des images de la galerie Linux de machines virtuelles Azure comprennent la version 2.0.6 ou ultérieure. Vous pouvez exécuter la commande **WAAgent -version** pour vérifier la version installée sur la machine virtuelle. Si la machine virtuelle exécute une version antérieure à 2.0.6, vous pouvez suivre ces [instructions (sur GitHub)](https://github.com/Azure/WALinuxAgent "instructions") pour la mettre à jour.

- **Interface de ligne de commande Azure**. Suivez [ce guide pour installer la CLI](../xplat-cli-install.md) afin de configurer l’environnement de CLI Azure sur votre machine. Une fois que la CLI Azure est installée, vous pouvez utiliser la commande **azure** de votre interface de ligne de commande (invite de ligne de commande, Terminal ou Bash) afin d’accéder aux commandes de la CLI Azure. Par exemple :
	- Exécutez la commande **azure vm extension set --help** pour obtenir une aide détaillée.
	- Exécutez la commande **azure login** pour vous connecter à Azure.
	- Exécutez la commande **azure vm list** pour répertorier l’ensemble des machines virtuelles disponibles sur Azure.
- Un compte de stockage dédié au stockage des données. Vous devez disposer d’un nom de compte de stockage préalablement créé et d’une clé d’accès pour charger les données dans votre stockage.


## Utiliser la commande de la CLI Azure pour activer l’extension de diagnostic Linux

### Scénario 1 Activer l’extension avec le jeu de données par défaut
Pour la version 2.0 et plus, les données collectées par défaut comprennent les éléments suivants :

- Toutes les informations de Rsyslog (y compris les journaux système, de sécurité et des applications)  
- Un ensemble principal de données système de base Notez que le jeu de données complet est décrit sur le site [System Center Cross Platform Solutions (Solutions multiplateforme pour System Center)](https://scx.codeplex.com/wikipage?title=xplatproviders). Si vous voulez activer des données supplémentaires, passez aux étapes des scénarios 2 et 3.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu suivant :

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Scénario 2 Personnaliser les mesures de surveillance des performances  
Cette section décrit comment personnaliser le tableau des données de diagnostic et de performance.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu décrit dans le scénario 1. Créez également un fichier nommé PublicConfig.json. Spécifiez les données spécifiques que vous souhaitez collecter.

Pour obtenir tous les fournisseurs et variables pris en charge, référencez le site [System Center Cross Platform Solutions (Solutions multiplateforme pour System Center)](https://scx.codeplex.com/wikipage?title=xplatproviders). Vous pouvez avoir plusieurs requêtes et les stocker dans plusieurs tables en ajoutant d’autres requêtes dans le script.

Par défaut, les données de Rsyslog sont toujours collectées.

    {
      	"perfCfg":
      	[
      	    {
      	        "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
      	        "table" : "LinuxMemory"
      	    }
      	]
    }


Étape 2. Exécutez la commande suivante : **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


###   Scénario 3 Charger vos propres fichiers journaux
Cette section explique comment collecter et télécharger des fichiers journaux spécifiques dans votre compte de stockage. Vous devez spécifier le chemin d’accès à votre fichier journal, ainsi que le nom de la table dans laquelle votre fichier journal doit être stocké. Vous pouvez créer plusieurs fichiers journaux en ajoutant plusieurs entrées fichier/table dans le script.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu décrit dans le scénario 1. Créez ensuite un autre fichier nommé PublicConfig.json, avec le contenu suivant :

    {
        "fileCfg" :
        [
            {
                "file" : "/var/log/mysql.err",
                "table" : "mysqlerr"
             }
        ]
    }


Étape 2. Exécutez **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions ’2.*’ --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


###   Scénario 4 Empêcher l’extension de collecter des journaux
Cette section explique comment empêcher l’extension de collecter des fichiers journaux. Notez que le processus de l’agent d’analyse sera toujours exécuté, même si cette reconfiguration est effectuée. Si vous souhaitez arrêter complètement le processus d’agent d’analyse, vous pouvez le faire en désactivant l’extension. La commande permettant de désactiver l’extension est la suivante : **azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'**.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu décrit dans le scénario 1. Créez un autre fichier appelé PublicConfig.json, avec le contenu suivant :

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Étape 2. Exécutez la commande suivante : **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.


## Passer en revue vos données
Les données de performance et de diagnostic sont stockées dans une table Azure Storage. Pour savoir comment accéder aux données de la table de stockage à l’aide des scripts de la CLI Azure, voir [Utilisation du stockage de tables Azure à partir de Ruby](../storage/storage-ruby-how-to-use-table-storage.md).

Vous pouvez aussi utiliser les outils d’interface utilisateur suivants pour accéder aux données :

1. Explorateur de serveurs Visual Studio. Accédez à votre compte de stockage. Après environ 5 minutes d’exécution de la machine virtuelle, quatre tables par défaut doivent s’afficher : « LinuxCpu », « LinuxDisk », « LinuxMemory » et « Linuxsyslog ». Double-cliquez sur le nom de chaque table pour afficher les données.

2. [Explorateur de stockage Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

![image](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

Si vous avez activé fileCfg ou perfCfg (comme décrit dans les scénarios 2 et 3), vous pouvez utiliser l’Explorateur de serveurs Visual Studio et l’Explorateur de stockage Microsoft Azure pour afficher les informations autres que les données par défaut.

## Problèmes connus
- Dans la version 2.0 de l’extension de diagnostic Linux, les informations de Rsyslog et du fichier journal spécifié par l’utilisateur sont accessibles uniquement via un script.

<!---HONumber=AcomDC_0615_2016-->