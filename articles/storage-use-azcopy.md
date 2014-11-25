<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram" />

# Prise en main de l'utilitaire de ligne de commande AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour charger, télécharger et copier des données efficacement vers et à partir du stockage de fichiers et d'objets blob Microsoft Azure. Ce guide fournit une vue d'ensemble de l'utilisation d'AzCopy.

> [WACOM.NOTE] Ce guide considère que vous avez installé AzCopy 2.5 ou une version plus récente. Remarque : il s'agit d'une version préliminaire d'AzCopy ; les options de ligne de commande et leurs fonctions sont susceptibles d'être modifiées dans les prochaines versions.

## Sommaire

-   [Téléchargement et installation d'AzCopy][Téléchargement et installation d'AzCopy]
-   [Compréhension de la syntaxe des lignes de commande d'AzCopy][Compréhension de la syntaxe des lignes de commande d'AzCopy]
-   [Limitation des écritures simultanées lors de la copie des données][Limitation des écritures simultanées lors de la copie des données]
-   [Copie d'objets blob Azure avec AzCopy][Copie d'objets blob Azure avec AzCopy]
-   [Copie des fichiers d'un partage de fichiers Azure avec AzCopy][Copie des fichiers d'un partage de fichiers Azure avec AzCopy]
-   [Versions d'AzCopy][Versions d'AzCopy]
-   [Étapes suivantes][Étapes suivantes]

## <span id="install"></span></a> Téléchargement et installation d'AzCopy

1.  Téléchargez la [dernière version d'AzCopy][dernière version d'AzCopy].
2.  Exécutez le programme d'installation. Par défaut, le programme d'installation d'AzCopy créé un dossier nommé `AzCopy` sous `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (sur un ordinateur exécutant la version 64 bits de Windows) ou `%ProgramFiles%\Microsoft SDKs\Azure\` (sur un ordinateur exécutant la version 32 bits de Windows). Cependant, vous pouvez modifier le chemin d'installation à partir de l'Assistant Installation.
3.  Si vous le souhaitez, vous pouvez ajouter l'emplacement d'installation d'AzCopy au chemin de votre système.

## <span id="syntax"></span></a> Compréhension de la syntaxe des lignes de commande d'AzCopy

Ensuite, ouvrez une fenêtre Commande, puis naviguez jusqu'au répertoire d'installation d'AzCopy sur votre ordinateur, où se trouve l'exécutable `AzCopy.exe`. La syntaxe de base d'une commande AzCopy est :

    AzCopy <source> <destination> [filepattern] [options]

-   Le paramètre `<source>` spécifie les données source à partir desquelles la copie peut s'effectuer. La source peut être un répertoire du système de fichiers, un conteneur d'objets blob, un répertoire virtuel d'objet blob ou un partage de fichiers de stockage.

-   Le paramètre `<destination>` spécifie la destination vers laquelle la copie va s'effectuer. La destination peut être un répertoire du système de fichiers, un conteneur d'objets blob, un répertoire virtuel d'objet blob ou un partage de fichiers de stockage.

-   Le comportement du paramètre optionnel `filepattern` est déterminé par l'emplacement des données source et la présence de l'option mode récursif. Le mode récursif est spécifié via l'option `/S`.

    Si la source spécifiée est un répertoire dans le système de fichiers, les caractères génériques standard sont appliqués et le modèle de fichier fourni est comparé aux fichiers présents dans le répertoire. Si l'option `/S` est spécifiée, AzCopy compare également le modèle spécifié à tous les fichiers présents dans les sous-dossiers du répertoire.

    Si la source spécifiée est un conteneur d'objets blob ou un répertoire virtuel, les caractères génériques ne sont pas appliqués. Si l'option `/S` est spécifiée, AzCopy interprète le modèle de fichier spécifié comme préfixe d'objet blob. Si l'option `/S` n'est pas spécifiée, AzCopy compare le modèle de fichier aux noms exacts d'objets blob.

    Si la source spécifiée est un partage de fichiers Azure, vous devez soit spécifier le nom exact du fichier, (*par ex.* `abc.txt`) pour copier un seul fichier, soit spécifier l'option `/S` pour copier récursivement tous les fichiers du partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l'option `/S`.

    Le modèle de fichier par défaut utilisé lorsqu'aucun modèle de fichier n'est spécifié est `*.*` pour un répertoire de système de fichiers, ou un préfixe vide pour un objet blob ou une ressource de stockage de fichiers Azure.

    > [WACOM.NOTE] Pour des questions de performance, la version 2.5 d'AzCopy ne prend plus en charge les modèles de fichiers multiples avec une seule commande. Dans les cas de modèles de fichier multiples, vous devez maintenant émettre plusieurs commandes, chacune possédant un seul modèle de fichier.

