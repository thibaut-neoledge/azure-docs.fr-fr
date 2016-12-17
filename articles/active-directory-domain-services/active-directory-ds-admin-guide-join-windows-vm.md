---
title: "Version préliminaire des services de domaine Azure Active Directory : associer une machine virtuelle Windows Server à un domaine géré | Microsoft Docs"
description: Joindre une machine virtuelle Windows Server aux services de domaine Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 74dbdb33-05db-4d47-badc-0d7bb6d0c8cb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: aad6bcd3eb704f090156d2ace80d2540a9543bd7


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle Windows Server à un domaine géré
> [!div class="op_single_selector"]
> * [Portail Azure Classic - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
> 
> 

<br>

Cet article explique comment joindre une machine virtuelle exécutant Windows Server 2012 R2 à un domaine géré par les services de domaine Azure Active Directory, à l’aide du portail Azure Classic.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Étape 1 : créer la machine virtuelle Windows Server
Suivez les instructions du didacticiel [Création d’une machine virtuelle exécutant Windows dans le portail Azure Classic](../virtual-machines/virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) . Vous devez impérativement vérifier que la nouvelle machine virtuelle est bien jointe au réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD. L’option Création rapide ne vous permet pas de joindre la machine virtuelle à un réseau virtuel. Vous devez donc utiliser l’option À partir de la galerie pour créer la machine virtuelle.

Pour créer une machine virtuelle Windows jointe au réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD, procédez comme suit :

1. Sur le portail Azure Classic, dans la barre de commandes en bas de la fenêtre, cliquez sur **Nouveau**.
2. Sous **Calculer**, cliquez sur **Machine virtuelle**, puis sur **À partir de la galerie**.
3. Le premier écran vous propose de **choisir une image** pour votre machine virtuelle dans la liste d’images disponibles. Choisissez l’image appropriée.
   
    ![Sélectionner une image](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Le deuxième écran vous permet de choisir un nom d’ordinateur, la taille, un nom d’utilisateur et un mot de passe d’administration. Utilisez le niveau et la taille requis pour exécuter votre application ou charge de travail. Le nom d’utilisateur que vous sélectionnez ici est un utilisateur d’administrateur local sur la machine. Ne saisissez pas les informations d’identification associées à un compte d’utilisateur de domaine ici.
   
    ![Configurer la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)
5. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Sélectionnez le réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD dans la liste déroulante **Région/Groupe d’affinités/Réseau virtuel** . Spécifiez un **nom DNS du service cloud** , selon les besoins de la machine virtuelle.
   
    ![Sélectionner un réseau virtuel pour la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)
   
   > [!WARNING]
   > Vérifiez que vous joignez la machine virtuelle au réseau virtuel au sein duquel vous avez activé les services de domaine Azure AD. En conséquence, la machine virtuelle peut « voir » le domaine et effectuer différentes tâches, par exemple une jonction au domaine. Si vous décidez de créer la machine virtuelle au sein d’un autre réseau virtuel, connectez ce dernier au réseau virtuel au sein duquel les services de domaine Azure AD sont activés.
   > 
   > 
6. Le quatrième écran vous permet d’installer l’agent de machine virtuelle et de configurer certaines des extensions disponibles.
   
    ![Terminé](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
7. Une fois que vous avez créé la machine virtuelle, le portail Classic la répertorie sous le nœud **Virtual Machines** . La machine virtuelle et le service cloud sont démarrés automatiquement et leur statut est répertorié comme **En cours d'exécution**.
   
    ![Machine virtuelle opérationnelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)

## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Étape 2 : connexion à la machine virtuelle Windows Server à l’aide du compte d’administrateur local
À présent, nous allons vous connecter à la nouvelle machine virtuelle Windows Server pour la joindre au domaine. Pour ce faire, utilisez les informations d’identification de l’administrateur local que vous avez spécifiées lors de la création de la machine virtuelle.

Pour vous connecter à la machine virtuelle, procédez comme suit :

1. Accédez au nœud **Virtual Machines** du portail Classic. Sélectionnez la machine virtuelle que vous avez créée à l’étape 1 et cliquez sur **Connecter** dans la barre de commandes, au bas de la fenêtre.
   
    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Le portail Azure Classic vous invite à ouvrir ou à enregistrer un fichier pourvu de l’extension .rdp, qui permet de se connecter à la machine virtuelle. Cliquez pour ouvrir le fichier lorsque son téléchargement est terminé.
3. À l’invite de connexion, entrez vos **informations d’identification d’administrateur local**que vous avez spécifiées lors de la création de la machine virtuelle. Nous avons utilisé « localhost\mahesh » dans cet exemple.

À ce stade, vous devez être connecté à la nouvelle machine virtuelle Windows à l’aide des informations d’identification de l’administrateur local. L’étape suivante consiste à joindre la machine virtuelle au domaine.

## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Étape 3 : joindre la machine virtuelle Windows Server au domaine géré par les services de domaine Azure AD
Pour joindre la machine virtuelle Windows Server au domaine géré par les services de domaine Azure AD, procédez comme suit :

1. Connectez-vous à la machine virtuelle Windows Server, comme indiqué à l’étape 2. Dans l’écran d’accueil, ouvrez **Gestionnaire de serveur**.
2. Cliquez sur l’option **Serveur local** dans le volet gauche de la fenêtre Gestionnaire de serveur.
   
    ![Lancer le gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Cliquez sur **GROUPE DE TRAVAIL** dans la section **PROPRIÉTÉS**. Dans la page de propriétés **Propriétés système**, cliquez sur **Modifier** pour joindre le domaine.
   
    ![Page Propriétés système](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Spécifiez le nom du domaine géré par les services de domaine Azure Active Directory dans la zone de texte **Domaine**, puis cliquez sur **OK**.
   
    ![Spécifier le domaine à joindre](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Vous devez entrer vos informations d’identification pour joindre le domaine. Veillez à **spécifier les informations d’identification d’un utilisateur appartenant au groupe « AAD DC Administrators »** . Seuls les membres de ce groupe disposent de privilèges suffisants pour pouvoir joindre des ordinateurs au domaine géré.
   
    ![Spécifier les informations d’identification pour la jonction au domaine](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Vous pouvez spécifier les informations d’identification de l’une des manières suivantes :
   
   * Format UPN : spécifiez le suffixe UPN du compte d’utilisateur, tel qu’il est configuré dans Azure AD. Dans cet exemple, le suffixe UPN de l’utilisateur « bob » est « bob@domainservicespreview.onmicrosoft.com ».
   * Format SAMAccountName : vous pouvez spécifier le nom du compte au format SAMAccountName. Dans cet exemple, l’utilisateur « bob » doit saisir « CONTOSO100\bob ».
     
     > [!NOTE]
     > **Nous conseillons d’utiliser le format UPN pour spécifier les informations d’identification.** La valeur SAMAccountName peut être générée automatiquement si le préfixe UPN d’un utilisateur est anormalement long (par ex. 'joereallylongnameuser'). Si, au sein de votre locataire Azure AD, plusieurs utilisateurs présentent le même préfixe UPN (par exemple, « bob »), le format SAMAccountName peut être généré automatiquement par le service. Dans ce cas, le format UPN peut être utilisé pour assurer une connexion fiable au domaine.
     > 
     > 
7. Une fois que la jonction au domaine a abouti, le message suivant s’affiche afin de vous accueillir dans le domaine. Redémarrez la machine virtuelle pour terminer la jonction au domaine.
   
    ![Bienvenue dans le domaine](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
### <a name="connectivity-issues"></a>Problèmes de connectivité
Si la machine virtuelle ne peut pas trouver le domaine, consultez les étapes suivantes pour résoudre le problème :

* Vérifiez que la machine virtuelle est connectée au réseau virtuel au sein duquel vous avez activé les services de domaine. Si tel n’est pas le cas, la machine virtuelle ne peut pas se connecter au domaine, et la jonction est donc impossible.
* Si la machine virtuelle est connectée à un autre réseau virtuel, assurez-vous que ce dernier est connecté au réseau virtuel au sein duquel les services de domaine Azure AD sont activés.
* Effectuez un test Ping du domaine en utilisant le nom du domaine géré (par exemple, « ping contoso100.com »). Si vous n’y parvenez pas, envoyez une commande Ping aux adresses IP du domaine affichées sur la page sur laquelle vous avez activé les services de domaine Azure Active Directory (par exemple, « ping 10.0.0.4 »). Si le test de l’adresse IP aboutit et non celui du domaine, il se peut que la fonction DNS ne soit pas correctement configurée. Vous n’avez peut-être pas configuré les adresses IP du domaine en tant que serveurs DNS pour le réseau virtuel.
* Essayez de vider le cache de résolution DNS sur la machine virtuelle (« ipconfig /flushdns »).

Si une boîte de dialogue s’affiche, vous demandant de saisir vos informations d’identification pour joindre le domaine, cela peut être le signe de problèmes de connectivité.

### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification
Reportez-vous à la procédure suivante si vous rencontrez des problèmes concernant les informations d’identification et que vous ne parvenez pas à joindre le domaine.

* Essayez d’utiliser le format UPN pour spécifier les informations d’identification. La valeur de SAMAccountName pour votre compte peut être générée automatiquement s’il existe plusieurs utilisateurs avec le même préfixe UPN sur votre client ou si votre préfixe UPN est trop long. Par conséquent, le format SAMAccountName de votre compte peut différer de ce à quoi vous vous attendiez ou de ce que vous utilisez dans votre domaine local.
* Essayez d’utiliser les informations d’identification d’un compte d’utilisateur appartenant au groupe « AAD DC Administrators » pour joindre des machines au domaine géré.
* Assurez-vous d'avoir [activé la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.
* Veillez à utiliser l’UPN de l’utilisateur tel que configuré dans Azure AD (par exemple, 'bob@domainservicespreview.onmicrosoft.com') pour vous connecter.
* Vérifiez que vous avez bien attendu la fin de la synchronisation des mots de passe, comme indiqué dans le guide de prise en main.

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)




<!--HONumber=Nov16_HO3-->


