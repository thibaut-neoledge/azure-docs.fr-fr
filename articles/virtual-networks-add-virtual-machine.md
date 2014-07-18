<properties linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

Ajout d'une machine virtuelle à un réseau virtuel
=================================================

Ce didacticiel vous guide lors de la création d'un compte de stockage Azure et d'une machine virtuelle à ajouter à un [réseau virtuel](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure.

**Important**

Si vous envisagez de créer une machine virtuelle pour installer une nouvelle forêt Active Directory, suivez les instructions de la rubrique [Installation d'une nouvelle forêt Active Directory dans Azure](../active-directory-forest/).

Objectifs
---------

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   [Création d'un compte de stockage](#CreateStorageAcct)

-   [Création et déploiement d'une machine virtuelle vers un réseau virtuel](#CreateVM)

Conditions préalables
---------------------

-   Vous devez avoir suivi l'un des didacticiels suivants :

    -   [Création d'un réseau virtuel dans Azure](/en-us/manage/services/networking/create-a-virtual-network/)

        OU

    -   [Création d'un réseau virtuel pour la connectivité entre différents locaux](/en-us/manage/services/networking/cross-premises-connectivity/)

-   Vous devez avoir un compte Windows Live avec au moins un abonnement actif en cours de validité.

-   Vous devez utiliser les noms des éléments du didacticiel [Création d'un réseau virtuel dans Azure](/en-us/manage/services/networking/create-a-virtual-network/) ou [Création d'un réseau virtuel pour la connectivité entre différents locaux](/en-us/manage/services/networking/cross-premises-connectivity/) :

    -   Le groupe d'affinités que vous avez attribué à votre réseau virtuel

    -   Le nom de votre réseau virtuel

    -   Les noms de vos sous-réseaux

Création d'un compte de stockage
--------------------------------

1.  Après avoir créé votre réseau virtuel dans le [portail de gestion Azure](http://manage.windowsazure.com/), dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**.

    ![NouveauCompteDeStockage](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  Dans le volet de navigation, cliquez sur **DATA SERVICES**, **STORAGE**, puis sur **QUICK CREATE**.

    ![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  Entrez les informations suivantes, puis cliquez sur la coche dans le coin inférieur droit de l'écran.
 -   **URL :** tapez *votrestockage*.
 -   **REGION/AFFINITY GROUP :** dans la liste déroulante, sélectionnez **YourAffinityGroup**.
 -   **ENABLE GEO-REPLICATION :** laissez cette case à cocher activée.

    ![CréerNouveauCompte](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.  Sur la page **Storage**, la colonne **STATUS** affiche **Online** une fois le processus terminé.

    ![NouveauCompteDeStockageCréé](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)

Création et déploiement d'une machine virtuelle vers un réseau virtuel
----------------------------------------------------------------------

**Pour créer et déployer une machine virtuelle vers un réseau virtuel :**

1.  Après avoir créé votre compte de stockage, dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**.

    ![NouvelleMachineVirtuelle](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  Dans le volet de navigation, cliquez sur **COMPUTE**, **MACHINE VIRTUELLE**, puis sur **FROM GALLERY**.

    ![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  Dans l'écran **VM OS Selection**, sélectionnez **Windows Server 2008 R2 SP1, octobre 2012** (ou la version la plus récente disponible), puis cliquez sur la flèche Suivant.

    ![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)

4.  Dans l'écran **Configuration de machine virtuelle**, entrez les informations suivantes, puis cliquez sur la flèche Suivant.

    **Conseil :** notez quelque part le nom d'utilisateur et le mot de passe, car vous aurez besoin de ces informations d'identification pour vous connecter à votre nouvelle machine virtuelle.
	-   **VIRTUAL MACHINE NAME :** tapez *VotreMachineV*.
	-   **NEW USER NAME :** en lecture seule.
	-   **NEW PASSWORD :** entrez un mot de passe fort.
	-   **CONFIRM PASSWORD :** entrez de nouveau le mot de passe.
	-   **SIZE :** sélectionnez **Small**.

    ![ConfigMV](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.  Dans l'écran **Virtual machine mode**, entrez les informations suivantes, puis cliquez sur la flèche Suivant.
	-   **Standalone Virtual Machine :** laissez cette option sélectionnée.
	-   **DNS NAME :** tapez *votreapplicationcloud*.
	-   **STORAGE ACCOUNT :** sélectionnez **yourstorage**.
	-   **REGION/AFFINITY GROUP/VIRTUAL NETWORK :** dans la liste déroulante, sélectionnez **YourVirtualNetwork**.

    ![ModeMV](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.  Dans l'écran **Virtual machine options**, entrez les informations suivantes, puis cliquez sur la coche. Votre machine virtuelle va maintenant être créée. Cela peut prendre jusqu'à 10 minutes.
	-   **AVAILABILITY SET :** sélectionnez **none**.
	-   **VIRTUAL NETWORK SUBNETS :** sélectionnez **FrontEndSubnet**.

    **REMARQUE**

    Vous devez sélectionner au moins un sous-réseau et NE PAS sélectionner le sous-réseau de la passerelle.

    ![OptionsMV](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

1.  Lorsque vos machines virtuelles ont été créées, dans l'écran correspondant, le statut **STATUS** est défini sur **Running**.

    ![InstancesMV](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)

2.  Dans le volet de navigation, cliquez sur **ALL ITEMS**. Tous les objets que vous avez créés seront affichés avec leur statut actuel.

    ![OngTous](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

Étapes suivantes
----------------

Pour installer un contrôleur de domaine supplémentaire pour votre domaine Active Directory local sur la machine virtuelle que vous venez de créer, consultez la rubrique [Installation d'un contrôleur de domaine Active Directory de réplica dans un réseau virtuel Azure](/en-us/manage/services/networking/replica-domain-controller/).

Voir aussi
----------

-   [Réseau virtuel Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [Configuration d'un réseau virtuel à l'aide de fichiers de configuration de réseau](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)