-   Les options disponibles pour le paramètre `options` sont décrites dans le tableau ci-dessous. Vous pouvez également taper `AzCopy /?` à partir de la ligne de commande pour obtenir de l'aide avec les options.

<table>
  <tr>
    <th>Nom de l'option</th>
    <th>Description</th>
    <th>Applicable au stockage d'objets blob (O/N)</th>
    <th>Applicable au stockage de fichiers (O/N)</th>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>Sp&eacute;cifie la cl&eacute; du compte de stockage pour la ressource de destination.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/DestSAS:&lt;container-SAS&gt;</b></td>
    <td>Sp&eacute;cifie une signature d'acc&egrave;s partag&eacute; (SAP) pour le conteneur de destination (le cas &eacute;ch&eacute;ant). Ajoutez des guillemets &agrave; la SAP, car elle peut contenir des caract&egrave;res sp&eacute;ciaux de ligne de commande.<br />
        Si la ressource de destination est un objet blob, vous pouvez sp&eacute;cifier soit cette option suivie par le jeton SAP, soit la SAP comme &eacute;l&eacute;ment d'URI de l'objet blob de destination, sans cette option.<br />
        Cette option est applicable uniquement pour le stockage d'objets blob, et n'est valide que lorsque vous chargez ou copiez des objets blob au sein du m&ecirc;me compte de stockage.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>Sp&eacute;cifie la cl&eacute; du compte de stockage pour la ressource source.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;container-SAS&gt;</b></td>
    <td>Sp&eacute;cifie une signature d'acc&egrave;s partag&eacute; pour le conteneur source (le cas &eacute;ch&eacute;ant). Ajoutez des guillemets &agrave; la SAP, car elle peut contenir des caract&egrave;res sp&eacute;ciaux de ligne de commande. 
        <br />
        Si aucune cl&eacute; ou SAP n'est fournie, le conteneur est lu via un acc&egrave;s anonyme. 
        <br />
        Cette option n'est applicable qu'au stockage d'objets blob.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Sp&eacute;cifie le mode r&eacute;cursif pour les op&eacute;rations de copie. En mode r&eacute;cursif, AzCopy copie tous les objets blob ou fichiers correspondant au mod&egrave;le de fichier sp&eacute;cifi&eacute;, incluant ceux qui se trouvent dans les sous-dossiers.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>Sp&eacute;cifie si la destination est un objet blob de blocs ou un objet blob de pages. Cette option est applicable uniquement lorsque la destination est un objet blob&nbsp;; sinon, une erreur est g&eacute;n&eacute;r&eacute;e. Si la destination est un objet blob et que cette option n'est pas sp&eacute;cifi&eacute;e, AzCopy suppose par d&eacute;faut qu'un objet blob de blocs est d&eacute;sir&eacute;.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMd5</b></td>
    <td>Calcule un hachage MD5 pour les donn&eacute;es t&eacute;l&eacute;charg&eacute;es et v&eacute;rifie que le hachage MD5 stock&eacute; dans la propri&eacute;t&eacute; Content-MD5 de l'objet blob ou du fichier correspond au hachage calcul&eacute;. La v&eacute;rification MD5 est d&eacute;sactiv&eacute;e par d&eacute;faut&nbsp;; vous devez donc sp&eacute;cifier cette option pour lancer la v&eacute;rification MD5 lorsque vous t&eacute;l&eacute;chargez des donn&eacute;es.
    <br />
    Remarque&nbsp;: Azure Storage ne garantit pas que le hachage MD5 stock&eacute; pour l'objet blob ou le fichier est &agrave; jour. Il est de la responsabilit&eacute; du client de mettre &agrave; jour le MD5 lorsque l'objet blob ou le fichier est modifi&eacute;.
    <br />
    AzCopy &eacute;tablit toujours la propri&eacute;t&eacute; Content-MD5 pour un objet blob ou fichier Azure apr&egrave;s l'avoir charg&eacute; sur le service.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indique si le transfert de captures instantan&eacute;es est activ&eacute; ou non. Cette option est valide uniquement lorsque la source est un objet blob. 
        <br />
        Les captures instantan&eacute;es d'objets blob transf&eacute;r&eacute;es sont renomm&eacute;es de cette fa&ccedil;on&nbsp;: [blob-name] (snapshot-time)[extension]. 
        <br />
        Par d&eacute;faut, les captures instantan&eacute;es ne sont pas copi&eacute;es.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>Stocke les messages de statut d&eacute;taill&eacute;s dans un fichier journal. Par d&eacute;faut, le fichier journal d&eacute;taill&eacute; est nomm&eacute; <code data-inline="1">AzCopyVerbose.log</code> dans <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code>. Si vous sp&eacute;cifiez un emplacement de fichier existant pour cette option, le journal d&eacute;taill&eacute; est ajout&eacute; &agrave; ce fichier.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>Sp&eacute;cifie un dossier de fichier journal pour reprendre une op&eacute;ration.<br />
        AzCopy est toujours en mesure de reprendre une op&eacute;ration qui a &eacute;t&eacute; interrompue.<br />
        Si cette option n'est pas sp&eacute;cifi&eacute;e ou est sp&eacute;cifi&eacute;e sans chemin de dossier, AzCopy cr&eacute;e le fichier journal &agrave; l'emplacement par d&eacute;faut, qui est <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Chaque fois que vous &eacute;mettez une commande sur AzCopy, il v&eacute;rifie si un fichier journal existe dans le dossier par d&eacute;faut ou dans un dossier que vous avez sp&eacute;cifi&eacute; via cette option. Si le fichier journal n'existe &agrave; aucun de ces emplacements, AzCopy consid&egrave;re l'op&eacute;ration comme nouvelle et g&eacute;n&egrave;re un nouveau fichier journal.
        <br />
        Si le fichier journal existe, AzCopy v&eacute;rifie si la ligne de commande que vous entrez correspond &agrave; la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l'op&eacute;ration incompl&egrave;te. Si elles ne correspondent pas, il vous sera demand&eacute; soit d'&eacute;craser le fichier journal pour d&eacute;marrer une nouvelle op&eacute;ration, soit d'annuler l'op&eacute;ration actuelle. 
        <br />
        Le fichier journal est supprim&eacute; lorsque l'op&eacute;ration est achev&eacute;e avec succ&egrave;s.
        <br />
        Remarque&nbsp;: reprendre une op&eacute;ration &agrave; partir d'un fichier journal cr&eacute;&eacute; par une version pr&eacute;c&eacute;dente d'AzCopy n'est pas pris en charge.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/@:response-file</b></td>
    <td>Sp&eacute;cifie un fichier qui contient des param&egrave;tres. AzCopy traite les param&egrave;tres dans le fichier comme s'ils avaient &eacute;t&eacute; sp&eacute;cifi&eacute;s dans la ligne de commande.<br /> 
        Dans un fichier r&eacute;ponse, vous pouvez soit sp&eacute;cifier de multiples param&egrave;tres sur une seule ligne, soit sp&eacute;cifier chaque param&egrave;tre sur sa propre ligne. Remarque&nbsp;: un param&egrave;tre individuel ne peut pas couvrir plusieurs lignes. 
        <br />
        Les fichiers r&eacute;ponse peuvent inclure des lignes de commentaires qui commencent par le symbole <code data-inline="1">#</code>. 
        <br />
        Vous pouvez sp&eacute;cifier plusieurs fichiers r&eacute;ponse. Toutefois, AzCopy ne prend pas en charge les fichiers r&eacute;ponse imbriqu&eacute;s.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Supprime toutes les invites de confirmation d'AzCopy.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Sp&eacute;cifie une op&eacute;ration de listing uniquement&nbsp;: aucune donn&eacute;e n'est copi&eacute;e.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>D&eacute;finit l'heure de la derni&egrave;re modification du fichier pour qu'elle soit identique &agrave; celle de l'objet blob ou du fichier source.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Exclut une ressource de source plus r&eacute;cente. La ressource n'est pas copi&eacute;e si la source est plus r&eacute;cente que la destination.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Exclut une ressource de source plus ancienne. La ressource n'est pas copi&eacute;e si la ressource de la source est plus ancienne que la destination.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Charge uniquement les fichiers dont l'attribut Archive est d&eacute;fini.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Charge uniquement les fichiers dont l'un des attributs sp&eacute;cifi&eacute;s est d&eacute;fini.<br />
        Les attributs disponibles incluent&nbsp;:  
        <br />
        R&nbsp;&nbsp;&nbsp;Fichiers en lecture seule
        <br />
        A&nbsp;&nbsp;&nbsp;Fichiers pr&ecirc;ts &agrave; &ecirc;tre archiv&eacute;s
        <br />
        S&nbsp;&nbsp;&nbsp;Fichiers syst&egrave;me
        <br />
        H&nbsp;&nbsp;&nbsp;Fichiers masqu&eacute;s
        <br />
        C&nbsp;&nbsp;&nbsp;Fichiers compress&eacute;s
        <br />
        N&nbsp;&nbsp;&nbsp;Fichiers normaux
        <br />
        E&nbsp;&nbsp;&nbsp;Fichiers crypt&eacute;s
        <br />
        T&nbsp;&nbsp;&nbsp;Fichiers temporaires
        <br />
        O&nbsp;&nbsp;&nbsp;Fichiers hors ligne
        <br />
        I&nbsp;&nbsp;&nbsp;Fichiers non index&eacute;s</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Exclut les fichiers dont l'un des attributs sp&eacute;cifi&eacute;s est d&eacute;fini.<br />
        Les attributs disponibles incluent&nbsp;:  
        <br />
        R&nbsp;&nbsp;&nbsp;Fichiers en lecture seule  
        <br />
        A&nbsp;&nbsp;&nbsp;Fichiers pr&ecirc;ts &agrave; &ecirc;tre archiv&eacute;s  
        <br />
        S&nbsp;&nbsp;&nbsp;Fichiers syst&egrave;me  
        <br />
        H&nbsp;&nbsp;&nbsp;Fichiers masqu&eacute;s  
        <br />
        C&nbsp;&nbsp;&nbsp;Fichiers compress&eacute;s  
        <br />
        N&nbsp;&nbsp;&nbsp;Fichiers normaux  
        <br />
        E&nbsp;&nbsp;&nbsp;Fichiers crypt&eacute;s  
        <br />
        T&nbsp;&nbsp;&nbsp;Fichiers temporaires  
        <br />
        O&nbsp;&nbsp;&nbsp;Fichiers hors ligne  
        <br />
        I&nbsp;&nbsp;&nbsp;Fichiers non index&eacute;s</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Indique le caract&egrave;re d&eacute;limiteur utilis&eacute; pour d&eacute;limiter les r&eacute;pertoires virtuels dans un nom d'objet blob.<br />
        Par d&eacute;faut, AzCopy utilise / comme caract&egrave;re d&eacute;limiteur. Toutefois, AzCopy prend en charge n'importe quel caract&egrave;re commun (tel que @, #, ou %) comme d&eacute;limiteur. Si vous avez besoin d'inclure l'un de ces caract&egrave;res sp&eacute;ciaux dans la ligne de commande, ajoutez des guillemets doubles au nom du fichier. 
        <br />
        Cette option est applicable uniquement au t&eacute;l&eacute;chargement d'objets blob.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC</b></td>
    <td>Sp&eacute;cifie le nombre d'op&eacute;rations simultan&eacute;es. 
        <br />
        Le nombre par d&eacute;faut est 8 fois le nombre de processeurs Core que vous ex&eacute;cutez. Si votre ordinateur poss&egrave;de 8&nbsp;processeurs Core, AzCopy d&eacute;marre par d&eacute;faut 64&nbsp;op&eacute;rations simultan&eacute;es.<br />
        Remarque&nbsp;: un grand nombre d'op&eacute;rations simultan&eacute;es dans un environnement &agrave; bande passante &eacute;troite peut surcharger la connexion r&eacute;seau et entraver le bon d&eacute;roulement des op&eacute;rations. Limitez les op&eacute;rations simultan&eacute;es en fonction de la bande passante de r&eacute;seau qui est disponible.</td>
    <td>O</td>
    <td>O</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob</b></td>
    <td>Sp&eacute;cifie que la ressource <code data-inline="1">source</code> est un objet blob disponible dans l'environnement de d&eacute;veloppement local, ex&eacute;cut&eacute; sur l'&eacute;mulateur de stockage.</td>
    <td>O</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob</b></td>
    <td>Sp&eacute;cifie que la ressource <code data-inline="1">destination</code> est un objet blob disponible dans l'environnement de d&eacute;veloppement local, ex&eacute;cut&eacute; sur l'&eacute;mulateur de stockage.</td>
    <td>O</td>
    <td>N</td>
  </tr>
