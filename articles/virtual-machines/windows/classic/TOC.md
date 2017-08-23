# Vue d'ensemble
## [À propos des machines virtuelles](../../virtual-machines-windows-about.md)
## [Disques et disques durs virtuels](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [Réseaux virtuels](../../../virtual-network/virtual-networks-overview.md)
## [FAQ](faq.md)
## [Comparaison des machines virtuelles, des sites web et des services cloud](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [Conteneurs](../../virtual-machines-windows-containers.md)

# Prise en main
## [Créer une machine virtuelle à l’aide du portail](tutorial.md)
## [Se connecter à une machine virtuelle](connect-logon.md)
## [Installation d'Azure PowerShell](/powershell/azure/overview)
## [Installation de l’interface de ligne de commande Azure](../../../cli-install-nodejs.md)

# Procédures

## Utiliser le stockage
### [Association d’un disque de données](attach-disk.md)
### [Détachement d'un disque de données](detach-disk.md)
### [Utiliser le lecteur D: comme disque de données](../../virtual-machines-windows-change-drive-letter.md)

## Réseau
### [Configuration des points de terminaison](setup-endpoints.md)
### [Connecter des machines virtuelles à un réseau virtuel ou à un service cloud](connect-vms.md)
### [Connecter des réseaux virtuels classiques à des réseaux virtuels Resource Manager](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [Créer un équilibrage de charge](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Gérer les groupes de sécurité réseau avec Azure PowerShell](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## Déployer
### [Créer une machine virtuelle personnalisée](createportal.md)
### [Créer et configurer une machine virtuelle à l’aide d’Azure Powershell](create-powershell.md)
### [Capturer une machine virtuelle Windows](capture-image.md)
### [Créer et charger un disque dur virtuel à l’aide de PowerShell](createupload-vhd.md)
### [Automatiser le déploiement de machine virtuelle Azure avec Chef](../../virtual-machines-windows-chef-automation.md)
### [Créer et gérer des machines virtuelles dans Visual Studio](manage-visual-studio.md)
### [Créer une machine virtuelle pour une application web avec Visual Studio](web-app-visual-studio.md)
### [Exécuter une tâche nécessitant beaucoup de ressources en langage Java](java-run-compute-intensive-task.md)
### [Application web Django Hello World](python-django-web-app.md)

## Configuration
### [Réinitialiser un mot de passe ou le service Bureau à distance](../../virtual-machines-windows-reset-rdp.md)
### [Installer et configurer Symantec Endpoint Protection](install-symantec.md)
### [Installer et configurer Trend Micro Deep Security comme service](install-trend.md)
### [Configurer un groupe à haute disponibilité](configure-availability.md)
### [Redimensionner une machine virtuelle Windows créée avec le modèle de déploiement classique](resize-vm.md)
### [Maintenance](planned-maintenance-schedule.md)

## Gérer
### [Migration à partir de déploiements classiques vers des déploiements Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Gérer vos machines virtuelles à l'aide d'Azure PowerShell](manage-psh.md)
### [À propos de l’agent et des extensions de machine virtuelle](agents-and-extensions.md)
### [Gérer les extensions de machine virtuelle](manage-extensions.md)
### [Extension de script personnalisé pour les machines virtuelles](extensions-customscript.md)
### [Injection de données personnalisées dans une machine virtuelle Azure](inject-custom-data.md)

## Planification
### [À propos des images](about-images.md)
### [Tailles de machines virtuelles](../../virtual-machines-windows-sizes.md)
### [Maintenance planifiée des machines virtuelles Azure](../../virtual-machines-windows-planned-maintenance.md)
### [Instructions d’implémentation des services d’infrastructure Azure](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## Gérer des charges de travail
### [HPC (High-Performance Computing, calcul haute performance)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [Mettre à l’échelle automatiquement des ressources](hpcpack-cluster-node-autogrowshrink.md)
#### [Gérer des nœuds de calcul](hpcpack-cluster-node-manage.md)
#### [Créer un cluster](hpcpack-cluster-powershell-script.md)
#### [Configurer un cluster pour exécuter des applications MPI](hpcpack-rdma-cluster.md)
#### [Exécuter des charges de travail Excel et SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Créer le nœud principal avec une image de Marketplace](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [Soumettre des travaux locaux vers Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## Résolution des problèmes
### [Connexions Bureau à distance](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[Étapes de dépannage détaillées pour les problèmes de connexion du Bureau à distance](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [Accéder à une application](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [Problèmes de déploiement classique liés à la création d’une machine virtuelle](troubleshoot-deployment-new-vm.md)
### [Problèmes de déploiement classique liés au redémarrage ou au redimensionnement d’une machine virtuelle existante](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [Réinitialiser le mot de passe RDP](reset-rdp.md)
### [Attacher un disque dur virtuel pour résoudre des problèmes de machine virtuelle](troubleshoot-recovery-disks-portal.md)

# Référence
## [PowerShell](/powershell/azure/overview)
## [Interface de ligne de commande Azure](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Créer des modèles Resource Manager](../../../resource-group-authoring-templates.md)
## [Modèles fournis par la communauté](https://azure.microsoft.com/documentation/templates)
## [REST Compute](/rest/api/compute)
## [REST Réseau](/rest/api)
## [REST Stockage](/rest/api/storageservices)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Tarification](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilité régionale](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
