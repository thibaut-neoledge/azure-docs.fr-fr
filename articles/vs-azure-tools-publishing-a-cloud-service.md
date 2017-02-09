---
title: "Publication d’un service cloud à l’aide des outils Azure | Microsoft Docs"
description: "Découvrez comment publier des projets de service cloud Azure à l&quot;aide de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5e83532c65c5f6699d401ec9371ac2ad7f3868e8


---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publication d’un service cloud à l’aide des outils Azure
Grâce aux outils Azure pour Microsoft Visual Studio, vous pouvez publier votre application Microsoft Azure directement depuis Visual Studio. Visual Studio prend en charge la publication intégrée dans l'environnement intermédiaire ou l'environnement de production d'un service cloud.

Avant de pouvoir publier une application Azure, vous devez disposer d’un abonnement Azure. Vous devez également configurer un service cloud et un compte de stockage utilisable par votre application. Vous pouvez les définir dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> Lorsque vous publiez, vous pouvez sélectionner l'environnement de déploiement pour votre service cloud. Vous devez également sélectionner un compte de stockage permettant de stocker le package de l'application pour le déploiement. Après le déploiement, le package de l’application est supprimé du compte de stockage.
> 
> 

Lorsque vous développez et testez une application Azure, vous pouvez utiliser Web Deploy pour publier des modifications de façon incrémentielle pour vos rôles web. Une fois que vous publiez votre application dans un environnement de déploiement, Web Deploy vous permet de déployer des modifications directement sur la machine virtuelle qui exécute le rôle web. Il est inutile de créer un package et de publier votre application Azure complète chaque fois que vous souhaitez mettre à jour votre rôle web pour tester les modifications. Avec cette approche, vos modifications de rôle web sont disponibles dans le cloud et vous pouvez donc les tester sans attendre que votre application soit publiée dans un environnement de déploiement.

Utilisez les procédures suivantes pour publier votre application Azure et mettre à jour un rôle web à l'aide de Web Deploy :

* Publier ou créer un package d'une application Azure à partir de Visual Studio
* Mettre à jour un rôle web dans le cadre du cycle de test et de développement

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publier ou créer un package d'une application Azure à partir de Visual Studio
Lorsque vous publiez votre application Azure, vous pouvez effectuer l'une des tâches suivantes :

* Créer un package de services : vous pouvez utiliser ce package et le fichier de configuration de service pour publier votre application dans un environnement de déploiement à partir du [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
* Publier votre projet Azure à partir de Visual Studio : pour publier votre application directement dans Azure, vous utilisez l'Assistant Publication. Pour plus d’informations, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Pour créer un package de services à partir de Visual Studio
1. Lorsque vous êtes prêt à publier votre application, ouvrez l'Explorateur de solutions, ouvrez le menu contextuel du projet Azure qui contient vos rôles, puis choisissez Publier.
2. Pour créer un package de services uniquement, procédez comme suit :  
   
   1. Dans le menu contextuel du projet Azure, choisissez **Package**.
   2. Dans la boîte de dialogue **Package Azure Application** , choisissez la configuration du service pour lequel vous souhaitez créer un package, puis la configuration de la version.
   3. (facultatif) Pour activer le Bureau à distance pour le service cloud après sa publication, activez la case à cocher **Activer le Bureau à distance pour tous les rôles**, puis sélectionnez **Paramètres** pour configurer le Bureau à distance. Si vous souhaitez déboguer votre service cloud après sa publication, activez le débogage à distance en sélectionnant **Activer le débogueur distant pour tous les rôles**.
      
      Pour plus d’informations, consultez [Utilisation du Bureau à distance avec des rôles Azure](vs-azure-tools-remote-desktop-roles.md).
   4. Pour créer le package, choisissez le lien **Package** .
      
      L’Explorateur de fichiers indique l'emplacement du package nouvellement créé. Vous pouvez copier cet emplacement afin de l’utiliser à partir du [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Pour publier ce package dans un environnement de déploiement, vous devez utiliser cet emplacement comme emplacement du package lorsque vous créez un service cloud et déployez ce package dans un environnement avec le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Facultatif) Pour annuler le processus de déploiement, dans le menu contextuel de cette ligne dans le journal d'activité, choisissez **Annuler et supprimer**. Cela arrête le processus de déploiement et supprime l'environnement de déploiement d'Azure.
   
   > [!NOTE]
   > Pour supprimer cet environnement de déploiement après son déploiement, vous devez utiliser le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Facultatif) Une fois vos instances de rôle démarrées, Visual Studio affiche automatiquement l’environnement de déploiement dans le nœud **Services cloud** de l’Explorateur de serveurs. À partir de là, vous pouvez voir l'état de chaque instance de rôle. Consultez [Gestion des ressources Azure avec Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). L’illustration suivante montre les instances de rôle toujours à l’état d’initialisation :
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Mettre à jour un rôle web dans le cadre du cycle de test et de développement
Si l'infrastructure principale de votre application est stable, mais que les rôles web ont besoin d’une mise à jour plus fréquente, vous pouvez utiliser Web Deploy pour mettre à jour un seul rôle web dans votre projet. Cette méthode est pratique lorsque vous ne souhaitez pas recréer et redéployer les rôles de travail principaux, ou si vous avez plusieurs rôles web et souhaitez mettre à jour uniquement un des rôles web.