</table>


## <span id="limit-writes"></span></a> Limitation des écritures simultanées lors de la copie des données

Lorsque vous copiez des objets blob ou des fichiers avec AzCopy, gardez en tête qu'une autre application peut être en train de modifier les données pendant que vous les copiez. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées pendant l'opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel (VHD) associé à une machine virtuelle Azure, assurez-vous qu'aucune autre application n'est en train d'écrire sur le disque VHD. Sinon, vous pouvez commencer par créer une capture instantanée du disque VHD et copier ensuite la capture instantanée.

Si vous ne pouvez pas empêcher d'autres applications d'écrire sur les objets blob ou les fichiers pendant qu'ils sont copiés, gardez en tête qu'au moment où la tâche sera terminée, les ressources copiées n'auront peut-être plus une parité complète avec les ressources source.

## <span id="copy-blobs"></span></a> Copie d'objets blob Azure avec AzCopy

Les exemples ci-dessous démontrent différents scénarios de copie d'objets blob avec AzCopy.

### Copie d'un seul objet blob

**Chargement d'un fichier à partir du système de fichiers vers le stockage d'objets blob :**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Téléchargement d'un objet blob à partir du stockage d'objets blob sur le système de fichiers :**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### Copie d'un objet blob via une copie côté serveur

Lorsque vous copiez un objet blob au sein d'un compte de stockage ou sur plusieurs comptes de stockage, une opération de copie côté serveur est exécutée. Pour plus d'informations sur les opérations de copie côté serveur, consultez [Introduction à la copie d'objets blob asynchrone entre plusieurs comptes][Introduction à la copie d'objets blob asynchrone entre plusieurs comptes].

