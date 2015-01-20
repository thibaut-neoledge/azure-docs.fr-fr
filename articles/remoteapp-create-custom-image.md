<properties title="How to create a custom template image for RemoteApp" pageTitle="Création d'une image de modèle personnalisée pour RemoteApp" description="Découvrez comment créer une image de modèle personnalisée pour RemoteApp. Vous pouvez utiliser ce modèle avec un déploiement hybride ou cloud." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Création d'une image de modèle personnalisée pour RemoteApp
Azure RemoteApp utilise une image de modèle Windows Server 2012 R2 pour héberger tous les programmes que vous souhaitez partager avec vos utilisateurs. Pour créer une image de modèle RemoteApp personnalisée, vous pouvez commencer par une image existante ou en créer une. Vous trouverez, ci-dessous, les exigences relatives à l'image qui peut être téléchargée en vue d'être utilisée avec Azure RemoteApp :


- La taille de l'image doit être un multiple de Mo. Si vous tentez de télécharger une image dont la taille n'est pas un multiple exact de Mo, le téléchargement échouera.
- La taille de l'image doit être de 127 Go ou moins. 
- Elle doit se trouver dans un fichier VHD (pour l'instant, les fichiers VHDX ne sont pas pris en charge).
- Le disque dur virtuel ne doit pas être une machine virtuelle de 2e génération.
- Le disque dur virtuel (VHD) peut être de taille fixe ou dynamique. L'utilisation d'un fichier VHD de taille dynamique est recommandée, car le téléchargement sur Azure prend moins de temps qu'un fichier VHD de taille fixe.
- Le disque doit être initialisé à l'aide du type de partitionnement MBR (Enregistrement de démarrage principal). Le style de partition GPT (GUID Partition Table) n'est pas pris en charge. 
- Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul contenant une installation de Windows. 
- Le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur doivent être installés.
- Le rôle service Broker pour les connexions Bureau à distance ne doit *pas* être installé.
- Le système de fichiers EFS (Encrypting File System) doit être désactivé.
- L'image doit être préparée avec Sysprep à l'aide des paramètres **/oobe /generalize /shutdown** (ne PAS utiliser le paramètre **/mode:vm**).


**Avant de commencer**

Avant de créer le service, vous devez effectuer les étapes suivantes :

- S'inscrire à RemoteApp. Vous pouvez le faire à l'adresse [http://azure.microsoft.com/fr-fr/services/remoteapp/](http://azure.microsoft.com/fr-fr/services/remoteapp/).
- Créer un compte d'utilisateur dans Active Directory à utiliser comme compte de service RemoteApp. Limiter les autorisations pour ce compte, de telle sorte qu'il puisse uniquement joindre des ordinateurs au domaine. Consultez [Configuration d'Azure Active Directory pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-ad/) pour plus d'informations.
- Collecter des informations sur votre réseau local : informations sur l'adresse IP et détails du périphérique VPN.
- Installer le module [Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/).
- Collecter des informations sur les utilisateurs auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs.



## **Création d'une image de modèle**##

Pour créer une image de modèle à partir de zéro, procédez comme suit :

1.	Recherchez une image ISO ou DVD Windows Server 2012 R2.
2.	Créez un disque dur virtuel.
4.	Installez Windows Server 2012 R2.
5.	Installez le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur.
6.	Installez les fonctionnalités supplémentaires dont vos applications ont besoin.
7.	Installez et configurez vos applications.
8.	Exécutez les éventuelles opérations de configuration Windows supplémentaires requises par vos applications.
9.	Désactivez le système de fichiers EFS.
9.	Préparez l'image avec SYSPREP.

La procédure détaillée de création d'une image se présente comme suit :

1.	Recherchez une image ISO ou DVD Windows Server 2012 R2. 
2.	Créez un fichier VHD à l'aide de la fonction Gestion des disques. 
	1.	Lancez la fonction Gestion des disques (diskmgmt.msc). 
	2.	Créez un disque dur virtuel de taille dynamique d'une taille minimale de 40 Go. (Estimez la quantité d'espace requise pour Windows, vos applications et vos personnalisations. L'espace requis pour Windows Server avec le rôle RDSH et la fonctionnalité Expérience utilisateur est d'environ 10 Go.)
		1.	Cliquez sur **Action > Créer un disque dur virtuel**.
		2.	Indiquez l'emplacement, la taille et le format du disque dur virtuel. Sélectionnez **Taille dynamique**, puis cliquez sur **OK**.

			Cette opération va prendre plusieurs secondes. Une fois le disque dur virtuel créé, un nouveau disque sans lettre de lecteur et avec l'état " Non initialisé " doit normalement apparaître dans la console Gestion des disques.

		- Cliquez sur le disque (et non sur l'espace non alloué), puis cliquez sur **Initialiser le disque**. Sélectionnez **MBR** (enregistrement de démarrage principal) comme style de partition, puis cliquez sur **OK**.
		- Créez un volume : cliquez avec le bouton droit sur l'espace non alloué, puis sur **Nouveau volume simple**. Vous pouvez accepter les paramètres par défaut de l'Assistant, mais veillez à attribuer une lettre de lecteur afin d'éviter les éventuels problèmes lors du téléchargement de l'image de modèle.
		- Cliquez avec le bouton droit sur le disque, puis cliquez sur **Détacher un disque dur virtuel**.

			



1. Installation de Windows Server 2012 R2 :
	1. Créez une machine virtuelle. Utilisez l'Assistant Nouvel ordinateur virtuel de Hyper-V Manager ou Client Hyper-V. 
		1. Dans la page Spécifier la génération, choisissez **Génération 1**.
		2. Dans la page Connecter un disque dur virtuel, sélectionnez **Utiliser un disque dur virtuel existant**, puis accédez au disque dur virtuel que vous avez créé à l'étape précédente.
		2. Dans la page Options d'installation, sélectionnez **Installer un système d'exploitation à partir d'un CD/DVD-ROM de démarrage**, puis l'emplacement de votre support d'installation Windows Server 2012 R2.
		3. Sélectionnez les autres options de l'Assistant nécessaires pour installer Windows et vos applications. Terminez l'Assistant.
	2.  Une fois l'Assistant terminé, modifiez les paramètres de la machine virtuelle et effectuez les autres modifications nécessaires pour installer Windows et vos programmes (par ex., nombre de processeurs virtuels), puis cliquez sur **OK**.
	4.  Connectez la machine virtuelle et installez Windows Server 2012 R2.
1. Installez le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur :
	1. Lancez le Gestionnaire de serveur.
	2. Cliquez sur **Ajouter des rôles et des fonctionnalités** dans l'écran d'accueil ou le menu **Gérer**.
	3. Cliquez sur **Suivant** dans la page Avant de commencer.
	4. Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis cliquez sur **Suivant**.
	5. Sélectionnez l'ordinateur local dans la liste, puis cliquez sur **Suivant**.
	6. Sélectionnez **Services Bureau à distance**, puis cliquez sur **Suivant**.
	7. Développez **Interfaces utilisateur et infrastructure** et sélectionnez **Expérience utilisateur**.
	8. Cliquez sur **Ajouter des fonctionnalités**, puis sur **Suivant**.
	9. Dans la page Services Bureau à distance, cliquez sur **Suivant**.
	10. Cliquez sur **Hôte de session Bureau à distance**.
	11. Cliquez sur **Ajouter des fonctionnalités**, puis sur **Suivant**.
	12. Dans la page Confirmer les sélections pour l'installation, sélectionnez **Redémarrer automatiquement le serveur de destination, si nécessaire**, puis cliquez sur **Oui** dans l'avertissement de redémarrage.
	13. Cliquez sur **Installer**. L'ordinateur redémarre.
1.	Installez les fonctionnalités supplémentaires requises par vos applications, telles que .NET Framework 3.5. Pour installer les fonctionnalités, exécutez l'Assistant Ajout de rôles et de fonctionnalités.
7.	Installez et configurez les programmes et applications que vous souhaitez publier via RemoteApp.

 	**Important :** 


	- Microsoft recommande d'installer le rôle RDSH avant d'installer des applications pour vous assurer que tous les problèmes de compatibilité des applications sont détectés avant le téléchargement de l'image dans RemoteApp.
	- Assurez-vous que votre application s'affiche dans le menu Démarrer. Vérifiez également que l'icône qui s'affiche dans le menu Démarrer est correcte. Sinon, changez-la. (Vous n'êtes pas *obligé* d'ajouter l'application au menu Démarrer, mais cela facilite la publication de l'application dans RemoteApp. Sinon, vous devez fournir le chemin d'installation de l'application quand vous publiez l'application.)

8.	Exécutez les éventuelles opérations de configuration Windows supplémentaires requises par vos applications.
9.	Désactivez le système de fichiers EFS. Exécutez la commande suivante dans une fenêtre de commande avec élévation de privilèges :

		Fsutil behavior set disableencryption 1

	Une autre solution consiste à définir ou à ajouter la valeur DWORD suivante dans le Registre : 

		HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.	Si vous générez votre image dans une machine virtuelle Azure, renommez le fichier **\%windir%\Panther\Unattend.xml**, car il empêchera de fonctionner le script de téléchargement utilisé plus tard. Remplacez le nom de ce fichier par Unattend.old de façon à conserver le fichier et pouvoir inverser votre déploiement, si nécessaire.
10.	Préparez l'image avec SYSPREP. Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges : 

	**C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**
	
	**Note:** Do not use the **/mode:vm** switch of the SYSPREP command even though this is a virtual machine. 


## Étapes suivantes ##
Maintenant que vous avez votre image de modèle personnalisée, vous devez télécharger cette image dans votre déploiement RemoteApp. Utilisez les informations des articles suivants pour créer votre collection :


- [Création d'une collection hybride de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/)
- [Création d'une collection cloud de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/)


<!--HONumber=35.2-->
