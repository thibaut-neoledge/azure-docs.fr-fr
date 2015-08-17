<properties
   pageTitle="Considérations relatives à Operations Manager avec Operational Insights"
   description="Si vous utilisez Microsoft Azure Operational Insights avec Operations Manager, votre configuration repose sur une distribution de groupes d'administration et d'agents d'Operations Manager pour collecter et envoyer des données vers le service Operational Insights pour l'analyse"
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
   ms.date="07/02/2015"
   ms.author="banders" />

# Considérations relatives à Operations Manager avec Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Si vous utilisez Microsoft Azure Operational Insights avec Operations Manager, votre configuration repose sur une distribution de groupes d'administration et d'agents d'Operations Manager pour collecter et envoyer des données vers le service Operational Insights pour l'analyse. Toutefois, si vous utilisez des agents qui se connectent directement au service Web, Operations Manager est inutile. Prenez en compte les éléments suivants lorsque vous utilisez Operational Insights avec Operations Manager.

Vous devrez également spécifier que les informations d'identification des charges de travail analysaient mon Operations Manager sur Operational Insights.

## Configuration requise et fonctions logicielles d'Operational Insights

Operational Insights se compose d'un service Web dans le cloud et de deux agents qui se connectent directement au service Web ou aux agents et groupes d'administration Operations Manager qui gèrent les ordinateurs de votre environnement.

Avant de sélectionner les groupes d'administration (pour gérer les agents et envoyer des données vers le service Operational Insights) et les agents d'Operations Manager (pour collecter des données), assurez-vous de bien comprendre les éléments suivants :

- L'agent Operations Manager est installé sur tout serveur à partir duquel vous souhaitez collecter et analyser des données.

- Le groupe d'administration Operations Manager transfère les données des agents au service Web Operational Insights. Il n'analyse pas les données.

- Le groupe d'administration Operations Manager doit avoir accès à Internet pour télécharger les données vers le service Web.

- Pour de meilleurs résultats, n'installez pas le serveur d'administration Operations Manager sur un ordinateur qui est également un contrôleur de domaine.

- Un agent Operations Manager doit avoir une connexion réseau au groupe d'administration Operations Manager afin de transférer les données.

Operational Insights peut utiliser le service de contrôle d'intégrité de System Center pour collecter et analyser les données. Operations Manager dépend du service de contrôle d'intégrité de System Center. Lorsque vous affichez les programmes installés sur votre serveur, vous verrez le logiciel d'agent de System Center Operations Manager, notamment dans Ajout/Suppression de programmes. Ne supprimez pas ce dernier, car Operational Insights en dépend. Si vous supprimez le logiciel de l'agent Operations Manager, Operational Insights ne fonctionnera plus.

## Coexistence avec Operations Manager

Lorsque vous utilisez Operations Manager, Operational Insights est uniquement pris en charge avec l'agent Operations Manager dans System Center Operations Manager 2012 R2 ou System Center Operations Manager 2012 SP1. Il n'est pas pris en charge avec les versions précédentes de System Center Operations Manager. Étant donné que l'agent Operations Manager est utilisé pour collecter des données, il utilise des informations d'identification spécifiques (comptes d'action ou comptes d'identification) pour prendre en charge certaines charges de travail analysées, comme SharePoint 2012.

## Operational Insights et SQL Server 2012

Lorsque vous utilisez Operations Manager, le service de contrôle d'intégrité de System Center s'exécute sous le compte système Local. Dans les versions de SQL Server antérieures à SQL Server 2008 R2, le compte Système local était activé par défaut et était membre du rôle serveur d'administrateur système. Dans SQL Server 2012, la connexion de Système local ne fait pas partie du rôle serveur d'administrateur système. Par conséquent, lorsque vous utilisez Operational Insights, il ne peut pas analyser complètement l'instance de SQL Server 2012 et toutes les règles ne peuvent pas générer des alertes.

## Connectivité du réseau interne et Internet

Lorsque vous utilisez des agents qui se connectent directement auprès du service Web, les agents doivent accéder à Internet pour envoyer des données au service Web et recevoir des informations de configuration mises à jour à partir du service Web.

Lorsque vous utilisez Operations Manager, le serveur d'administration doit accéder à Internet pour envoyer des données au service Web et recevoir les informations de configuration mises à jour à partir du service Web. Toutefois, vos agents n'ont pas besoin d'accéder à Internet. Si vous avez des agents Operations Manager sur des serveurs qui ne sont pas connectés à Internet, vous pouvez utiliser le service Web, s'ils peuvent communiquer avec un serveur d'administration connecté à Internet.

## Gestion des clusters