**Copie d'un objet blob au sein d'un compte de stockage :**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**Copie d'un objet blob sur plusieurs comptes de stockage :**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### Copie d'un objet blob à partir de la région secondaire

Si le stockage géo-redondant avec accès en lecture est activé pour votre compte de stockage, vous pouvez alors copier des données à partir de la région secondaire.

**Copie d'un objet blob sur le compte primaire à partir du secondaire :**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**Téléchargement d'un objet blob du secondaire vers un fichier du système de fichiers :**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### Chargement d'un fichier vers un nouveau conteneur d'objets blob ou un répertoire virtuel

**Chargement d'un fichier vers un nouveau conteneur d'objets blob**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

Remarque : si le conteneur de destination spécifié n'existe pas, AzCopy le crée et y charge le fichier.

**Chargement d'un fichier vers un nouveau répertoire virtuel d'objet blob**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

Remarque : si le répertoire virtuel spécifié n'existe pas, AzCopy charge le fichier pour inclure le répertoire virtuel dans son nom (*par ex.*, `vd/abc.txt` dans le modèle ci-dessus).

### Téléchargement d'un objet blob vers un nouveau dossier

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

Si le dossier `C:\myfolder` n'existe pas encore, AzCopy le crée dans le système de fichiers et télécharge `abc.txt` dans le nouveau dossier.

