<properties 
   pageTitle="Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs"
	description="Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="storage"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs

Si vous avez installé Azure Tools pour Microsoft Visual Studio, vous pouvez afficher les données relatives aux objets blob, aux files d’attente et aux tables à partir de vos comptes de stockage Azure. Le nœud Azure Storage de l’Explorateur de serveurs affiche les données qui se trouvent dans votre compte d’émulateur de stockage local et dans vos autres comptes de stockage Azure.

Pour afficher l’Explorateur de serveurs dans Visual Studio, choisissez **Affichage**, puis **Explorateur de serveurs** dans la barre de menus. Le nœud Azure Storage affiche tous les comptes de stockage qui existent sous chaque abonnement/certificat Azure auquel vous êtes connecté. Si votre compte de stockage n’apparaît pas, vous pouvez l’ajouter en suivant les instructions fournies [plus loin dans cette rubrique](#add-storage-accounts-by-using-server-explorer).

À partir de la version 2.7 du Kit de développement logiciel (SDK) Azure, vous pouvez également utiliser Cloud Explorer pour afficher et gérer vos ressources Azure. Pour plus d’informations, consultez [Gestion des ressources Azure avec Cloud Explorer](https://msdn.microsoft.com/library/azure/mt185741.aspx).


## Afficher et gérer les ressources de stockage dans Visual Studio

L’Explorateur de serveurs affiche automatiquement la liste des objets blob, des files d’attente et des tables de votre compte d’émulateur de stockage. Le compte d’émulateur de stockage est répertorié dans l’Explorateur de serveurs sous le nœud Azure Storage, en tant que nœud **Développement**.

Pour afficher les ressources du compte de l’émulateur de stockage, développez le nœud **Développement**. Si l’émulateur de stockage n’a pas encore été démarré quand vous développez le nœud **Développement**, il démarrera automatiquement. Le démarrage peut prendre plusieurs secondes. Vous pouvez continuer à travailler dans d’autres parties de Visual Studio pendant le démarrage de l’émulateur de stockage.

Pour afficher les ressources dans un compte de stockage, développez le nœud du compte de stockage dans l’Explorateur de serveurs. Les sous-nœuds suivants s’affichent :

- Objets blob

- Files d’attente

- Tables

## Utilisation des ressources d’objets blob

Le nœud Objets blob affiche la liste des conteneurs associés au compte de stockage sélectionné. Les conteneurs d’objets blob contiennent des fichiers d’objets blob, que vous pouvez ranger dans des dossiers et des sous-dossiers. Pour plus d’informations, consultez la page [Utilisation du stockage d’objets blob à partir de .NET](..storage/storage-dotnet-how-to-use-blobs/).

###Pour créer un conteneur d’objets blob

1. Ouvrez le menu contextuel du nœud **Objets blob**, puis choisissez **Créer un conteneur d’objets blob**.

1. Entrez le nom du nouveau conteneur dans la boîte de dialogue **Créer un conteneur d’objets blob**, puis choisissez **OK**. ![Ajout d’un conteneur d’objets blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744153.bmp)

    >[AZURE.NOTE]Le nom du conteneur d’objets blob doit commencer par un chiffre (0-9) ou une lettre minuscule (a-z).

###Pour supprimer un conteneur d’objets blob

- Ouvrez le menu contextuel du conteneur d’objets blob que vous voulez supprimer, puis sélectionnez **Supprimer**.

###Pour afficher la liste des éléments d’un conteneur d’objets blob

- Ouvrez le menu contextuel d’un conteneur d’objets blob de la liste, puis choisissez **Afficher le conteneur d’objets blob**.

    Le contenu d’un conteneur d’objets blob s’affiche dans un onglet que l’on appelle vue du conteneur d’objets blob.

    ![VST\_SE\_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Vous pouvez effectuer les opérations suivantes sur les objets blob en utilisant les boutons situés dans l’angle supérieur droit de la vue du conteneur d’objets blob :


    - Entrer une valeur de filtre et l’appliquer

    - Actualiser la liste des objets blob du conteneur

    - Charger un fichier

    - Supprimer un objet blob


        >[AZURE.NOTE]Si vous supprimez un fichier du conteneur d’objets blob, le fichier sous-jacent ne sera pas supprimé. Il sera uniquement supprimé du conteneur d’objets blob.


    - Ouvrir un objet blob

    - Enregistrer un objet blob sur l’ordinateur local

###Pour créer un dossier ou un sous-dossier dans un conteneur d’objets blob 

1. Dans l’Explorateur de serveurs, choisissez le conteneur d’objets blob. Dans la fenêtre du conteneur, choisissez le bouton **Charger l’objet blob**.

    ![Chargement d’un fichier dans un dossier d’objets blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Dans la boîte de dialogue **Télécharger un nouveau fichier**, choisissez le bouton **Parcourir** pour spécifier le fichier à charger, puis entrez un nom de dossier dans la zone **Dossier (facultatif)**.

    Vous pouvez ajouter des sous-dossiers dans les dossiers du conteneur en suivant la même procédure. Si vous ne spécifiez pas de nom de dossier, le fichier sera chargé dans le niveau supérieur du conteneur d’objets blob et apparaîtra dans le dossier spécifié du conteneur.

    ![Dossier ajouté à un conteneur d’objets blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Double-cliquez sur le dossier ou appuyez sur Entrée pour afficher le contenu du dossier. Une fois le dossier ouvert, vous pouvez retourner à la racine du conteneur en choisissant le bouton **Ouvrir le répertoire parent** (flèche haut).

###Pour supprimer un dossier du conteneur

 - Supprimer tous les fichiers d’un dossier 

    >[AZURE.NOTE]Étant donné que les dossiers des conteneurs d’objets blob sont des dossiers virtuels, vous ne pouvez pas créer de dossier vide, ni supprimer un dossier pour supprimer son contenu. Pour supprimer un dossier, vous devez d’abord supprimer tout son contenu.

### Pour filtrer les objets blob d’un conteneur

Vous pouvez filtrer les objets blob qui sont affichés en spécifiant un préfixe commun.

Par exemple, si vous entrez le préfixe `hello` dans la zone de texte de filtre, puis choisissez le bouton **Exécuter** (**!**), seuls les objets blob qui commencent par « hello » s’afficheront.

![VST\_SE\_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE]Le champ de filtre respecte la casse et ne prend pas en charge le filtrage à l’aide de caractères génériques. Les objets blob ne peuvent être filtrés que par préfixe. Le préfixe peut inclure un délimiteur si vous utilisez des délimiteurs pour organiser les objets blob en hiérarchie virtuelle. Par exemple, un filtrage à l’aide du préfixe HelloFabric/ retournera tous les objets blob commençant par cette chaîne.

### Pour télécharger les données des objets blob

- Dans l’Explorateur de serveurs, ouvrez le menu contextuel d’un ou plusieurs objets blob, puis choisissez **Ouvrir**. Vous pouvez aussi choisir le nom de l’objet blob, puis cliquer sur le bouton **Ouvrir** ou double-cliquer sur le nom de l’objet blob.

    La progression du téléchargement d’un objet blob s’affiche dans la fenêtre **Journal des activités Azure**.

    L’objet blob s’ouvre dans l’éditeur par défaut pour ce type de fichier. Si le système d’exploitation reconnaît le type de fichier, le fichier s’ouvre dans une application installée localement. Sinon, vous êtes invité à choisir une application qui est appropriée pour le type de fichier de l’objet blob. Le fichier local qui est créé quand vous téléchargez un objet blob est en lecture seule.

    Les données d’objets blob sont mises en cache localement et vérifiées par rapport à l’heure de dernière modification de l’objet blob dans le service Blob. Si l’objet blob a été mis à jour depuis son dernier téléchargement, il sera de nouveau téléchargé. Dans le cas contraire, l’objet blob sera chargé à partir du disque local. Par défaut, un objet blob est téléchargé dans un répertoire temporaire. Pour télécharger des objets blob dans un répertoire spécifique, ouvrez le menu contextuel des objets blob sélectionnés, puis choisissez **Enregistrer sous**. Quand vous enregistrez un objet blob de cette manière, le fichier blob n’est pas ouvert, et le fichier local est créé avec des attributs en lecture-écriture.

### Pour charger des objets blob

- Choisissez le bouton **Charger l’objet blob** quand le conteneur est ouvert sur la vue du conteneur d’objets blob.

    Vous pouvez choisir un ou plusieurs fichiers à charger, et vous pouvez charger des fichiers de tout type. Le **journal des activités Azure** affiche la progression du chargement. Pour plus d’informations sur l’utilisation des données d’objets blob, consultez [Utilisation du service de stockage d’objets blob Azure dans .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### Pour afficher les journaux transférés vers des objets blob

- Si vous utilisez les diagnostics Azure pour enregistrer des données à partir de votre application Azure et si vous avez transféré des journaux vers votre compte de stockage, vous verrez les conteneurs créés par Azure pour ces journaux. L’affichage de ces journaux dans l’Explorateur de serveurs permet d’identifier facilement les problèmes de votre application, en particulier si elle est déployée sur Azure. Pour plus d’informations sur les diagnostics Azure, consultez [Collecte des données de journalisation avec les diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Pour obtenir l’URL d’un objet blob

- Ouvrez le menu contextuel de l’objet blob, puis choisissez **Copier l’URL**.

### Pour modifier un objet blob

- Sélectionnez l’objet blob, puis choisissez le bouton **Ouvrir l’objet blob**.

    Le fichier est téléchargé vers un emplacement temporaire et ouvert sur l’ordinateur local. Vous devez charger de nouveau l’objet blob chaque fois que vous y apportez des modifications.

### Utiliser des ressources de file d’attente

Les files d’attente des services de stockage sont hébergées dans un compte de stockage Azure. Vous pouvez les utiliser pour permettre à vos rôles de service cloud de communiquer entre eux et avec d’autres services par un mécanisme de transmission de messages. Vous pouvez accéder par programmation à la file d’attente via un service cloud et via un service web pour les clients externes. Vous pouvez également accéder à la file d’attente directement à l’aide de l’Explorateur de serveurs dans Visual Studio.

Quand vous développez un service cloud qui utilise des files d’attente, vous pouvez utiliser Visual Studio pour créer des files d’attente et les utiliser de manière interactive quand vous développez et testez votre code.

Dans l’Explorateur de serveurs, vous pouvez afficher les files d’attente dans un compte de stockage, créer et supprimer des files d’attente, ouvrir une file d’attente pour consulter ses messages et ajouter des messages à une file d’attente. Quand vous ouvrez une file d’attente, vous pouvez afficher les messages qu’elle contient. De plus, vous pouvez effectuer les actions suivantes sur la file d’attente à l’aide des boutons situés dans l’angle supérieur gauche :

- Actualiser la vue de la file d’attente

- Envoyer un message vers la file d’attente

- Supprimer le premier message de la file d’attente

- Effacer l’intégralité du contenu de la file d’attente

L’illustration suivante montre une file d’attente contenant deux messages.

![Affichage d’une file d’attente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Pour plus d’informations sur les files d’attente des services de stockage, consultez [Comment : utiliser le service de File d’attente](http://go.microsoft.com/fwlink/?LinkID=264702). Pour plus d’informations sur le service web des files d’attente des services de stockage, consultez [Concepts de File d’attente](http://go.microsoft.com/fwlink/?LinkId=264788). Pour plus d’informations sur la façon d’envoyer des messages vers une file d’attente des services de stockage à l’aide de Visual Studio, consultez [Envoi de messages à une file d’attente de services de stockage](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE]Les files d’attente de services de stockage sont différentes des files d’attente Service Bus. Pour plus d’informations sur les files d’attente Service Bus, consultez « Files d’attente, rubriques et abonnements Service Bus ».

### Utiliser des ressources de table

Le service de stockage Table Azure stocke de grandes quantités de données structurées. Il s’agit d’une banque de données NoSQL qui accepte les appels authentifiés provenant de l’intérieur et de l’extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.

### Création d’une table

1. Dans l’Explorateur de serveurs, sélectionnez le nœud **Tables** du compte de stockage, puis choisissez **Créer une table**.

1. Dans la boîte de dialogue **Créer une table**, entrez un nom pour la table.

### Pour afficher des données de table

1. Dans l’Explorateur de serveurs, ouvrez le nœud **Azure**, puis ouvrez le nœud **Stockage**.

1. Ouvrez le nœud du compte de stockage qui vous intéresse, puis ouvrez le nœud **Tables** pour afficher la liste des tables associées au compte de stockage.

1. Ouvrez le menu contextuel d’une table, puis choisissez **Afficher la table**.

    
    ![Table Azure dans l’Explorateur de solutions](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

La table est organisée en entités (lignes) et propriétés (colonnes). Par exemple, l’illustration suivante montre les entités répertoriées dans le **Concepteur de tables** :


### Pour modifier des données de table

1. Dans le **Concepteur de tables**, ouvrez le menu contextuel d’une entité (une seule ligne) ou d’une propriété (une seule cellule), puis choisissez **Modifier**.

    ![Ajouter ou modifier une entité de table](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Il n’est pas nécessaire que les entités d’une table possèdent les mêmes propriétés (colonnes). Gardez à l’esprit les restrictions suivantes lors de l’affichage et de la modification des données.


    - Vous ne pouvez pas afficher ni modifier des données binaires (type octet), mais vous pouvez les stocker dans une table.

    - Vous ne pouvez pas modifier les valeurs **PartitionKey** et **RowKey**, car le stockage de table d’Azure ne prend pas en charge cette opération.

    - Vous ne pouvez pas créer de propriété appelée Timestamp, car les services Azure Storage utilisent une propriété portant ce nom.

    - Si vous entrez une valeur DateTime, vous devez respecter un format approprié pour les paramètres régionaux et de langue de votre ordinateur. Par exemple, MM/DD/YYYY HH:MM:SS [AM|PM] pour l’anglais (États-Unis).

### Pour ajouter des entités

1. Dans le **Concepteur de tables**, choisissez le bouton **Ajouter une entité** situé dans l’angle supérieur droit de la vue de la table.

    ![Ajouter une entité](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Dans la boîte de dialogue **Ajouter une entité**, entrez les valeurs des propriétés **PartitionKey** et **RowKey**.

    ![Boîte de dialogue Ajouter une entité](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Entrez les valeurs avec précaution, car vous ne pourrez pas les modifier après avoir fermé la boîte de dialogue, sauf si vous supprimez l’entité, puis l’ajoutez de nouveau.

## Pour filtrer des entités

Vous pouvez personnaliser les entités qui s’affichent dans une table à l’aide du Générateur de requêtes.

1. Pour ouvrir le Générateur de requêtes, ouvrez une table pour afficher son contenu.

1. Cliquez sur le bouton situé tout à droite de la barre d’outils de la vue de la table.

    La boîte de dialogue **Générateur de requêtes** s’affiche. L’illustration suivante montre la génération d’une requête dans le Générateur de requêtes.

    ![Générateur de requêtes](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Quand vous avez terminé la création de la requête, fermez la boîte de dialogue. Le formulaire de texte de la requête qui en résulte s’affiche dans une zone de texte en tant que filtre WCF Data Services.

1. Pour exécuter la requête, cliquez sur l’icône représentant un triangle vert.

    Vous pouvez également filtrer les données d’entité qui s’affichent dans le **Concepteur de tables** si vous entrez une chaîne de filtrage WCF Data Services directement dans le champ de filtre. Ce type de chaîne est similaire à une clause SQL WHERE, mais il est toutefois envoyé au serveur en tant que requête HTTP. Pour plus d’informations sur la création de chaînes de filtrage, consultez [Construction de chaînes de filtrage pour le Concepteur de tables](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    L’illustration suivante montre un exemple de chaîne de filtrage valide :

    ![VST\_SE\_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## Actualiser les données de stockage

Les opérations de connexion aux données et de récupération des données entre l’Explorateur de serveurs et le compte de stockage peuvent prendre jusqu’à une minute. Si la connexion est impossible, l’opération peut expirer. Vous pouvez continuer à travailler dans d’autres parties de Visual Studio pendant la récupération des données. Pour annuler une opération si elle prend trop de temps, choisissez le bouton **Arrêter l’actualisation** dans la barre d’outils de l’Explorateur de serveurs.

### Pour actualiser les données d’un conteneur d’objets blob

- Sélectionnez le nœud **Objets blob** sous **Storage**, puis choisissez le bouton **Actualiser** dans la barre d’outils de l’Explorateur de serveurs.

- Pour actualiser la liste des objets blob, choisissez le bouton **Exécuter**.

### Pour actualiser les données d’une table

- Sélectionnez le nœud **Table** sous **Storage**, puis choisissez le bouton **Actualiser**. 

- Pour actualiser la liste des entités qui s’affiche dans le **Concepteur de tables**, choisissez le bouton **Exécuter** dans le **Concepteur de tables**.

### Pour actualiser les données d’une file d’attente

- Sélectionnez le nœud **Files d’attente**, puis choisissez le bouton **Actualiser**.

### Pour actualiser tous les éléments d’un compte de stockage

- Choisissez le nom du compte, puis le bouton **Actualiser** dans la barre d’outils de l’Explorateur de serveurs.

## Ajouter des comptes de stockage à l’aide de l’Explorateur de serveurs

Il existe deux façons d’ajouter des comptes de stockage à l’aide de l’Explorateur de serveurs. Vous pouvez créer un compte de stockage dans votre abonnement Azure, ou vous pouvez attacher un compte de stockage existant.

### Pour créer un compte de stockage à l’aide de l’Explorateur de serveurs

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel du nœud Storage, puis sélectionnez Créer un compte de stockage.

    ![Créer un compte de stockage Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage**.

    - L’abonnement Azure auquel vous voulez ajouter le compte de stockage

    - Le nom que vous voulez utiliser pour le nouveau compte de stockage

    - La région ou le groupe d’affinités (comme l’Ouest des États-Unis ou l’Est de l’Asie)

    - Le type de réplication que vous voulez utiliser pour le compte de stockage, par exemple géo-redondant.

1. Cliquez sur **Créer**.

    Le nouveau compte de stockage s’affiche dans la liste **Stockage** de l’Explorateur de solutions.

### Pour attacher un compte de stockage existant à l’aide de l’Explorateur de serveurs

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel du nœud Azure Storage, puis sélectionnez **Attacher un stockage externe**.

    ![Ajout d’un compte de stockage existant](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage**.

    - Le nom du compte de stockage existant que vous voulez attacher. Vous pouvez entrer un nom ou le sélectionner dans la liste.

    - La clé du compte de stockage sélectionné. Cette valeur est généralement fournie quand vous sélectionnez un compte de stockage. Si vous voulez que Visual Studio se souvienne de la clé du compte de stockage, cochez la case Mémoriser la clé du compte.

    - Le protocole à utiliser pour se connecter au compte de stockage, par exemple HTTP, HTTPS ou un point de terminaison personnalisé. Pour plus d’informations sur les points de terminaison personnalisés, consultez [Configuration de chaînes de connexion Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### Pour afficher les points de terminaison secondaires 

- Si vous avez créé un compte de stockage à l’aide de l’option de réplication **Read-Access Geo Redundant**, vous pouvez afficher ses points de terminaison secondaires. Ouvrez le menu contextuel du compte, puis choisissez **Propriétés**.

    ![Points de terminaison de stockage secondaires](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### Pour supprimer un compte de stockage depuis l’Explorateur de serveurs

- Dans l’Explorateur de serveurs, ouvrez le menu contextuel du compte, puis choisissez **Supprimer**. Si vous supprimez un compte de stockage, toutes les informations de clé enregistrées pour ce compte seront également supprimées.

>[AZURE.NOTE]Si vous supprimez un compte de stockage à partir de l’Explorateur de serveurs, cela n’affectera pas votre compte de stockage ni les données qu’il contient. Cela supprimera simplement sa référence dans l’Explorateur de serveurs. Pour supprimer définitivement un compte de stockage, utilisez le Portail de gestion Azure.

## Voir aussi

[Accès aux services de stockage Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx)

<!---HONumber=September15_HO1-->