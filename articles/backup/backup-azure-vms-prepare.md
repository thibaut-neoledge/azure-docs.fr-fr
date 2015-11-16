<properties
	pageTitle="Préparation de votre environnement pour la sauvegarde des machines virtuelles Azure | Microsoft Azure"
	description="Assurez-vous que votre environnement est prêt pour la sauvegarde des machines virtuelles Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Préparer votre environnement pour la sauvegarde des machines virtuelles Azure
Avant de sauvegarder une machine virtuelle Azure, vous devez remplir ces conditions préalables requises pour préparer votre environnement. Si cela est déjà fait, vous pouvez démarrer la [sauvegarde de vos machines virtuelles](backup-azure-vms.md) ; sinon, exécutez les étapes ci-dessous pour vous assurer que votre environnement est prêt.


## 1\. Archivage de sauvegarde

![Archivage de sauvegarde](./media/backup-azure-vms-prepare/step1.png)

Pour démarrer la sauvegarde de vos machines virtuelles Azure, vous devez d'abord créer un archivage de sauvegarde. Un archivage est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. L’archivage contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

Cette image illustre les relations entre les différentes entités d’Azure Backup : ![Entités et relation d’Azure Backup](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Pour créer un archivage de sauvegarde :

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez l’abonnement correct à associer à l’archivage de sauvegarde. Dans chaque abonnement Azure, vous pouvez avoir plusieurs archivages de sauvegarde pour organiser les machines virtuelles protégées.

3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.

4. Dans **Region**, sélectionnez la région géographique du coffre. L'archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans différentes régions, créez un archivage dans chacune d’elles. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : l'archivage de sauvegarde et le service Azure Backup s'en chargent automatiquement.

    ![Créer un archivage de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-prepare/creating-vault.png)

6. Un message confirme que l'archivage a été correctement créé et l'archivage est affiché dans la page Services de récupération avec l'état Actif. Assurez-vous que l'option de redondance de stockage appropriée est choisie juste après la création de l'archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Cliquez sur l’archivage de sauvegarde pour accéder à la page **Démarrage rapide**, où sont affichées les instructions pour la sauvegarde des machines virtuelles Azure.

    ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\. Connectivité réseau

![Connectivité réseau](./media/backup-azure-vms-prepare/step2.png)

L’extension de sauvegarde requiert une connectivité aux IP publiques Azure pour fonctionner correctement, car elle envoie des commandes à un point de terminaison Azure Storage (URL HTTP) pour gérer les instantanés de la machine virtuelle. Sans la bonne connectivité Internet, ces requêtes HTTP de la machine virtuelle expirent et l’opération de sauvegarde échoue.

### Restrictions de réseau grâce aux groupes de sécurité réseau

Si votre déploiement comporte des restrictions d’accès (via un groupe de sécurité réseau, par exemple), vous devez prendre des mesures supplémentaires pour garantir que le trafic de sauvegarde vers le coffre de sauvegarde Azure ne restera pas affecté.

Il existe deux façons d’offrir un chemin pour le trafic de sauvegarde :

1. Mettez sur liste blanche les [plages IP de datacenter Azure](http://www.microsoft.com/fr-FR/download/details.aspx?id=41653).
2. Déployer un proxy HTTP pour acheminer le trafic.

Le compromis se fait entre la possibilité de gestion, le contrôle granulaire et le coût.

|Option|Avantages|Inconvénients|
|------|----------|-------------|
|OPTION 1 : plages IP de liste blanche| Aucun coût supplémentaire<br><br>Pour l’ouverture d’accès à un groupe de sécurité réseau, utilisez l’applet de commande <i>AzureNetworkSecurityRule de jeu</i> | Difficile à gérer, car les plages IP impactées changent au fil du temps, <br>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage.|
|OPTION 2 : Proxy HTTP| Contrôle granulaire dans le proxy sur les URL de stockage autorisées, <br>un seul point d’accès Internet aux machines virtuelles,<br>non soumises aux modifications d’adresse Azure IP| Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy.|

### Utilisation d’un proxy HTTP pour la sauvegarde de la machine virtuelle
Lorsque vous sauvegardez une machine virtuelle, les commandes de gestion de capture instantanée sont envoyées à partir de l’extension de sauvegarde vers le stockage Azure à l’aide d’une API HTTPS. Ce trafic doit être acheminé depuis l’extension via le proxy, car seul le proxy est configuré sera pour avoir accès à l’Internet public.

>[AZURE.NOTE]Il n’existe aucune recommandation pour le logiciel de serveur proxy qui doit être utilisé. Assurez-vous de choisir un proxy compatible avec les étapes de configuration mentionnées ci-dessous.

Dans l’exemple ci-dessous, la machine virtuelle d’application doit être configurée pour utiliser la machine virtuelle de Proxy pour tout le trafic HTTP pour l’internet public. La machine virtuelle de Proxy doit être configurée pour autoriser le trafic entrant depuis des machines virtuelles dans le réseau virtuel. Et enfin, le groupe de sécurité réseau (nommé *verrou de sécurité réseau*) a besoin d’une nouvelle règle de sécurité qui autorise une règle de sécurité autorisant trafic Internet sortant à partir de la machine virtuelle de Proxy.

![Groupe de sécurité réseau avec diagramme de déploiement du proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) permettre les connexions réseau sortantes :**

1. Pour les machines Windows, exécutez la commande suivante dans une invite de commande élevée :

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	Cela permet d’installer une configuration de proxy au niveau de l’ordinateur et de l’utiliser pour le trafic HTTP/HTTPS sortant.

2. Pour les machines Linux, ajoutez la ligne suivante au fichier ```/etc/environment``` :

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	Ajoutez les lignes suivantes au fichier ```/etc/waagent.conf``` :

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) Autoriser les connexions entrantes sur le serveur proxy :**

1. Ouvrez le pare-feu Windows sur le serveur proxy ; cliquez avec le bouton droit sur *Règles de trafic entrant*, puis cliquez sur **Nouvelle règle...**.

	![Ouvrir le pare-feu](./media/backup-azure-vms-prepare/firewall-01.png)

	![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-02.png)
2. Dans l’*Assistant Nouvelle règle de trafic entrant* choisissez l’option **personnalisée** comme *Type de règle*, puis cliquez sur Suivant. Dans l’écran servant à sélectionner le *programme*, choisissez **Tous les programmes**, puis cliquez sur Suivant.

3. Dans l’écran *Protocoles et ports*, utilisez les entrées dans le tableau ci-dessous et cliquez sur Suivant :

	![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-03.png)

| Champ d’entrée | Valeur |
| --- | --- |
| Type de protocole | TCP |
| Port local | Sélectionnez *Ports spécifiques* dans la liste déroulante puis, dans la zone de texte, saisissez le ```<Proxy Port>``` configuré. |
| Port distant | Dans la liste déroulante, sélectionnez *tous les Ports*. |

Pour le reste de l’Assistant, cliquez jusqu’à la fin et donnez un nom à cette règle.

**C) Ajouter une règle d’exception au groupe de sécurité réseau :**