### Téléchargement de fichiers et de sous-dossiers d'un répertoire vers un conteneur, récursivement

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

Spécifier l'option `/S` engendre la copie des contenus du répertoire spécifié vers le stockage d'objets blob récursivement, ce qui implique également la copie de tous les sous-dossiers et de leurs fichiers. Par exemple, si les fichiers suivants se trouvent dans le dossier `C:\myfolder` :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l'opération de copie, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Chargement de fichiers d'un répertoire vers un conteneur, non-récursivement

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

Si vous ne spécifiez pas l'option `/S` dans la ligne de commande, AzCopy ne copie pas récursivement. Seuls les fichiers du répertoire spécifié sont copiés ; les sous-dossiers et leurs fichiers ne sont PAS copiés. Par exemple, si les fichiers suivants se trouvent dans le dossier `C:\myfolder` :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l'opération de copie, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt

### Téléchargement de tous les objets blob d'un conteneur vers un répertoire du système de fichiers, récursivement

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

Si les objets blob suivants se trouvent dans le conteneur spécifié :

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Après l'opération de copie, le répertoire `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### Téléchargement d'objets blob d'un répertoire d'objets blob virtuel vers un répertoire du système de fichiers, récursivement

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

Si les objets blob suivants se trouvent dans le conteneur spécifié :

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Après l'opération de copie, le répertoire `C:\myfolder` inclut les fichiers suivants. Remarque : seuls les objets blob du répertoire virtuel sont copiés :

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### Chargement des fichiers correspondant au modèle de fichier spécifié vers un conteneur, récursivement

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

