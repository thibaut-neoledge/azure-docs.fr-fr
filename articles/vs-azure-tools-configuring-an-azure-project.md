<properties
   pageTitle="Configuration d’un projet Azure Cloud Services à l’aide de plusieurs configurations de service | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure en modifiant les fichiers ServiceDefinition.csdef et ServiceConfiguration.cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Configuration d’un projet Azure Cloud Services utilisant plusieurs configurations de service

Un projet de service cloud Azure comprend deux fichiers de configuration : ServiceDefinition.csdef et ServiceConfiguration.cscfg. Ces fichiers sont fournis avec votre application de service cloud Azure et déployés dans Azure.

- Le fichier **ServiceDefinition.csdef** contient les métadonnées requises par l'environnement Azure selon les besoins de votre application de service cloud, notamment les rôles qu'elle contient. Ce fichier contient également les paramètres de configuration qui s'appliquent à toutes les instances. Ces paramètres de configuration peuvent être lus à l'exécution à l'aide de l'API Azure Service Hosting Runtime. Ce fichier ne peut pas être mis à jour lorsque votre service est en cours d’exécution dans Azure.

- Le fichier **ServiceConfiguration.cscfg** définit des valeurs des paramètres de configuration définis dans le fichier de définition de service et spécifie le nombre d'instances à exécuter pour chaque rôle. Ce fichier peut être mis à jour lorsque votre service cloud est en cours d’exécution dans Azure.

Les outils Windows Azure pour Visual Studio fournissent des pages de propriétés que vous pouvez utiliser pour définir des paramètres de configuration stockés dans ces fichiers. Pour accéder aux pages de propriétés, dans le menu contextuel de la référence de rôle de **l’Explorateur de solutions**, choisissez **Propriétés**, comme illustré dans la figure suivante. Vous pouvez également double-cliquer sur la référence de rôle.

![VS\_Solution\_Explorer\_Roles\_Properties](./media/vs-azure-tools-configuring-an-azure-project/IC784076.png)

