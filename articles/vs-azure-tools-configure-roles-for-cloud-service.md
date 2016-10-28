<properties
   pageTitle="Configurer les rôles pour un service cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment installer et configurer des rôles pour les services cloud Azure à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Configuration des rôles pour un service cloud Azure avec Visual Studio

Un service cloud Azure peut avoir un ou plusieurs rôles de travail ou rôles web. Pour chaque rôle, vous devez définir le mode de configuration de ce rôle et vous devez également configurer le mode d’exécution de ce rôle. Pour en savoir plus sur les rôles dans les services cloud, regardez la vidéo [Introduction aux services cloud Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). Les informations pour votre service cloud sont stockées dans les fichiers suivants :

- **ServiceDefinition.csdef**

    Le fichier de définition de service définit les paramètres d’exécution pour votre service cloud, y compris les rôles requis, les points de terminaison et la taille de la machine virtuelle. Aucune des données stockées dans ce fichier ne peut être modifiée lorsque votre rôle est en cours d’exécution.

- **ServiceConfiguration.cscfg**

    Le fichier de configuration de service configure le nombre d'instances d'un rôle exécutées et les valeurs des paramètres définis pour un rôle. Les données stockées dans ce fichier peuvent être modifiées lorsque votre rôle est en cours d’exécution.

Pour être en mesure de stocker différentes valeurs pour ces paramètres pour définir le mode d’exécution de votre rôle, vous pouvez disposer de plusieurs configurations de service. Vous pouvez utiliser une configuration de service différente pour chaque environnement de déploiement. Par exemple, vous pouvez définir votre chaîne de connexion de compte de stockage pour utiliser l’émulateur de stockage Azure local dans une configuration de service local et créer une autre configuration de service pour utiliser le stockage Azure dans le cloud.

Lorsque vous créez un service cloud Azure dans Visual Studio, deux configurations de service sont créées par défaut. Ces configurations sont ajoutées à votre projet Azure. Les configurations sont nommées :

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Configurer un service cloud Azure

Vous pouvez configurer un service cloud Azure à partir de l’Explorateur de solutions dans Visual Studio, comme le montre l’illustration suivante.

