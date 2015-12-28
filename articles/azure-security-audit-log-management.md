<properties
   pageTitle="Gestion des journaux d’audit et de sécurité Microsoft Azure | Microsoft Azure"
   description="L’article fournit une introduction à la génération, la collecte et l’analyse des journaux de sécurité à partir des services hébergés sur Azure. Il est destiné aux professionnels de l’informatique et analystes de sécurité qui s’occupent quotidiennement de la gestion des ressources d’informations, y compris ceux qui sont responsables de la sécurité et de la conformité de leur organisation."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Gestion des journaux d’audit et de sécurité Microsoft Azure

Azure permet aux clients de générer et de collecter des événements de sécurité à partir des rôles Azure IaaS (Infrastructure as a Service) et PaaS (Platform as a Service) vers le stockage central dans leurs abonnements. Les clients peuvent ensuite utiliser [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/) pour agréger et analyser les événements collectés. En outre, ces événements collectés peuvent être exportés vers les systèmes SIEM (Security Information and Event Management) locaux pour la surveillance en cours.

Le cycle de vie de journalisation, d’analyse et de surveillance de la sécurité Azure inclut les phases suivantes :

- **Génération** : instrumenter des applications et l’infrastructure pour déclencher des événements
- **Collecte** : configurer Azure pour collecter les divers journaux de sécurité dans un compte de stockage
- **Analyse** : utiliser des outils Azure comme HDInsight et les systèmes SIEM locaux pour analyser les journaux et générer des perspectives sur la sécurité
- **Surveillance et rapports** : Azure offre des systèmes de surveillance et d’analyse centralisés qui fournissent une visibilité continue et des alertes en temps voulu

Cet article est axé sur les phases de génération et de collecte du cycle de vie.

## Génération de journaux
Les événements de sécurité sont déclenchés dans le journal des événements Windows pour les canaux **Système**, **Sécurité** et **Application** dans les machines virtuelles. Pour vous assurer que les événements sont consignés sans perte de données potentielle, il est important de configurer correctement la taille du journal des événements. Définissez la taille du journal des événements selon le nombre d’événements générés par les paramètres de stratégie d’audit et les stratégies de collecte d’événements définies. Pour plus d’informations, consultez [Planification de la gestion et de la surveillance de l’audit de sécurité](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4).