### <a name="requirements"></a>Configuration requise
Voici la configuration requise pour utiliser Web Deploy afin de mettre à jour votre rôle web :

* **À des fins de développement et de test uniquement :** les modifications sont apportées directement à la machine virtuelle sur laquelle le rôle web est en cours d'exécution. Si cette machine virtuelle doit être recyclée, les modifications sont perdues car le package d'origine que vous avez publié sert à recréer la machine virtuelle pour le rôle. Vous devez republier votre application afin d’obtenir les dernières modifications pour le rôle web.
* **Seuls les rôles web peuvent être mis à jour :** les rôles de travail ne peuvent pas être mis à jour. En outre, vous ne pouvez pas mettre à jour RoleEntryPoint dans web role.cs.
* **Peut prendre en charge une seule instance d'un rôle web :** vous ne pouvez pas avoir plusieurs instances d'un rôle web dans votre environnement de déploiement. Toutefois, plusieurs rôles web, chacun avec une seule instance, sont pris en charge.
* **Vous devez activer les connexions Bureau à distance :** cela est nécessaire afin que le Web Deploy puisse utiliser le nom d'utilisateur et le mot de passe pour se connecter à la machine virtuelle pour déployer les modifications sur le serveur qui exécute Internet Information Services (IIS). En outre, vous devrez peut-être vous connecter à la machine virtuelle pour ajouter un certificat approuvé à IIS sur cette machine virtuelle. (La connexion à distance pour IIS utilisée par Web Deploy est ainsi sécurisée).

La procédure suivante suppose que vous utilisez l’Assistant **Publication d’application Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Pour activer Web Deploy lorsque vous publiez votre application
1. Afin d’activer la case à cocher **Activer Web Deploy** pour tous les rôles web, vous devez d'abord configurer les connexions Bureau à distance. Sélectionnez **Activer le Bureau à distance** pour tous les rôles, puis fournissez les informations d’identification qui seront utilisées pour se connecter à distance dans le champ **Configuration Bureau à distance** qui apparaît. Consultez [Utilisation du Bureau à distance avec des rôles Azure](vs-azure-tools-remote-desktop-roles.md) pour plus d’informations.
2. Afin d’activer Web Deploy pour tous les rôles web dans votre application web, sélectionnez **Activer Web Deploy pour tous les rôles web**.
   
    Un triangle d'avertissement jaune s'affiche. Web Deploy utilise par défaut un certificat non fiable et auto-signé, ce qui n'est pas recommandé pour télécharger des données sensibles. Si vous devez sécuriser ce processus pour des données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions Web Deploy. Ce certificat doit être un certificat approuvé. Pour savoir comment procéder, consultez la section **Pour sécuriser Web Deploy** plus loin dans cette rubrique.