Si les fichiers suivants se trouvent dans le dossier `C:\myfolder` :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l'opération de copie, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Téléchargement d'objets blob avec le préfixe spécifié vers le système de fichiers, récursivement

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

Si les objets blob suivants se trouvent dans le conteneur spécifié, tous les objets blob commençant par le préfixe `a` sont copiés :

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Après l'opération de copie, le dossier `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Remarque : le préfixe s'applique au répertoire virtuel, qui forme la première partie du nom de l'objet blob. Dans l'exemple ci-dessus, le répertoire virtuel ne correspond pas au préfixe spécifié ; il n'est donc pas copié.

### Copie d'un objet blob et de ses captures instantanées vers un autre compte de stockage

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

Après l'opération de copie, le conteneur cible inclut l'objet blob et ses captures instantanées. Si on part du principe que l'exemple ci-dessus comprend deux captures instantanées, le conteneur inclut l'objet blob et les captures instantanées suivants :

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### Utilisation d'un fichier réponse pour spécifier les paramètres de ligne de commande

    AzCopy /@:"C:\myfolder\abc.txt"

Vous pouvez inclure n'importe quels paramètres de ligne de commande AzCopy dans un fichier réponse. AzCopy traite les paramètres du fichier comme s'ils avaient été spécifiés sur la ligne de commande, réalisant une substitution directe avec les contenus du fichier.

**Spécification d'un ou plusieurs fichiers réponse à une ligne**

Si un fichier réponse dénommé `source.txt` qui spécifie un conteneur source :

    http://myaccount.blob.core.windows.net/mycontainer

Et un fichier réponse nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

    C:\myfolder

Et un fichier réponse nommé `options.txt` qui spécifie les options pour AzCopy :

    /S /Y

