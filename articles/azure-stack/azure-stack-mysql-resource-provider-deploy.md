---
title: "Utiliser des bases de données MySQL en tant que PaaS sur Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer le fournisseur de ressources MySQL et fournir des bases de données MySQL en tant que service sur Azure Stack."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: JeffGo
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 45c7edcc645e82107805b3e62d87655a830fb22a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack


Vous pouvez déployer un fournisseur de ressources MySQL sur Azure Stack. Après avoir déployé le fournisseur de ressources, vous pouvez créer des serveurs et des bases de données MySQL avec des modèles de déploiement Azure Resource Manager et fournir des bases de données MySQL en tant que service. Les bases de données MySQL, qui sont courantes sur les sites web, prennent en charge de nombreuses plateformes de site web. Par exemple, après avoir déployé le fournisseur de ressources, vous pouvez créer des sites web WordPress à partir du module complémentaire PaaS (Platform as a Service) Azure Web Apps pour Azure Stack.

Pour déployer le fournisseur MySQL sur un système qui n’a pas accès à Internet, vous pouvez copier le fichier [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) sur un partage local et fournir ce nom de partage quand vous y êtes invité (voir ci-dessous). Vous devez également installer les modules PowerShell Azure et Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources MySQL Server

Le fournisseur de ressources est constitué de trois composants :

- **La machine virtuelle d’adaptateur de fournisseur de ressources MySQL**, qui est une machine virtuelle Windows exécutant les services de fournisseur.
- **Le fournisseur de ressources proprement dit**, qui traite les demandes d’approvisionnement et expose les ressources de base de données.
- **Les serveurs qui hébergent MySQL Server**, qui fournissent de la capacité pour les bases de données, appelés serveurs d’hébergement. 

Cette version ne crée plus d’instance de MySQL. Vous devez les créer et/ou fournir un accès à des instances SQL externes. Vous pouvez accéder à la [galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) pour obtenir un exemple de modèle capable de créer un serveur MySQL pour vous, ou télécharger et déployer un serveur MySQL à partir de la Place de marché.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Si ce n’est déjà fait, inscrivez votre Kit de développement et téléchargez l’image Windows Server 2016 Datacenter - Eval à partir de Gestion dans la Place de marché. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image).

