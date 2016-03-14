<properties
	pageTitle="Préparation de votre environnement pour la sauvegarde des machines virtuelles Azure | Microsoft Azure"
	description="Assurez-vous que votre environnement est prêt pour la sauvegarde de machines virtuelles dans Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="sauvegardes ; sauvegarde ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/01/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Préparer votre environnement pour la sauvegarde des machines virtuelles Azure

Avant de pouvoir sauvegarder une machine virtuelle (VM) Azure, trois conditions sont requises.

- Vous devez créer un coffre de sauvegarde ou identifier un coffre de sauvegarde existant *dans la même région que votre machine virtuelle*.
- Établissez une connectivité réseau entre les adresses Internet publiques Azure et les points de terminaison de stockage Azure.
- Installez l’agent de machine virtuelle sur celle-ci.

Si vous savez que ces conditions existent déjà dans votre environnement, passez à l’[article traitant de la sauvegarde de vos machines virtuelles](backup-azure-vms.md). Sinon, continuez votre lecture. Cet article vous guidera dans les étapes nécessaires pour préparer votre environnement pour la sauvegarde d’une machine virtuelle Azure.


## Limites lors de la sauvegarde et la restauration d’une machine virtuelle

>[AZURE.NOTE] Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). La liste suivante indique les limites lors du déploiement dans le modèle classique.