Pour plus d’informations sur les schémas sous-jacents des fichiers de définition et de configuration de service, voir la [Référence de schéma](https://msdn.microsoft.com/library/azure/dd179398.aspx). Pour plus d’informations sur la configuration du service, voir [Gestion des configurations de service et des profils](vs-azure-tools-service-configurations-and-profiles-how-to-manage.md).

## Configuration des propriétés de rôle

Les pages de propriétés d’un rôle web et d’un rôle de travail sont similaires, bien qu'il existe quelques différences, décrites dans les sections suivantes. Dans la page **Mise en cache**, vous pouvez configurer les services de mise en cache Azure.

### Page de configuration

Dans la page **Configuration**, vous pouvez définir les propriétés suivantes.

**Instances**

Définir la propriété **Nombre d’instances** sur le nombre d’instances que le service doit exécuter pour ce rôle.

Définir la propriété **Taille de la machine virtuelle** sur **Très petite**, **Petite**, **Moyenne**, **Grande** ou **Très grande**. Pour en savoir plus, voir [Configurer les tailles pour les services cloud](https://msdn.microsoft.com/library/azure/ee814754.aspx).

**Action de démarrage** (rôle web uniquement)

Définissez cette propriété pour spécifier que Visual Studio doit lancer un navigateur web pour les points de terminaison HTTP, les points de terminaison HTTPS, ou les deux lorsque vous commencez le débogage.

L’option **Point de terminaison HTTPS** est disponible uniquement si vous avez déjà défini un point de terminaison HTTPS pour votre rôle. Vous pouvez définir un point de terminaison HTTPS dans la page de propriétés **Points de terminaison**.

Si vous avez déjà ajouté un point de terminaison HTTPS, l’option **Point de terminaison HTTPS** est activée par défaut, et Visual Studio ouvre un navigateur pour ce point de terminaison lorsque vous démarrez le débogage, en plus d’un navigateur pour votre point de terminaison HTTP. Cela suppose que les deux options de démarrage soient activées.

**Diagnostics**

Par défaut, le diagnostic est activé pour le rôle web. Le projet de service cloud Azure et le compte de stockage sont configurés pour utiliser l'émulateur de stockage local. Lorsque vous êtes prêt à déployer sur Azure, vous pouvez cliquer sur le bouton de génération (**...**) pour que le compte de stockage utilise Azure Storage dans le cloud. Vous pouvez transférer les données de diagnostic au compte de stockage à la demande ou à intervalles planifiés automatiquement. Pour plus d’informations sur les diagnostics Azure, consultez [Collecte des données de journalisation avec les diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Page Paramètres

Sur la page **Paramètres**, vous pouvez ajouter des paramètres de configuration à votre service. Les paramètres de configuration sont des paires nom-valeur. Le code exécuté dans le rôle peut lire les valeurs de vos paramètres de configuration à l'exécution à l'aide des classes fournies par la [bibliothèque managée Azure](http://go.microsoft.com/fwlink?LinkID=171026). Plus précisément, la méthode [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) renvoie la valeur d'un paramètre de configuration nommé à l'exécution.

**Configuration d’une chaîne de connexion à un compte de stockage**

Une chaîne de connexion est un paramètre de configuration qui fournit des informations de connexion et d'authentification à l'émulateur de stockage ou à un compte de stockage Azure. Chaque fois que votre code doit accéder aux données de services de stockage Azure (données Blob, File d'attente ou Table) à partir du code en cours d’exécution dans un rôle, vous devez définir une chaîne de connexion pour ce compte de stockage.

Une chaîne de connexion qui pointe vers un compte de stockage Azure doit utiliser un format défini. Pour plus d’informations sur la création de chaînes de connexion, voir [Configuration de chaînes de connexion Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758697.aspx).

Lorsque vous êtes prêt à tester votre service avec les services de stockage Azure, ou lorsque vous êtes prêt à déployer votre service cloud dans Azure, vous pouvez modifier la valeur des chaînes de connexion pour qu’elles pointent vers votre compte de stockage Azure. Cliquez sur (…), puis sélectionnez Entrer les informations d’identification de compte de stockage. Entrez vos informations de compte, notamment le nom et la clé de votre compte. La boîte de dialogue Chaîne de connexion de compte de stockage vous permet également d’indiquer si vous souhaitez utiliser les points de terminaison HTTPS par défaut (option par défaut), les points de terminaison HTTP par défaut ou des points de terminaison personnalisés. Vous pouvez décider d’utiliser des points de terminaison personnalisés si vous avez inscrit un nom de domaine personnalisé pour votre service, comme décrit dans [Configuration d’un nom de domaine personnalisé pour des données d’objets blob dans un compte de stockage Azure](storage-custom-domain-name.md).

>[AZURE.IMPORTANT]Vous devez modifier vos chaînes de connexion pour qu’elles pointent vers un compte de stockage Azure avant de déployer votre service. Dans le cas contraire, le rôle pourrait ne pas démarrer, ou alterner entre les états initialisation, occupé et arrêt.

### Page des points de terminaison

Un rôle de travail peut avoir un nombre quelconque de points de terminaison HTTP, HTTPS ou TCP. Les points de terminaison peuvent être des points de terminaison d'entrée, accessibles aux clients externes, ou des points de terminaison internes, accessibles aux autres rôles qui s'exécutent dans le service.

- Pour rendre un point de terminaison HTTP accessible aux clients externes et aux navigateurs web, spécifiez « Entrée » comme type de point de terminaison et spécifiez un nom et un numéro de port public.

- Pour rendre un point de terminaison HTTPS accessible aux clients et aux navigateurs web externes, spécifiez Entrée comme type de point de terminaison, puis spécifiez un nom, un numéro de port public et un nom de certificat de gestion.

  Pour pouvoir spécifier un certificat de gestion, vous devez définir celui-ci dans la page de propriétés **Certificats**.

- Pour rendre un point de terminaison accessible en interne par d’autres rôles du service cloud, spécifiez **Interne** comme type de point de terminaison, puis spécifiez un nom et des ports privés potentiels pour ce point de terminaison.

### Page de stockage local

Vous pouvez utiliser la page de propriétés Stockage local pour réserver une ou plusieurs ressources de stockage local pour un rôle. Une ressource de stockage local est un répertoire réservé dans le système de fichiers de la machine virtuelle Azure dans lequel s’exécute l’instance d’un rôle. Pour plus d’informations sur l’utilisation des ressources de stockage local, consultez [Configurer les ressources de stockage local](../cloud-services/cloud-services-configure-local-storage-resources.md).

### Page Certificats

Dans la page **Certificats**, vous pouvez associer des certificats à votre rôle. Les certificats que vous ajoutez peuvent être utilisés pour configurer vos points de terminaison HTTPS dans la page de propriétés **Points de terminaison**.

La page de propriétés **Certificats** ajoute des informations sur vos certificats à votre configuration de service. Notez que vos certificats ne sont pas empaquetés avec votre service. Vous devez les charger séparément sur Azure via le [portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Pour associer un certificat à votre rôle, donnez un nom au certificat. Ce nom permet de faire référence au certificat lorsque vous configurez un point de terminaison HTTPS dans la page de propriétés **Points de terminaison**. Ensuite, spécifiez si le magasin de certificats est **Ordinateur local** ou **Utilisateur actuel**, ainsi que le nom du magasin. Enfin, entrez l'empreinte du certificat. Si le certificat figure dans le magasin **Utilisateur actuel\\Personnel (Mon)**, vous pouvez entrer l’empreinte numérique du certificat en sélectionnant celui-ci dans une liste. S’il se trouve dans un autre emplacement, entrez la valeur de l’empreinte numérique manuellement.

Lorsque vous ajoutez un certificat à partir du magasin de certificats, les éventuels certificats intermédiaires sont automatiquement ajoutés aux paramètres de configuration. Ces certificats intermédiaires doivent également être chargés sur Azure afin de configurer correctement votre service pour SSL.

Les certificats de gestion que vous associez à votre service ne s'appliquent à votre service que lorsqu'il s'exécute dans le cloud. Lorsque votre service s'exécute dans l'environnement de développement local, il utilise un certificat standard géré par l'émulateur de calcul.

## Configuration du projet de service cloud Azure

Pour configurer des paramètres qui s’appliquent à l’ensemble d’un projet de service cloud Azure, ouvrez le menu contextuel de ce nœud de projet, puis sélectionnez **Propriétés** pour ouvrir ses pages de propriétés. Le tableau suivant montre ces pages de propriétés.

|Page Propriété|Description|
|---|---|
|Application|Sur cette page, vous pouvez afficher des informations sur la version des outils Azure que ce projet de service cloud utilise et vous pouvez passer à la version actuelle des outils.|
|Événements de build|Sur cette page, vous pouvez définir des événements pré-build et post-build.|
|Développement|Sur cette page, vous pouvez spécifier des instructions de configuration de build et les conditions dans lesquelles les événements post-build s’exécutent.|
|Web|Sur cette page, vous pouvez configurer les paramètres relatifs au serveur web.|

## Étapes suivantes

Pour en savoir plus sur la configuration de projets de service cloud Azure, voir [Gestion des rôles dans les projets de services cloud Azure avec Visual Studio](vs-azure-tools-cloud-service-project-managing-roles.md).

<!---HONumber=Sept15_HO4-->