3. Choisissez **Suivant** pour afficher l’écran **Résumé**, puis **Publier** pour déployer le service cloud.
   
    Le service cloud est publié. La machine virtuelle créée comporte des connexions à distance activées pour IIS, de sorte que Web Deploy peut être utilisé pour mettre à jour vos rôles web sans les republier.
   
   > [!NOTE]
   > Si vous avez plusieurs instances configurées pour un rôle web, un message d'avertissement s'affiche, indiquant que chaque rôle web sera limité à une seule instance dans le package créé pour publier votre application. Sélectionnez **OK** pour continuer. Comme indiqué dans la section Configuration requise, vous pouvez avoir plusieurs rôles web, mais une seule instance de chaque rôle.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Pour mettre à jour votre rôle web à l'aide de Web Deploy
1. Pour utiliser Web Deploy, apportez des modifications au code du projet pour un des rôles web de Visual Studio que vous souhaitez publier, puis cliquez sur ce nœud de projet dans votre solution et pointez sur **Publier**. La boîte de dialogue **Publier le site Web** s’affiche.
2. (Facultatif) Si vous avez ajouté un certificat SSL approuvé à utiliser pour les connexions à distance pour IIS, vous pouvez désactiver la case à cocher **Autoriser les certificats non approuvés** . Pour plus d’informations sur l’ajout d’un certificat afin de sécuriser Web Deploy, consultez la section **Pour sécuriser Web Deploy** plus loin dans cette rubrique.
3. Pour utiliser Web Deploy, le mécanisme de publication a besoin du nom d'utilisateur et du mot de passe que vous définissez pour votre connexion Bureau à distance lors de la publication initiale du package.
   
   1. Dans **Nom d’utilisateur**, entrez le nom d’utilisateur.
   2. Dans **Mot de passe**, entrez le mot de passe.
   3. (Facultatif) Si vous souhaitez enregistrer ce mot de passe de ce profil, choisissez **Enregistrer le mot de passe**.
4. Pour publier les modifications apportées à votre rôle web, choisissez **Publier**.
   
    La ligne d'état indique que la **publication a démarré**. Une fois la publication terminée, un message indique que la **publication a réussi** . Les modifications ont maintenant été déployées sur le rôle web de votre machine virtuelle. Vous pouvez désormais démarrer votre application Azure dans l'environnement Azure pour tester vos modifications.

### <a name="to-make-web-deploy-secure"></a>Pour sécuriser Web Deploy
1. Web Deploy utilise par défaut un certificat non fiable et auto-signé, ce qui n'est pas recommandé pour télécharger des données sensibles. Si vous devez sécuriser ce processus pour des données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions Web Deploy. Ce certificat doit être un certificat approuvé, que vous obtenez auprès d'une autorité de certification (CA).
   
    Afin de sécuriser Web Deploy pour chaque machine virtuelle et chacun de vos rôles web, vous devez télécharger le certificat de confiance que vous souhaitez utiliser pour le déploiement web vers le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). Cela permet de s'assurer que le certificat est ajouté à la machine virtuelle créée pour le rôle web lorsque vous publiez votre application.
