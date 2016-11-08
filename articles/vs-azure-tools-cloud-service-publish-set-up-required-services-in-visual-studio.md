---
title: Préparation de la publication ou du déploiement d'une application Azure à partir de Visual Studio | Microsoft Docs
description: Découvrez les procédures de configuration des services de compte de stockage et cloud et de votre application Azure.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher

---
# Préparer la publication ou le déploiement d’une application Azure à partir de Visual Studio
## Vue d'ensemble
Pour pouvoir publier un projet de service cloud, vous devez configurer les services suivants :

* Un **service cloud** pour exécuter vos rôles dans l'environnement Azure
* Un **compte de stockage** qui fournit l'accès aux services Blob, File d'attente et Table.

Utilisez les procédures suivantes pour configurer ces services et votre application

## Création d'un service cloud
Pour publier un service cloud dans Azure, vous devez d'abord créer un service cloud, qui exécute vos rôles dans l'environnement Azure. Vous pouvez créer un service cloud dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), comme décrit à la section **Pour créer un service cloud à l’aide du portail Azure Classic**, plus loin dans cette rubrique. Vous pouvez également créer un service cloud dans Visual Studio en utilisant l’Assistant Publication.

### Pour créer un service cloud à l’aide de Visual Studio
1. Ouvrez le menu contextuel du projet Azure, puis sélectionnez **Publier**.
   
    ![VST\_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Si vous n’êtes pas connecté, connectez-vous avec votre nom d’utilisateur et votre mot de passe de compte Microsoft ou de compte professionnel associé à votre abonnement Azure.
3. Sélectionnez le bouton **Suivant** pour accéder à la page **Paramètres**.
   
    ![Paramètres courants de l'Assistant Publication](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. Dans la liste **Services cloud**, sélectionnez **Créer**. La boîte de dialogue **Créer des services Azure** apparaît.
5. Notez le nom de votre service cloud. Le nom constitue une partie de l'URL de votre service et doit par conséquent être unique d’un point de vue global. Le nom ne respecte pas la casse.

### Pour créer un service cloud à l’aide du portail Azure Classic
1. Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkId=253103) sur le site web Microsoft.
2. (facultatif) Pour afficher la liste des services cloud que vous avez déjà créés, cliquez sur le lien Services cloud sur le côté gauche de la page.
3. Sélectionnez l’icône **+** dans le coin inférieur gauche, puis sélectionnez **Service cloud** dans le menu qui s’affiche. Un autre écran s’affiche avec deux options, **Création rapide** et **Création personnalisée**. Si vous sélectionnez **Création rapide**, vous pouvez créer un service cloud en spécifiant simplement son URL et la région où il sera physiquement hébergé. Si vous sélectionnez **Création personnalisée**, vous pouvez publier immédiatement un service cloud en spécifiant un package (fichier .cspkg), un fichier de configuration (.cscfg) et un certificat. La création personnalisée n’est pas nécessaire si vous avez l’intention de publier votre service cloud à l’aide de la commande **Publier** dans un projet Azure. La commande **Publier** est disponible dans le menu contextuel pour un projet Azure.
4. Sélectionnez **Création rapide** pour publier ultérieurement votre service cloud à l’aide de Visual Studio.
5. Spécifiez un nom pour votre service cloud. L'URL complète s'affiche en regard du nom.
6. Dans la liste, sélectionnez la région où se trouvent la plupart de vos utilisateurs.
7. En bas de la fenêtre, sélectionnez le lien **Créer un service cloud**.

## Créez un compte de stockage.
Un compte de stockage fournit l'accès aux services Blob, File d'attente et Table. Vous pouvez créer un compte de stockage en utilisant Visual Studio ou le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkId=253103).

