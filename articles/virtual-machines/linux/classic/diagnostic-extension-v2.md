---
title: "Analyse d’une machine virtuelle Linux avec une extension de machine virtuelle | Microsoft Docs"
description: "Découvrez comment utiliser l’extension de diagnostic Linux pour surveiller les données de performances et de diagnostic d’une machine virtuelle Linux dans Azure."
services: virtual-machines-linux
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b8c6e2e22d8478b6e92e7b7942f15d37a840fed3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux

Ce document décrit la version 2.3 de l’extension de diagnostic Linux.

> [!IMPORTANT]
> Cette version est dépréciée et sa publication peut cesser au-delà du 30 juin 2018. Elle a été remplacée par la version 3.0. Pour plus d’informations, consultez la [documentation de la version 3.0 de l’extension de diagnostic Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Introduction

(**Remarque** : l’extension de diagnostic Linux est une extension open source sur [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) , où les informations les plus récentes sur l’extension sont publiées en priorité. Vous voudrez peut-être consulter la [page GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) en premier.)

L’extension de diagnostic Linux aide l’utilisateur à surveiller les machines virtuelles Linux s’exécutant sur Microsoft Azure. Elle présente les fonctionnalités suivantes :

* Elle collecte et télécharge les informations sur les performances du système depuis la machine virtuelle Linux vers la table de stockage de l’utilisateur, y compris les informations de diagnostic et de syslog.
* Elle permet aux utilisateurs de personnaliser les mesures de données collectées et chargées.
* Elle permet aux utilisateurs de télécharger les fichiers journaux spécifiés dans la table de stockage désignée.

Dans la version 2.3 actuelle, les données comprennent :

* tous les journaux Rsyslog de Linux, y compris les journaux système, de sécurité et d’applications ;
* toutes les données système qui sont spécifiées sur le site [System Center Cross Platform Solutions (Solutions multiplateforme pour System Center)](https://scx.codeplex.com/wikipage?title=xplatproviders);
* les fichiers journaux spécifiés par l’utilisateur.

Cette extension fonctionne avec les modèles de déploiement Classic et Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Version actuelle de l’extension et obsolescence des anciennes versions

La dernière version de l’extension est **2.3**. Les **anciennes versions (2.0, 2.1 et 2.2) seront dépréciées et leur publication cessera à la fin de cette année (2017)**. Si vous avez installé l’extension de diagnostic Linux avec la mise à niveau automatique de version mineure désactivée, nous vous conseillons fortement de désinstaller cette extension et de la réinstaller en activant la mise à niveau automatique de version mineure. Sur des machines virtuelles classiques (ASM), vous pouvez obtenir cela en spécifiant « 2.* » comme version si vous installez l’extension via l’interface CLI Azure XPLAT ou Powershell. Sur les machines virtuelles ARM, vous pouvez y parvenir en incluant « "autoUpgradeMinorVersion": true » dans le modèle de déploiement de machine virtuelle. En outre, toute nouvelle installation de l’extension doit s’effectuer avec l’option de mise à niveau automatique de version mineure activée.

## <a name="enable-the-extension"></a>Activer l’extension

Vous pouvez activer cette extension via le [portail Azure](https://portal.azure.com/#), Azure PowerShell ou les scripts de l’interface CLI Azure.

Pour afficher et configurer les données système et de performances directement à partir du portail Azure, suivez ces [étapes sur le blog d’Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Cet article se concentre sur la procédure permettant d’activer et de configurer l’extension à l’aide des commandes de la CLI Azure. Cela vous permet de lire et d’afficher les données directement à partir de la table de stockage.

Sachez que les méthodes de configuration décrites ici ne fonctionnent pas pour le portail Azure. Pour afficher et configurer les données sur les performances et système directement depuis le portail Azure, vous devez activer cette extension via le portail.

## <a name="prerequisites"></a>Composants requis

* **Agent Microsoft Azure Linux version 2.0.6 ou ultérieure**.

  Notez que la plupart des images de la galerie Linux de machines virtuelles Azure comprennent la version 2.0.6 ou ultérieure. Vous pouvez exécuter **WAAgent -version** pour vérifier la version installée sur la machine virtuelle. Si la machine virtuelle exécute une version antérieure à 2.0.6, vous pouvez suivre [ces instructions sur GitHub](https://github.com/Azure/WALinuxAgent "instructions") pour la mettre à jour.
* **Interface de ligne de commande Azure**. Suivez [ce guide pour installer l’interface CLI](../../../cli-install-nodejs.md) afin de configurer l’environnement CLI Azure sur votre machine. Une fois que l’interface CLI Azure est installée, vous pouvez utiliser la commande **azure** de votre interface de ligne de commande (invite de commande, Terminal ou Bash) pour accéder aux commandes CLI Azure. Par exemple :

  * Exécutez **azure vm extension set --help** pour obtenir une aide détaillée.
  * Exécutez **azure login** pour vous connecter à Azure.
  * Exécutez **azure vm list** pour répertorier l’ensemble des machines virtuelles disponibles sur Azure.
* Un compte de stockage dédié au stockage des données. Vous devez disposer d’un nom de compte de stockage préalablement créé et d’une clé d’accès pour charger les données dans votre stockage.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Utiliser la commande de la CLI Azure pour activer l’extension de diagnostic Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scénario 1 Activer l’extension avec le jeu de données par défaut

Dans la version 2.3 ou ultérieure, les données collectées par défaut comprennent les éléments suivants :

* Toutes les informations de Rsyslog (y compris les journaux système, de sécurité et des applications)  
* Un ensemble principal de données système de base Notez que le jeu de données complet est décrit sur le site [System Center Cross Platform Solutions (Solutions multiplateforme pour System Center)](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Si vous voulez activer des données supplémentaires, passez aux étapes des scénarios 2 et 3.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu suivant :

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Étape 2. Exécutez **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scénario 2 Personnaliser les mesures de surveillance des performances

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


Étape 2. Exécutez **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scénario 3 Charger vos propres fichiers journaux

Cette section explique comment collecter et télécharger des fichiers journaux spécifiques dans votre compte de stockage. Vous devez spécifier le chemin d’accès à votre fichier journal, ainsi que le nom de la table dans laquelle votre fichier journal doit être stocké. Vous pouvez créer plusieurs fichiers journaux en ajoutant plusieurs entrées fichier/table dans le script.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu décrit dans le scénario 1. Créez ensuite un autre fichier nommé PublicConfig.json, avec le contenu suivant :

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Étape 2. Exécutez `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Notez qu’avec ce paramètre sur les versions d’extension antérieure à la version 2.3, tous les journaux écrits sur `/var/log/mysql.err` peuvent être dupliqués dans `/var/log/syslog` (ou `/var/log/messages` selon le distributeur Linux) également. Si vous souhaitez éviter ce doublon d’enregistrement, vous pouvez exclure la journalisation des journaux `local6` dans votre configuration rsyslog. Cela dépend de la distribution Linux, mais sur un système Ubuntu 14.04, le fichier à modifier est `/etc/rsyslog.d/50-default.conf` et vous pouvez remplacer la ligne `*.*;auth,authpriv.none -/var/log/syslog` par `*.*;auth,authpriv,local6.none -/var/log/syslog`. Ce problème est résolu dans la dernière version du correctif logiciel 2.3 (2.3.9007). Donc si vous avez la version d’extension 2.3, ce problème ne doit pas se produire. Si le problème existe toujours même après le redémarrage de votre machine virtuelle, veuillez nous contacter et nous aider à comprendre la raison pour laquelle la dernière version du correctif logiciel n’est pas installée automatiquement.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scénario 4 Empêcher l’extension de collecter des journaux

Cette section explique comment empêcher l’extension de collecter des fichiers journaux. Notez que le processus de l’agent d’analyse sera toujours exécuté, même si cette reconfiguration est effectuée. Si vous souhaitez arrêter complètement le processus d’agent d’analyse, vous pouvez le faire en désactivant l’extension. La commande pour désactiver l’extension est `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Étape 1. Créez un fichier nommé PrivateConfig.json, avec le contenu décrit dans le scénario 1. Créez un autre fichier appelé PublicConfig.json, avec le contenu suivant :

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Étape 2. Exécutez **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Passer en revue vos données

Les données de performance et de diagnostic sont stockées dans une table Azure Storage. Pour savoir comment accéder aux données de la table de stockage à l’aide des scripts de la CLI Azure, consultez la page [Utilisation du stockage de tables Azure à partir de Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) .

Vous pouvez aussi utiliser les outils d’interface utilisateur suivants pour accéder aux données :

1. Explorateur de serveurs Visual Studio. Accédez à votre compte de stockage. Après environ 5 minutes d’exécution de la machine virtuelle, quatre tables par défaut doivent s’afficher : « LinuxCpu », « LinuxDisk », « LinuxMemory » et « Linuxsyslog ». Double-cliquez sur le nom de chaque table pour afficher les données.
1. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer");

![image](./media/diagnostic-extension/no1.png)

Si vous avez activé fileCfg ou perfCfg (comme décrit dans les scénarios 2 et 3), vous pouvez utiliser l’Explorateur de serveurs Visual Studio et l’Explorateur de stockage Microsoft Azure pour afficher les informations autres que les données par défaut.

## <a name="known-issues"></a>Problèmes connus

* Les informations de Rsyslog et le fichier journal spécifié par l’utilisateur sont accessibles seulement via un script.