2. Pour ajouter un certificat SSL approuvé à IIS afin d'utiliser des connexions à distance, procédez comme suit :
   
   1. Pour vous connecter à la machine virtuelle qui exécute le rôle web, sélectionnez l’instance du rôle web dans **Cloud Explorer** ou l’**Explorateur de serveurs**, puis choisissez la commande **Connexion à l’aide de Bureau à distance**. Pour obtenir la procédure détaillée de connexion à la machine virtuelle, consultez [Utilisation du Bureau à distance avec des rôles Azure](vs-azure-tools-remote-desktop-roles.md).
      
      Votre navigateur vous invitera à télécharger un fichier RDP.
   2. Pour ajouter un certificat SSL, ouvrez le service de gestion dans le Gestionnaire des services IIS. Dans le Gestionnaire des services IIS, activez SSL en ouvrant le lien **Liaisons** dans le volet **Action**. La boîte de dialogue **Ajouter la liaison de Site** s'affiche. Choisissez **Ajouter**, puis sélectionnez HTTPS dans la liste déroulante **Type**. Dans la liste **Certificat SSL** , sélectionnez le certificat SSL signé par une autorité de certification et que vous avez téléchargé sur le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). Pour plus d'informations, consultez [Configurer des paramètres de connexion pour le service de gestion](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Si vous ajoutez un certificat SSL approuvé, le triangle d'avertissement jaune n'apparaît plus dans l’ **Assistant Publication**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Inclure des fichiers dans le package de services
Vous devrez peut-être inclure des fichiers spécifiques dans votre package de services afin qu'ils soient disponibles sur la machine virtuelle créée pour un rôle. Par exemple, vous pouvez ajouter à votre package de services un fichier .exe ou .msi utilisé par un script de démarrage. Ou vous devrez peut-être ajouter un assembly nécessaire à un projet de rôle web ou de travail. Pour inclure des fichiers, vous devez les ajouter à la solution pour votre application Azure.

### <a name="to-include-files-in-the-service-package"></a>Pour inclure des fichiers dans le package de services
1. Pour ajouter un assembly à un package de services, procédez comme suit :
   
   1. Dans **l'Explorateur de solutions** , ouvrez le nœud de projet pour le projet auquel il manque l'assembly référencé.
   2. Pour ajouter l’assembly au projet, ouvrez le menu contextuel du dossier **Références**, puis choisissez **Ajouter une référence**. La boîte de dialogue Ajouter une référence s'affiche.
   3. Choisissez la référence que vous souhaitez ajouter, puis cliquez sur le bouton **OK** .
      
      La référence est ajoutée à la liste sous le dossier **Références** .
   4. Ouvrez le menu contextuel pour l'assembly que vous avez ajouté, puis choisissez **Propriétés**. La fenêtre **Propriétés** s'affiche.
      
      Pour inclure cet assembly dans le package de services, dans la **liste de copie locale**, choisissez **True**.
2. Dans **l'Explorateur de solutions** , ouvrez le nœud de projet pour le projet auquel il manque l'assembly référencé.
3. Pour ajouter l’assembly au projet, ouvrez le menu contextuel du dossier **Références**, puis choisissez **Ajouter une référence**. La boîte de dialogue **Ajouter une référence** s'affiche.
4. Choisissez la référence que vous souhaitez ajouter, puis cliquez sur le bouton **OK** .
   
    La référence est ajoutée à la liste sous le dossier **Références** .
5. Ouvrez le menu contextuel pour l'assembly que vous avez ajouté, puis choisissez **Propriétés**. La fenêtre Propriétés apparaît.
6. Pour inclure cet assembly dans le package de services, dans la **liste de copie locale**, choisissez **True**.
7. Pour inclure dans le package de services des fichiers qui ont été ajoutés à votre projet de rôle web, ouvrez le menu contextuel du fichier, puis choisissez **Propriétés**. Dans la fenêtre **Propriétés**, choisissez **Contenu** dans la liste déroulante **Action de génération**.
8. Pour inclure dans le package de services des fichiers qui ont été ajoutés à votre projet de rôle web, ouvrez le menu contextuel du fichier, puis choisissez **Propriétés**. Dans la fenêtre **Propriétés**, choisissez **Copier si plus récent** dans la liste déroulante **Copier dans le répertoire de sortie**.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la publication sur Azure depuis Visual Studio, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md).




<!--HONumber=Nov16_HO3-->