L'agent Operations Manager est pris en charge sur les ordinateurs exécutant Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008 configurés comme faisant partie d'un cluster de basculement Windows. Vous pouvez afficher les clusters dans le portail Operational Insights. Dans la page Serveurs, les clusters sont identifiés en tant que TYPE=CLUSTER (et non TYPE=AGENT, qui est l'identification des ordinateurs physiques).

Les règles de découverte et de configuration s'exécutent sur les nœuds actifs et passifs du cluster, mais toutes les alertes générées sur les nœuds passifs sont ignorées. Si un nœud passe de passif à actif, les alertes de ce nœud sont affichées automatiquement, sans intervention de votre part.

Certaines alertes peuvent être générées à deux reprises, en fonction de la règle générant l'alerte. Par exemple, une règle qui détecte un pilote défectueux en examinant le système d'exploitation génère des alertes pour le serveur physique et pour le cluster.

L'analyse de la configuration des nœuds passifs n'est pas prise en charge.

Operational Insights ne gère pas le regroupement ou la liaison des ordinateurs exécutant Windows Server qui font partie du même cluster de basculement.

## Mise à l'échelle de votre environnement Operational Insights

Lorsque vous planifiez votre déploiement Operational Insights (en particulier lorsque vous analysez le nombre d'agents d'Operations Manager qui doivent transférer des données via un seul groupe d'administration), prenez en compte la capacité de ce serveur en termes d'espace de fichier.

Examinez les variables suivantes :

- Nombre d'agents par groupe d'administration

- La taille moyenne des données transférées de l'agent au groupe d'administration par jour. Par défaut, chaque agent télécharge des fichiers CAB sur le groupe d'administration deux fois par jour. La taille des fichiers CAB dépend de la configuration du serveur (par exemple le nombre de moteurs SQL Server et de bases de données) et de l'intégrité du serveur (comme le nombre d'alertes générées). Dans la plupart des cas, la taille de téléchargement quotidien est généralement inférieure à 100 Ko.

- La période d'archivage pour conserver les données dans le groupe d'administration (la valeur par défaut est de 5 jours)

Par exemple, si vous supposez une taille de téléchargement quotidienne de 100 Ko par agent et la période d'archivage par défaut, vous aurez besoin du stockage suivant pour le groupe d'administration :

Nombre d'agents|Espace requis estimé pour le groupe d'administration
---|---
5|\~2,5 Mo (5 agents x 100 Ko de données/jour x 5 jours = 2 500 Ko)
50|\~25 Mo (50 agents x 100 Ko de données/jour x 5 jours = 25 000 Ko)

## Comptes d’identification Operations Manager pour Operational Insights

Operational Insights utilise le groupe de gestion et l’agent d’Operations Manager pour collecter et envoyer des données au service Operational Insights. Operational Insights utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte de domaine. Vous devez fournir les informations d’identification en configurant un compte d’identification Operations Manager.

Les sections ci-dessous décrivent comment définir des comptes d’identification Operations Manager pour les charges de travail suivantes :

- Évaluation de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

### Définir le compte d’identification pour l’évaluation de SQL

 Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser ce compte d’identification.

#### Configuration du compte d’identification SQL dans la console Operations

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Profil d'identification de l'évaluation SQL Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.
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
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### Définition du compte d’identification pour Virtual Machine Manager

Assurez-vous que le compte d’identification dispose de privilèges pour les actions suivantes :

- Utiliser le module VMM Windows PowerShell

- Interroger la base de données VMM

- Administrer à distance les agents VMM s’exécutant sur des hôtes de virtualisation

Utilisez les étapes suivantes pour définir le compte lors de la connexion de Operational Insights à Operations Manager.

#### Définition des informations d’identification pour VMM

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification VMM Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour VMM ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE]Le type de compte d’identification doit être Windows.

5. Cliquez sur **Enregistrer**.


### Définition du compte d’identification pour Lync Server

 Le compte d’identification doit être membre des groupes d’administrateurs locaux et de sécurité Lync RTCUniversalUserAdmins.

#### Définition des informations d’identification pour un compte Lync

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification Lync Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d’identification Windows membre des groupes d’administrateurs locaux et de sécurité Lync RTCUniversalUserAdmins.
	>[AZURE.NOTE]Le type de compte d’identification doit être Windows.

5. Cliquez sur **Enregistrer**.


### Définition du compte d’identification pour SharePoint


#### Définition des informations d’identification pour un compte SharePoint

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification SharePoint Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SharePoint ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE]Le type de compte d’identification doit être Windows.

5. Cliquez sur **Enregistrer**.



## Emplacements géographiques

Si vous souhaitez analyser des données à partir de serveurs dans divers emplacements géographiques, envisagez de disposer d'un groupe d'administration par emplacement. Cela peut améliorer les performances de transfert de données de l'agent au groupe d'administration.

<!---HONumber=August15_HO6-->