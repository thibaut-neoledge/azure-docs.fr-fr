    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Créer des modèles de machines virtuelles

## Vue d'ensemble

Après avoir [créé un laboratoire](devtest-lab-create-lab.md), vous pouvez [ajouter des machines virtuelles à ce laboratoire](devtest-lab-add-vm-with-artifacts.md) à partir d’une liste de modèles de machines virtuelles. Cet article explique comment télécharger et configurer un fichier d’image de disque dur virtuel (VHD) qui servira de modèle pour créer vos machines virtuelles. Si vous n’êtes pas familiarisé avec les images VHD, consultez l’article [Création et téléchargement d’un VHD Windows Server dans Azure](../virtual-machines-create-upload-vhd-windows-server.md) pour apprendre à créer une image VHD. Lorsque vous aurez créé ou que vous pourrez accéder à une image de disque dur virtuel, cet article vous aidera à télécharger et à créer un modèle à partir de cette image.

## Créer un modèle de machine virtuelle

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, appuyez sur le laboratoire souhaité.

1. Dans le panneau du laboratoire, appuyez sur **Paramètres**.

    ![Paramètres du laboratoire](./media/devtest-lab-create-template/lab-blade-settings.png)

1. Dans le panneau **Paramètres** du laboratoire, appuyez sur **Modèles**.

    ![Option des modèles](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. Dans le panneau **Modèles**, appuyez sur **+ Modèle**.

    ![Ajouter un modèle](./media/devtest-lab-create-template/add-template.png)

1. Dans le panneau **Ajouter un modèle** :

	1. Entrez le nom du modèle. Ce nom s’affiche dans la liste des modèles lors de la création d’une nouvelle machine virtuelle.

	1. Entrez la description du modèle. Cette description s’affiche dans la liste des modèles lors de la création d’une nouvelle machine virtuelle.

	1. Appuyez sur **Image**.

	1. Si l’image de votre choix n’est pas répertoriée et que vous souhaitez l’ajouter, passez à la section [Ajouter une nouvelle image de modèle](#add-a-new-template-image), puis revenez ici lorsque vous avez terminé.

	1. Sélectionnez l’image de votre choix.

	1. Appuyez sur **OK** pour fermer le panneau **Ajouter un modèle**.

1. Appuyez sur **Configuration du système d’exploitation**.

1. Dans l’onglet **Configuration du système d’exploitation**, sélectionnez **Windows** ou **Linux**.

1. Si vous sélectionnez **Windows**, cochez la case pour indiquer si *Sysprep* a été exécuté sur la machine.

1. Entrez un **Nom d’utilisateur** pour la machine.

1. Entrez un **Mot de passe** pour la machine. **Remarque :** le mot de passe s’affiche en texte clair.

1. Sélectionnez **OK** pour fermer le panneau **Configuration du système d’exploitation**.

1. Indiquez un **Emplacement**.

1. Appuyez sur **OK** pour créer le modèle.

##Ajouter une nouvelle image de modèle

Pour ajouter une nouvelle image de modèle, vous devez avoir accès à un fichier d’image de disque dur virtuel.

1. Dans le panneau **Ajouter une image de modèle**, appuyez sur **Télécharger une image à l’aide de PowerShell**.

    ![Télécharger une image](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Le panneau suivant affiche des instructions pour modifier et exécuter un script PowerShell qui télécharge un fichier d’image de disque dur virtuel sur votre abonnement Azure. **Remarque :** la taille du fichier image et votre vitesse de connexion influent sur la durée de ce processus.

##Étapes suivantes

Lorsque vous avez ajouté un modèle de machine virtuelle à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [Ajouter une machine virtuelle à votre instance du service DevTest Lab](devtest-lab-add-vm-with-artifacts).

<!---HONumber=AcomDC_0128_2016-->