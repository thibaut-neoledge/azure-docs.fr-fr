



Pour plus d’informations sur les agents de machine virtuelle et la manière dont ils prennent en charge les extensions de machine virtuelle, consultez [Vue d’ensemble de l’agent de machine virtuelle et des extensions de machine virtuelle](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

##Extensions de machine virtuelle Azure

Les extensions de machine virtuelle mettent en œuvre la plupart des fonctionnalités stratégiques à utiliser avec vos machines virtuelles, notamment des fonctionnalités de base telles que la réinitialisation des mots de passe, la configuration de RDP, et bien d'autres encore. Comme de nouvelles extensions sont ajoutées en permanence, le nombre de fonctionnalités disponibles pour vos machines virtuelles Azure augmente lui aussi. Par défaut, de nombreuses extensions de machine virtuelle de base sont installées lorsque vous créez votre machine virtuelle à partir de la galerie, par exemple **IaaSDiagnostics** (actuellement uniquement pour les machines virtuelles Windows), **VMAccess** et **BGInfo** (également uniquement pour les machines virtuelles Windows). Cependant, toutes les extensions ne sont pas mises en œuvre sur Windows et Linux à un moment précis, à cause du flux constant de nouvelles extensions et de mises à jour.

##Connectivité et gestion de base

Les extensions suivantes sont indispensables pour activer, réactiver ou désactiver la connectivité de base avec vos machines virtuelles une fois qu'elles sont créées et sont en cours d'exécution

|Nom de l'extension de machine virtuelle|Description de la fonctionnalité|Informations complémentaires
|---|---|---|
|VMAccessAgent (Windows)|Créez, mettez à jour et réinitialisez les informations utilisateur et la configuration des connexions RDP.|[Windows](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md)
|VMAccessForLinux (Linux)|Créez, mettez à jour et réinitialisez les informations utilisateur et la configuration des connexions SSH.|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##Gestion du déploiement et de la configuration

Les extensions suivantes prennent en charge différents types de scénarios de déploiement et de gestion de la configuration et des fonctionnalités. Consultez également la rubrique concernant les opérations et la gestion de machines virtuelles ci-dessous.

|Nom de l'extension de machine virtuelle|Description de la fonctionnalité|Informations complémentaires|
|---|---|---|
|**MSEnterpriseApplication**|Met en œuvre des fonctionnalités de prise en charge par Windows System Center.|[Rôles de machine virtuelle System Center 2012 R2](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus Deploy** (basé sur une extension DSC)|Prend en charge le déploiement automatisé d'applications web ASP.NET et de services Windows dans des environnements de développement, de test et de production.|[Mise en route d'Octopus Deploy](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Visual Studio Release Manager** (basé sur une extension DSC)|Prend en charge un déploiement continu avec Visual Studio.|[Automatiser les déploiements avec Release Management](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|Crée un client Chef sous Windows. (Peut également utiliser l’extension DSC, voir ci-dessous.)|[Chef et Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|Installe le démon Docker pour prendre en charge des commandes Docker distantes.|[Utilisation de l’extension de machine virtuelle Docker](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)Pour plus d’informations, voir le [Guide de l’utilisateur Docker VM Extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md)|
|**DSC**|Extension PowerShell DSC (Desired State Configuration).|[Extension Azure PowerShell DSC (Desired State Configuration, configuration d'état souhaité)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|Met en œuvre les fonctionnalités de Puppet Enterprise. |[Puppet sur Azure](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux)|Appelle des scripts personnalisés sur la machine virtuelle à tout moment : au démarrage ou pendant le cycle de vie.|[Extension de script personnalisé](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|Consomme les données de diagnostics collectées par **IaaSDiagnostics** et d’autres sources de données telles que les [mesures Azure Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx) et les transforme en un jeu de données agrégé adapté au processus de contrôle d’hôte SAP.|[Surveillance Azure améliorée pour SAP](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##Sécurité et protection

Les extensions abordées dans cette section fournissent des fonctionnalités de sécurité stratégiques pour vos machines virtuelles Azure.

|Nom de l'extension de machine virtuelle|Description de la fonctionnalité|Informations complémentaires|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Permet aux clients Microsoft Azure de chiffrer leurs données de machine virtuelle dans une infrastructure partagée mutualisée et de contrôler entièrement les clés de chiffrement pour les données chiffrées dans l'infrastructure de stockage Azure.|[Sécurisation des machines virtuelles Microsoft Azure utilisant le chiffrement BitLocker et le système d'exploitation natif](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|Protège votre machine virtuelle contre les logiciels malveillants.|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|Active la prise en charge de la plate-forme Deep Security de TrendMicro pour détecter et bloquer les intrusions et les logiciels malveillants, ajouter un pare-feu, gérer la réputation web, inspecter le journal et surveiller l'intégrité.|[Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Azure](../articles/virtual-machines/virtual-machines-windows-classic-install-trend.md)|
|**PortalProtectExtension**|Protège des menaces pesant sur votre environnement Microsoft SharePoint.|[Sécurisation de votre déploiement SharePoint sur Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Microsoft Antimalware pour les services cloud Azure et les machines virtuelles est une fonctionnalité de protection en temps réel qui permet d'identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant lorsque des logiciels malveillants ou indésirables connus tentent de s'installer ou de s'exécuter sur votre système.|[Télécharger la documentation sur la protection contre les logiciels malveillants](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 assure la sécurité et les performances sur les systèmes physiques et virtuels.|[Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure](../articles/virtual-machines/virtual-machines-windows-classic-install-symantec.md)

##Fonctionnement et gestion des machines virtuelles

Prend en charge des fonctionnalités de gestion des opérations courantes. Référez-vous également à la rubrique concernant la gestion du déploiement et de la configuration ci-dessus.

|**Nom de l'extension de machine virtuelle**|Description de la fonctionnalité|Informations complémentaires|
|---|---|---|
|**AzureVmLogCollector**|Vous pouvez utiliser l’extension **AzureVMLogCollector** à la demande pour exécuter une collecte unique de journaux depuis une ou plusieurs machines virtuelles de service cloud (à partir des rôles Web et de travail) et transférer les fichiers collectés vers un compte de stockage Azure, le tout sans connexion à distance à une machine virtuelle quelconque. |[AzureLogCollector Extension](../articles/virtual-machines/virtual-machines-windows-log-collector-extension.md)|
|**IaaSDiagnostics**|Active, désactive et configure Azure Diagnostics et est également utilisé par **AzureCATExtensionHandler** pour prendre en charge la surveillance SAP.|[Surveillance des machines virtuelles Microsoft Azure avec l'extension Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|Permet aux administrateurs de machines virtuelles Azure d'automatiser les mises à jour du système d'exploitation des machines virtuelles à l'aide de configurations personnalisées. Vous pouvez utiliser l’extension OSPatching pour configurer des mises à jour du système d’exploitation de vos machines virtuelles. Vous pouvez notamment spécifier la fréquence et le moment de l’installation des correctifs de système d’exploitation, spécifier les correctifs à installer et configurer le comportement de redémarrage après les mises à jour.|[Billet de blog OS Patching Extension](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Voir aussi le fichier Lisez-moi et la source sur GitHub dans [OS Patching Extension](https://github.com/Azure/azure-linux-extensions) (Extension OSPatching).|

##Développement et débogage

Ces extensions de machine virtuelle sont répertoriées ici par souci d’exhaustivité, car elles prennent en charge des fonctionnalités Visual Studio et ne sont pas destinées à être utilisées directement.

|Nom de l'extension de machine virtuelle|Description de la fonctionnalité|Informations complémentaires|
|---|---|---|
|**VS14CTPDebugger**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.4|[Débogage à distance dans Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.4||
|**VS2012Debugger**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.4||
|**RemoteDebugVS14CTP**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.3||
|**RemoteDebugVS2013**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.3||
|**RemoteDebugVS2012**|Permet le débogage à distance depuis Visual Sudio à l'aide du Kit de développement logiciel (SDK) Azure 2.3||
|**WebDeployForVSDevTest**|Installe et configure IIS et Web Deploy sur Windows Server. Sa suppression ou sa désactivation ne sont pas prises en charge.|

##Fonctionnalités diverses

Ces extensions permettent la prise en charge d'autres fonctionnalités de machine virtuelle qui peuvent être utiles.

|Nom de l'extension de machine virtuelle|Description de la fonctionnalité|Informations complémentaires|
|---|---|---|
|**BGInfo**|Présente une image consolidée des informations de serveur utiles sur le Bureau lors de l'utilisation de RDP.|[Extension BGInfo](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|Installe, configure et conserve les pilotes de périphérique réseau avec accès direct à la mémoire à distance (RDMA) sur une machine virtuelle A8 ou A9 exécutant Windows Server 2012 R2 ou Windows Server 2012. Permet aux machines virtuelles A8 ou A9 en cluster d’utiliser le réseau RDMA pendant l’exécution d’applications MPI parallèles.|[À propos des instances de calcul intensif A8, A9, A10 et A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0330_2016-->