>[AZURE.NOTE]Lors de l’utilisation de WEF (Windows Event Forwarding) ou des diagnostics Azure (décrits dans la section [Collecte de journaux](#log-collection)) pour extraire des journaux des services cloud ou des machines virtuelles, tenez compte de l’impact éventuel des pannes du système. Par exemple, si votre environnement WEF tombe en panne pendant un certain temps, vous devez vous assurer que la taille du journal est assez grande pour prendre en compte une durée plus longue ou vous préparer à une perte possible des données du journal.

Pour les applications des services cloud qui sont déployées dans Azure et les machines virtuelles créées à partir du [Marketplace de machines virtuelles Azure](http://azure.microsoft.com/marketplace/virtual-machines/#microsoft), un ensemble d’événements de sécurité de système d’exploitation sont activés par défaut. Les clients peuvent ajouter, supprimer ou modifier des événements à auditer en personnalisant la stratégie d’audit de système d’exploitation. Pour plus d’informations, consultez [Informations de référence sur les paramètres de stratégie de sécurité](http://technet.microsoft.com/library/jj852210.aspx).

Vous pouvez utiliser les méthodes suivantes pour générer des journaux supplémentaires à partir du système d’exploitation (par exemple, les modifications de stratégie d’audit) et des composants Windows (comme IIS) :

- Stratégie de groupe pour déployer des paramètres de stratégie pour les machines virtuelles dans Azure appartenant à un domaine.
- Configuration d’état souhaité pour émettre et gérer les paramètres de stratégie. Pour plus d’informations, consultez [Configuration d’état souhaité Azure PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx).
- Code de démarrage du rôle de déploiement du service pour déployer des paramètres pour les services cloud (scénario PaaS).

La configuration des tâches de démarrage de rôle Azure permet l’exécution du code avant le démarrage d’un rôle. Vous pouvez définir une tâche de démarrage pour un rôle en ajoutant l’élément **Startup** à la définition du rôle dans le fichier de définition de service, comme illustré dans l’exemple suivant. Pour en savoir plus, voir [Exécuter des tâches de démarrage dans Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

Le fichier de tâche qui doit être exécuté comme tâche de démarrage (EnableLogOnAudit.cmd dans l’exemple suivant) doit être inclus dans votre package de génération. Si vous utilisez Visual Studio, ajoutez le fichier à votre projet cloud, cliquez avec le bouton droit sur le nom de fichier, cliquez sur **Propriétés**, puis affectez à **Copier dans le répertoire de sortie** la valeur **Toujours copier**.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenu d’EnableLogOnAudit.cmd :

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

[Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) qui est utilisé dans l’exemple précédent est un outil en ligne de commande inclus dans le système d’exploitation Windows Server qui vous permet de gérer les paramètres de stratégie d’audit.

En plus de générer des journaux des événements Windows, différents composants du système d’exploitation Windows peuvent être configurés pour générer des journaux qui sont importants pour l’analyse de la sécurité et la surveillance. Par exemple, les journaux Internet Information Services (IIS) et les journaux http.err sont automatiquement générés pour les rôles web et ils peuvent être configurés pour la collecte. Ces journaux fournissent des informations précieuses qui peuvent être utilisées pour identifier un accès non autorisé ou des attaques contre votre rôle web. Pour plus d’informations, consultez [Configurer la journalisation dans IIS](http://technet.microsoft.com/library/hh831775.aspx) et [Journalisation avancée pour IIS : journalisation personnalisée](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging).

Pour modifier la journalisation IIS dans un rôle web, les clients peuvent ajouter une tâche de démarrage au fichier de définition de service de rôle web. L’exemple suivant active la journalisation HTTP pour un site web nommé Contoso et spécifie qu’IIS doit enregistrer toutes les demandes pour celui-ci.

La tâche qui met à jour la configuration IIS doit être incluse dans le fichier de définition de service du rôle web. Les modifications suivantes apportées au fichier de définition de service permettent d’exécuter une tâche de démarrage qui configure la journalisation IIS en exécutant un script appelé ConfigureIISLogging.cmd.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenu de ConfigureIISLogging:cmd

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>Collecte de journaux
La collecte des journaux et des événements de sécurité à partir des services cloud ou des machines virtuelles dans Azure se produit via deux méthodes principales :

- Les diagnostics Azure collectent les événements dans le compte de stockage Azure d’un client
- WEF (Windows Event Forwarding), une technologie dans les ordinateurs exécutant Windows

Certaines différences clés entre ces deux technologies figurent dans le tableau ci-dessous. Selon vos besoins et ces différences clés, vous devez choisir la méthode appropriée pour implémenter la collecte de journaux.

| Azure Diagnostics | WEF (Windows Event Forwarding) |
|-----|-----|
|Prend en charge les services cloud et les machines virtuelles Azure | Prend en charge les machines virtuelles Azure appartenant à un domaine uniquement |
|Prend en charge différents formats de journaux, tels que les journaux des événements Windows, les traces du [suivi d’événements pour Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) et les journaux IIS. Pour plus d’informations, consultez [Sources de données prises en charge par les diagnostics Azure](#diagnostics) |Prend en charge les journaux des événements Windows uniquement |
|Émet les données collectées vers le stockage Azure |Déplace les données collectées vers des serveurs collecteurs centraux |

##	Collecte de données d’événements de sécurité avec WEF
Pour les machines virtuelles Azure appartenant à un domaine, vous pouvez configurer WEF à l’aide des paramètres de stratégie de groupe de la même manière que pour les ordinateurs appartenant à un domaine local. Pour plus d’informations, consultez la page [Cloud hybride](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx).

Avec cette approche, une organisation peut acheter un abonnement IaaS, se connecter au réseau d’entreprise en utilisant [ExpressRoute](http://azure.microsoft.com/services/expressroute/) ou un réseau VPN de site à site, puis joindre les machines virtuelles présentes dans Azure au domaine d’entreprise. Ensuite, vous pouvez configurer WEF à partir des machines appartenant au domaine.

Le transfert d’événements se compose de deux parties : la source et le collecteur. La source est l’ordinateur dans lequel les journaux de sécurité sont générés. Le collecteur est le serveur centralisé qui collecte et consolide les journaux des événements. Les administrateurs informatiques peuvent s’abonner aux événements pour pouvoir recevoir et stocker les événements qui sont transférés à partir d’ordinateurs distants (la source des événements). Pour plus d’informations, consultez [Configurer les ordinateurs pour transférer et recueillir les événements](http://technet.microsoft.com/library/cc748890.aspx).

Les événements Windows collectés peuvent être envoyés à des outils d’analyse en local, par exemple un système SIEM, pour une analyse plus approfondie.

## Collecte de données de sécurité avec les diagnostics Azure
Les diagnostics Azure vous permettent de collecter des données de diagnostic à partir d’un rôle web ou d’un rôle de travail de service cloud, ou depuis des machines virtuelles exécutées dans Azure. Il s’agit d’une extension de l’agent invité prédéfinie qui doit être activée et configurée pour la collecte de données. L’abonnement d’un client peut inclure l’émission de données vers le stockage Azure.

Les données sont chiffrées pendant le transport (en utilisant HTTPS). Les exemples fournis dans ce document utilisent la version 1.2 des diagnostics Azure. Nous vous recommandons d’effectuer la mise à niveau vers la version 1.2 ou supérieure pour la collecte de données de sécurité. Pour plus d’informations, consultez [Collecte des données de journalisation avec les diagnostics Azure](http://msdn.microsoft.com/library/gg433048.aspx).

Le diagramme suivant illustre un flux de données de haut niveau pour la collecte de données de sécurité qui utilise les diagnostics Azure ainsi qu’une analyse et une surveillance plus approfondies.

![][1]

Les diagnostics Azure déplacent les journaux des applications de services cloud du client et des [machines virtuelles Azure](virtual-machines-about.md) vers le stockage Azure. Selon le format de journal, certaines données sont stockées dans les tables Azure et d’autres dans des objets BLOB. Les données collectées dans le [stockage Azure](storage-introduction.md) peuvent être téléchargées vers les systèmes SIEM locaux à l’aide de la bibliothèque cliente de stockage Azure pour la surveillance et l’analyse.

En outre, HDInsight permet d’effectuer une analyse plus approfondie des données dans le cloud. Voici quelques exemples de collecte de données de sécurité qui utilisent les diagnostics Azure.

### Collecte de données de sécurité à partir des machines virtuelles Azure à l’aide des diagnostics Azure

Les exemples suivants utilisent la version 1.2 des diagnostics Azure et les applets de commande Azure PowerShell pour activer la collecte de données de sécurité à partir des machines virtuelles. Les données sont collectées à partir des machines virtuelles selon un intervalle planifié (qui est configurable) et envoyées au stockage Azure dans le cadre de l’abonnement du client. Dans cette section, nous allons découvrir deux scénarios de collecte de journaux avec les diagnostics Azure :

1. Configurer une nouvelle instance d’un pipeline de collecte de journaux de sécurité sur une machine virtuelle.
2. Mettre à jour un pipeline de collecte de journaux de sécurité existant avec une nouvelle configuration sur une machine virtuelle.

#### Configurer une nouvelle instance d’un pipeline de collecte de journaux de sécurité sur une machine virtuelle
Dans cet exemple, nous configurons une nouvelle instance d’un pipeline de collecte de journaux de sécurité qui utilise les diagnostics Azure et détectons les événements d’échec d’ouverture de session (ID d’événement 4624 et 4625) à partir des machines virtuelles. Vous pouvez implémenter les étapes suivantes à partir de votre environnement de développement, ou vous pouvez utiliser une session Bureau à distance via le protocole RDP (Remote Desktop Protocol) pour le nœud dans le cloud.

##### Étape 1 : Installer le Kit de développement logiciel (SDK) Azure PowerShell
Le Kit de développement logiciel (SDK) Azure PowerShell fournit des applets de commande pour configurer les diagnostics Azure sur des machines virtuelles Azure. Les applets de commande nécessaires sont disponibles dans Azure PowerShell version 0.8.7 ou ultérieure. Pour plus d’informations, consultez [Comment installer et configurer Azure PowerShell](powershell-install-configure.md).

##### Étape 2 : Préparer le fichier de configuration
Préparez le fichier de configuration en fonction des événements que vous souhaitez collecter. Voici un exemple d’un fichier de configuration des diagnostics Azure pour collecter des événements Windows à partir du canal **Sécurité**, des filtres ayant été ajoutés pour collecter uniquement les événements de réussites et d’échecs d’ouvertures de session. Pour plus d’informations, consultez [Schéma de configuration de la version 1.2 des diagnostics Azure](http://msdn.microsoft.com/library/azure/dn782207.aspx).

Le compte de stockage peut être spécifié dans le fichier de configuration ou en tant que paramètre quand vous exécutez les applets de commande Azure PowerShell pour configurer les diagnostics Azure.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

Quand vous enregistrez le contenu précédent en tant que fichier XML, définissez **UTF-8** pour l’encodage. Si vous utilisez le Bloc-notes, l’option d’encodage disponible apparaît dans la boîte de dialogue « Enregistrer sous ». Le tableau ci-dessous répertorie certains attributs essentiels à noter dans le fichier de configuration.

| Attribut | Description |
|----- |-----|
| overallQuotaInMB | Quantité maximale d’espace sur le disque local qui peut être consommée par les diagnostics Azure (la valeur est configurable). |
| scheduledTransferPeriod | Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. |
| Nom | Dans WindowsEventLog, cet attribut est la requête XPath qui décrit les événements Windows à collecter. Vous pouvez filtrer la collecte de données en ajoutant un filtre, tel que l’ID d’événement, le nom du fournisseur ou le canal. |

Toutes les données du journal des événements Windows sont déplacées vers une table nommée **WADWindowsEventLogsTable**. Actuellement, les diagnostics Azure ne prennent pas en charge la modification du nom de la table.

##### <a name="step3"></a> Étape 3 : Valider le fichier XML de configuration
Utilisez la procédure suivante pour valider qu’il n’existe aucune erreur dans le fichier XML de configuration et qu’il est compatible avec le schéma des diagnostics Azure :

1. Pour télécharger le fichier de schéma, exécutez la commande suivante, puis enregistrez le fichier.

    (Get-AzureServiceAvailableExtension -ExtensionName ’PaaSDiagnostics’ -ProviderNamespace ’Microsoft.Azure.Diagnostics’).PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath ’WadConfigSchema.xsd’

2. Après avoir téléchargé le fichier de schéma, vous pouvez valider le fichier XML de configuration par rapport au schéma. Pour valider le fichier à l’aide de Visual Studio :
  - Ouvrez le fichier XML dans Visual Studio
  - Appuyez sur F4 pour ouvrir **Propriétés**
  - Cliquez sur **Schéma**, sur **Ajouter**, sélectionnez le fichier de schéma que vous avez téléchargé (WadConfigSchema.XSD), puis cliquez sur **OK**

3.	Dans le menu **Affichage**, cliquez sur **Liste d’erreurs** pour voir s’il existe des erreurs de validation.

##### <a name="step4"></a> Étape 4 : Configurer les diagnostics Azure
 Utilisez les étapes suivantes pour activer les diagnostics Azure et démarrer la collecte de données :

 1.	Pour ouvrir Azure PowerShell, tapez **Add-AzureAccount** et appuyez sur Entrée.
 2.	Connectez-vous à votre compte Azure.
 3.	Exécutez le script PowerShell suivant. Veillez à mettre à jour storage\_name, key, config\_path, service\_name et vm\_name.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### Étape 5 : Générer des événements
À des fins de démonstration, nous allons créer des événements d’ouverture de session et vérifier que les données sont diffusées vers le stockage Azure. Comme illustré précédemment à l’étape 2, le fichier XML est configuré pour collecter l’ID d’événement 4624 (réussite d’ouverture de session) et l’ID d’événement 4625 (échec d’ouverture de session) à partir du canal **Sécurité**.

 Pour générer ces événements :

1.	Ouvrez une session RDP pour votre machine virtuelle.
2.	Entrez des informations d’identification incorrectes pour générer des événements d’ouverture de session ayant échoué (ID d’événement 4625).
3.	Après quelques tentatives infructueuses d’ouverture de session, entrez les informations d’identification correctes pour générer un événement d’ouverture de session réussie (ID d’événement 4624).

##### Étape 6 : Afficher les données
Environ cinq minutes après avoir terminé les étapes précédentes, les données doivent commencer à être diffusées vers le compte de stockage du client selon la configuration du fichier XML. De nombreux outils sont disponibles pour afficher les données du stockage Azure. Pour plus d’informations, consultez :

- [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Explorateur de stockage Azure 6 version préliminaire 3 (août 2014)](http://azurestorageexplorer.codeplex.com/)

Pour afficher vos données :

1.	Dans Visual Studio (2013, 2012 et 2010 avec SP1), cliquez sur **Affichage**, puis sur **Explorateur de serveurs**.
2.	Accédez au compte de stockage.
3.	Cliquez sur **Tables** puis double-cliquez sur les tables appropriées pour afficher les journaux de sécurité collectés à partir des machines virtuelles. ![][2]

4.	Cliquez avec le bouton droit sur la table nommée WADWindowsEventLogsTable, puis cliquez sur **Afficher les données** pour ouvrir la vue de table, comme illustré ici :

![][3]

Dans la table de stockage précédente, **PartitionKey**, **RowKey** et **Timestamp** sont des propriétés système.

- **PartitionKey** est un horodatage en secondes, et il s’agit d’un identificateur unique pour la partition dans la table.
- **RowKey** est un identificateur unique pour une entité dans une partition.

Ensemble, les propriétés **PartitionKey** et **RowKey** identifient de manière unique chaque entité d’une table.

- Timestamp est une valeur date/heure qui est conservée sur le serveur pour suivre quand une entité a été modifiée pour la dernière fois.

>[AZURE.NOTE]La taille maximale des lignes dans une table de stockage Azure est limitée à 1 Mo. Un compte de stockage peut contenir jusqu’à 200 To de données d’objets BLOB, de files d’attente et de tables si le compte a été créé après juin 2012. Par conséquent, la taille de votre table peut atteindre 200 To si les files d’attente et les objets BLOB n’occupent pas d’espace de stockage. Les comptes créés avant juin 2012 ont une limite de taille de 100 To.

L’explorateur de stockage vous permet également de modifier les données de la table. Double-cliquez sur une ligne particulière dans la vue Table pour ouvrir la fenêtre Modifier l’entité comme illustré ici :

![][4]

#### Mettre à jour un pipeline de collecte de journaux de sécurité existant avec une nouvelle configuration sur une machine virtuelle
Dans cette section, nous mettons à jour un pipeline de collecte de journaux de sécurité des diagnostics Azure existant sur une machine virtuelle et détectons les erreurs de journal des événements de l’application Windows.

##### Étape 1 : Mettre à jour le fichier de configuration pour inclure les événements intéressants
Le fichier des diagnostics Azure créé dans l’exemple précédent doit être mis à jour pour inclure les types d’erreurs de journal des événements de l’application Windows.

>[AZURE.NOTE]Les paramètres de configuration des diagnostics Azure existants doivent être fusionnés avec le nouveau fichier de configuration. Les paramètres définis dans le nouveau fichier remplacent les configurations existantes.

Pour récupérer le paramètre de configuration existant, vous pouvez utiliser l’applet de commande **Get-AzureVMDiagnosticsExtension**. Voici un exemple de script Azure PowerShell pour récupérer la configuration existante :

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
Mettez à jour la configuration des diagnostics Azure pour collecter les événements critiques et les erreurs de journal des événements de l’application Windows comme suit :

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Validez le fichier de configuration en effectuant les mêmes étapes que celles indiquées précédemment dans [Étape 3 : Valider le fichier XML de configuration](#step3).

##### Étape 2 : Mettre à jour les diagnostics Azure pour utiliser le nouveau fichier de configuration
Utilisez les applets de commande **Set-AzureVMDiagnosticsExtension** et **Update-AzureVM** pour mettre à jour la configuration comme indiqué précédemment dans [Étape 4 : Configurer les diagnostics Azure](#step4).

##### Étape 3 : Vérifier les paramètres de configuration
Exécutez la commande suivante pour vérifier que les paramètres de configuration ont été mis à jour :

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### Étape 4 : Générer des événements
Pour cet exemple, exécutez la commande suivante pour générer un journal des événements de l’application de type **Erreur** :

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

Ouvrez l’Observateur d’événements pour vérifier que l’événement est créé.

![][6]

##### Étape 5 : Afficher les données
Ouvrez l’Explorateur de serveurs dans Visual Studio pour afficher les données du journal. Vous devez voir un **EventID 100** créé sur **ContosoDesktop** comme illustré ici :

![][7]

## Collecte de données de sécurité à partir des services cloud Azure à l’aide des diagnostics Azure

Nous allons maintenant utiliser les diagnostics Azure pour explorer les deux mêmes scénarios de collecte de journaux à partir des services cloud Azure que dans la section précédente relative aux machines virtuelles (IaaS) :

1.	Configurer une nouvelle instance d’un pipeline de collecte de journaux de sécurité dans un service cloud.
2.	Mettre à jour un pipeline de collecte de journaux de sécurité existant avec une nouvelle configuration dans un service cloud.

La procédure pas à pas de cette section inclut les opérations suivantes :

1.	Créer un service cloud.
2.	Configurer le service cloud pour la collecte de journaux de sécurité à l’aide des diagnostics Azure.
3.	Illustrer la génération et la collecte d’événements de sécurité dans le service cloud :

    - Ajouter un administrateur à un groupe local avec une élévation de privilèges
    - Création de processus
4.	Mettre à jour un pipeline de collecte de journaux de sécurité existant dans un service cloud :

    - Activer l’audit des événements de pare-feu d’hôte (comme exemple d’événements de sécurité réseau) en utilisant l’outil Auditpol
    - Configurer les données d’audit de pare-feu à collecter et afficher les événements collectés dans le compte de stockage du client
5.	Afficher la détection des pics et la distribution d’événements de sécurité Windows.
6.	Configurer la collecte de journaux IIS et vérifier les données.

Tous les événements et les journaux sont rassemblés dans un compte de stockage du client dans Azure. Les événements peuvent être affichés et exportés par le client sur des systèmes SIEM locaux. Ils peuvent également être agrégés et analysés en utilisant HDInsight.

### Configurer une nouvelle instance d’un pipeline de collecte de journaux dans un service cloud
Dans cet exemple, nous configurons une nouvelle instance d’un pipeline de collecte de journaux de sécurité qui utilise les diagnostics Azure et détectons l’ajout de l’utilisateur à un groupe local ainsi que les événements de création de processus sur une instance de service cloud.

#### Étape 1 : Créer un service cloud (rôle web) et déployer

1.	Sur votre ordinateur de développement, lancez Visual Studio 2013.
2.	Créez un projet de service cloud (notre exemple utilise ContosoWebRole).
3.	Sélectionnez le rôle web **ASP.NET**.
4.	Sélectionnez le projet **MVC**.
5.	Dans l’Explorateur de solutions, cliquez sur **Rôles**, puis double-cliquez sur le rôle web (WebRole1) pour ouvrir la fenêtre **Propriétés**.
6.	Sous l’onglet **Configuration**, décochez la case **Activer les diagnostics** pour désactiver la version des diagnostics Azure qui est fournie avec Visual Studio 2013. ![][8]

7.	Générez votre solution pour vérifier que vous n'avez pas d'erreurs.
8.	Ouvrez le fichier WebRole1/Controllers/HomeController.cs.
9.	Ajoutez la méthode suivante pour permettre à l’exemple d’application d’enregistrer le code d’état HTTP 500 comme exemple d’événement de journal IIS (cela sera utilisé dans l’exemple IIS ultérieurement) :

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 Cliquez avec le bouton droit sur le nom du projet de service cloud et cliquez sur **Publier**.

#### Étape 2 : Préparer le fichier de configuration
Vous allez maintenant préparer le fichier de configuration des diagnostics Azure pour ajouter les événements qui peuvent aider à détecter les situations suivantes :

- Ajout d’un nouvel utilisateur à un groupe local
- Création de processus

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### Étape 3 : Valider le fichier XML de configuration
Validez le fichier de configuration en effectuant les mêmes étapes que celles indiquées précédemment dans [Étape 3 : Valider le fichier XML de configuration](#step3).
#### Étape 4 : Configurer les diagnostics Azure
Exécutez le script Azure PowerShell suivant pour activer les diagnostics Azure (veillez à mettre à jour storage\_name, key, config\_path et service\_name).

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Pour vérifier que votre service a la dernière configuration de diagnostic, exécutez la commande Azure PowerShell suivante :

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Étape 5 : Générer des événements
Pour générer des événements :

1.	Pour démarrer une session Bureau à distance à votre instance de service cloud, dans Visual Studio, ouvrez l’Explorateur de serveurs, cliquez avec le bouton droit sur l’instance de rôle et cliquez sur Connexion à l’aide de Bureau à distance.
2.	Ouvrez une invite de commandes avec élévation de privilèges et exécutez les commandes suivantes pour créer un compte d’administrateur local sur la machine virtuelle :


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	Ouvrez l’Observateur d’événements, ouvrez le canal **Sécurité** et notez qu’un événement 4732 a été créé, comme illustré ici :

![][9]

#### Étape 6 : Afficher les données
Patientez environ cinq minutes pour permettre à l’agent des diagnostics Azure d’émettre des événements vers la table de stockage.

![][10]

Pour valider l’événement de création de processus, ouvrez un Bloc-notes. Comme illustré ici, un événement de création de processus a été enregistré dans le canal Sécurité.

![][11]

Vous pouvez maintenant afficher le même événement dans votre compte de stockage, comme illustré ici :

![][12]

### Mettre à jour un pipeline de collecte de journaux de sécurité existant dans un service cloud avec une nouvelle configuration
Dans cette section, nous mettons à jour un pipeline de collecte de journaux de sécurité des diagnostics Azure existant et détectons les événements de modification du Pare-feu Windows sur une instance de service cloud.

Pour détecter les modifications de pare-feu, nous allons mettre à jour la configuration existante pour inclure les événements de modification de pare-feu.

#### Étape 1 : Obtenir la configuration existante
>[AZURE.NOTE]Les nouveaux paramètres de configuration remplacent la configuration existante. Il est ainsi important que les paramètres de configuration des diagnostics Azure existants soient fusionnés avec le nouveau fichier de configuration.

Pour récupérer le paramètre de configuration existant, vous pouvez utiliser l’applet de commande **Get-AzureServiceDiagnosticsExtension** :

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Étape 2 : Mettre à jour le fichier XML de configuration pour inclure des événements de pare-feu

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Validez le contenu XML en utilisant le même processus de validation que décrit précédemment dans [Étape 3 : Valider le fichier XML de configuration](#step3).

#### Étape 3 : Mettre à jour les diagnostics Azure pour utiliser la nouvelle configuration

Exécutez le script Azure PowerShell suivant pour mettre à jour les diagnostics Azure pour utiliser la nouvelle configuration (veillez à mettre à jour storage\_name, key, config\_path et service\_name avec les informations de votre service cloud).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Pour vérifier que votre service a la dernière configuration de diagnostic, exécutez la commande Azure PowerShell suivante :

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Étape 4 : Activer les événements de pare-feu

1.	Ouvrez une session Bureau à distance à votre instance de service cloud.
2.	Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante :

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### Étape 5 : Générer des événements

1.	Ouvrez le Pare-feu Windows, puis cliquez sur **Règles de trafic entrant**.
2.	Cliquez sur **Ajouter une nouvelle règle**, puis sur **Port**.
3.	Dans le champ **Ports locaux**, tapez **5000**, puis cliquez sur **Suivant** à trois reprises.
4.	Dans le champ **Nom**, tapez **Test5000**, puis cliquez sur **Terminer**.
5.	Ouvrez l’Observateur d’événements, ouvrez le canal **Sécurité** et notez qu’un ID d’événement 4946 a été créé, comme illustré ici :

![][13]

#### Étape 6 : Afficher les données
Patientez environ cinq minutes pour permettre à l’agent des diagnostics Azure d’émettre les données d’événement vers la table de stockage.

![][14]

### Détection des pics et distribution d’événements de sécurité
Une fois que les événements apparaissent dans le compte de stockage du client, les applications peuvent utiliser les bibliothèques clientes de stockage pour accéder à l’agrégation d’événements et l’effectuer. Pour obtenir un exemple de code pour accéder aux données de table, consultez [Comment : récupérer des données de table](storage-dotnet-how-to-use-tables.md).

Voici un exemple d’agrégation d’événements. Il est possible d’examiner plus en détail les pics dans la distribution d’événements pour déceler toute activité anormale.

![][15]

### Collecte et traitement de journaux IIS à l’aide de HDInsight
Dans cette section, nous collectons des journaux IIS de votre instance de rôle web et les déplaçons vers un objet BLOB Azure dans le compte de stockage du client.

#### Étape 1 : Mettre à jour le fichier de configuration pour inclure la collecte de journaux IIS

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Dans la précédente configuration des diagnostics Azure, **containerName** est le nom de conteneur d’objets BLOB vers lequel les journaux seront déplacés dans le compte de stockage du client.

Validez le fichier de configuration en effectuant les mêmes étapes que celles indiquées précédemment dans [Étape 3 : Valider le fichier XML de configuration](#step3).


#### Étape 2 : Mettre à jour les diagnostics Azure pour utiliser une nouvelle configuration
Exécutez le script Azure PowerShell suivant pour mettre à jour les diagnostics Azure pour utiliser la nouvelle configuration (veillez à mettre à jour storage\_name, key, config\_path et service\_name avec les informations de votre service cloud).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Pour vérifier que votre service a la dernière configuration de diagnostic, exécutez la commande Azure PowerShell suivante :

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Étape 3 : Générer des journaux IIS

1.	Ouvrez un navigateur web et accédez au rôle web de service cloud (par exemple, http://contosowebrole.cloudapp.net/).
2.	Accédez aux pages **À propos de** et **Contact** pour créer des événements de journal.
3.	Accédez à une page qui génère un code d’état 500 (par exemple, http://contosowebrole.cloudapp.net/Home/StatusCode500). Vous devez voir une erreur telle que celle qui suit. N’oubliez pas que nous avons ajouté du code pour **StatusCode500** à l’étape 1 de la section intitulée Configurer une nouvelle instance d’un pipeline de collecte de journaux dans un service cloud. ![][16]
4.	Ouvrez une session Bureau à distance à votre instance de service cloud.
5.	Ouvrez le Gestionnaire des services IIS.
6.	La journalisation IIS est activée par défaut et définie pour générer toutes les heures des fichiers qui contiennent tous les champs au format W3C. Cliquez sur **Parcourir** et il y aura au moins un fichier journal, comme illustré ici : ![][17]

7.	Patientez environ cinq minutes pour permettre à l’agent des diagnostics Azure d’émettre le fichier journal vers le conteneur d’objets BLOB. Pour valider les données, ouvrez l’**Explorateur de serveurs** > **Stockage** > **Compte de stockage** > **Objets BLOB**. Comme illustré ici, l’objet BLOB **iislogs** est créé : ![][18]

8.	Cliquez avec le bouton droit et sélectionnez **Afficher le conteneur d’objets blob** pour afficher le fichier journal IIS stocké dans l’objet BLOB : ![][19]
9.	Une fois que les événements IIS apparaissent dans le compte de stockage du client, les applications qui tirent parti de l’analyse HDInsight peuvent être utilisées pour effectuer l’agrégation d’événements. Le graphique en courbes suivant est un exemple de tâche d’agrégation d’événements qui affiche le code d’état HTTP 500 : ![][20]

## Recommandations en matière de collecte de journaux de sécurité
Vous pouvez suivre les recommandations ci-dessous quand vous collectez des journaux de sécurité :

- Collectez vos propres événements de journal d’audit spécifiques à l’application de service.
- Configurez uniquement les données dont vous avez besoin pour l’analyse et la surveillance. La capture d’un trop grand nombre de données peut compliquer la résolution des problèmes et avoir un impact sur les coûts de stockage ou de service.
- Fusionnez les paramètres de configuration des diagnostics Azure existants avec les modifications apportées. Le nouveau fichier de configuration remplace les paramètres de configuration existants.
- Choisissez avec soin l’intervalle de **période de transfert planifiée**. Des temps de transfert plus courts améliorent la pertinence des données, mais peuvent augmenter la charge de traitement et les coûts de stockage.

>[AZURE.NOTE]L’autre variable qui affecte considérablement la quantité de données collectées est le niveau de journalisation. Voici un exemple montrant comment filtrer les journaux par niveau de journalisation :

    System!*[System[(Level =2)]]

Le niveau de journalisation est cumulatif. Si le filtre a la valeur **Avertissement**, les événements **Erreur** et **Critique** seront également collectés.

- Effacez régulièrement les données de diagnostic du stockage Azure si vous n’en avez plus besoin.

>[AZURE.NOTE]Pour en savoir plus sur les données de diagnostic, consultez [Stocker et afficher des données de diagnostics dans le stockage Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx). Les conteneurs et les tables qui stockent les données de diagnostic ressemblent aux autres conteneurs et tables ; vous pouvez supprimer des objets BLOB et des entités de ceux-ci de la même façon que vous le feriez pour d’autres données. Vous pouvez supprimer les données de diagnostic par programme via l’une des bibliothèques clientes de stockage, ou visuellement via un [client de l’explorateur de stockage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

- Il est recommandé de stocker les données des journaux de sécurité et les données de service dans des comptes de stockage distincts. Cet isolement permet de garantir que l’enregistrement des données des journaux de sécurité n’affecte pas les performances de stockage pour les données de service de production.
- Choisissez la durée de rétention des journaux en fonction de la stratégie de conformité de votre organisation et des besoins en termes d’analyse et de surveillance des données.

## Exportation des journaux de sécurité vers un autre système
Vous pouvez télécharger des données BLOB à l’aide de la bibliothèque cliente de stockage Azure, puis les exporter vers votre système local pour traitement. Pour obtenir un exemple de code pour gérer les données BLOB, consultez [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md).

De même, vous pouvez télécharger des données de sécurité stockées dans les tables Azure à l’aide de la bibliothèque cliente de stockage Azure. Pour en savoir plus sur l’accès aux données stockées dans des tables, consultez [Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md).

## Rapports Azure Active Directory
Azure Active Directory (Azure AD) inclut un ensemble de rapports de journaux de sécurité, d’utilisation et d’audit qui offrent une vue d’ensemble de l’intégrité et la sécurité de votre locataire Azure AD. Par exemple, Azure AD a la capacité d’analyser automatiquement l’activité des utilisateurs et de signaler les accès anormaux qu’il consignera dans des rapports que les clients pourront consulter.

Ces rapports sont disponibles via le [portail de gestion Azure](https://manage.windowsazure.com/) sous **Active Directory** > **Annuaire**. Certains de ces rapports sont gratuits et d’autres sont proposés dans le cadre d’une édition Azure AD Premium. Pour plus d’informations sur les rapports Azure AD, consultez [Affichage de vos rapports d’accès et d’utilisation](http://msdn.microsoft.com/library/azure/dn283934.aspx).

## Journaux des opérations Azure
Les journaux des opérations associées aux ressources de votre abonnement Azure sont également disponibles via la fonctionnalité **Journaux des opérations** dans le portail de gestion.

Pour afficher les **journaux des opérations**, ouvrez le [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Services de gestion**, puis sur **Journaux des opérations**.

## <a name="diagnostics"></a> Sources de données prises en charge par les diagnostics Azure

| Source de données | Description |
|----- | ----- |
| Journaux IIS | Informations sur les sites web IIS |
| Journaux d'infrastructure Azure Diagnostic | Informations sur les diagnostics Azure |
| Journaux d'échecs de requête IIS | Informations sur les demandes qui ont échoué auprès d’une application ou d’un site web IIS |
| Journaux d'événements Windows | Informations envoyées au système de journalisation des événements Windows |
| Compteurs de performances | Système d’exploitation et compteurs de performances personnalisés |
| Vidages sur incident | Informations sur l’état du processus en cas d’incident d’application |
| Journaux d'erreurs personnalisés | Journaux créés par votre application ou service |
| .NET EventSource | Événements générés par votre code à l’aide de la classe EventSource .NET |
| Suivi d’événements pour Windows basé sur les manifestes | Événements de suivi d’événements pour Windows générés par n’importe quel processus |

## Ressources supplémentaires
Les ressources suivantes fournissent des informations générales sur Microsoft Azure et les services Microsoft connexes :

- [Centre de gestion de la confidentialité Microsoft Azure](http://azure.microsoft.com/support/trust-center/)

    Informations sur la façon dont la sécurité et la confidentialité sont intégrées dans le développement d’Azure et dont Azure répond à un grand ensemble de normes de conformité internationales et sectorielles

- [Page d’accueil de Microsoft Azure](http://www.microsoft.com/windowsazure/)

    Informations générales et liens sur Microsoft Azure

- [Centre de documentation Microsoft Azure](http://msdn.microsoft.com/windowsazure/default.aspx)

    Conseils pour les scripts d’automatisation et services Azure

- [Centre de réponse aux problèmes de sécurité Microsoft (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    Le MSRC travaille avec les partenaires et les chercheurs en sécurité dans le monde entier afin d’empêcher les incidents de sécurité et d’améliorer la sécurité des produits Microsoft

- [Adresse électronique du centre de réponse aux problèmes de sécurité Microsoft (MSRC)](mailto:secure@microsoft.com)

    Envoyez un message électronique pour signaler à Microsoft des failles de sécurité, y compris concernant Microsoft Azure

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_1217_2015-->