---
title: "Utilisation de bases de données SQL sur Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer des bases de données SQL en tant que service sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources SQL Server"
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
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 018d556d52aa1a1f436460d9811c43f9b45bd440
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---

# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données SQL sur Microsoft Azure Stack


Utiliser l’adaptateur de fournisseur de ressources SQL Server pour exposer des bases de données SQL en tant que service d’Azure Stack. Après avoir installé le fournisseur de ressources et l’avoir connecté à une ou plusieurs instances SQL Server, vous et vos utilisateurs pouvez créer des bases de données pour des applications natives cloud, des sites web basés sur SQL et des charges de travail basées sur SQL sans avoir à approvisionner une machine virtuelle qui héberge SQL Server à chaque fois.

Le fournisseur de ressources ne prend pas en charge toutes les fonctionnalités de gestion de base de données d’[Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Par exemple, les pools de bases de données élastiques et la possibilité de mettre à l’échelle les performances de base de données ne sont pas pris en charge. L’API n’est pas compatible avec SQL Database.


## <a name="sql-server-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressource SQL Server
Le fournisseur de ressources n’est pas basé sur Azure SQL Database et n’offre pas toutes ses fonctionnalités de gestion de base de données. Par exemple, les pools de bases de données élastiques et la possibilité de ralentir ou d’augmenter automatiquement les performances de la base de données ne sont pas disponibles. Toutefois, le fournisseur de ressources prend en charge des opérations CRUD (créer, lire, mettre à jour et supprimer) similaires.

Le fournisseur de ressources est constitué de trois composants :

- **La machine virtuelle d’adaptateur de fournisseur de ressources SQL**, qui est une machine virtuelle Windows exécutant les services de fournisseur.
- **Le fournisseur de ressources proprement dit**, qui traite les demandes d’approvisionnement et expose les ressources de base de données.
- **Les serveurs qui hébergent SQL Server**, qui offrent de la capacité pour les bases de données, et qui sont appelés « serveurs d’hébergement ». 

Cette version ne crée plus d’instance SQL. Vous devez en créer une (ou plusieurs) et/ou fournir un accès à des instances SQL externes. Un certain nombre d’options sont disponibles, notamment des modèles dans la [galerie des modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) et des éléments de la Place de marché. 

>[!NOTE]
Si vous avez téléchargé des éléments SQL de la Place de marché, pensez à télécharger également l’extension IaaS de SQL, sans quoi le déploiement de ces éléments échouera.


## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Si ce n’est pas déjà fait, inscrivez votre kit de développement et téléchargez l’image Windows Server 2016 EVAL disponible par le biais de la Gestion de la Place de marché. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Le runtime .NET 3.5 n’est plus nécessaire.

2. [Téléchargez le fichier binaire du fournisseur de ressources SQL](https://aka.ms/azurestacksqlrp) et extrayez-le sur l’hôte du kit de développement.

3. Connectez-vous à l’hôte du kit de développement et extrayez le fichier de programme d’installation du fournisseur de ressources SQL dans un répertoire temporaire.

4. Le certificat racine Azure Stack est récupéré et un certificat auto-signé est créé dans le cadre de ce processus. 

    __Facultatif :__ Si vous devez fournir vos propres certificats, préparez-les et copiez-les dans un répertoire local si vous souhaitez personnaliser les certificats (passés au script d’installation). Vous avez besoin des certificats suivants :

    a. Un certificat générique pour *.dbadapter.\<région\>.\<fqdn_externe\>. Ce certificat doit être approuvé, par exemple émis par une autorité de certification (autrement dit, la chaîne d’approbation doit exister sans qu’aucun certificat intermédiaire ne soit nécessaire). (Vous pouvez utiliser un certificat de site unique avec le nom de machine virtuelle explicite que vous fournissez lors de l’installation.)

    b. Le certificat racine utilisé par Azure Resource Manager pour votre instance d’Azure Stack. S’il est introuvable, le certificat racine est récupéré.


5. Ouvrez une **nouvelle** console PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers. Utilisez une nouvelle fenêtre pour éviter les problèmes qui peuvent se produire à cause des modules PowerShell incorrects déjà chargés sur le système.

6. Si vous avez installé des versions des modules PowerShell AzureRm ou AzureStack autres que 1.2.9 ou 1.2.10, vous devrez les supprimer ou l’installation ne sera pas effectuée. Cela inclut les versions 1.3 ou ultérieures.

7. Exécutez le script DeploySqlProvider.ps1 avec les paramètres répertoriés ci-dessous.

Le script effectue les étapes suivantes :

* Si nécessaire, téléchargement d’une version compatible d’Azure PowerShell.
* Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
* Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL par le biais de la galerie.
* Déploiement d’une machine virtuelle à l’aide de l’image de Windows Server 2016 créée à l’étape 1 et installation du fournisseur de ressources.
* Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
* Inscription de votre fournisseur de ressources auprès du Azure Resource Manager local (Locataire et Administrateur).

> [!NOTE]
> Si l’installation prend plus de 90 minutes, elle risque d’échouer et un message d’erreur s’affiche à l’écran ainsi que dans le fichier journal, mais une nouvelle tentative de déploiement est effectuée à partir de l’étape ayant échoué. Les systèmes qui ne répondent pas aux spécifications recommandées en matière de mémoire et de noyau risquent de ne pas pouvoir déployer le fournisseur de ressources SQL.
>

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
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeploySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "SqlRPRG" -VmName "SqlVM" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass
 ```

### <a name="deploysqlproviderps1-parameters"></a>Paramètres de DeploySqlProvider.ps1
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation des paramètres échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **DirectoryTenantID** | ID de répertoire AD FS ou Azure (GUID). | _obligatoire_ |
| **AzCredential** | Fournissez les informations d’identification du compte d’administration de service Azure Stack. Vous devez utiliser les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack). | _obligatoire_ |
| **VMLocalCredential** | Définissez les informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. Ce mot de passe est également utilisé pour le compte **sa** (administrateur système) SQL. | _obligatoire_ |
| **ResourceGroupName** | Définissez un nom pour un groupe de ressources dans lequel les éléments créés par ce script seront stockés. Par exemple, *SqlRPRG*. |  _obligatoire_ |
| **VmName** | Définissez le nom de la machine virtuelle sur laquelle installer le fournisseur de ressources. Par exemple, *SqlVM*. |  _obligatoire_ |
| **DependencyFilesLocalPath** | Vos fichiers de certificats doivent également être placés dans ce répertoire. | _facultatif_ |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _obligatoire_ |
| **MaxRetryCount** | Définissez le nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Définissez le délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprimez le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

> [!NOTE]
>  Une fois le script d’installation terminé, vous devez actualiser le portail pour afficher le panneau d’administration.


1. Sur le bureau de la machine virtuelle Console, cliquez sur **Portail Microsoft Azure Stack** et connectez-vous au portail en tant qu’administrateur de services.

2. Vérifiez que le déploiement a réussi. Cliquez sur **Groupes de ressources** &gt; et sur le groupe de ressources que vous avez utilisé, puis vérifiez que la partie principale du tableau (partie supérieure) indique **_date_ (Réussi)**.

      ![Vérifier le déploiement du fournisseur de ressources SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="provide-capacity-by-connecting-to-a-hosting-sql-server"></a>Fournir de la capacité par le biais d’une connexion à un serveur SQL d’hébergement

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Créez une machine virtuelle SQL, sauf si vous en avez déjà une disponible. La Gestion de la Place de marché offre certaines options pour le déploiement de machines virtuelles SQL.

3. Cliquez sur **Fournisseurs de ressources** &gt; **SQLAdapter** &gt; **Serveurs d’hébergement** &gt; **+Ajouter**.

    Dans le panneau **Serveurs d’hébergement SQL**, vous pouvez connecter le fournisseur de ressources SQL Server à des instances réelles de SQL Server qui font office de serveur principal du fournisseur de ressources.

    ![Serveurs d’hébergement](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.PNG)

4. Remplissez le formulaire avec les détails de connexion de votre instance de SQL Server.

    ![Nouveau serveur d’hébergement](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.PNG)

    > [!NOTE]
    > Tant que l’instance SQL est accessible par le locataire et l’administrateur Azure Resource Manager, elle peut être placée sous contrôle du fournisseur de ressources. L’instance SQL __doit__ être allouée exclusivement au fournisseur de ressources.

5. À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour différencier les offres de service. Par exemple, vous pouvez avoir une instance SQL Enterprise qui fournit une capacité de base de données et une sauvegarde automatique, réserver des serveurs à hauts niveaux de performances pour les différents services, etc. Le nom de la référence doit refléter les propriétés afin que les locataires puissent placer correctement leurs bases de données, et tous les serveurs d’hébergement d’une référence doivent avoir les mêmes fonctionnalités.

    Exemple :

    ![Références (SKU)](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer une base de données tant que la référence n’a pas été créée.


## <a name="create-your-first-sql-database-to-test-your-deployment"></a>Créer votre première base de données SQL pour tester votre déploiement

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Cliquez sur **+ Nouveau** &gt; **Données + stockage** &gt; **Base de données SQL Server (préversion)** &gt; **Ajouter**.

3. Remplissez le formulaire avec les détails sur la base de données, notamment le **Nom de la base de données** et une **Taille maximale**, puis changez les autres paramètres si nécessaire. Vous êtes invité à choisir une référence (SKU) pour votre base de données. À mesure que des serveurs d’hébergement sont ajoutés, une référence (SKU) leur est attribuée et des bases de données sont créées dans ce pool de serveurs d’hébergement qui constituent la référence.

    ![Nouvelle base de données](./media/azure-stack-sql-rp-deploy/newsqldb.png)


4. Renseignez les paramètres de connexion : **Connexion à la base de données** et **Mot de passe**. Il s’agit d’informations d’identification d’authentification SQL qui sont créées pour votre accès à cette base de données uniquement. Le nom d’utilisateur de connexion doit être globalement unique. Créez un paramètre de connexion ou sélectionnez-en un qui existe déjà. Vous pouvez réutiliser les paramètres de connexion d’autres bases de données utilisant la même référence (SKU).

    ![Créer une connexion de base de données](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Envoyez le formulaire, puis attendez que le déploiement se termine.

    Dans le panneau qui s’affiche, notez le champ « Chaîne de connexion ». Vous pouvez utiliser cette chaîne dans toute application qui nécessite un accès à SQL Server (par exemple, une application web) dans Azure Stack.

    ![Récupérer la chaîne de connexion](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="add-capacity"></a>Ajouter de la capacité

Ajoutez de la capacité en ajoutant des hôtes SQL supplémentaires dans le portail Azure Stack et en les associant à une référence (SKU) appropriée. Si vous voulez utiliser une autre instance SQL que celle installée sur la machine virtuelle du fournisseur, cliquez sur **Fournisseurs de ressources** &gt; **SQLAdapter** &gt; **Serveurs d’hébergement SQL** &gt; **+Ajouter**.

## <a name="making-sql-databases-available-to-tenants"></a>Mise à disposition des bases de données SQL pour les locataires

Créez des plans et des offres pour mettre des bases de données SQL à la disposition de locataires. Vous devez créer un plan, ajouter le service Microsoft.SqlAdapter au plan, puis ajouter un Quota existant ou en créer un. Si vous créez un quota, vous pouvez spécifier la capacité pour autoriser le locataire.
    ![Créer des plans et des offres pour inclure des bases de données](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="tenant-usage-of-the-resource-provider"></a>Utilisation du locataire du fournisseur de ressources

Les bases de données libre-service sont fournies par le biais de l’expérience du portail du locataire.

## <a name="removing-the-sql-adapter-resource-provider"></a>Suppression du fournisseur de ressources de l’adaptateur SQL

Pour supprimer le fournisseur de ressources, il est essentiel de commencer par supprimer toutes les dépendances.

1. Vérifiez que vous avez le package de déploiement d’origine que vous avez téléchargé pour cette version du fournisseur de ressources.

2. Toutes les bases de données de locataire doivent être supprimées du fournisseur de ressources (cela ne supprime pas les données). Cette opération doit être effectuée par les locataires eux-mêmes.

3. L’administrateur doit supprimer les serveurs d’hébergement de l’adaptateur SQL.

4. L’administrateur doit supprimer tous les plans qui référencent l’adaptateur SQL.

5. L’administrateur doit supprimer toutes les références (SKU) et tous les quotas associés à l’adaptateur SQL.

6. Réexécutez le script de déploiement avec le paramètre -Uninstall, les points de terminaison Azure Resource Manager, DirectoryTenantID et les informations d’identification du compte d’administrateur de service.



## <a name="next-steps"></a>Étapes suivantes


Essayez d’autres [services PaaS](azure-stack-tools-paas-services.md) comme le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) et le [fournisseur de ressources App Services](azure-stack-app-service-overview.md).