Appellent AzCopy avec ces fichiers réponse résidant tous dans un répertoire `C:\responsefiles`, utilisez cette commande :

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**Spécification d'un fichier réponse multiligne**

Si un fichier réponse nommé `copyoperation.txt`, qui contient les lignes suivantes : Chaque paramètre AzCopy est spécifié sur sa propre ligne :

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

Appelle AzCopy avec ces fichiers réponse, utilisez cette commande :

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

Remarque : chacun des paramètres AzCopy doit être spécifié sur une seule ligne. Par exemple, AzCopy échouera si vous séparez le paramètre en deux lignes, comme démontré ici pour le paramètre `/sourcekey` :

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Spécification d'une signature d'accès partagé (SAP)

**Spécification d'une SAP pour le conteneur source utilisant l'option /sourceSAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**Spécification d'une SAP pour le conteneur source sur l'URI du conteneur source**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**Spécification d'une SAP pour le conteneur de destination utilisant l'option /destSAS**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**Spécification d'une SAP pour les conteneurs source et de destination**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### Spécification d'un dossier de fichier journal

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n'existe à aucun de ces emplacements, AzCopy considère l'opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l'opération incomplète. Si elles ne correspondent pas, il vous sera demandé soit d'écraser le fichier journal pour démarrer une nouvelle opération, soit d'annuler l'opération actuelle.

**Utilisation de l'emplacement par défaut pour le fichier journal**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

Si vous omettez l'option `/Z`, ou spécifiez l'option `/Z` sans le chemin du dossier, comme démontré ci-dessus, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l'opération en se basant sur le fichier journal.

**Spécification d'un emplacement personnalisé pour le fichier journal**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

Cet exemple crée le fichier journal s'il n'existe pas déjà. S'il existe, AzCopy reprend l'opération en se basant sur le fichier journal.

**Reprise d'une opération AzCopy**

    AzCopy /Z:C:\journalfolder\

Cet exemple reprend la dernière opération, qui est susceptible de ne pas avoir abouti.

### Génération d'un fichier journal

**Écriture dans le fichier journal détaillé à l'emplacement par défaut**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

Si vous spécifiez l'option `/V` sans fournir de chemin de fichier pour le journal détaillé, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Écriture dans le fichier journal détaillé à l'emplacement personnalisé**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

Remarque : si vous spécifiez un chemin relatif suivant l'option `/V`, tel que `/V:test/azcopy1.log`, le journal détaillé est alors créé dans le répertoire en cours d'utilisation dans un sous-dossier nommé `test`.

### Définition de l'heure de la dernière modification des fichiers téléchargés pour qu'elle soit identique à celle des objets blob source

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### Exclusion des objets blob de l'opération de copie en se basant sur l'heure de leur dernière modification

Spécifiez l'option `/MT` pour comparer l'heure de la dernière modification de l'objet blob source et du fichier de destination.

**Exclusion des objets blobs qui sont plus récents que le fichier de destination**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**Exclusion des objets blobs qui sont plus anciens que le fichier de destination**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### Spécification du nombre d'opérations simultanées pour démarrer

L'option `/NC` spécifie le nombre d'opérations de copie simultanées. Par défaut, AzCopy démarre des opérations simultanées à huit fois le nombre de processeurs Core que vous avez. Si vous exécutez AzCopy sur un réseau à bande passante étroite, vous pouvez spécifier un nombre inférieur pour cette option afin d'éviter l'échec causé par la compétition de ressources.

### Exécution d'AzCopy sur les ressources d'objets blob dans l'émulateur de stockage

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Copie des fichiers d'un partage de fichiers Azure avec AzCopy

Les exemples ci-dessous démontrent différents scénarios de copie de fichiers Azure avec AzCopy.

### Téléchargement d'un fichier à partir d'un partage de fichiers Azure vers le système de fichiers

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

