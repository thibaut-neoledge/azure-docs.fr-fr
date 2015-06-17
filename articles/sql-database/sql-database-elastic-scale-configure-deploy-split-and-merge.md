<properties 
	title="Split and Merge Service Tutorial" 
	pageTitle="Didacticiel sur le service de fractionnement/fusion SQL Azure" 
	description="Fractionnement et fusion avec l'infrastructure élastique" 
	metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" 
	services="sql-database" documentationCenter=""  
	manager="jhubbard" authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" ms.devlang="na" 
	ms.topic="article" ms.date="03/05/2015" 
	ms.author="torsteng" />

# Didacticiel sur le service de fractionnement/fusion de l'infrastructure élastique

## Téléchargement des packages du service de fractionnement/fusion 
1. Téléchargez la dernière version de NuGet à partir de [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Ouvrez une invite de commandes et accédez au répertoire où vous avez téléchargé nuget.exe. 
3. Téléchargez le dernier package de fractionnement/fusion dans le répertoire actif à l'aide de la commande ci-dessous : 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

Les étapes ci-dessus téléchargent les fichiers de fractionnement/fusion dans le répertoire actif. Les fichiers sont placés dans un répertoire nommé **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** où *x.x.xxx.x* reflète le numéro de version. Les fichiers du service de fractionnement/fusion sont situés dans le sous-répertoire **content\splitmerge\service** et les scripts PowerShell de fractionnement/fusion (ainsi que les fichiers .dll clients nécessaires) dans le sous-répertoire **content\splitmerge\powershell**.

## Conditions préalables 

1. Créez une base de données SQL Azure qui servira de base de données d'état du service de fractionnement/fusion. Accédez au [portail de gestion Azure](https://manage.windowsazure.com). En bas à gauche, cliquez sur **Nouveau**, puis sur **Services de données** -> **Base de données SQL** -> **Création personnalisée**. Indiquez le nom de la base de données et créez un utilisateur et un mot de passe. Veillez à enregistrer le nom et le mot de passe pour une utilisation ultérieure.

2. Vérifiez que votre serveur de base de données SQL Azure autorise les services Microsoft Azure à s'y connecter. Accédez au [portail de gestion Azure](https://manage.windowsazure.com). Dans le volet gauche, cliquez sur **Bases de données SQL**. Cliquez ensuite sur **Serveurs** dans le ruban en haut de l'écran, puis sélectionnez votre serveur. Cliquez sur **Configurer** en haut, puis assurez-vous que le paramètre **Services Microsoft Azure** est défini sur **Oui**.

    ![Allowed services][1]

3. Créez un compte Azure Storage qui sera utilisé comme emplacement de destination pour les diagnostics. Accédez au [portail de gestion Azure](https://manage.windowsazure.com). En bas à gauche, cliquez sur **Nouveau**, sur **Services de données**, **Stockage**, puis sur **Création rapide**. 

4. Créez un service cloud Azure qui contient votre service de fractionnement/fusion.  Accédez au [portail de gestion Azure](https://manage.windowsazure.com). En bas à gauche, cliquez sur **Nouveau**, sur **Calcul**, **Service de cloud computing**, puis sur **Création rapide**. 


## Configuration de votre service de fractionnement/fusion 

### Configuration du service de fractionnement/fusion 

1. Dans le dossier dans lequel vous avez téléchargé les bits de fractionnement/fusion, créez une copie du fichier **ServiceConfiguration.Template.cscfg** fourni avec **SplitMergeService.cspkg** et appelez-le **ServiceConfiguration.cscfg**.

2. Ouvrez ServiceConfiguration.cscfg dans votre éditeur de texte préféré. Nous vous recommandons d'utiliser Visual Studio, car il valide les entrées telles que le format des empreintes numériques de certificat. 

3. Créez une base de données ou sélectionnez-en une qui fera office de base de données d'état pour les opérations de fractionnement/fusion et qui permettra de récupérer la chaîne de connexion de la base de données concernée. Dans la base de données SQL Azure, la chaîne de connexion se présente généralement sous la forme suivante :

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Entrez cette chaîne de connexion dans le fichier cscfg dans les sections de rôle **SplitMergeWeb** et **SplitMergeWorker** du paramètre ElasticScaleMetadata.

5.    La configuration de la cible pour les journaux de diagnostic nécessite un compte de stockage dans Azure. La configuration du service de fractionnement et de fusion nécessite la chaîne de connexion à votre compte Azure Storage. La chaîne de connexion doit se présenter sous la forme suivante :

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Pour déterminer la clé d'accès, accédez au [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur l'onglet **Stockage** sur la gauche, sélectionnez le nom correspondant à votre compte de stockage, puis cliquez sur **Gérer les clés d'accès** en bas. Cliquez sur le bouton **Copier** pour obtenir la **Clé d'accès primaire**.

![manage access keys][2]

6.    Entrez le nom du compte de stockage et l'une des clés d'accès fournies dans les espaces réservés de la chaîne de connexion de stockage. Cette chaîne de connexion est utilisée dans les sections de rôle **SplitMergeWeb** et **SplitMergeWorker** du paramètre **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Vous pouvez utiliser différents comptes de stockage pour différents rôles. 

### Configuration de la sécurité 
Pour obtenir des instructions détaillées permettant de configurer la sécurité du service, consultez la rubrique [Configurations de sécurité de l'infrastructure élastique](../sql-database-elastic-scale-configure-security.md).

Dans le cadre  d'un simple déploiement de test permettant de mener à bien ce didacticiel, le bon fonctionnement du service nécessite d'effectuer un nombre minimum d'étapes de configuration. Ces dernières permettent uniquement à l'ordinateur/au compte qui les exécute de communiquer avec le service.

### Création d'un certificat auto-signé 

Créez un répertoire à partir duquel vous allez exécuter la commande suivante à l'aide d'une fenêtre d'[invite de commandes développeur pour Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) :

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Un mot de passe vous est demandé pour protéger la clé privée. Entrez un mot de passe fort et confirmez-le. Vous êtes ensuite de nouveau invité à entrer le mot de passe. Cliquez sur **Oui** à la fin pour l'importer dans le magasin racine des autorités de certification approuvées.

### Création d'un fichier PFX 

Exécutez la commande suivante à partir de la même fenêtre que celle où makecert a été exécuté. Utilisez le même mot de passe que celui utilisé pour créer le certificat :

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importation du certificat client dans le magasin personnel
1. Dans l'Explorateur Windows, double-cliquez sur **MyCert.pfx**.
2. Dans l'**Assistant Importation de certificat** sélectionnez **Utilisateur actuel** et cliquez sur **Suivant**.
3. Vérifiez le chemin d'accès au fichier et cliquez sur **Suivant**.
4. Tapez le mot de passe, laissez l'option **Inclure toutes les propriétés étendues** activée, puis cliquez sur **Suivant**.
5. Laissez l'option **Sélectionner automatiquement le magasin de certificats[...]** activée, puis cliquez sur **Suivant**.
6. Cliquez sur **Terminer** puis sur **OK**.

### Téléchargement du fichier PFX dans le service cloud

Accédez au [portail de gestion Azure](https://manage.windowsazure.com).

1. Sélectionnez **Cloud Services**.
2. Sélectionnez le service cloud créé ci-dessus pour le service de fractionnement/fusion.
3. Dans le menu supérieur, cliquez sur **Certificats**.
4. Cliquez sur **Télécharger** dans la barre inférieure.
5. Sélectionnez le fichier PFX et entrez le même mot de passe que ci-dessus.
6. Lorsque vous avez terminé, copiez l'empreinte de certificat à partir de la nouvelle entrée dans la liste.

### Mise à jour du fichier de configuration de service

Collez l'empreinte de certificat copiée précédemment dans l'attribut d'empreinte/de valeur des paramètres suivants.
Pour le rôle web :

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" /> 
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Pour le rôle de travail :

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Veuillez noter que pour les déploiements de production, des certificats distincts doivent être utilisés pour l'autorité de certification, le chiffrement, le certificat de serveur et les certificats clients. Pour plus d'informations, consultez la rubrique [Configuration de la sécurité](../sql-database-elastic-scale-configure-security.md).

### Déploiement de votre service de fractionnement/fusion
1. Accédez au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur l'onglet **Services de cloud** sur la gauche et sélectionnez le service cloud que vous avez créé précédemment. 
3. Cliquez sur **Tableau de bord**. 
4. Choisissez l'environnement intermédiaire, puis cliquez sur **Télécharger un nouveau déploiement intermédiaire**.

    ![Staging][3]

5. Dans la boîte de dialogue, entrez une étiquette de déploiement. Pour  'Package' et 'Configuration', cliquez sur 'From Local' et choisissez le fichier **SplitMergeService.cspkg** et le fichier .cscfg que vous avez configuré précédemment.
6. Assurez-vous que la case à cocher **Déployer même si un ou plusieurs rôles contiennent une seule instance** est activée.
7. Appuyez sur le bouton en forme de coche dans le coin inférieur droit pour commencer le déploiement. Cette opération peut prendre plusieurs minutes.

![Upload][4]


## Résolution des problèmes de déploiement
Si votre rôle web ne parvient pas à être en ligne, il s'agit probablement d'un problème avec la configuration de sécurité. Vérifiez que le protocole SSL est configuré comme décrit ci-dessus.

Si votre rôle de travail ne parvient pas à être en ligne, mais que votre rôle web réussit, il s'agit probablement d'un problème de connexion à la base de données d'états que vous avez créée précédemment. 

* Assurez-vous que la chaîne de connexion du fichier .cscfg ne contient aucune erreur. 
* Vérifiez que le serveur et la base de données existent et que l'identifiant utilisateur et le mot de passe sont corrects.
* Dans la base de données SQL Azure, la chaîne de connexion doit se présenter sous la forme suivante : 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Assurez-vous que le nom du serveur ne commence pas par **https://**.
* Vérifiez que votre serveur de base de données SQL Azure autorise les services Microsoft Azure à s'y connecter. Pour ce faire, ouvrez https://manage.windowsazure.com et cliquez sur " Bases de données SQL " sur la gauche, puis sur " Serveurs " en haut et sélectionnez votre serveur. Cliquez sur **Configurer** en haut et assurez-vous que le paramètre **Services Microsoft Azure** est défini sur " Oui ". (voir la section " Configuration requise " en haut de cet article).

* Passez en revue les journaux de diagnostic pour votre instance de service de fractionnement/fusion. Ouvrez une instance de Visual Studio, puis dans la barre de menus, cliquez sur **Affichage** et **Explorateur de serveurs**. Cliquez sur l'icône **Microsoft Azure** pour vous connecter à votre abonnement Azure. Ensuite, accédez à Windows Azure -> Stockage -> <votre compte de stockage> -> Tables -> WADLogsTable. Pour plus d'informations, consultez la page [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx) 

    ![][5]

## Test du déploiement du service de fractionnement/fusion
### Connexion avec un navigateur Web

Déterminez le point de terminaison web de votre service de fractionnement/fusion. Vous pouvez le trouver dans le portail de gestion Azure en accédant au **Tableau de bord** de votre service cloud et en effectuant une recherche dans la zone **URL du site** sur le côté droit. Remplacez **http://** par **https://**, car les paramètres de sécurité par défaut désactivent le point de terminaison HTTP. Chargez la page correspondant à cette URL dans votre navigateur.

### Test des scripts PowerShell

Le déploiement et votre environnement peuvent être testés en exécutant les exemples de scripts PowerShell fournis.

Les fichiers de script inclus sont les suivants :

1. **SetupSampleSplitMergeEnvironment.ps1** : configure une couche de données de test pour la fusion et le fractionnement (voir le tableau ci-dessous pour obtenir une description détaillée)
2. **ExecuteSampleSplitMerge.ps1** : exécute les opérations de test sur la couche de données de test (voir le tableau ci-dessous pour obtenir une description détaillée)
3. **GetMappings.ps1** : exemple de script de niveau supérieur qui imprime l'état actuel des mappages de partitions.
4. **ShardManagement.psm1**  : script d'assistance qui encapsule l'API ShardManagement
5. **SqlDatabaseHelpers.psm1** : script d'assistance pour la création et la gestion des bases de données SQL

<table style="width:100%">
  <tr>
    <th>Fichier PowerShell</th>
    <th>Étapes</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Crée une base de données pour le Gestionnaire de cartes de partitions</td>
  </tr>
  <tr>
    <td>2.    Crée 2 bases de données de partition. 
  </tr>
  <tr>
    <td>3.    Crée une carte de partitions pour les bases de données concernées (supprime les cartes de partitions existantes sur ces dernières). </td>
  </tr>
  <tr>
    <td>4.    Crée un petit exemple de table dans les deux partitions et remplit la table dans l'une des partitions.</td>
  </tr>
  <tr>
    <td>5.    Déclare le SchemaInfo pour la table partitionnée.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Fichier PowerShell</th>
    <th>Étapes</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Envoie une demande de fractionnement vers le serveur web frontal du service de fractionnement/fusion, qui fractionne la moitié des données de la première partition et les envoie vers la deuxième partition.</td>
  </tr>
  <tr>
    <td>2.    Interroge le serveur Web frontal sur l'état de la demande de fractionnement et attend jusqu'à ce que la demande soit terminée.</td>
  </tr>
  <tr>
    <td>3.    Envoie une demande de fusion vers le serveur Web frontal du service de fractionnement/fusion, qui déplace les données de la deuxième partition vers la première partition.</td>
  </tr>
  <tr>
    <td>4.    Interroge le serveur web frontal sur l'état de la demande de fusion et attend jusqu'à ce que la demande soit terminée.</td>
  </tr>
</table>

## Utilisation de PowerShell pour vérifier le déploiement

1.    Ouvrez une nouvelle fenêtre PowerShell et accédez au répertoire dans lequel vous avez téléchargé le package de fractionnement/fusion, puis accédez au répertoire " powershell ".
2.    Créez un serveur de base de données SQL Azure (ou choisissez un serveur existant) dans lequel le Gestionnaire des cartes de partitions et les partitions seront créés. 

    Remarque : le script SetupSampleSplitMergeEnvironment.ps1 crée, par défaut, ces bases de données sur le même serveur pour simplifier le script. Il ne s'agit pas d'une restriction du service de fractionnement/fusion.

    Une connexion d'authentification SQL avec un accès en lecture/écriture aux bases de données est requis pour le service de fractionnement/fusion afin de déplacer les données et de mettre à jour la carte de partitions. Le service de fractionnement/fusion s'exécutant dans le cloud, il ne prend pas actuellement en charge l'authentification intégrée.

    Assurez-vous que le serveur SQL Azure est configuré pour autoriser l'accès à partir de l'adresse IP de l'ordinateur exécutant les scripts. Pour accéder à ce paramètre, sélectionnez Serveur SQL Azure / Configuration / Adresses IP autorisées.

3.    Exécutez le script SetupSampleSplitMergeEnvironment.ps1 pour créer l'exemple d'environnement. 

    L'exécution de ce script efface toutes les structures de données de gestion des cartes de partitions existantes dans la base de données du Gestionnaire des cartes de partitions et dans les partitions. Il peut être utile de réexécuter le script si vous souhaitez réinitialiser la carte de partitions ou les partitions.

    Exemple de ligne de commande :

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Exécutez le script Getmappings.ps1 pour afficher les mappages qui existent actuellement dans l'exemple d'environnement.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Exécutez le script ExecuteSampleSplitMerge.ps1 pour exécuter une opération de fractionnement (déplacement de la moitié des données de la première partition vers la deuxième partition), puis une opération de fusion (redéplacement des données sur la première partition). Si vous avez configuré SSL et laissé le point de terminaison http désactivé, assurez-vous d'utiliser le point de terminaison https:// à la place.

    Exemple de ligne de commande :

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Si vous recevez l'erreur ci-dessous, il s'agit probablement d'un problème avec le certificat de votre point de terminaison web. Essayez de vous connecter au point de terminaison web avec votre navigateur web préféré et vérifiez s'il existe une erreur de certificat.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Si l'opération s'est déroulée sans erreur, le résultat doit ressembler à ce qui suit :

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Faites des essais avec d'autres types de données. Tous ces scripts nécessitent un paramètre -ShardKeyType facultatif qui vous permet de spécifier le type de clé. La valeur par défaut est Int32, mais vous pouvez également spécifier Int64, Guid ou Binary. 

## Création de vos propres demandes 

Le service peut être utilisé à l'aide de l'interface utilisateur Web ou par l'importation et l'utilisation du module PowerShell SplitMerge.psm1 qui envoie vos demandes via le rôle Web.

Le service de fractionnement/fusion peut déplacer les données dans les tables partitionnées et les tables de référence. Une table partitionnée possède une colonne de clés de partitionnement et comporte des données de ligne différentes sur chaque partition. Une table de référence n'est pas partitionnée. De ce fait, elle comporte les mêmes données de ligne sur chaque partition. Les tables de référence sont utiles pour les données qui ne changent pas souvent et qui sont utilisées pour S'ASSOCIER à des tables partitionnées dans les requêtes.

Pour effectuer une opération de fractionnement/fusion, vous devez déclarer les tables partitionnées et les tables de référence que vous souhaitez déplacer. Cette opération est effectuée avec l'API **SchemaInfo**. Cette API se trouve dans l'espace de noms **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Pour chaque table partitionnée, créez un objet **ShardedTableInfo** décrivant le nom du schéma parent de la table (facultatif, la valeur par défaut est " dbo "), le nom de la table et le nom de la colonne de cette table qui comporte la clé de partitionnement.
2.    Pour chaque table de référence, créez un objet **ReferenceTableInfo** décrivant le nom du schéma parent de la table (facultatif, la valeur par défaut est " dbo ") et le nom de la table.
3.    Ajoutez les objets TableInfo ci-dessus à un nouvel objet **SchemaInfo**.
4.    Obtenez une référence à un objet **ShardMapManager** et appelez **GetSchemaInfoCollection**.
5.    Ajoutez **SchemaInfo** à **SchemaInfoCollection**, en fournissant le nom de la carte de partitions.

Le script SetupSampleSplitMergeEnvironment.ps1 contient un exemple de cette opération.

Notez que le service de fractionnement/fusion ne crée pas la base de données cible (ou le schéma pour les tables de la base de données) à votre place. Ils doivent être créés au préalable avant d'envoyer une demande au service.


## Résolution des problèmes
Le message ci-dessous peut apparaître lors de l'exécution des exemples de scripts PowerShell :

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Cette erreur signifie que votre certificat SSL n'est pas correctement configuré. Suivez les instructions de la section 'Connecting with a web browser'.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png

<!--HONumber=47-->
 