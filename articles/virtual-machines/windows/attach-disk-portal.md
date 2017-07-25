---
title: "Attacher un disque de données non géré à une machine virtuelle Windows - Azure | Microsoft Docs"
description: "Découvrez comment attacher un disque de données non géré nouveau ou existant à une machine virtuelle Windows dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c0886302c82641f8cc1a00d3972870d58ba8afb4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Attachement d’un disque de données non géré à une machine virtuelle Windows dans le portail Azure

Cet article vous explique comment attacher des disques non gérés nouveaux et existants à des machines virtuelles Windows par le biais du portail Azure. Vous pouvez également [attacher un disque de données à l’aide de PowerShell](./attach-disk-ps.md). Avant cela, passez en revue les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](sizes.md).
* Pour utiliser le stockage Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.


Vous pouvez également [attacher un disque de données à l’aide de PowerShell](attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans le panneau Machines virtuelles, cliquez sur **Disques**.
   
Continuez en suivant les instructions pour attacher un [nouveau disque](#option-1-attach-a-new-disk) ou un [disque existant](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Option 1 : attacher et initialiser un nouveau disque
1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Dans le panneau **Attacher un disque géré**, entrez le nom du disque dans **Nom**, puis sélectionnez **Nouveau (disque vide)** dans **Type de source**.
3. Sous **Conteneur de stockage**, cliquez sur le bouton **Parcourir** pour accéder au compte de stockage et au conteneur dans lequel vous souhaitez stocker le nouveau disque dur virtuel, puis cliquez sur **Sélectionner**. 
  
   ![Examen des paramètres d’un disque](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. Une fois les paramètres du disque de données configurés, cliquez sur **OK**.
4. Dans le panneau **Disques**, cliquez sur **Enregistrer** pour ajouter le disque à la configuration de la machine virtuelle.


### <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, voir [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Cliquez sur le menu **Démarrer** dans la machine virtuelle, tapez **diskmgmt.msc** et appuyez sur **Entrée**. Le composant logiciel enfichable Gestion des disques démarre.
2. L’outil Gestion des disques détermine que votre nouveau disque n’est pas initialisé et affiche la fenêtre Initialiser le disque.
3. Vérifiez que le nouveau disque est sélectionné, puis cliquez sur **OK** pour l’initialiser.
4. Le nouveau disque apparaît maintenant comme **non alloué**. Cliquez avec le bouton droit n’importe où sur le disque, puis sélectionnez **Nouveau volume simple**. **L’Assistant Nouveau volume simple** se lance.
5. Exécutez l’Assistant en conservant tous les paramètres par défaut. Lorsque vous avez terminé, sélectionnez **Terminer**.
6. Fermez Gestion des disques.
7. Une fenêtre contextuelle s’affiche et vous permet de formater le nouveau disque pour que vous puissiez l’utiliser. Cliquez sur **Formater le disque**.
8. Dans la boîte de dialogue **Formater un nouveau disque**, vérifiez les paramètres puis cliquez sur **Démarrer**.
9. Un message vous informe que le formatage des disques va effacer toutes les données. Cliquez sur **OK**.
10. Lorsque le formatage est terminé, cliquez sur **OK**.


## <a name="option-2-attach-an-existing-disk"></a>Option 2 : attacher un disque existant
1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Dans le panneau **Attacher un disque non géré**, sous **Type de source**, sélectionnez **Objet blob existant**.

    ![Examen des paramètres d’un disque](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. Cliquez sur **Parcourir** pour accéder au compte de stockage et au conteneur où se trouve le disque dur virtuel existant. Cliquez sur le disque dur virtuel, puis sur **Sélectionner**.
4. Cliquez sur **OK** dans le panneau **Attacher un disque non géré**.
5. Dans le panneau **Disques**, cliquez sur **Enregistrer** pour ajouter le disque à la configuration de la machine virtuelle.
   


## <a name="use-trim-with-standard-storage"></a>Utilisation de TRIM avec le stockage standard

Si vous utilisez le stockage standard (disque dur), vous devez activer la fonction TRIM. TRIM ignore les blocs inutilisés sur le disque afin que vous soyez facturé uniquement pour le stockage que vous utilisez réellement. Vous pouvez ainsi faire des économies si vous créez des fichiers volumineux, puis les supprimez. 

Vous pouvez exécuter cette commande pour vérifier le paramètre TRIM. Ouvrez une invite de commandes sur votre machine virtuelle Windows et saisissez :

```
fsutil behavior query DisableDeleteNotify
```

Si la commande renvoie 0, TRIM est bien activé. Si la valeur 1 est renvoyée, exécutez la commande suivante pour activer TRIM :
```
fsutil behavior set DisableDeleteNotify 0
```

Après la suppression de données de votre disque, vous pouvez vous assurer du bon vidage des opérations TRIM en exécutant la défragmentation avec TRIM :

```
defrag.exe <volume:> -l
```

Pour vous assurer que les blocs de données inutilisés sont bien effacés sur tout le volume, vous pouvez également le formater.


## <a name="next-steps"></a>Étapes suivantes
Si votre application doit utiliser le lecteur D: pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