![Configurer le service cloud](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### Pour configurer un service cloud Azure

1. Pour configurer chaque rôle dans votre projet Azure depuis l’**Explorateur de solutions**, ouvrez le menu contextuel pour le rôle dans le projet Azure, puis choisissez **Propriétés**.

    Une page s’affiche avec le nom du rôle dans l'éditeur Visual Studio. La page affiche les champs pour l’onglet **Configuration**.

1. Dans la liste **Configuration du service** sélectionnez le nom de la configuration de service que vous souhaitez modifier.

    Si vous souhaitez apporter des modifications à toutes les configurations de service pour ce rôle, vous pouvez sélectionner **Toutes les configurations**.

    >[AZURE.IMPORTANT] Si vous choisissez une configuration de service spécifique, certaines propriétés sont désactivées parce qu’elles peuvent être définies uniquement pour toutes les configurations. Pour modifier ces propriétés, vous devez choisir Toutes les configurations.

    Vous pouvez maintenant choisir un onglet pour mettre à jour toutes les propriétés activées sur cette vue.

## Modifier le nombre d’instances d’un rôle

Pour améliorer la performance de votre service cloud, vous pouvez modifier le nombre d’instances d’un rôle qui s’exécutent, en fonction du nombre d’utilisateurs ou de la charge attendue pour un rôle particulier. Une machine virtuelle distincte est créée pour chaque instance d’un rôle quand le service cloud s’exécute dans Azure. Cela affectera la facturation pour le déploiement de ce service cloud. Pour plus d’informations sur la facturation, consultez [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

### Pour modifier le nombre d’instances pour un rôle

1. Choisissez l’onglet **Configuration**.

1. Dans la liste **Configuration du service**, choisissez la configuration de service que vous voulez mettre à jour.

    >[AZURE.NOTE] Vous pouvez définir le nombre d’instances pour une configuration de service spécifique ou pour toutes les configurations de service.

1. Dans la zone de texte **Nombre d’instances**, entrez le nombre d’instances que vous voulez démarrer pour ce rôle.

    >[AZURE.NOTE] Chaque instance est exécutée sur une machine virtuelle distincte quand vous publiez votre service cloud sur Azure.

1. Choisissez le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## Gérer des chaînes de connexion pour des comptes de stockage

Vous pouvez ajouter, supprimer ou modifier des chaînes de connexion pour vos configurations de service. Vous pouvez vouloir des chaînes de connexion différentes pour les différentes configurations de service. Par exemple, vous pouvez vouloir une chaîne de connexion locale pour une configuration de service local qui a pour valeur `UseDevelopmentStorage=true`. Vous pouvez aussi vouloir définir une configuration de service cloud qui utilise un compte de stockage dans Azure.

>[AZURE.WARNING] Lorsque vous entrez les informations de clé du compte de stockage Azure pour une chaîne de connexion de compte de stockage, ces informations sont stockées localement dans le fichier de configuration de service. Toutefois, ces informations ne sont pas stockées sous forme de texte chiffré pour le moment.

Si vous utilisez une valeur différente pour chaque configuration de service, il n’est pas nécessaire d’utiliser des chaînes de connexion différentes dans votre service cloud ni de modifier votre code quand vous publiez votre service cloud sur Azure. Vous pouvez utiliser le même nom pour la chaîne de connexion dans votre code et la valeur sera différente, en fonction de la configuration de service que vous sélectionnez quand vous générez votre service cloud ou quand vous le publiez.

### Pour gérer des chaînes de connexion pour des comptes de stockage

1. Choisissez l’onglet **Paramètres**.

1. Dans la liste **Configuration du service**, choisissez la configuration de service que vous voulez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour des chaînes de connexion pour une configuration de service spécifique, mais si vous avez besoin d’ajouter ou de supprimer une chaîne de connexion vous devez sélectionner Toutes les configurations.

1. Pour ajouter une chaîne de connexion, choisissez le bouton **Ajouter un paramètre**. Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **Nom**, entrez le nom que vous voulez utiliser pour la chaîne de connexion.

1. Dans la liste déroulante **Type**, choisissez **Chaîne de connexion**.

1. Pour modifier la valeur pour la chaîne de connexion, choisissez le bouton de sélection (…). La boîte de dialogue **Créer une chaîne de connexion de stockage** s’affiche.

1. Pour utiliser l’émulateur de compte de stockage local, sélectionnez la case d’option **Émulateur de stockage Microsoft Azure**, puis le bouton **OK**.

1. Pour utiliser un compte de stockage dans Azure, sélectionnez la case d’option **Votre abonnement**, puis le compte de stockage souhaité.

1. Pour utiliser des informations d’identification personnalisées, sélectionnez la case d’option **Informations d’identification entrées manuellement**. Entrez le nom du compte de stockage, ainsi que la clé primaire ou secondaire. Pour des informations sur la façon de créer un compte de stockage et d’entrer les détails pour le compte de stockage dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, consultez [Préparer la publication ou le déploiement d’une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Pour supprimer une chaîne de connexion, sélectionnez la chaîne de connexion, puis choisissez le bouton **Supprimer un paramètre**.

1. Choisissez l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

1. Pour accéder à la chaîne de connexion dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration. Le code suivant montre un exemple où le stockage d’objets blob est créé et où les données sont téléchargées à l’aide d’une chaîne de connexion `MyConnectionString` depuis le fichier de configuration de service quand un utilisateur choisit **Button1** dans la page Default.aspx dans le rôle web pour un service cloud Azure. Ajoutez les instructions using suivantes à Default.aspx.cs :

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ouvrez Default.aspx.cs en mode Conception, et ajoutez un bouton à partir de la boîte à outils. Ajoutez le code suivant à la méthode `Button1_Click`. Ce code utilise `GetConfigurationSettingValue` pour obtenir la valeur depuis le fichier de configuration de service pour la chaîne de connexion. Un objet blob est ensuite créé dans le compte de stockage qui est référencé dans la chaîne de connexion `MyConnectionString` et, enfin, le programme ajoute du texte à l’objet blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

Les paramètres personnalisés dans le fichier de configuration de service vous permettent d’ajouter un nom et une valeur pour une chaîne pour une configuration de service spécifique. Vous pouvez choisir d’utiliser ce paramètre pour configurer une fonctionnalité dans votre service cloud en lisant la valeur du paramètre et en utilisant cette valeur pour contrôler la logique de votre code. Vous pouvez modifier ces valeurs de configuration de service sans devoir régénérer votre package de services ou lorsque votre service cloud est en cours d’exécution. Votre code peut vérifier les notifications produites lorsqu’un paramètre est modifié. Consultez [RoleEnvironment.Changing Event](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Vous pouvez ajouter, supprimer ou modifier des paramètres personnalisés pour vos configurations de service. Vous pouvez vouloir différentes valeurs pour ces chaînes pour différentes configurations de service.

Si vous utilisez une valeur différente pour chaque configuration de service, il n’est pas nécessaire d’utiliser des chaînes différentes dans votre service cloud ni de modifier votre code quand vous publiez votre service cloud sur Azure. Vous pouvez utiliser le même nom pour la chaîne dans votre code et la valeur sera différente, en fonction de la configuration de service que vous sélectionnez quand vous générez votre service cloud ou quand vous le publiez.

### Pour ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

1. Choisissez l’onglet **Paramètres**.

1. Dans la liste **Configuration du service**, choisissez la configuration de service que vous voulez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour des chaînes pour une configuration de service spécifique, mais si vous devez ajouter ou supprimer une chaîne, vous devez sélectionner **Toutes les configurations**.

1. Pour ajouter une chaîne, choisissez le bouton **Ajouter un paramètre**. Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **Nom**, tapez le nom que vous voulez utiliser pour la chaîne.

1. Dans la liste déroulante **Type**, choisissez **Chaîne**.

1. Pour ajouter ou modifier la valeur pour la chaîne, tapez la nouvelle valeur dans la zone de texte **Valeur**.

1. Pour supprimer une chaîne, sélectionnez la chaîne, puis choisissez le bouton **Supprimer un paramètre**.

1. Choisissez le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

1. Pour accéder à la chaîne dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration.

    Vous devez vous assurer que les instructions using suivantes sont déjà ajoutées à Default.aspx.cs tout comme vous l’avez fait dans la procédure précédente.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ajoutez le code suivant à la méthode `Button1_Click` pour accéder à cette chaîne de la même façon que vous accédez à une chaîne de connexion. Votre code peut ensuite exécuter du code spécifique basé sur la valeur de la chaîne de paramètres pour le fichier de configuration de service qui est utilisé.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## Gérer le stockage local pour chaque instance de rôle

Vous pouvez ajouter le stockage de système de fichiers local pour chaque instance d’un rôle. Vous pouvez stocker ici des données locales auxquelles aucun autre rôle n’a besoin d’accéder. Toutes les données que vous n’avez pas besoin d’enregistrer dans un objet table, blob ou stockage de base de données SQL peuvent être stockées ici. Par exemple, vous pourriez utiliser ce stockage local pour mettre en cache des données susceptibles de devoir être utilisées à nouveau. D’autres instances d’un rôle ne peuvent pas avoir accès à ces données stockées.

Les paramètres de stockage local s’appliquent à toutes les configurations de service. Vous pouvez uniquement ajouter, supprimer ou modifier le stockage local pour toutes les configurations de service.

### Pour gérer le stockage local pour chaque instance de rôle

1. Choisissez l’onglet **Stockage local**.

1. Dans la liste **Configuration du service**, choisissez **Toutes les configurations**.

1. Pour ajouter une entrée de stockage local, choisissez le bouton **Ajouter le stockage local**. Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **Nom**, tapez le nom que vous voulez utiliser pour ce stockage local.

1. Dans la zone de texte **Taille**, entrez la taille en Mo dont vous avez besoin pour ce stockage local.

1. Pour supprimer les données dans ce stockage local quand la machine virtuelle pour ce rôle est recyclée, cochez la case **Nettoyer après le recyclage des rôles**.

1. Pour modifier une entrée de stockage local existante, choisissez la ligne que vous devez mettre à jour. Ensuite, vous pouvez modifier les champs, comme décrit dans les étapes précédentes.

1. Pour supprimer une entrée de stockage local, sélectionnez l’entrée de stockage dans la liste, puis choisissez le bouton **Supprimer le stockage local**.

1. Pour enregistrer ces modifications dans les fichiers de configuration de service, choisissez l’icône **Enregistrer** dans la barre d’outils.

1. Pour accéder au stockage local que vous avez ajouté dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration de ressource locale. Utilisez les lignes suivantes de code pour accéder à cette valeur et créer un fichier appelé **MyStorageTest.txt** et écrire une ligne de données de test dans ce fichier. Vous pouvez ajouter ce code dans la méthode `Button_Click` que vous avez utilisée dans les procédures précédentes :

1. Vous devez vous assurer que les instructions using suivantes sont ajoutées à Default.aspx.cs :

    ```
    using System.IO;
    using System.Text;
    ```

1. Ajoutez le code suivant à la méthode `Button1_Click`. Cela crée le fichier dans le stockage local et écrit les données de test dans ce fichier.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Facultatif) Pour consulter ce fichier que vous avez créé quand vous exécutez votre service cloud localement, procédez comme suit :

  1. Exécutez le rôle web et sélectionnez **Button1** pour vous assurer que le code `Button1_Click` est appelé.

  1. Dans la zone de notification, ouvrez le menu contextuel pour l’icône Azure et choisissez **Afficher l’interface utilisateur de l’émulateur de calcul**. La boîte de dialogue **Émulateur de calcul Azure** s’affiche.

  1. Sélectionnez le rôle web.

  1. Dans la barre de menus, choisissez **Outils**, **Ouvrir magasin local**. Une fenêtre Explorateur Windows s’affiche.

  1. Dans la barre de menus, entrez **MyStorageTest.txt** dans la zone de texte **Rechercher**, puis choisissez **Entrée** pour commencer la recherche.

    Le fichier est affiché dans les résultats de recherche.

  1. Pour consulter le contenu du fichier, ouvrez le menu contextuel pour le fichier et choisissez **Ouvrir**.

## Collecter les diagnostics du service cloud

Vous pouvez collecter les données de diagnostic pour votre service cloud Azure. Ces données sont ajoutées à un compte de stockage. Vous pouvez vouloir des chaînes de connexion différentes pour les différentes configurations de service. Par exemple, vous pouvez vouloir un compte de stockage local pour une configuration de service local qui a pour valeur UseDevelopmentStorage=true. Vous pouvez aussi vouloir définir une configuration de service cloud qui utilise un compte de stockage dans Azure. Pour plus d’informations concernant les diagnostics Azure, consultez Recueillir des données de journaux à l’aide des diagnostics Azure.

>[AZURE.NOTE] La configuration de service local est déjà configurée pour utiliser des ressources locales. Si vous utilisez la configuration de service cloud pour publier votre service cloud Azure, la chaîne de connexion que vous spécifiez quand vous publiez est également utilisée pour la chaîne de connexion des diagnostics, à moins que vous ayez spécifié une chaîne de connexion. Si vous créez un package pour votre service cloud à l’aide de Visual Studio, la chaîne de connexion dans la configuration de service n’est pas modifiée.

### Pour collecter les diagnostics du service cloud

1. Choisissez l’onglet **Configuration**.

1. Dans la liste **Configuration du service**, choisissez la configuration de service que vous voulez mettre à jour ou choisissez **Toutes les configurations**.

    >[AZURE.NOTE] Vous pouvez mettre à jour le compte de stockage pour une configuration de service spécifique, mais si vous voulez activer ou désactiver des diagnostics, vous devez choisir Toutes les configurations.

1. Pour activer des diagnostics, cochez la case **Activer les diagnostics**.

1. Pour modifier la valeur pour le compte de stockage, choisissez le bouton de sélection (…).

    La boîte de dialogue **Créer une chaîne de connexion de stockage** s’affiche.

1. Pour utiliser une chaîne de connexion locale, choisissez l’option Émulateur de stockage Azure, puis choisissez le bouton **OK**.

1. Pour utiliser un compte de stockage associé à votre abonnement Azure, choisissez l’option **Votre abonnement**.

1. Pour utiliser un compte de stockage pour la chaîne de connexion locale, choisissez l’option **Informations d’identification entrées manuellement**.

    Pour plus d’informations sur la façon de créer un compte de stockage et d’entrer les détails pour le compte de stockage dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, consultez [Préparer la publication ou le déploiement d’une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Choisissez le compte de stockage que vous souhaitez utiliser dans **Nom du compte**.

    Si vous entrez manuellement les informations d’identification du compte de stockage, copiez ou entrez la clé primaire dans **Clé du compte**. Vous pouvez copier cette clé depuis le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). Pour copier cette clé, suivez ces étapes depuis la vue **Comptes de stockage** dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) :
    
  1. Sélectionnez le compte de stockage que vous voulez utiliser pour votre service cloud.

  1. Choisissez le bouton **Gérer les clés d’accès** situé en bas de l’écran. La boîte de dialogue **Gérer les clés d’accès** s’affiche.

  1. Pour copier la clé d’accès, choisissez le bouton **Copier dans le Presse-papiers**. Vous pouvez maintenant coller cette clé dans le champ **Clé du compte**.

1. Pour utiliser le compte de stockage que vous fournissez, ainsi que la chaîne de connexion pour les diagnostics (et la mise en cache) quand vous publiez votre service cloud sur Azure, cochez la case **Mettre à jour les chaînes de connexion de stockage de développement pour les diagnostics et la mise en cache avec les informations d’identification du compte de stockage Azure lors de la publication vers Azure**.

1. Choisissez le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## Modifier la taille de la machine virtuelle utilisée pour chaque rôle

Vous pouvez définir la taille de machine virtuelle pour chaque rôle. Vous pouvez uniquement définir cette taille pour toutes les configurations de service. Si vous sélectionnez une plus petite taille de machine, alors moins de cœurs d’UC, de mémoire et de stockage disque local sont attribués. La bande passante attribuée est également plus petite. Pour plus d’informations concernant ces tailles et les ressources attribuées, consultez [Tailles pour les services cloud](cloud-services/cloud-services-sizes-specs.md).

Les ressources requises pour chaque machine virtuelle dans Azure affectent le coût de l’exécution de votre service cloud dans Azure. Pour plus d’informations sur la facturation Azure, consultez [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

### Pour modifier la taille de la machine virtuelle

1. Choisissez l’onglet **Configuration**.

1. Dans la liste **Configuration du service**, choisissez **Toutes les configurations**.

1. Pour sélectionner la taille pour la machine virtuelle pour ce rôle, choisissez la taille appropriée dans la liste **Taille de la machine virtuelle**.

1. Choisissez le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## Gérer les points de terminaison et les certificats pour vos rôles

Pour configurer des points de terminaison réseau, spécifiez le protocole, le numéro de port et les informations de certificat SSL pour le protocole HTTPS. Les versions antérieures à juin 2012 prennent en charge HTTP, HTTPS et TCP. La version de juin 2012 prend en charge ces protocoles et UDP. Vous ne pouvez pas utiliser UDP pour les points de terminaison d’entrée dans l’émulateur de calcul. Utilisez ce protocole uniquement pour les points de terminaison internes.

Pour améliorer la sécurité de votre service cloud Azure, vous pouvez créer des points de terminaison qui utilisent le protocole HTTPS. Par exemple, si vous avez un service cloud utilisé par des clients pour passer des commandes, vous voulez vous assurer que leurs informations sont sécurisées à l’aide de SSL.

Vous pouvez aussi ajouter des points de terminaison qui peuvent être utilisés en interne ou en externe. Les points de terminaison externes sont appelés points de terminaison d’entrée. Un point de terminaison d’entrée donne aux utilisateurs un autre point d’accès à votre service cloud. Si vous avez un service WCF, vous pouvez vouloir exposer un point de terminaison interne pour un rôle web à utiliser pour accéder à ce service.

>[AZURE.IMPORTANT] Vous pouvez uniquement mettre à jour des points de terminaison pour toutes les configurations de service.

Si vous ajoutez des points de terminaison HTTPS, vous devez utiliser un certificat SSL. Pour cela, vous pouvez associer des certificats à votre rôle pour toutes les configurations de service et les utiliser pour vos points de terminaison.

>[AZURE.IMPORTANT] Ces certificats ne font pas partie d’un package avec votre service. Vous devez télécharger séparément vos certificats sur Azure via le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).

Les certificats de gestion que vous associez à vos configurations de service s’appliquent seulement quand votre service cloud s’exécute dans Azure. Quand votre service cloud s’exécute dans l’environnement de développement local, un certificat standard géré par l’émulateur de calcul Azure est utilisé.

### Pour ajouter un certificat à un rôle

1. Choisissez l’onglet **Certificats**.

1. Dans la liste **Configuration du service**, choisissez **Toutes les configurations**.

    >[AZURE.NOTE] Pour ajouter ou supprimer des certificats, vous devez sélectionner Toutes les Configurations. Vous pouvez mettre à jour le nom et l’empreinte numérique pour une configuration de service spécifique, si cela est exigé.

1. Pour ajouter un certificat pour ce rôle, choisissez le bouton **Ajouter un certificat**. Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **Nom**, saisissez le nom du certificat.

1. Dans la liste **Emplacement du magasin**, choisissez l’emplacement pour le certificat que vous voulez ajouter.

1. Dans la liste **Nom du magasin**, choisissez le magasin que vous voulez utiliser pour sélectionner le certificat.

1. Pour ajouter le certificat, choisissez le bouton de sélection (...). La boîte de dialogue **Sécurité Windows** s’affiche.

1. Choisissez le certificat que vous voulez utiliser dans la liste, puis choisissez le bouton **OK**.

    >[AZURE.NOTE] Lorsque vous ajoutez un certificat depuis le magasin de certificats, tous les certificats intermédiaires sont automatiquement ajoutés aux paramètres de configuration. Ces certificats intermédiaires doivent également être chargés sur Azure afin de configurer correctement votre service pour SSL.

1. Pour supprimer un certificat, sélectionnez le certificat, puis choisissez le bouton **Supprimer le certificat**.

1. Choisissez l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans les fichiers de configuration de service.

### Pour gérer les points de terminaison pour un rôle

1. Choisissez l’onglet **Points de terminaison**.

1. Dans la liste **Configuration du service**, choisissez **Toutes les configurations**.

1. Pour ajouter un point de terminaison, choisissez le bouton **Ajouter un point de terminaison**. Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **Nom**, tapez le nom que vous voulez utiliser pour ce point de terminaison.

1. Choisissez le type de point de terminaison dont vous avez besoin dans la liste **Type**.

1. Choisissez le protocole pour le point de terminaison dont vous avez besoin dans la liste **Protocole**.

1. Si c’est un point de terminaison d’entrée, entrez le port public à utiliser dans la zone de texte **Port public**.

1. Dans la zone de texte **Port privé**, tapez le port privé à utiliser.

1. Si le point de terminaison nécessite le protocole https, dans la liste **Nom du certificat SSL**, choisissez un certificat à utiliser.

    >[AZURE.NOTE] Cette liste montre les certificats que vous avez ajoutés pour ce rôle sous l’onglet **Certificats**.

1. Choisissez le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans les fichiers de configuration de service.

## Étapes suivantes
En savoir plus sur les projets Azure dans Visual Studio en lisant [Configuration d’un projet Azure](vs-azure-tools-configuring-an-azure-project.md). En savoir plus sur le schéma de service cloud en lisant [Référence de schéma](https://msdn.microsoft.com/library/azure/dd179398).

<!---HONumber=AcomDC_0817_2016-->