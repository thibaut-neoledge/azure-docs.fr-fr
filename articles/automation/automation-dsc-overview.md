<properties 
   pageTitle="Vue d'ensemble d'Azure Automation DSC" 
   description="Vue d'ensemble de la configuration d'état souhaité (DSC) Azure Automation, les termes s'y rapportant et les problèmes connus" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="05/04/2015"
   ms.author="coreyp"/>

# Vue d'ensemble d'Azure Automation DSC #

>[AZURE.IMPORTANT]**Azure Automation DSC est actuellement en version préliminaire limitée**, et n'est pas pris en charge pour les charges de production. Pour le moment, il est basé uniquement sur des applets de commande et ne dispose d'aucune interface graphique utilisateur. En vous inscrivant à la version préliminaire d'Azure Automation DSC, vous reconnaissez que cette fonctionnalité est en version préliminaire et qu'elle est soumise à des conditions d'utilisation différentes ou limitées, tel que stipulé dans le [contrat de service](https://go.microsoft.com/fwLink/p/?LinkID=389530&clcid=0x409). Vous reconnaissez également accepter les [Conditions d'utilisation supplémentaires de la version préliminaire](https://go.microsoft.com/fwLink/p/?LinkID=247638&clcid=0x409). Bien qu'actuellement ce service puisse être utilisé gratuitement, une tarification sera introduite par la suite.

## Qu'est-ce que PowerShell DSC ? ##
Le service de configuration d'état souhaité (DSC, Desired State Configuration) est une nouvelle plateforme de gestion de Windows PowerShell qui permet de gérer la configuration d'hôtes physiques et de machines virtuelles à l'aide d'une syntaxe PowerShell déclarative.

DSC fournit un ensemble d'extensions de langage Windows PowerShell, de nouvelles applets de commande Windows PowerShell, ainsi que des ressources que vous pouvez utiliser pour spécifier de manière déclarative la façon dont vous voulez que votre environnement logiciel soit configuré. Il fournit également un moyen de tenir à jour et de gérer des configurations existantes.

### Applications pratiques ###
Voici quelques exemples de scénarios où vous pouvez utiliser des ressources DSC intégrées pour configurer et gérer un ensemble de machines (également appelées « nœuds cibles ») de manière automatisée :

- Activation ou désactivation de fonctionnalités et de rôles de serveur
- Gestion de paramètres du Registre
- Gestion de fichiers et de répertoires
- Démarrage, arrêt et gestion de processus et de services
- Gestion de groupes et de comptes d'utilisateur
- Déploiement de nouveaux logiciels
- Gestion de variables d'environnement
- Exécution de scripts Windows PowerShell
- Correction d'une configuration qui s'est éloignée de l'état souhaité
- Détection de l'état de configuration actuel sur un nœud donné

En outre, vous pouvez créer des ressources personnalisées pour configurer l'état de tout paramètre d'application ou système.


Pour en savoir plus sur PowerShell DSC, consultez le blog suivant : [Configuration in a DevOps world - Windows PowerShell Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx)

##Qu'est-ce qu'Azure Automation DSC ?##
Azure Automation DSC s'appuie sur les concepts de base introduits dans PowerShell DSC pour fournir une expérience de gestion des configurations encore plus facile. Azure Automation DSC apporte la même couche de gestion à la [configuration d'état souhaité PowerShell](https://technet.microsoft.com/library/dn249912.aspx) <link> que celle proposée aujourd'hui par Azure Automation pour l'écriture de scripts PowerShell.

Azure Automation DSC vous permet de [créer et de gérer des configurations d'état souhaité PowerShell](https://technet.microsoft.com/library/dn249918.aspx), d'importer des [ressources DSC](https://technet.microsoft.com/library/dn282125.aspx) et de générer des configurations de nœuds DSC (documents MOF), le tout dans le cloud. Ces éléments DSC seront placés sur le [server collecteur DSC](https://technet.microsoft.com/library/dn249913.aspx) Azure Automation afin que les nœuds cibles (tels que les machines virtuelles et physiques) dans le cloud ou localement puissent les choisir, se conformer à l'état qu'elle spécifie et retourner à Azure Automation un rapport attestant de leur conformité à l'état souhaité.

## Conditions d'utilisation d'Azure Automation DSC ##
### Configuration ###
PowerShell DSC a introduit un nouveau concept appelé « configurations ». Les configurations vous permettent de définir, via la syntaxe PowerShell, l'état souhaité pour votre environnement. Pour configurer votre environnement à l'aide de DSC, commencez par définir un bloc de script Windows PowerShell en utilisant le mot clé « configuration », suivi d'un identificateur, puis d'accolades ({}) pour délimiter le bloc.

![texte de remplacement](./media/automation-dsc-overview/AADSC_1.png)

Dans le bloc de configuration, vous pouvez définir des blocs de configuration de nœuds qui spécifient la configuration souhaitée pour un ensemble de nœuds (machines) de votre environnement qui doivent être configurés exactement de la même manière. De cette façon, une configuration de nœuds représente un « rôle » qu'un ou plusieurs nœuds doivent adopter. Un bloc de configuration de nœuds commence par le mot clé « node ». Faites suivre ce mot clé du nom du rôle, qui peut être une variable. Après le nom de la machine, utiliser des accolades {} pour délimiter le bloc de configuration de nœuds.

![alt text](./media/automation-dsc-overview/AADSC_2.png)
 
Dans le bloc de configuration de nœuds, vous pouvez définir des blocs de ressources pour configurer les ressources DSC spécifiques. Un bloc de ressources commence par le nom de la ressource, suivi de l'identificateur que vous souhaitez spécifier pour ce bloc, puis d'accolades {} pour délimiter le bloc.

![texte de remplacement](./media/automation-dsc-overview/AADSC_3.png)

Pour plus d'informations sur le mot clé « configuration », consultez le blog suivant : [Understanding Configuration Keyword in Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Présentation du mot clé « configuration » dans la configuration d'état souhaité")

L'exécution (compilation) d'une configuration DSC produit une ou plusieurs configurations de nœuds DSC (documents MOF), qui sont les nœuds DSC devant être en conformité avec l'état souhaité.

Azure Automation DSC vous permet d'importer, de créer et de compiler des configurations DSC dans Azure Automation, de la même façon que les Runbooks peuvent être importés, créés et démarrés dans Azure Automation.

Azure Automation DSC fournit actuellement les applets de commande suivantes dans le **module PowerShell Azure Resource Manager** pour la gestion des configurations DSC :

- `Get-AzureAutomationDscConfiguration`
- `Import-AzureAutomationDscConfiguration`

>[AZURE.IMPORTANT]Une configuration ne doit contenir qu'un seul bloc de configuration, et doit porter le même nom que la configuration dans Azure Automation DSC.

###Configuration de nœuds###

Lors de la compilation d'une configuration DSC, une ou plusieurs configurations de nœuds sont générées, en fonction des blocs Node de la configuration. Une configuration de nœud est semblable à un document « MOF » ou « document de configuration » (si ces termes PS DSC vous sont familiers) et représente un « rôle », par exemple de serveur web ou de travail, dont un ou plusieurs nœuds doivent adopter l'état souhaité.

Les nœuds PS DSC sont informés des configurations de nœuds qu'ils doivent adopter via des méthodes Push ou Pull DSC. Azure Automation DSC repose sur la méthode Pull DSC, où les nœuds demandent des configurations de nœuds qu'ils doivent appliquer à partir de serveurs collecteurs Azure Automation DSC. Étant donné que les nœuds effectuent la demande à Azure Automation DSC, ils peuvent se trouver derrière des pare-feu, avoir tous les ports entrants fermés, etc. Ils ont uniquement besoin d'un accès sortant à Internet.

Azure Automation DSC fournit actuellement les applets de commande suivantes dans le **module PowerShell Azure Resource Manager** pour la gestion des configurations de nœuds DSC : `Get-AzureAutomationDscNodeConfiguration`


###Nœud###

Un nœud DSC est une machine dont la configuration est gérée par DSC. Il peut s'agir d'une machine virtuelle Azure ou bien d'une machine virtuelle locale/d'un physique hôte. Les nœuds adoptent des configurations de nœuds pour devenir conformes et assurer leur conformité avec l'état souhaité qu'ils définissent. Ils peuvent également envoyer un rapport sur leur état de configuration et leur conformité à un serveur de rapports.

Azure Automation DSC facilite l'intégration de nœuds en vue de leur gestion par Azure Automation DSC, et permet de changer la configuration de nœud affectée à chaque nœud côté serveur, de sorte que la prochaine fois qu'un nœud recherchera des instructions sur le serveur, il adoptera un rôle différent et modifiera sa configuration afin de s'y conformer. Les nœuds signalent également à Azure Automation DSC leur état et leur conformité à la configuration.

Azure Automation DSC fournit actuellement les applets de commande suivantes dans le [module PowerShell Azure Resource Manager](../powershell-azure-resource-manager.md) pour la gestion des nœuds DSC :

-	`Get-AzureAutomationDscNode`  
-	`Register-AzureAutomationDscNode` (utilisée pour intégrer des machines virtuelles Azure v2 en tant que nœuds)
-	`Get-AzureAutomationDscOnboardingMetaconfig` (utilisée pour intégrer des nœuds)
-	`Set-AzureAutomationDscNode` (utilisée pour définir/mettre à jour les mappages de configuration nœud à nœud)
-	`Unregister-AzureAutomationDscNode`
-	`Get-AzureAutomationDscNodeReport`
-	`Export-AzureAutomationDscNodeReportContent`

L'applet de commande `Get-AzureAutomationRegistrationInfo` peut être utilisée pour obtenir l'URL et la clé d'enregistrement nécessaires pour intégrer des machines virtuelles Azure classiques à un compte Azure Automation, via l'extension de machine virtuelle Azure Automation DSC dans le portail Azure ou via PowerShell.


Extension de machine virtuelle Azure Automation DSC :

![texte de remplacement](./media/automation-dsc-overview/AADSC_4.png)


PowerShell :

    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""


    # get Azure Automation DSC registration info
    Switch-AzureMode AzureResourceManager

    $Account = Get-AzureAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName

    $RegistrationInfo = $Account | Get-AzureAutomationRegistrationInfo


    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    Switch-AzureMode AzureServiceManagement

    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
        SasToken = ""
        ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfig.zip"
        ConfigurationFunction = "RegistrationMetaConfig.ps1\RegistrationMetaConfig"

        # update these DSC agent configurations if these defaults are not what you want. 
        # See https://technet.microsoft.com/fr-fr/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
        Properties = @{
            RegistrationKey = $RegistrationInfo.PrimaryKey
            RegistrationUrl = $RegistrationInfo.Endpoint
            NodeConfigurationName = "configname.webserver"
            ConfigurationMode = "ApplyAndMonitor"
            ConfigurationModeFrequencyMins = 15
            RefreshFrequencyMins = 30
           RebootNodeIfNeeded = $False
           ActionAfterReboot = "ContinueConfiguration"
           AllowModuleOverwrite = $False
        }
    } 

    $VM = Set-AzureVMExtension `
        -VM $vm `
        -Publisher Microsoft.Powershell `
        -ExtensionName DSC `
        -Version 1.9 `
        -PublicConfiguration $PublicConfiguration

    $VM | Update-AzureVM
 

###Ressource###
Les ressources DSC créent des blocs que vous pouvez utiliser pour définir une configuration d'état souhaité (DSC) Windows PowerShell. DSC est fourni avec un ensemble de fonctionnalités intégrées permettant de configurer des ressources telles que des fichiers et des dossiers, des fonctionnalités et des rôles de serveur, des paramètres du Registre, des variables d'environnement, ainsi que des services et des processus. Pour connaître la liste complète des ressources DSC intégrées et savoir comment les utiliser, consultez [Ressources de configuration d'état souhaité Windows PowerShell intégrées](https://technet.microsoft.com/library/dn249921.aspx).

Il est également possible d'importer des ressources DSC dans le cadre des modules PowerShell pour étendre l'ensemble de ressources DSC intégrées. Les ressources autres que les ressources par défaut seront extraites par les nœuds DSC à partir du serveur collecteur DSC, si la configuration de nœud que le nœud est censé adopter contient des références à ces ressources. Pour savoir comment créer des ressources personnalisées, consultez [Créer des ressources de configuration d'état souhaité Windows PowerShell personnalisées](https://technet.microsoft.com/library/dn249927.aspx).

Azure Automation DSC est fourni avec exactement les mêmes ressources DSC intégrées que PS DSC. Des ressources supplémentaires peuvent être ajoutées à Azure Automation DSC en important dans Azure Automation des modules PowerShell contenant les ressources.

Azure Automation DSC fournit actuellement les applets de commande suivantes dans le [module PowerShell Azure Resource Manager](../powershell-azure-resource-manager.md) pour la gestion des nœuds DSC :

- `New-AzureAutomationModule`
- `Remove-AzureAutomationModule`
- `Set-AzureAutomationModule`
- `Get-AzureAutomationModule`

###Tâche de compilation###
Dans Azure Automation DSC, une tâche de compilation est une instance de compilation d'une configuration destinée à créer une ou plusieurs configurations de nœuds. Elles sont semblables aux tâches de Runbook Azure Automation, sauf qu'elles n'effectuent en fait aucune tâche excepté créer des configurations de nœuds. Toutes les configurations de nœuds créées par une tâche de compilation sont automatiquement placées sur le serveur collecteur Azure Automation DSC, et remplacent les versions précédentes des configurations de nœuds, si elles existaient pour cette configuration. Le nom d'une configuration de nœud produite par une tâche de compilation se présente sous la forme « <Configuration-name>.<Node configuration-block-name> ». Par exemple, la compilation de la configuration ci-dessous produirait une configuration de nœud unique appelée « MyConfiguration.webserver »


![alt text](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]À l'instar des Runbooks, les configurations peuvent être publiées. Cela n'est pas lié au placement des éléments DSC sur le serveur collecteur Azure Automation DSC. Suite à une tâche de compilation, les éléments DSC sont placés sur le serveur collecteur Azure Automation DSC. Pour plus d'informations sur la « publication » dans Azure Automation, consultez [Publication d'un Runbook](https://msdn.microsoft.com/library/dn903765.aspx).


Azure Automation DSC fournit actuellement les applets de commande suivantes dans le [module PowerShell Azure Resource Manager](../powershell-azure-resource-manager.md) pour la gestion des tâches de compilation :

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Pièges/Problèmes connus :##

- Dans la mesure où Azure Automation DSC est en version préliminaire, lorsque vous utilisez cette fonctionnalité pour la première fois, vous devez vous y inscrire à l'aide d'applets de commande PowerShell Azure. Vous pouvez vous inscrire en appelant les deux applets de commande suivantes :

 - `Register-AzureProvider –ProviderNamespace Microsoft.Automation`
 - `Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation` 
 
>[AZURE.NOTE]L'inscription à Azure Automation DSC peut prendre jusqu'à une heure avant que la fonctionnalité puisse être utilisée.

- Azure Automation DSC ne prend pas en charge les configurations DSC partielles ou composites pour le moment.

- La dernière version de WMF 5 doit être installée pour que l'agent PowerShell DSC pour Windows puisse communiquer avec Azure Automation. L'agent PowerShell DSC pour Linux ne prend pas en charge la communication avec Azure Automation pour le moment. Cela devrait être mis à jour prochainement.

- Azure Automation ne prend pas en charge l'utilisation côte à côte de modules PowerShell. Cela signifie que toutes les configurations au sein d'un compte Automation doivent fonctionner avec la dernière version d'un module PowerShell importé dans ce compte Automation, ainsi qu'avec toutes les ressources PowerShell DSC contenues dans ce module et que la configuration utilise.

- Le serveur collecteur PowerShell DSC traditionnel s'attend à ce que des fichiers ZIP de modules soient placés sur le serveur collecteur au format **NomModule_Version.zip**. Azure Automation s'attend à ce que des modules PowerShell soient importés avec des noms sous la forme **NomModule.zip**. Pour plus d'informations sur le format de module d'intégration nécessaire pour importer le module dans Azure Automation, consultez [ce billet de blog](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/).

- Les modules PowerShell qui spécifient des ressources DSC côte à côte dans le module, en utilisant le format « version par dossier », ne fonctionneront pas dans Azure Automation pour le moment.

- En cas d'importation d'un module PowerShell dans Azure Automation à l'aide des applets de commande `New-AzureAutomationModule` ou `Set-AzureAutomationModule`, l'importation du module est asynchrone. Même si l'applet de commande retourne un résultat avec succès, cela ne signifie pas que l'importation du module a réussi. Pour vérifier si l'importation a réussi, utilisez `Get-AzureAutomationModule –Name <ModuleName>` (veillez à obtenir uniquement ce module, et non pas tous les modules), puis vérifiez que le champ **ProvisioningState** du module indique la réussite (« succeeded »).

- Les modules PowerShell importés dans Azure Automation ne peuvent pas contenir de fichiers .doc ou .docx. Certains modules PowerShell contenant des ressources DSC contiennent ces fichiers à des fins d'aide. Ces fichiers doivent être supprimés des modules avant l'importation de ces derniers dans Azure Automation.

- Dans Azure Automation, les configurations prennent actuellement en charge uniquement les chaînes correspondant à des valeurs de paramètres. Si vous voulez passer autre chose qu'une chaîne pour la valeur d'un paramètre, passez la valeur de paramètre en tant que chaîne JSON, puis, dans la configuration, utilisez ConvertFrom-Json pour reconvertir la chaîne JSON en valeur qui n'est pas une chaîne.

- Lorsqu'un nœud est d'abord enregistré avec un compte Azure Automation, ou que le nœud est modifié pour être mappé à une autre configuration de nœud côté serveur, son état sera conforme, même s'il n'est pas réellement conforme à la configuration de nœud à laquelle qu'il est maintenant mappé. Dès que le nœud a envoyé son premier rapport après l'inscription ou qu'un mappage de configuration de nœud a changé, l'état du nœud peut être approuvé.

- Lorsqu'une machine virtuelle Azure est intégrée pour la gestion avec Azure Automation DSC à l'aide de `Register-AzureAutomationDscNode`, `Set-AzureAutomationDscExtension`, ou de l'extension de machine virtuelle Azure Automation DSC dans le portail Azure en version préliminaire, si l'inscription échoue avec le message **Le nom de l'ordinateur n'a pas été indiqué et le répertoire de configuration ne contient aucun fichier de configuration**, il s'agit d'une fausse alerte et l'inscription de la machine virtuelle a réussi. Il est possible de vérifier si l'inscription a réussi à l'aide de l'applet de commande `Get-AzureAutomationDscNode`.

 

<!---HONumber=62-->