2. [Téléchargez le fichier binaire du fournisseur de ressources MySQL](https://aka.ms/azurestackmysqlrp) et extrayez-le sur l’hôte du Kit de développement.

3. Connectez-vous à l’hôte du Kit de développement et extrayez le fichier du programme d’installation du fournisseur de ressources MySQL dans un répertoire temporaire.

4. Le certificat racine Azure Stack est récupéré et un certificat auto-signé est créé dans le cadre de ce processus. 

    __Facultatif :__ Si vous devez fournir vos propres certificats, préparez-les et copiez-les dans un répertoire local si vous souhaitez personnaliser les certificats (passés au script d’installation). Vous avez besoin des éléments suivants :

    a. Un certificat générique pour *.dbadapter.\<région\>.\<fqdn_externe\>. Ce certificat doit être approuvé, par exemple émis par une autorité de certification (autrement dit, la chaîne d’approbation doit exister sans qu’aucun certificat intermédiaire ne soit nécessaire). (Vous pouvez utiliser un certificat de site unique avec le nom de machine virtuelle explicite que vous fournissez lors de l’installation.)

    b. Le certificat racine utilisé par Azure Resource Manager pour votre instance d’Azure Stack. S’il est introuvable, le certificat racine est récupéré.

5. Ouvrez une **nouvelle** console PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers. Utilisez une nouvelle fenêtre pour éviter les problèmes qui peuvent se produire à cause des modules PowerShell incorrects déjà chargés sur le système.

6. Si vous avez installé des versions des modules PowerShell AzureRm ou AzureStack autres que 1.2.9 ou 1.2.10, vous devrez les supprimer ou l’installation ne sera pas effectuée. Cela inclut les versions 1.3 ou ultérieures.

7. Exécutez DeployMySqlProvider.ps1.

Ce script effectue les étapes suivantes :

* Si nécessaire, téléchargement d’une version compatible d’Azure PowerShell.
* Téléchargement du fichier binaire de connecteur MySQL (peut être fourni en mode hors connexion).
* Chargement du certificat et de tous les autres artefacts sur un compte de stockage Azure Stack.
* Publication des packages de la galerie afin que vous puissiez déployer des bases de données MySQL par le biais de la galerie.
* Déploiement d’une machine virtuelle qui héberge votre fournisseur de ressources.
* Inscription d’un enregistrement DNS local mappé à votre machine virtuelle de fournisseur de ressources.
* Inscription de votre fournisseur de ressources auprès de l’instance Azure Resource Manager locale.

Spécifiez au moins les paramètres obligatoires sur la ligne de commande ou, si vous exécutez sans paramètres, vous êtes invité à les entrer. 

Voici un exemple que vous pouvez exécuter à partir de l’invite PowerShell (mais changez les points de terminaison du portail et les informations du compte si nécessaire) :


```
# Install the AzureRM.Bootstrapper module
Install-Module -Name AzureRm.BootStrapper -Force

# Installs and imports the API Version Profile required by Azure Stack into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.10 -Force

# Download the Azure Stack Tools from GitHub and set the environment
cd c:\
Invoke-Webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
Expand-Archive master.zip -DestinationPath . -Force

# This endpoint may be different for your installation
Import-Module C:\AzureStack-Tools-master\Connect\AzureStack.Connect.psm1
Add-AzureRmEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external" 

# For AAD, use the following
$tenantID = Get-AzsDirectoryTenantID -AADTenantName "<your directory name>" -EnvironmentName AzureStackAdmin

# For ADFS, replace the previous line with
# $tenantID = Get-AzsDirectoryTenantID -ADFS -EnvironmentName AzureStackAdmin

$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeployMySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "MySqlRG" -VmName "MySQLRP" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath
 ```

### <a name="deploymysqlproviderps1-parameters"></a>Paramètres de DeployMySqlProvider.ps1

Vous pouvez spécifier ces paramètres sur la ligne de commande. Si vous ne le faites pas, ou si la validation des paramètres échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **DirectoryTenantID** | ID de répertoire AD FS ou Azure (guid) | _obligatoire_ |
| **ArmEndpoint** | Point de terminaison Azure Resource Manager d’administration Azure Stack | _obligatoire_ |
| **TenantArmEndpoint** | Point de terminaison Azure Resource Manager de locataire Azure Stack | _obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack (utilisez le même compte que celui utilisé pour le déploiement d’Azure Stack) | _obligatoire_ |
| **VMLocalCredential** | Compte d’administrateur local de la machine virtuelle du fournisseur de ressources MySQL | _obligatoire_ |
| **ResourceGroupName** | Groupe de ressources pour les éléments créés par ce script |  _obligatoire_ |
| **VmName** | Nom de la machine virtuelle contenant le fournisseur de ressources |  _obligatoire_ |
| **AcceptLicense** | Ignore l’invite demandant d’accepter la licence GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |
| **DependencyFilesLocalPath** | Chemin vers un partage local contenant [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Si vous les fournissez, les fichiers de certificats doivent être placés dans ce répertoire. | _facultatif_ |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _obligatoire_ |
| **MaxRetryCount** | Chaque opération est retentée en cas d’échec | 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes | 120 |
| **Désinstaller** | Supprimer le fournisseur de ressources | Non |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non |


En fonction de la vitesse de téléchargement et des performances système, l’installation peut durer entre 20 minutes et plusieurs heures. Vous devez actualiser le portail d’administration si le panneau MySQLAdapter n’est pas disponible.

> [!NOTE]
> Si l’installation prend plus de 90 minutes, elle risque d’échouer et vous verrez un message d’erreur à l’écran et dans le fichier journal. Une nouvelle tentative de déploiement est effectuée à partir de l’étape ayant échoué. Les systèmes qui ne répondent pas aux spécifications recommandées en matière de mémoire et de noyau risquent de ne pas pouvoir déployer le fournisseur de ressources MySQL.


## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Fournir de la capacité en se connectant à un serveur d’hébergement MySQL

1. Connectez-vous au portail Azure Stack en tant qu’administrateur de service.

2. Cliquez sur **Fournisseurs de ressources** &gt; **MySQLAdapter** &gt; **Serveurs d’hébergement** &gt; **+ Ajouter**.

    Dans le panneau **Serveurs d’hébergement MySQL**, vous pouvez connecter le fournisseur de ressources MySQL Server à des instances réelles de MySQL Server qui font office de backend du fournisseur de ressources.

    ![Serveurs d’hébergement](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Remplissez le formulaire avec les détails de connexion de votre instance de MySQL Server. Fournissez le nom de domaine complet (FQDN) ou une adresse IPv4 valide, et pas le nom court de machine virtuelle. Cette installation ne fournit plus d’instance de MySQL par défaut. La taille fournie aide le fournisseur de ressources à gérer la capacité de la base de données. Elle doit être proche de la capacité physique du serveur de base de données.

    > [!NOTE]
    > Tant que l’instance MySQL est accessible par le locataire et l’administrateur Azure Resource Manager, elle peut être placée sous contrôle du fournisseur de ressources. L’instance de MySQL __doit__ être allouée exclusivement au fournisseur de ressources.

4. À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour pouvoir différencier les offres de service. Par exemple, vous pouvez avoir une instance d’entreprise qui fournit la capacité de base de données et la sauvegarde automatique, réserver les serveurs hautes performances pour les différents services, et ainsi de suite. Le nom de la référence doit refléter les propriétés afin que les locataires puissent placer correctement leurs bases de données, et tous les serveurs d’hébergement d’une référence doivent avoir les mêmes fonctionnalités.


>[!NOTE]
Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer une base de données tant que la référence n’a pas été créée.


## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Créer votre première base de données MySQL pour tester votre déploiement


1. Connectez-vous au portail Azure Stack en tant qu’administrateur de service.

2. Cliquez sur le bouton **+ Nouveau** &gt; **Données + stockage** &gt; **Base de données MySQL (préversion)**.

3. Remplissez le formulaire avec les détails de la base de données.

    ![Créer une base de données MySQL test](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sélectionnez une référence (SKU).

    ![Sélectionner une référence](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Créez un paramètre de connexion. Vous pouvez réutiliser un paramètre de connexion ou en créer un nouveau. Ce paramètre contient le nom d’utilisateur et le mot de passe de la base de données.

    La chaîne de connexion inclut le nom réel du serveur de base de données. Copiez-le à partir du portail.

    ![Obtenir la chaîne de connexion pour la base de données MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La longueur des noms d’utilisateurs ne doit pas dépasser 32 caractères avec MySQL 5.7, ou 16 caractères dans les éditions antérieures. Il s’agit d’une limitation des implémentations de MySQL.


## <a name="add-capacity"></a>Ajouter de la capacité

Augmentez la capacité en ajoutant des serveurs MySQL dans le portail Azure Stack. Si vous souhaitez utiliser une autre instance de MySQL, cliquez sur **Fournisseurs de ressources** &gt; **MySQLAdapter** &gt; **Serveurs d’hébergement MySQL** &gt; **+Ajouter**.


## <a name="making-mysql-databases-available-to-tenants"></a>Mise à disposition des bases de données MySQL pour les locataires
Créez des plans et des offres pour mettre les bases de données MySQL à disposition des locataires. Ajoutez le service Microsoft.MySqlAdapter, ajoutez un quota, et ainsi de suite.

![Créer des plans et des offres pour inclure des bases de données](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>Suppression du fournisseur de ressources de l’adaptateur MySQL

Pour supprimer le fournisseur de ressources, il est essentiel de supprimer d’abord toutes les dépendances.

1. Vérifiez que vous avez le package de déploiement d’origine que vous avez téléchargé pour cette version du fournisseur de ressources.

2. Toutes les bases de données de locataire doivent être supprimées du fournisseur de ressources (cela ne supprime pas les données). Cette opération doit être effectuée par les locataires eux-mêmes.

3. Les locataires doivent annuler l’inscription à partir de l’espace de noms.

4. L’administrateur doit supprimer les serveurs d’hébergement de l’adaptateur MySQL

5. L’administrateur doit supprimer tous les plans qui référencent l’adaptateur MySQL.

6. L’administrateur doit supprimer tous les quotas associés à l’adaptateur MySQL.

7. Réexécutez le script de déploiement avec le paramètre -Uninstall, les points de terminaison Azure Resource Manager, DirectoryTenantID et les informations d’identification du compte d’administrateur de service.




## <a name="next-steps"></a>Étapes suivantes


Essayez d’autres [services PaaS](azure-stack-tools-paas-services.md) comme le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) et le [fournisseur de ressources App Services](azure-stack-app-service-overview.md).

