<properties
	pageTitle="Définition du lecteur D d'une machine virtuelle comme lecteur de données | Microsoft Azure"
	description="Décrit comment modifier les lettres de lecteurs pour une machine virtuelle Windows créée à l'aide du modèle de déploiement classique, afin que vous puissiez utiliser le lecteur D: comme lecteur de données."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# Utilisation du lecteur D comme lecteur de données sur une machine virtuelle Windows 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Si vous avez besoin d'utiliser le lecteur D pour stocker des données, suivez ces instructions pour utiliser une autre lettre de lecteur pour le disque temporaire. N'utilisez jamais le disque temporaire pour stocker des données à conserver.

## Association du disque de données

Tout d'abord, vous devrez attacher le disque de données à la machine virtuelle. Pour attacher un nouveau disque, consultez [Association d'un disque de données à une machine virtuelle Windows][Attach].

Si vous souhaitez utiliser un disque de données existant, assurez-vous que vous avez également téléchargé le disque dur virtuel vers le compte de stockage. Pour plus d'instructions, consultez les étapes 3 et 4 de la rubrique [Création et téléchargement d'un disque dur virtuel Windows Server dans Azure][VHD].


## Déplacement temporaire du fichier pagefile.sys vers le lecteur C

1. Connexion à la machine virtuelle. 

2. Avec le bouton droit, cliquez sur le menu **Démarrer** et sélectionnez **Système**.

3. Dans le menu de gauche, sélectionnez **Paramètres système avancés**.

4. Dans la section **Performances**, sélectionnez **Paramètres**.

5. Sélectionnez l'onglet **Avancé**.

5. Dans la section **Mémoire virtuelle**, sélectionnez **Modifier**.

6. Sélectionnez le lecteur **C**, puis cliquez sur **Taille gérée par le système** et sur **Définir**.

7. Sélectionnez le lecteur **D**, puis cliquez sur **Aucun fichier d'échange** et sur **Définir**.

8. Cliquez sur Appliquer. Vous serez averti que l'ordinateur doit être redémarré pour que les modifications prennent effet.

9. Redémarrez la machine virtuelle.




## Modification des lettres de lecteurs 

1. Lorsque la machine virtuelle a redémarré, connectez-vous de nouveau.

2. Cliquez sur le menu **Démarrer**, tapez **diskmgmt.msc** et appuyez sur Entrée. La Gestion des disques démarre.

3. Avec le bouton droit, cliquez sur **D**, le disque de stockage temporaire, puis sélectionnez **Modifier la lettre de lecteur et les chemins d'accès**.

4. Sous la lettre de lecteur, sélectionnez le lecteur **G**, puis cliquez sur **OK**.

5. Avec le bouton droit, cliquez sur le disque de données, puis sélectionnez **Modifier la lettre de lecteur et les chemins d'accès**.

6. Sous la lettre de lecteur, sélectionnez le lecteur **D**, puis cliquez sur **OK**.

7. Avec le bouton droit, cliquez sur **G**, le disque de stockage temporaire, puis sélectionnez **Modifier la lettre de lecteur et les chemins d'accès**.

8. Sous la lettre de lecteur, sélectionnez le lecteur **E**, puis cliquez sur **OK**.

> [AZURE.NOTE]Si votre machine virtuelle a d'autres disques ou lecteurs, utilisez la même méthode pour réaffecter les lettres de lecteur des autres disques et lecteurs. Vous voulez que la configuration des disques soit la suivante : - C : disque du système d'exploitation - D : disque des données - E : disque temporaire



## Déplacement de pagefile.sys vers le disque de stockage temporaire 

1. Avec le bouton droit, cliquez sur le menu **Démarrer** et sélectionnez **Système**.

2. Dans le menu de gauche, sélectionnez **Paramètres système avancés**.

3. Dans la section **Performances**, sélectionnez **Paramètres**.

4. Sélectionnez l'onglet **Avancé**.

5. Dans la section **Mémoire virtuelle**, sélectionnez **Modifier**.

6. Sélectionnez le disque du système d'exploitation **C**, puis cliquez sur **Aucun fichier d'échange** et sur **Définir**.

7. Sélectionnez le disque de stockage temporaire **E**, puis cliquez sur **Taille gérée par le système** et sur **Définir**.

8. Cliquez sur **Apply**. Vous serez averti que l'ordinateur doit être redémarré pour que les modifications prennent effet.

9. Redémarrez la machine virtuelle.




## Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server][Logon]

[Détachement d’un disque de données d’une machine virtuelle Windows][Detach]

[À propos des comptes de stockage Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Nov15_HO2-->