<properties 
   pageTitle="Comptes d'identification Operations Manager pour Operational Insights"
   description="En savoir plus sur la configuration des comptes d'identification Operations Manager à utiliser avec Operational Insights"
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
   ms.date="03/20/2015"
   ms.author="banders" />

# Comptes d'identification Operations Manager pour Operational Insights

Microsoft Azure Operational Insights utilise le groupe de gestion et l'agent d'Operations Manager pour collecter et envoyer des données au service Operational Insights. Operational Insights utilise les packs d'administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d'administration dans un contexte de sécurité différent, comme un compte de domaine. Vous devez fournir les informations d'identification en configurant un compte d'identification Operations Manager.

Les sections ci-dessous décrivent comment définir des comptes d'identification Operations Manager pour les charges de travail suivantes :

- Évaluation de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

## Définir le compte d'identification pour l'évaluation de SQL

 Si vous utilisez déjà le pack d'administration de SQL Server, vous devez utiliser ce compte d'identification.

### Configuration du compte d'identification SQL dans la console Operations

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Profil d'identification de l'évaluation SQL Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE] Le type de compte d'identification doit être Windows. Le compte d'identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.

5. Cliquez sur **Enregistrer**.

6. Modifiez puis exécutez l'exemple T-SQL suivant sur chaque instance de SQL Server afin d'accorder les autorisations minimales requises pour que le compte d'identification puisse effectuer l'évaluation de SQL. Toutefois, vous n'avez pas besoin de faire cela si un compte d'identification fait déjà partie du rôle de serveur sysadmin sur les instances de SQL Server.

---
    -- Remplacez <UserName> par le nom d'utilisateur utilisé comme compte d'identification.
    USE master
    
    -- Créez la connexion de l'utilisateur ou commentez cette ligne si la connexion est déjà créée.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Accordez les autorisations aux utilisateurs.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Ajoutez un utilisateur de base de données pour toutes les bases de données sur l'instance SQL Server : cela est nécessaire pour la connexion à chaque base de données.
    -- REMARQUE : Cette commande doit être exécutée chaque fois que de nouvelles bases de données sont ajoutées aux instances SQL Server.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Configuration du compte d'identification SQL à l'aide de Windows Powershell

Ouvrez une fenêtre PowerShell et exécutez le script suivant après l'avoir mis à jour avec vos informations :

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Définition du compte d'identification pour Virtual Machine Manager

Assurez-vous que le compte d'identification dispose de privilèges pour les actions suivantes :

- Utiliser le module VMM Windows PowerShell

- Interroger la base de données VMM

- Administrer à distance les agents VMM s'exécutant sur des hôtes de virtualisation

Utilisez les étapes suivantes pour définir le compte lors de la connexion de Operational Insights à Operations Manager.

### Définition des informations d'identification pour VMM

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification VMM Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour VMM ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE] Le type de compte d'identification doit être Windows.

5. Cliquez sur **Enregistrer**.


## Définition du compte d'identification pour Lync Server

 Le compte d'identification doit être membre des groupes d'administrateurs locaux et de sécurité Lync RTCUniversalUserAdmins.

### Définition des informations d'identification pour un compte Lync

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification Lync Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows membre des groupes d'administrateurs locaux et de sécurité Lync RTCUniversalUserAdmins. 
	>[AZURE.NOTE] Le type de compte d'identification doit être Windows.

5. Cliquez sur **Enregistrer**.


## Définition du compte d'identification pour SharePoint


### Définition des informations d'identification pour un compte SharePoint

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Compte d'identification SharePoint Operational Insights**.

3. Sur la page **Comptes d'identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SharePoint ou cliquez sur **Nouveau** pour en créer un. 
	>[AZURE.NOTE] Le type de compte d'identification doit être Windows.

5. Cliquez sur **Enregistrer**.


<!--HONumber=52-->