- La sauvegarde de machines virtuelles basées sur Azure Resource Manager (ARM) (ou « IaaS V2 ») n’est pas prise en charge pour le moment.
- La sauvegarde de machines virtuelles ayant plus de 16 disques de données n’est pas prise en charge.
- La sauvegarde de machines virtuelles à l’aide du stockage Premium n’est pas prise en charge.
- La sauvegarde de machines virtuelles avec une adresse IP réservée et sans point de terminaison n’est pas prise en charge.
- Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Commencez par supprimer la machine virtuelle existante et tous les disques associés, puis restaurez les données de sauvegarde.
- La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
- La sauvegarde de machines virtuelles à l’aide du service Azure Backup est prise en charge dans toutes les régions publiques d’Azure (voir la [liste](https://azure.microsoft.com/regions/#services) des régions prises en charge). Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de l’archivage.
- La sauvegarde de machines virtuelles à l’aide du service Azure Backup n’est prise en charge que pour certaines versions de système d’exploitation :
  - **Linux** : consultez la [liste des distributions approuvées par Azure](../virtual-machines/virtual-machines-linux-endorsed-distributions.md). D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle.
  - **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.
	- La restauration d’une machine virtuelle de contrôleur de domaine qui fait partie d’une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. En savoir plus sur la [restauration d’un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-restore-vms.md#restoring-domain-controller-vms).
	- La restauration de machines virtuelles qui ont des configurations réseau spéciales suivantes est prise en charge uniquement par le biais de PowerShell. Les machines virtuelles créées à l’aide du flux de travail de restauration de l’interface utilisateur n’ont pas ces configurations réseau une fois l’opération de restauration terminée. Pour plus d’informations, consultez [Restauration de machines virtuelles avec des configurations de réseau spéciales](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
		- Machines virtuelles avec configuration d’un équilibreur de charge (internes et externes)
		- Machines virtuelles avec plusieurs adresses IP réservées
		- Machines virtuelles avec plusieurs cartes réseau

## Création d’un coffre de sauvegarde pour une machine virtuelle

Un coffre de sauvegarde est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre de sauvegarde contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

Cette image illustre les relations entre les différentes entités Azure Backup : ![Entités et relations Azure Backup](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Pour créer un archivage de sauvegarde :

1. Connectez-vous au [portail Azure](http://manage.windowsazure.com/).

2. Dans le portail Azure, cliquez sur **Nouveau** > **Intégration hybride** > **Sauvegarde**. Lorsque vous cliquez sur **Sauvegarde**, vous accédez automatiquement à la version classique du portail (illustrée après la remarque).

    ![Portail Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Si la dernière utilisation de votre abonnement s’est déroulée dans le portail classique, alors il est possible que votre abonnement s’ouvre dans le portail classique. Dans ce cas, pour créer un coffre de sauvegarde, cliquez sur **Nouveau** > **Data Services** > **Recovery Services** > **Coffre de sauvegarde** > **Création rapide** (voir l’image ci-dessous).

    ![Créer un archivage de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

3. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

4. Dans **Region**, sélectionnez la région géographique du coffre. L’archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans plusieurs régions, vous devez créer un coffre de sauvegarde dans chaque région. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : l’archivage de sauvegarde et le service Azure Backup s’en chargent automatiquement.

5. Sous **Abonnement**, sélectionnez l’abonnement à associer au coffre de sauvegarde. Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.

6. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-prepare/creating-vault.png)

7. Un message confirme que le coffre a été correctement créé. Il est répertorié dans la page **Recovery Services** comme étant **Actif**. Veillez à choisir l’option de redondance de stockage appropriée juste après la création de l’archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultslist.png)

8. Cliquez sur l’archivage de sauvegarde pour accéder à la page **Démarrage rapide** où sont affichées les instructions pour la sauvegarde des machines virtuelles Azure.

    ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-azure-vms-prepare/vmbackup-instructions.png)


## Connectivité réseau

L’extension de sauvegarde nécessite une connectivité aux adresses IP publiques Azure pour fonctionner correctement, car elle envoie des commandes à un point de terminaison Azure Storage (URL HTTP) pour gérer les instantanés de la machine virtuelle. Sans une bonne connectivité Internet, ces requêtes HTTP de la machine virtuelle expirent et l’opération de sauvegarde échoue.

### Restrictions de réseau grâce aux groupes de sécurité réseau

Si votre déploiement comporte des restrictions d’accès (par le biais d’un groupe de sécurité réseau, par exemple), vous devez prendre des mesures supplémentaires pour garantir que le trafic de sauvegarde vers l’archivage de sauvegarde n’est pas affecté.

Il existe deux façons d’offrir un chemin pour le trafic de sauvegarde :

1. Mettez sur liste blanche les [plages IP des centres de données Azure](http://www.microsoft.com/fr-FR/download/details.aspx?id=41653).
2. Déployer un proxy HTTP pour acheminer le trafic.

Le compromis se fait entre la possibilité de gestion, le contrôle granulaire et le coût.

|Option|Avantages|Inconvénients|
|------|----------|-------------|
|OPTION 1 : plages IP de liste blanche| Aucun coût supplémentaire<br><br>Pour l’ouverture d’accès à un groupe de sécurité réseau, utilisez l’applet de commande <i>Set-AzureNetworkSecurityRule</i>. | Difficile à gérer, car les plages IP impactées changent au fil du temps.<br>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage.|
|OPTION 2 : Proxy HTTP| Contrôle granulaire dans le proxy sur les URL de stockage autorisées.<br>Un seul point d’accès Internet aux machines virtuelles.<br>Non soumis aux modifications d’adresse IP Azure.| Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy.|

### Utilisation d’un proxy HTTP pour les sauvegardes de machine virtuelle
Lorsque vous sauvegardez une machine virtuelle, les commandes de gestion de capture instantanée sont envoyées à partir de l’extension de sauvegarde vers le stockage Azure à l’aide d’une API HTTPS. Ce trafic doit être acheminé depuis l’extension au moyen du proxy, car seul le proxy est configuré pour avoir accès à l’Internet public.

>[AZURE.NOTE] Il n’existe aucune recommandation pour le logiciel de serveur proxy qui doit être utilisé. Assurez-vous de choisir un proxy compatible avec les étapes de configuration ci-dessous.

Dans l’exemple ci-dessous, la machine virtuelle d’application doit être configurée pour utiliser la machine virtuelle de proxy pour tout le trafic HTTP pour l’Internet public. La machine virtuelle de proxy doit être configurée pour autoriser le trafic entrant depuis des machines virtuelles dans le réseau virtuel. Enfin, le groupe de sécurité réseau (nommé *verrou de sécurité réseau*) a besoin d’une nouvelle règle de sécurité qui autorise le trafic Internet sortant à partir de la machine virtuelle de proxy.

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

1. Ouvrez le Pare-feu Windows sur le serveur proxy. Pour accéder au pare-feu, le plus simple consiste à rechercher le Pare-feu Windows avec fonctions de sécurité avancées.

    ![Ouvrir le pare-feu](./media/backup-azure-vms-prepare/firewall-01.png)

2. Dans la boîte de dialogue Pare-feu Windows, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle...**.

    ![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-02.png)

3. Dans l’**Assistant Nouvelle règle de trafic entrant**, choisissez l’option **personnalisée** comme **Type de règle**, puis cliquez sur **Suivant**.
4. Dans la page servant à sélectionner le **programme**, choisissez **Tous les programmes**, puis cliquez sur **Suivant**.

5. Dans la page **Protocole et ports**, entrez les informations suivantes, puis cliquez sur **Suivant** :

    ![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-03.png)

    - pour *Type de protocole*, choisissez *TCP*
    - pour *Port local*, choisissez *Ports spécifiques* et dans le champ situé en dessous, spécifiez le ```<Proxy Port>``` qui a été configuré.
    - pour *Port distant*, sélectionnez *Tous les ports*

    Pour le reste de l’Assistant, cliquez jusqu’à la fin et donnez un nom à cette règle.

**C) Ajouter une règle d’exception au groupe de sécurité réseau :**

Dans une invite de commandes Azure PowerShell, saisissez la commande suivante :

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Cette commande ajoute au groupe de sécurité réseau une exception autorisant le trafic TCP à partir d’un port 10.0.0.5 de n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS). Si vous devez joindre un port spécifique de l’Internet public, ajoutez également ```-DestinationPortRange```.

*Veillez à remplacer les noms dans l’exemple par les détails correspondant à votre déploiement.*


## Agent VM

Avant de sauvegarder la machine virtuelle Azure, vérifiez que l’agent de machine virtuelle Azure est correctement installé sur la machine virtuelle. L’agent de machine virtuelle étant un composant facultatif au moment de la création de la machine virtuelle, vérifiez que la case de l’agent de machine virtuelle est cochée avant d’approvisionner la machine virtuelle.

### Installation et mise à jour manuelles

L’agent de machine virtuelle est déjà présent dans les machines virtuelles créées à partir de la galerie Azure. Cependant, les machines virtuelles migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite. Pour en savoir plus, consultez la page [Installation de l’agent de machine virtuelle sur une machine virtuelle existante](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle | <li>Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. <li>[Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. | <li>Installez l’[agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de GitHub. Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. <li> [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |
| Mise à jour de l’agent de machine virtuelle | La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. | Suivez les instructions fournies dans la rubrique [Mise à jour d’un agent de machine virtuelle Linux](../virtual-machines-linux-update-agent.md). <br><br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Validation de l’installation de l’agent de machine virtuelle | <li>Accédez au dossier *C:\\WindowsAzure\\Packages* dans la machine virtuelle Azure. <li>Le fichier WaAppAgent.exe doit être présent.<li> Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure. | N/A |


En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extension de sauvegarde

Pour sauvegarder la machine virtuelle, le service Azure Backup installe une extension vers l’agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l'extension de sauvegarde sans intervention supplémentaire de l'utilisateur.

L’extension de sauvegarde est installée si la machine virtuelle est en cours de fonctionnement. Une machine virtuelle en cours d’exécution présente également le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de la machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée (c’est-à-dire, si la machine virtuelle est hors connexion). Dans ce cas, le point de récupération est *cohérent suite à l’incident* comme indiqué ci-dessus.


## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes
À présent que votre environnement est prêt pour sauvegarder votre machine virtuelle, l’étape logique suivante consiste à créer une sauvegarde. L’article sur la planification fournit des informations détaillées sur la sauvegarde des machines virtuelles.

- [Sauvegarde de machines virtuelles](backup-azure-vms.md)
- [Planification de votre infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
- [Gestion des sauvegardes de machines virtuelles](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0302_2016-->