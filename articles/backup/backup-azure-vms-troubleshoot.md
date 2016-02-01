<properties
	pageTitle="Dépannage de la sauvegarde de machines virtuelles Azure | Microsoft Azure"
	description="Dépannage de la sauvegarde et de la restauration de machines virtuelles Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="trinadhk;jimpark;aashishr"/>


# Dépannage de la sauvegarde de machine virtuelle Azure
Vous pouvez résoudre les erreurs rencontrées pendant l’utilisation d’Azure Backup à l’aide des informations figurant dans le tableau ci-dessous.

## Découverte

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Échec de la découverte de nouveaux éléments : Microsoft Azure Backup a rencontré une erreur interne. Patientez quelques minutes et recommencez l’opération. | Recommencez le processus de découverte après 15 minutes.
| Découverte | Échec de la découverte de nouveaux éléments : une autre opération de découverte est déjà en cours. Veuillez patienter jusqu’à la fin de l’opération de découverte en cours. | Aucun |

## S’inscrire
| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| S’inscrire | Le nombre de disques de données attachés à la machine virtuelle a dépassé la limite autorisée : Détachez des disques de données de cette machine virtuelle et recommencez l’opération La sauvegarde Azure prend en charge jusqu’à 16 disques de données rattachés à une machine virtuelle Azure à des fins de sauvegarde. | Aucun |
| S’inscrire | Microsoft Azure Backup a rencontré une erreur interne. Veuillez patienter quelques minutes et réessayez l’opération. Si le problème persiste, contactez le support technique Microsoft. | Vous pouvez obtenir cette erreur en raison d’une des configurations non prises en charge suivantes : <ul><li>LRS Premium</ul> |
| S’inscrire | Échec de l’inscription avec un délai d’expiration de l’opération Installer l’agent | Vérifiez si la version de système d’exploitation de la machine virtuelle est prise en charge. |
| S’inscrire | Échec de l’exécution de la commande - Une autre opération est en cours sur cet élément Attendez que l’opération précédente aboutisse | Aucun |
| S’inscrire | Les machines virtuelles dotées de disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge par la sauvegarde | Aucun |
| S’inscrire | L’agent de machine virtuelle n’est pas présent sur la machine virtuelle. Veuillez installer les logiciels prérequis, l’agent de machine virtuelle, puis relancez l’opération. | [En savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |

## Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Sauvegarde | La copie des disques durs virtuels à partir de l’archive de sauvegarde a expiré - Retentez l’opération dans quelques minutes. Si le problème persiste, contactez le support technique Microsoft. | Cela se produit lorsqu’il y a trop de données à copier. Vérifiez que vous disposez de moins de 16 disques de données. |
| Sauvegarde | Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané. La sous-tâche de machine virtuelle de capture instantanée a expiré. Consultez le guide de dépannage pour connaître la procédure de résolution de ce problème. | Cette erreur est générée si un problème existe avec l’agent de machine virtuelle ou si l’accès réseau à l’infrastructure Azure est bloqué. <ul> <li>En savoir plus sur le [débogage des problèmes d’agent de machine virtuelle](#vm-agent) <li>En savoir plus sur le [débogage des problèmes de mise en réseau](#networking) <li>Si l’agent de machine virtuelle fonctionne correctement, découvrez comment [résoudre les problèmes de capture instantanée de machine virtuelle](#Troubleshoot-VM-Snapshot-Issues)</ul><br>Si l’agent de machine virtuelle ne génère aucun problème, redémarrez la machine virtuelle. Il arrive que l’état incorrect d’une machine virtuelle provoque des problèmes et le redémarrage de la machine virtuelle réinitialise cet « état incorrect » |
| Sauvegarde | Échec de l’opération de sauvegarde avec une erreur interne - Retentez l’opération dans quelques minutes. Si le problème persiste, contactez le support technique Microsoft. | Vous pouvez obtenir cette erreur pour 2 raisons : <ol><li> Il y a trop de données à copier. <li>La machine virtuelle d’origine a été supprimée, et par conséquent la sauvegarde ne peut pas être effectuée. Afin de conserver les données de sauvegarde d’une machine virtuelle supprimée, mais d’arrêter les erreurs de sauvegarde, ôtez la protection de la machine virtuelle, puis choisissez l’option de conservation des données. Cela empêchera la planification de sauvegarde, ainsi que l’affichage récurrent de messages d’erreur. |
| Sauvegarde | Échec de l’installation de l’extension Azure Recovery Services sur l’élément sélectionné - L’agent VM est un composant requis pour l’extension Azure Recovery Services. Installez d’abord l’agent Azure VM, puis recommencez l’opération d’inscription. | <ol> <li>Vérifiez si l’agent de machine virtuelle a été installé correctement. <li>Vérifiez que l’indicateur de la configuration de la machine virtuelle est défini correctement.</ol> [En savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |
| Sauvegarde | Échec de l’exécution de la commande - Une autre opération est en cours sur cet élément. Attendez que l’opération précédente aboutisse, puis réessayez. | Une sauvegarde ou un travail de restauration existant pour la machine virtuelle est en cours d’exécution, et aucun nouveau travail ne peut être démarré pendant l’exécution d’un travail existant. |
| Sauvegarde | L’installation de l’extension a échoué en renvoyant l’erreur « COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator | Cela signifie généralement que le service COM+ ne fonctionne pas. Pour résoudre ce problème, contactez le support Microsoft. |
| Sauvegarde | L’opération d’instantané a échoué, en renvoyant l’erreur d’opération VSS « Ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du panneau de configuration. | Désactivez BitLocker pour tous les lecteurs sur l’ordinateur virtuel et vérifiez si le problème VSS est résolu |
| Sauvegarde | Les machines virtuelles dotées de disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge par la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure introuvable | Cela se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle pour effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources [nom du service cloud] <br>(OU) <li> Désactivez la protection de cette machine virtuelle pour empêcher la création de travaux de sauvegarde.</ol> |
| Sauvegarde | L’agent de machine virtuelle n’est pas présent sur la machine virtuelle. Veuillez installer les logiciels prérequis, l’agent de machine virtuelle, puis relancez l’opération. | [En savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |

## Travaux
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | L’annulation n’est pas prise en charge pour ce type de tâche. Veuillez patienter jusqu’à ce que le travail soit terminé. | Aucun |
| Annuler le travail | Le travail n’est pas dans un état annulable. Veuillez patienter jusqu’à ce que le travail soit terminé. <br>OU<br> Le travail sélectionné n’est pas dans un état annulable. Attendez que le travail se termine.| Selon toute probabilité, le travail est presque terminé ; veuillez patienter jusqu’à ce que le travail soit terminé |
| Annuler le travail | Impossible d’annuler le travail, car il n’est pas en cours. L’annulation est prise en charge uniquement pour les travaux en cours. Veuillez tenter l’annulation de la tâche en cours d’exécution. | Cet événement est dû à un état provisoire. Attendez une minute et relancez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler le travail. Veuillez patienter jusqu’à la fin du travail. | Aucun |


## Restauration
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Échec de la restauration avec une erreur interne du cloud | <ol><li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec des paramètres DNS. Vous pouvez vérifier <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si une adresse est configurée, cela signifie que des paramètres DNS sont configurés.<br> <li>Le service cloud vers lequel vous essayez de restaurer est configuré avec une adresse IP réservée et les machines virtuelles existantes dans le service cloud sont dans un état arrêté.<br>Vous pouvez voir si un service cloud utilise des adresses IP réservées à l’aide des applets de commande Powershell suivantes :<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Vous essayez de restaurer une machine virtuelle avec les configurations réseau spéciales suivantes dans le même service cloud. <br>- Machines virtuelles sous configuration de l’équilibreur de charge (interne et externe)<br>- Machines virtuelles avec plusieurs adresses IP réservées<br>- Machines virtuelles avec plusieurs cartes réseau<br>Sélectionnez un nouveau service cloud dans l’interface utilisateur ou reportez-vous aux [considérations relatives à la restauration](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations réseau spéciales</ol> |
| Restauration | Le nom DNS sélectionné est déjà utilisé. Veuillez spécifier un autre nom DNS et réessayez. | Le nom DNS fait référence au nom du service cloud (se termine généralement par .cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom de machine virtuelle pendant la restauration. <br><br> Notez que cette erreur ne s’affiche que pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell va réussir, car elle ne fait que restaurer les disques et ne crée pas de machine virtuelle. L’erreur se rencontre lorsque la machine virtuelle est explicitement créée par vos soins après l’opération de restauration du disque. |
| Restauration | La configuration de réseau virtuel spécifiée n’est pas correcte. Veuillez indiquer une autre configuration de réseau virtuel et réessayez. | Aucun |
| Restauration | Le service cloud spécifié utilise une adresse IP réservée, ce qui ne correspond pas à la configuration de machine virtuelle en cours de restauration. Veuillez spécifier un autre service cloud n’utilisant pas l’adresse IP réservée ou choisir un autre point de restauration. | Aucun |
| Restauration | Le service cloud a atteint la limite du nombre de points de terminaison d’entrée. Recommencez l’opération en spécifiant un autre service cloud ou en utilisant un point de terminaison existant. | Aucun |
| Restauration | Le coffre de stockage et le compte de stockage cible se trouvent dans deux régions différentes. Vérifiez que le compte de stockage spécifié dans l’opération de restauration se trouve dans la même région Azure que le coffre de sauvegarde. | Aucun |
| Restauration | Le compte de stockage spécifié pour l’opération de restauration n’est pas pris en charge. Seuls les comptes de stockage de base/Standard avec des paramètres de réplication redondants au niveau géographique ou local sont pris en charge. Veuillez sélectionner un compte de stockage pris en charge | Aucun |
| Restauration | Le type de compte de stockage spécifié pour l’opération de restauration n’est pas en ligne. Assurez-vous que le compte de stockage spécifié dans l’opération de restauration est en ligne | Cela peut se produire cas d’erreur temporaire dans le stockage Azure ou en cas de panne. Veuillez choisir un autre compte de stockage. |
| Restauration | Le quota de groupe de ressources a été atteint. Veuillez supprimer certains groupes de ressources de la version préliminaire du portail ou contactez l’assistance Azure pour étendre les limites. | Aucun |
| Restauration | Le sous-réseau sélectionné n’existe pas. Veuillez sélectionner un sous-réseau qui existe | Aucun |


## Stratégie
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Créer une stratégie | Impossible de créer la stratégie. Veuillez réduire les choix en matière de rétention pour poursuivre la configuration de la stratégie. | Aucun |


## Agent VM

### Configuration de l’agent de machine virtuelle
En règle générale, l’agent de machine virtuelle est déjà présent dans les machines virtuelles qui sont créées à partir de la galerie Azure. Cependant, les machines virtuelles qui sont migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite. Pour en savoir plus, consultez la page [Installation de l’agent de machine virtuelle sur une machine virtuelle existante](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles Windows :

- Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin de privilèges d’administrateur pour terminer l’installation.
- [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

Pour les machines virtuelles Linux :

- Installez l’[agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de github.
- [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.


### Mise à jour de l’agent de machine virtuelle
Pour les machines virtuelles Windows :

- La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Toutefois, vous devez vous assurer qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle.

Pour les machines virtuelles Linux :

- Suivez les instructions fournies dans la rubrique [Mise à jour d’un agent de machine virtuelle Linux](../virtual-machines-linux-update-agent.md).


### Validation de l’installation de l’agent de machine virtuelle
Pour vérifier la version de l’agent de machine virtuelle sur les machines virtuelles Windows :

1. Ouvrez une session sur la machine virtuelle Azure et accédez au dossier *C:\\WindowsAzure\\Packages*. Le fichier WaAppAgent.exe doit être présent.
2. Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure

## Résoudre les problèmes de capture instantanée de machine virtuelle
La sauvegarde de machine virtuelle émet une commande de capture instantanée à destination du stockage sous-jacent. Le fait de ne pas avoir accès au stockage ou tout retard dans l’exécution d’une tâche de capture instantanée peut faire échouer la sauvegarde. Voici les causes possibles de l’échec d’une tâche de capture instantanée.

1. Accès réseau au stockage bloqué par l’utilisation du groupe de sécurité réseau<br> Découvrez plus en détail comment [activer l’accès réseau](backup-azure-vms-prepare.md#2-network-connectivity) au stockage en utilisant une liste d’adresses IP approuvées ou à travers un serveur proxy.
2.  Les machines virtuelles sur lesquelles la sauvegarde SQL Server est configurée peuvent occasionner des retards dans les tâches de capture instantanée. <br> Par défaut, la sauvegarde de machine virtuelle émet une sauvegarde complète VSS sur les machines virtuelles Windows. Sur les machines virtuels qui exécutent des serveurs SQL Server et sur lesquelles la sauvegarde SQL Server est configurée, cela peut occasionner des retards d’exécution des captures instantanées. Définissez la clé de Registre suivante si vous rencontrez des échecs de sauvegarde en raison de problèmes de capture instantanée.

	```
	[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
	"USEVSSCOPYBACKUP"="TRUE"
	```
3.  État de la machine virtuelle rapporté de manière incorrecte en raison de l’arrêt de la machine virtuelle dans RDP. <br> Si vous avez arrêté la machine virtuelle dans RDP, retournez sur le portail pour vérifier que l’état de la machine virtuelle est correct. Si ce n’est pas le cas, arrêtez la machine virtuelle sur le portail à l’aide de l’option « Arrêter » dans le tableau de bord de la machine virtuelle.
4.  Plusieurs machines virtuelles du même service cloud sont configurées pour exécuter la sauvegarde en même temps.<br> Il est recommandé d’étaler les planifications de sauvegarde entre les différentes machines virtuelles d’un même service cloud.
5.  Forte sollicitation du processeur/de la mémoire sur la machine virtuelle.<br> Si la machine virtuelle sollicite fortement le processeur (utilisation supérieure à 90 %) ou la mémoire, la tâche de capture instantanée est mise en file d’attente, et le retard qui en résulte débouche sur une expiration de délai. En pareille situation, essayez de procéder à des sauvegardes à la demande.

<br>

## Mise en réseau
Comme toutes les extensions, l’extension de sauvegarde a besoin d’accéder à l’Internet public pour fonctionner. En l’absence d’accès Internet public, plusieurs cas de figure sont possibles :

- L’installation de l’extension peut échouer.
- Les opérations de sauvegarde (telles que l’instantané de disque) peuvent échouer.
- L’affichage de l’état de l’opération de sauvegarde peut échouer.

La nécessité de résoudre des adresses Internet publiques a été évoquée [ici](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vous devez vérifier la configuration DNS du réseau virtuel et vous assurer que les URI Azure peuvent être résolus.

Une fois que la résolution de noms a été effectuée correctement, l’accès aux adresses IP Azure doit également être fourni. Pour débloquer l’accès à l’infrastructure Azure, effectuez une des opérations suivantes :

1. Mettez sur liste blanche les plages IP de datacenter Azure.
    - Obtenez la liste des [adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) à débloquer.
    - Débloquez les adresses IP à l’aide de l’applet de commande [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx). Exécutez cette applet de commande dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécution en tant qu’administrateur).
    - Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l'accès aux adresses IP.
2. Créez un chemin d'accès pour le trafic HTTP
    - Si vous des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic. Les étapes du déploiement d’un serveur proxy HTTP sont expliquées [ici](backup-azure-vms-prepare.md#2-network-connectivity).
    - Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l'accès à Internet à partir du proxy HTTP.

>[AZURE.NOTE]Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne. Si vous avez besoin d’une adresse IP privée statique, vous devez la configurer via la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être laissée désactivée. Pour plus d’informations sur la définition d’une adresse IP privée interne statique, cliquez [ici](virtual-networks-reserved-private-ip.md).

<!---HONumber=AcomDC_0121_2016-->