### Pour créer un compte de stockage à l’aide de Visual Studio
1. Dans l’**Explorateur de solutions**, ouvrez le menu contextuel du nœud **Stockage**, puis sélectionnez **Créer un compte de stockage**.
   
    ![Créer un compte de stockage Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage**.
   
   * L’abonnement Azure auquel vous voulez ajouter le compte de stockage.
   * Le nom que vous voulez utiliser pour le nouveau compte de stockage
   * La région ou le groupe d’affinités (comme l’Ouest des États-Unis ou l’Est de l’Asie)
   * Le type de réplication que vous voulez utiliser pour le compte de stockage, par exemple géo-redondant.
3. Lorsque vous avez terminé, sélectionnez **Créer**. Le nouveau compte de stockage s’affiche dans la liste **Stockage** de l’**Explorateur de serveurs**.

### Pour créer un compte de stockage à l’aide du portail Azure Classic
1. Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkId=253103) sur le site web Microsoft.
2. (facultatif) Pour afficher vos comptes de stockage, sélectionnez le lien **Stockage** dans le panneau sur le côté gauche de la page.
3. Dans le coin inférieur gauche de la page, sélectionnez l’icône **+**.
4. Dans le menu qui s’affiche, sélectionnez **Stockage**, puis sélectionnez **Création rapide**.
5. Attribuez au compte de stockage un nom qui donne lieu à une URL unique.
6. Attribuez un nom à votre service cloud. L'URL complète s'affiche en regard du nom.
7. Dans la liste de régions, sélectionnez une région où se trouvent la plupart de vos utilisateurs.
8. Spécifiez si vous souhaitez activer la géo-réplication. Si vous activez la géo-réplication, vos données seront enregistrées dans plusieurs emplacements physiques afin de réduire les risques de perte. Cette fonctionnalité rend le stockage plus onéreux, mais vous pouvez réduire le coût en activant la géolocalisation dès la création du compte de stockage au lieu d'ajouter la fonctionnalité ultérieurement. Pour plus d’informations, consultez [Géo-réplication](http://go.microsoft.com/fwlink/?LinkId=253108).
9. En bas de la fenêtre, sélectionnez le lien **Créer un compte de stockage**.

Après avoir créé votre compte de stockage, vous verrez les URL que vous pouvez utiliser pour accéder aux ressources dans chacun des services de stockage Azure, ainsi que les clés d'accès primaire et secondaire de votre compte. Ces clés vous permettent d'authentifier des demandes adressées aux services de stockage.

> [!NOTE]
> La clé d'accès secondaire fournit le même accès à votre compte de stockage que la clé d'accès primaire et est générée comme sauvegarde au cas où votre clé primaire serait compromise. En outre, il est recommandé de régénérer régulièrement vos clés d'accès. Vous pouvez modifier un paramètre de la chaîne de connexion pour utiliser la clé secondaire pendant que vous régénérez la clé primaire, puis vous pouvez le modifier pour utiliser la clé primaire régénérée pendant que vous régénérez la clé secondaire.
> 
> 

## Configurer votre application pour utiliser les services fournis par le compte de stockage
Vous devez configurer tous les rôles qui accèdent aux services de stockage pour utiliser les services de stockage Azure que vous avez créés. Pour ce faire, vous pouvez utiliser plusieurs configurations de service pour votre projet Azure. Par défaut, deux sont créées dans votre projet Azure. En utilisant plusieurs configurations de service, vous pouvez utiliser la même chaîne de connexion dans votre code, tandis que la chaîne de connexion de chaque configuration de service possède une valeur distincte. Par exemple, vous pouvez utiliser une configuration de service pour exécuter et déboguer votre application localement à l'aide de l'émulateur de stockage Azure et une autre configuration de service pour publier votre application dans Azure. Pour plus de configuration sur les configurations de service, consultez la page [Configuration de votre projet Azure à l’aide de plusieurs configurations de service](vs-azure-tools-multiple-services-project-configurations.md).