Remarque : si la source spécifiée est un partage de fichiers Azure, vous devez soit spécifier le nom de fichier exact, (*par ex.* `abc.txt`) pour copier un seul fichier, soit spécifier l'option `/S` pour copier récursivement tous les fichiers du partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l'option `/S`.

### Téléchargement de fichiers et de dossiers d'un partage de fichiers Azure vers le système de fichiers, récursivement

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

Remarque : les dossiers vides ne sont pas copiés.

### Chargement de fichiers et de dossiers du système de fichiers vers un partage de fichiers Azure, récursivement

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Remarque : les dossiers vides ne sont pas copiés.

### Chargement des fichiers correspondant au modèle de fichier spécifié vers un partage de fichiers Azure, récursivement

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> Versions d'AzCopy

| Version    | Nouveautés                                                                                                                                             |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **Version actuelle. Optimise les performances pour les scénarios de copie à grande échelle et introduit plusieurs améliorations d'usage importantes.** |
| V2.4.1     | Permet de spécifier le dossier de destination dans l'Assistant Installation.                                                                           |
| V2.4.0     | Permet de charger et de télécharger des fichiers pour le stockage de fichiers Azure.                                                                   |
| V2.3.0     | Prend en charge les comptes de stockage géo-redondants avec accès en lecture.                                                                          |
| V2.2.2     | Amélioré pour utiliser la version 3.0.3. de la bibliothèque cliente Azure Storage.                                                                     |
| V2.2.1     | Ne rencontre plus de problèmes de performance lors de la copie de grandes quantités de fichiers au sein du même compte de stockage.                    |
| V2.2       | Permet de paramétrer le délimiteur de répertoire virtuel pour des noms d'objets blob. Permet de spécifier le chemin du fichier journal.                |
| V2.1       | Fournit plus de 20 options pour prendre en charge le chargement, le téléchargement et les opérations de copie des objets blob efficacement.            |

## <span id="next-steps"></span></a>Étapes suivantes

Pour plus d'informations sur Azure Storage et AzCopy, consultez les ressources suivantes :

### Documentation d'Azure Storage :

-   [Introduction à Azure Storage][Introduction à Azure Storage]
-   [Stockage des fichiers dans le stockage d'objets blob][Stockage des fichiers dans le stockage d'objets blob]
-   [Création d'un partage de fichiers SMB dans Azure avec le stockage de fichiers][Création d'un partage de fichiers SMB dans Azure avec le stockage de fichiers]

### Billets de blog Azure Storage :

-   [Introduction au service de fichiers Microsoft Azure][Introduction au service de fichiers Microsoft Azure]
-   [AzCopy 2.5 : Optimisation pour les scénarios de copie à grande échelle][AzCopy 2.5 : Optimisation pour les scénarios de copie à grande échelle]
-   [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture][AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture]
-   [AzCopy : Transfert des données avec mode reprise et jeton SAP][AzCopy : Transfert des données avec mode reprise et jeton SAP]
-   [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes][AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes]
-   [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Microsoft Azure][AzCopy : Chargement/téléchargement des fichiers pour les objets blob Microsoft Azure]

  [Téléchargement et installation d'AzCopy]: #install
  [Compréhension de la syntaxe des lignes de commande d'AzCopy]: #syntax
  [Limitation des écritures simultanées lors de la copie des données]: #limit-writes
  [Copie d'objets blob Azure avec AzCopy]: #copy-blobs
  [Copie des fichiers d'un partage de fichiers Azure avec AzCopy]: #copy-files
  [Versions d'AzCopy]: #versions
  [Étapes suivantes]: #next-steps
  [dernière version d'AzCopy]: http://aka.ms/downloadazcopy
  [Introduction à la copie d'objets blob asynchrone entre plusieurs comptes]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Introduction à Azure Storage]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-introduction/
  [Stockage des fichiers dans le stockage d'objets blob]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Création d'un partage de fichiers SMB dans Azure avec le stockage de fichiers]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-files/
  [Introduction au service de fichiers Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5 : Optimisation pour les scénarios de copie à grande échelle]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy : Transfert des données avec mode reprise et jeton SAP]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