Dans une invite de commandes Azure PowerShell, saisissez la commande suivante :

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Cette commande ajoute au groupe de sécurité réseau une exception autorisant le trafic TCP à partir de n’importe quel port 10.0.0.5 de n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS). Si vous devez joindre un port spécifique de l’Internet public, veillez à ajouter ```-DestinationPortRange``` également.

*Veillez à remplacer les noms dans l’exemple par les détails correspondant à votre déploiement.*

## 3\. Agent VM

![Agent VM](./media/backup-azure-vms-prepare/step3.png)

Avant de sauvegarder la machine virtuelle Azure, assurez-vous que l'agent de machine virtuelle Azure est correctement installé sur la machine virtuelle. L'agent de machine virtuelle étant un composant facultatif au moment de la création de la machine virtuelle, assurez-vous que la case de l'agent de machine virtuelle est cochée avant de configurer la machine virtuelle.

### Installation et mise à jour manuelles

L’agent de machine virtuelle est déjà présent dans les machines virtuelles qui sont créées à partir de la galerie Azure. Cependant, les machines virtuelles qui sont migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite. Pour en savoir plus, consultez la page [Installation de l’agent de machine virtuelle sur une machine virtuelle existante](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle | <li>Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. <li>[Mettez à jour la propriété de machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. | <li> Installez l’[agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de github. Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. <li> [Mettez à jour la propriété de machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |
| Mise à jour de l’agent de machine virtuelle | La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Assurez-vous qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. | Suivez les instructions fournies dans la rubrique [Mise à jour d’un agent de machine virtuelle Linux](../virtual-machines-linux-update-agent.md). <br><br>Assurez-vous qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Validation de l’installation de l’agent de machine virtuelle | <li>Accédez au dossier *C:\\WindowsAzure\\Packages* dans la machine virtuelle Azure. <li>Le fichier WaAppAgent.exe doit être présent.<li> Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure | - |


En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extension de sauvegarde

Pour sauvegarder la machine virtuelle, le service Azure Backup installe une extension vers l'agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l'extension de sauvegarde sans intervention supplémentaire de l'utilisateur.

L’extension de sauvegarde est installée si la machine virtuelle est en cours de fonctionnement. Une machine virtuelle en cours d'exécution présente également le plus de chance d'obtenir un point de récupération d'application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de machine virtuelle, même si elle est éteinte et si l'extension n'a pas été installée (c'est-à-dire, si la machine virtuelle est hors ligne). Dans ce cas, le point de récupération sera *cohérent suite à l’incident* comme indiqué ci-dessus.


## Limites

- La sauvegarde de machines virtuelles basées sur Azure Resource Manager (ou « IaaS V2 ») n'est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plus de 16 disques de données n'est pas prise en charge.
- La sauvegarde de machines virtuelles à l'aide du stockage Premium n'est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plusieurs adresses IP réservées n'est pas prise en charge.
- La sauvegarde de machines virtuelles avec une adresse IP réservée et aucun point de terminaison défini n'est pas prise en charge.
- La sauvegarde de machines virtuelles à l'aide de plusieurs cartes réseau n'est pas prise en charge.
- La sauvegarde de machines virtuelles dans une configuration à charge équilibrée (interne ou accessible via Internet) n'est pas prise en charge.
- Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Commencez par supprimer la machine virtuelle existante et tous les disques associés, puis restaurez les données de sauvegarde.
- La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
- La sauvegarde de machines virtuelles à l'aide du service Azure Backup n'est pas prise en charge dans toutes les régions publiques d'Azure. Voici la [liste](http://azure.microsoft.com/regions/#services) des régions prises en charge. Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de l’archivage.
- La sauvegarde de machines virtuelles à l'aide du service Azure Backup n'est prise en charge que pour certaines versions de système d'exploitation :
  - **Linux** : la liste des distributions approuvées par Azure est disponible [ici](../virtual-machines-linux-endorsed-distributions.md). D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle.
  - **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.
- La restauration d'une machine virtuelle de contrôleur de domaine qui fait partie d'une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. En savoir plus sur la [restauration d’un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes

- [Planification de votre infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
- [Sauvegarde de machines virtuelles](backup-azure-vms.md)
- [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO2-->