### Pour configurer votre application afin d’utiliser les services fournis par le compte de stockage
1. Dans Visual Studio, ouvrez votre solution Azure. Dans l’Explorateur de solutions, ouvrez le menu contextuel de chaque rôle de votre projet Azure qui accède aux services de stockage et sélectionnez **Propriétés**. Une page s’affiche avec le nom du rôle dans l'éditeur Visual Studio. La page affiche les champs de l’onglet **Configuration**.
2. Dans les pages Propriétés du rôle, sélectionnez **Paramètres**.
3. Dans la liste **Configuration du service** sélectionnez le nom de la configuration de service que vous souhaitez modifier. Si vous souhaitez apporter des modifications à toutes les configurations de service pour ce rôle, vous pouvez sélectionner **Toutes les configurations**. Pour plus d’informations sur la procédure de mise à jour des configurations de service, consultez la section **Gérer les chaînes de connexion pour les comptes de stockage** dans la rubrique [Configuration des rôles pour un service cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Pour modifier les paramètres d’une chaîne de connexion, sélectionnez le bouton **...** en regard du paramètre. La boîte de dialogue **Créer une chaîne de connexion de stockage** s’affiche.
5. Sous **Connexion avec**, sélectionnez l’option **Votre abonnement**.
6. Dans la liste **Abonnement** sélectionnez votre abonnement. Si la liste d’abonnements n’inclut pas celui que vous souhaitez, sélectionnez le lien **Télécharger les paramètres de publication**.
7. Dans la liste **Nom du compte**, sélectionnez le nom de votre compte de stockage. Les outils Azure récupèrent automatiquement les informations d'identification du compte de stockage à l’aide du fichier .publishsettings. Pour spécifier manuellement les informations d’identification de votre compte de stockage, sélectionnez l’option **Informations d’identification entrées manuellement**, puis poursuivez cette procédure. Vous pouvez obtenir le nom et la clé primaire de votre compte de stockage dans le [portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=213885). Si vous ne souhaitez pas spécifier manuellement les paramètres de votre compte de stockage, sélectionnez le bouton **OK** pour fermer la boîte de dialogue.
8. Sélectionnez le lien **Entrer les informations d’identification du compte de stockage**.
9. Dans le champ **Nom du compte**, entrez le nom de votre compte de stockage.
   
   > [!NOTE]
   > Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), puis sélectionnez le bouton **Stockage**. Le portail affiche la liste des comptes de stockage. Si vous sélectionnez un compte, une page associée s'ouvre. Vous pouvez copier le nom du compte de stockage à partir de cette page. Si vous utilisez une version précédente du portail classique, le nom de votre compte de stockage apparaît dans l’affichage **Comptes de stockage**. Pour copier ce nom, mettez-le en surbrillance dans la fenêtre **Propriétés** de cet affichage, puis appuyez sur les touches Ctrl+C. Pour coller le nom dans Visual Studio, sélectionnez la zone de texte **Nom du compte**, puis appuyez sur les touches Ctrl+V.
   > 
   > 
10. Dans la zone **Clé du compte**, entrez votre clé primaire, ou copiez-collez-la à partir du [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). Pour copier cette clé :
    
    1. En bas de la page du compte de stockage approprié, sélectionnez le bouton **Gérer les clés**.
    2. Sur la page **Gérer l’accès aux clés**, sélectionnez le texte de la clé d’accès primaire, puis appuyez sur les touches Ctrl+C.
    3. Dans les outils Azure, collez la clé dans la zone **Clé du compte**.
    4. Vous devez sélectionner une des options suivantes pour déterminer la façon dont le service accédera au compte de stockage :
       
       * **Utiliser HTTP**. Il s'agit de l'option standard. Par exemple : `http://<account name>.blob.core.windows.net`.
       * **Utiliser HTTPS** pour une connexion sécurisée. Par exemple : `https://<accountname>.blob.core.windows.net`.
       * **Spécifier des points de terminaison personnalisés** pour chacun des trois services. Vous pouvez ensuite taper ces points de terminaison dans le champ du service.
         
         > [!NOTE]
         > Si vous créez des points de terminaison personnalisés, vous pouvez créer une chaîne de connexion plus complexe. Lorsque vous utilisez ce format de chaîne, vous pouvez spécifier des points de terminaison de service de stockage qui incluent un nom de domaine personnalisé que vous avez enregistré pour votre compte de stockage avec le service Blob. Vous pouvez également accorder l'accès uniquement aux ressources Blob dans un seul conteneur au moyen d’une signature d'accès partagé. Pour plus d’informations sur la création de points de terminaison personnalisés, consultez [Configuration des chaînes de connexion Azure Storage](storage/storage-configure-connection-string.md).
         > 
         > 
11. Pour enregistrer les modifications de la chaîne de connexion, sélectionnez le bouton **OK**, puis sélectionnez le bouton **Enregistrer** dans la barre d’outils. Après avoir enregistré ces modifications, vous pouvez récupérer la valeur de cette chaîne de connexion dans votre code à l’aide de [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Lorsque vous publiez votre application dans Azure, sélectionnez la configuration de service qui contient le compte de stockage Azure pour la chaîne de connexion. Une fois votre application publiée, vérifiez que l'application fonctionne comme prévu par rapport aux services de stockage Azure

## Étapes suivantes
Pour en savoir plus sur la publication d’applications entre Visual Studio et Azure, consultez la page [Publication d’un service cloud en utilisant les outils Azure](vs-azure-tools-publishing-a-cloud-service.md).

<!---HONumber=AcomDC_0817_2016-->