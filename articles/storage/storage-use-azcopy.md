<properties 
	pageTitle="Prise en main de l'utilitaire de ligne de commande AzCopy" 
	description="Apprenez à utiliser l'utilitaire AzCopy pour télécharger, charger et copier des objets blob et le contenu de fichiers." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>


<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tamram"/>


# Prise en main de l'utilitaire de ligne de commande AzCopy

## Vue d'ensemble

AzCopy est un utilitaire de ligne de commande conçu pour charger, télécharger et copier des données avec efficacité vers et à partir d'un stockage de fichiers, d'objets blob et de tables Microsoft Azure. Ce guide fournit une vue d'ensemble de l'utilisation d'AzCopy.

> [AZURE.NOTE]Ce guide part du principe que vous avez installé AzCopy 3.2.0 ou une version plus récente. AzCopy 3.x est désormais disponible.
> 
> Ce guide couvre également l’utilisation d’AzCopy 4.2.0, qui est une version préliminaire d’AzCopy. Dans ce guide, les fonctions fournies uniquement dans la version préliminaire sont désignées comme *version préliminaire*.
> 
> Notez que pour AzCopy 4.x, les options de ligne de commande et leurs fonctions sont susceptibles d'être modifiées dans les prochaines versions.

## Téléchargement et installation d'AzCopy

1. Téléchargez la [dernière version d'AzCopy](http://aka.ms/downloadazcopy) ou la [version préliminaire la plus récente](http://aka.ms/downloadazcopypr).
2. Exécutez le programme d'installation. Par défaut, le programme d'installation d'AzCopy créé un dossier nommé `AzCopy` sous `%ProgramFiles(x86)%\Microsoft SDKs\Azure` (sur un ordinateur exécutant la version 64 bits de Windows) ou `%ProgramFiles%\Microsoft SDKs\Azure` (sur un ordinateur exécutant la version 32 bits de Windows). Cependant, vous pouvez modifier le chemin d'installation à partir de l'Assistant Installation.
3. Si vous le souhaitez, vous pouvez ajouter l'emplacement d'installation d'AzCopy au chemin de votre système.

## Compréhension de la syntaxe des lignes de commande d'AzCopy

Ensuite, ouvrez une fenêtre Commande, puis naviguez jusqu'au répertoire d'installation d'AzCopy sur votre ordinateur, où se trouve l'exécutable `AzCopy.exe`. La syntaxe de base d'une commande AzCopy est :

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [AZURE.NOTE]À partir de la version AzCopy 3.0.0, la syntaxe de la ligne de commande AzCopy nécessite que chaque paramètre soit spécifié avec son nom. *Exemple :*, `/ParameterName:ParameterValue`.

## Écrire votre première commande AzCopy

**Chargement d'un fichier à partir du système de fichiers vers le stockage d'objets blob :**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

Notez que lorsque vous copiez un fichier unique, vous devez spécifier l’option /Pattern avec le nom du fichier. Vous trouverez plusieurs exemples dans la section suivante de cet article.

## Introduction aux paramètres

Les paramètres AzCopy sont décrits dans le tableau ci-dessous. Vous pouvez également taper une des commandes suivantes dans la ligne de commande pour obtenir de l'aide sur l'utilisation d'AzCopy :

- Pour l'aide détaillée sur la ligne de commande AzCopy : `AzCopy /?`
- Pour l'aide détaillée sur un paramètre AzCopy : `AzCopy /?:SourceKey`
- Pour des exemples de ligne de commande : `AzCopy /?:Samples` 

<table>
  <tr>
    <th>Nom de l'option</th>
    <th>Description</th>
    <th>Applicable au stockage d'objets blob (O/N)</th>
    <th>S'applique au stockage de fichiers (O/N) (version préliminaire uniquement)</th>
    <th>S'applique au stockage de tables (O/N) (version préliminaire uniquement)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source></b></td>
    <td>Spécifie les données sources à partir desquelles la copie peut s'effectuer. La source peut être un répertoire du système de fichiers, un conteneur d'objets blob, un répertoire virtuel d'objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table&#160;Azure.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination></b></td>
    <td>Spécifie la destination vers laquelle la copie va s'effectuer. La destination peut être un répertoire du système de fichiers, un conteneur d'objets blob, un répertoire virtuel d'objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table&#160;Azure.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;modèle_fichier></b></td>
      <td>
          Spécifie un modèle de fichier qui indique les fichiers à copier. Le comportement du paramètre /Pattern est déterminé par l'emplacement des données sources et la présence de l'option mode récursif. Le mode récursif est spécifié via l'option&#160;/S.
          <br />

          Si la source spécifiée est un répertoire dans le système de fichiers, les caractères génériques standard sont appliqués et le modèle de fichier fourni est comparé aux fichiers présents dans le répertoire. Si l'option&#160;/S est spécifiée, AzCopy compare également le modèle spécifié à tous les fichiers présents dans les sous-dossiers du répertoire.
          <br />

          Si la source spécifiée est un conteneur d'objets blob ou un répertoire virtuel, les caractères génériques ne sont pas appliqués. Si l'option&#160;/S est spécifiée, AzCopy interprète le modèle de fichier spécifié comme un préfixe d'objet blob. Si l'option&#160;/S n'est pas spécifiée, AzCopy compare le modèle de fichier aux noms exacts d'objets blob.
          <br />

          Si la source spécifiée est un partage de fichiers&#160;Azure, vous devez soit spécifier le nom exact du fichier (abc.txt) pour copier un seul fichier, soit spécifier l'option&#160;/S&#160;pour copier récursivement tous les fichiers dans le partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l'option&#160;/S.
          <br />

          AzCopy tient compte de la casse uniquement quand la /Source est un conteneur d’objets blob ou un répertoire virtuel d’objets blob.
          <br/>

          Le modèle de fichier par défaut utilisé lorsqu'aucun modèle de fichier n'est spécifié est *.* pour un emplacement de système de fichiers, ou un préfixe vide pour un emplacement&#160;Azure&#160;Storage. La spécification de plusieurs modèles de fichiers n'est pas prise en charge.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;clé_stockage></b></td>
    <td>Spécifie la clé du compte de stockage pour la ressource de destination.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;jeton_sas></b></td>
    <td class="auto-style1">Spécifie une signature d’accès partagé (SAP) avec les autorisations de lecture et d’écriture pour la destination (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.<br />

        Si la ressource de destination est un conteneur d’objets blob, un partage de fichiers ou une table, vous pouvez spécifier soit cette option suivie du jeton&#160;SAP, soit la SAP comme élément d’URI de l’objet blob, du partage de fichiers ou de la table de destination, sans cette option.<br />

        Si la source et la destination sont toutes les deux des objets blob, l'objet blob de destination doit se trouver dans le même compte de stockage que l'objet blob source.</td>
    <td class="auto-style1">O</td>
    <td class="auto-style1">O<br />
 (version préliminaire uniquement)</td>
    <td class="auto-style1">O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;clé_stockage></b></td>
    <td>Spécifie la clé du compte de stockage pour la ressource source.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;jeton_sas></b></td>
    <td>Spécifie une signature d’accès partagé avec les autorisations de lecture et de listing pour la source (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.
        <br />

        Si la ressource source est un conteneur d'objets blob et si aucune clé ou SAP n'est fournie, le conteneur est lu via un accès anonyme.
        <br />

        Si la source est un partage de fichiers ou une table, une clé ou une SAP doit être fournie.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Spécifie le mode récursif pour les opérations de copie. En mode récursif, AzCopy copie tous les objets blob ou fichiers correspondant au modèle de fichier spécifié, incluant ceux qui se trouvent dans les sous-dossiers.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page | append></b></td>
    <td>Spécifie si la destination est un objet blob de blocs, un objet blob de pages ou un objet blob d’ajouts. Cette option s'applique uniquement lors du téléchargement d'un objet blob&#160;; sinon, une erreur est générée. Si la destination est un objet blob et si cette option n'est pas spécifiée, AzCopy crée par défaut un objet blob de blocs.</td>
    <td>O</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>Calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans la propriété Content-MD5 de l'objet blob ou du fichier correspond au hachage calculé. La vérification MD5 est désactivée par défaut&#160;; vous devez donc spécifier cette option pour lancer la vérification MD5 lorsque vous téléchargez des données.
	<br />

    Remarque&#160;: Azure Storage ne garantit pas que le hachage MD5 stocké pour l'objet blob ou le fichier est à jour. Il est de la responsabilité du client de mettre à jour le MD5 lorsque l'objet blob ou le fichier est modifié.
	<br />

    AzCopy établit toujours la propriété Content-MD5 pour un objet blob ou fichier Azure après l'avoir chargé sur le service.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indique si le transfert de captures instantanées est activé ou non. Cette option est valide uniquement lorsque la source est un objet blob. 
        <br />

        Les captures instantanées d'objets blob transférées sont renommées de cette façon&#160;: [nom_d'objet_Blob] (durée de capture instantanée) [extension]. 
        <br />

        Par défaut, les captures instantanées ne sont pas copiées.</td>
    <td>O</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[fichier_journal détaillé]</b></td>
    <td>Stocke les messages de statut détaillés dans un fichier journal. Par défaut, le fichier journal détaillé est nommé <code>AzCopyVerbose.log</code> dans <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Si vous spécifiez un emplacement de fichier existant pour cette option, le journal détaillé est ajouté à ce fichier.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>Spécifie un dossier de fichier journal pour reprendre une opération.<br />

        AzCopy peut toujours reprendre une opération qui a été interrompue.<br />

        Si cette option n'est pas spécifiée ou est spécifiée sans chemin de dossier, AzCopy crée le fichier journal à l'emplacement par défaut, qui est <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />

        Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n'existe à aucun de ces emplacements, AzCopy considère l'opération comme nouvelle et génère un nouveau fichier journal.
        <br />

		Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l'opération incomplète. Si elles ne correspondent pas, il vous sera demandé soit d'écraser le fichier journal pour démarrer une nouvelle opération, soit d'annuler l'opération actuelle. 
        <br />

        Le fichier journal est supprimé lorsque l'opération est achevée avec succès.
		<br />

		Remarque&#160;: reprendre une opération à partir d'un fichier journal créé par une version précédente d'AzCopy n'est pas pris en charge.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/@:fichier de paramètres</b></td>
    <td>Spécifie un fichier qui contient des paramètres. AzCopy traite les paramètres dans le fichier comme s'ils avaient été spécifiés dans la ligne de commande.<br />
 
		Dans un fichier réponse, vous pouvez soit spécifier de multiples paramètres sur une seule ligne, soit spécifier chaque paramètre sur sa propre ligne. Remarque&#160;: un paramètre individuel ne peut pas couvrir plusieurs lignes. 
        <br />

		Les fichiers réponse peuvent inclure des lignes de commentaires qui commencent par le symbole <code>#</code>. 
        <br />

        Vous pouvez spécifier plusieurs fichiers réponse. Toutefois, AzCopy ne prend pas en charge les fichiers réponse imbriqués.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Supprime toutes les invites de confirmation d'AzCopy.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Spécifie une opération de listing uniquement&#160;: aucune donnée n'est copiée.
    <br />

    AzCopy interprète l’utilisation de cette option comme une simulation de l’exécution de la ligne de commande sans cette option /L et compte le nombre d’objets copiés. Vous pouvez spécifier l’option /V en même temps pour déterminer les objets destinés à être copiés dans le journal détaillé.
    <br />

    Le comportement de cette option est également déterminé par l’emplacement des données sources et la présence de l’option mode récursif /S et de l’option modèle de fichier /Pattern.
    <br />

    AzCopy nécessite les autorisations de listing et de lecture sur cet emplacement source quand cette option est utilisée.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Définit l'heure de la dernière modification du fichier pour qu'elle soit identique à celle de l'objet blob ou du fichier source.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Exclut une ressource de source plus récente. La ressource n'est pas copiée si la source est plus récente que la destination.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Exclut une ressource de source plus ancienne. La ressource n'est pas copiée si la ressource de la source est plus ancienne que la destination.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Charge uniquement les fichiers dont l'attribut Archive est défini.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Télécharge uniquement les fichiers qui ont le jeu d'attributs spécifiés.<br />

        Les attributs disponibles incluent&#160;:  
        <br />

        R&#160;&#160;&#160;Fichiers en lecture seule
        <br />

        A&#160;&#160;&#160;Fichiers prêts à être archivés
        <br />

        S&#160;&#160;&#160;Fichiers système
        <br />

        H&#160;&#160;&#160;Fichiers masqués
        <br />

        C&#160;&#160;&#160;Fichiers compressés
        <br />

        N&#160;&#160;&#160;Fichiers normaux
        <br />

        E&#160;&#160;&#160;Fichiers cryptés
        <br />

        T&#160;&#160;&#160;Fichiers temporaires
        <br />

        O&#160;&#160;&#160;Fichiers hors ligne
        <br />

        I&#160;&#160;&#160;Fichiers non indexés</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Exclut les fichiers dont l'un des attributs spécifiés est défini.<br />

        Les attributs disponibles incluent&#160;:  
        <br />

        R&#160;&#160;&#160;Fichiers en lecture seule  
        <br />

        A&#160;&#160;&#160;Fichiers prêts à être archivés  
        <br />

        S&#160;&#160;&#160;Fichiers système  
        <br />

        H&#160;&#160;&#160;Fichiers masqués  
        <br />

        C&#160;&#160;&#160;Fichiers compressés  
        <br />

        N&#160;&#160;&#160;Fichiers normaux  
        <br />

        E&#160;&#160;&#160;Fichiers cryptés  
        <br />

        T&#160;&#160;&#160;Fichiers temporaires  
        <br />

        O&#160;&#160;&#160;Fichiers hors ligne  
        <br />

        I&#160;&#160;&#160;Fichiers non indexés</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;délimiteur></b></td>
    <td>Indique le caractère délimiteur utilisé pour délimiter les répertoires virtuels dans un nom d'objet blob.<br />

        Par défaut, AzCopy utilise / comme caractère délimiteur. Toutefois, AzCopy prend en charge n'importe quel caractère commun (tel que @, #, ou %) comme délimiteur. Si vous avez besoin d'inclure l'un de ces caractères spéciaux dans la ligne de commande, ajoutez des guillemets doubles au nom du fichier. 
        <br />

        Cette option est applicable uniquement au téléchargement d'objets blob.</td>
    <td>O</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;nombre_d'opérations_simultanées></b></td>
    <td>Spécifie le nombre d'opérations simultanées.
        <br />

        AzCopy lance par défaut un certain nombre d'opérations simultanées pour augmenter la vitesse de transfert des données. Remarque&#160;: un grand nombre d'opérations simultanées dans un environnement à faible bande passante peut surcharger la connexion réseau et entraver le bon déroulement des opérations. Limitez les opérations simultanées en fonction de la bande passante de réseau qui est disponible.
        <br />

		Le nombre maximal d'opérations simultanées est égal à&#160;512.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>Spécifie que la ressource <code>source</code> est un objet blob disponible dans l'environnement de développement local, exécuté sur l'émulateur de stockage.</td>
    <td>O</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>Spécifie que la ressource <code>destination</code> est un objet blob disponible dans l'environnement de développement local, exécuté sur l'émulateur de stockage.</td>
    <td>O</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;"key1#key2#key3#..."></strong></td>
    <td>Fractionne la plage de clés de partition pour activer l'exportation des données de la table en parallèle, ce qui augmente la vitesse d'exportation.
        <br />

        Si cette option n'est pas spécifiée, AzCopy utilise un seul thread pour exporter des entités de table. Exemple&#160;: si l'utilisateur spécifie&#160;/PKRS:"aa#bb", AzCopy lance trois opérations simultanées.
        <br />

        Chaque opération exporte une des trois plages de clés de partition (voir ci-dessous)&#160;: 
        <br />

        &#160;&#160;&#160;[&lt;première clé de partition>, aa) 
        <br />

        &#160;&#160;&#160;[aa, bb)
        <br />

        &#160;&#160;&#160;[bb, &lt;dernière clé de partition>] </td>
    <td>N</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size></strong></td>
    <td>Spécifie la taille de fractionnement du fichier exporté en Mo. La valeur minimale autorisée est de 32.
        <br />

        Si cette option n'est pas spécifiée, AzCopy exporte les données de la table dans un seul fichier.
        <br />

        Si les données de la table sont exportées dans un objet blob et si la taille du fichier exporté atteint la limite de 200&#160;Go pour la taille de l'objet blob, AzCopy fractionne le fichier exporté, même si cette option n'est pas spécifiée. </td>
    <td>N</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:InsertOrSkip | InsertOrMerge | InsertOrReplace</b>
</td>
    <td>Spécifie le comportement pour l'importation des données d'une table.
        <br />

        InsertOrSkip - Ignore une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
        <br />

        InsertOrMerge - Fusionne une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
        <br />

        InsertOrReplace - Remplace une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table. </td>
    <td>N</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;fichier_manifeste></b></td>
    <td>Spécifie le fichier manifeste pour l’importation et l’exportation de la table. <br />

    Cette option est facultative pendant l’exportation&#160;; AzCopy génère un fichier manifeste avec un nom prédéfini si cette option n’est pas spécifiée.
    <br />

    Cette option est nécessaire pendant l’importation pour localiser les fichiers de données.</td>
    <td>N</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
  <tr>
    <td><b>/SyncCopy</b></td>
    <td>Indique s'il faut copier de manière synchronisée les objets blob ou les fichiers entre deux points de terminaison Azure Storage. <br />

		AzCopy utilise par défaut la copie asynchrone du côté serveur. Spécifiez cette option pour effectuer une copie synchrone, qui télécharge les objets blob ou les fichiers vers la mémoire locale et les télécharge Azure Storage. Vous pouvez utiliser cette option pour la copie de fichiers dans le stockage d'objets blob, le stockage de fichiers ou depuis le stockage d'objets blob vers le stockage de fichiers ou vice versa.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/ SetContentType : &lt;type de contenu></b></td>
    <td>Spécifie le type de contenu MIME pour les fichiers ou les objets blob de destination. <br />

		AzCopy définit le type de contenu pour un objet blob ou un fichier sur <code>application/octet-stream</code> par défaut. Vous pouvez définir le type de contenu pour tous les objets blob ou les fichiers en spécifiant explicitement une valeur pour cette option. Si vous spécifiez cette option sans valeur, AzCopy définit chaque type de contenu d'objet blob ou de fichier en fonction de son extension de fichier.</td>
    <td>O</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
    <td>N</td>
  </tr>
    <tr>
    <td><b>/PayloadFormat:&lt;JSON | CSV></b></td>
    <td>Spécifie le format du fichier de données de table exporté.<br />

    Si cette option n’est pas spécifiée, AzCopy exporte le fichier de données de table au format JSON par défaut.</td>
    <td>N</td>
    <td>N</td>
    <td>O<br />
 (version préliminaire uniquement)</td>
  </tr>
</table>
<br/>


## Limitation des écritures simultanées lors de la copie des données

Lorsque vous copiez des objets blob ou des fichiers avec AzCopy, gardez en tête qu'une autre application peut être en train de modifier les données pendant que vous les copiez. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées pendant l'opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel (VHD) associé à une machine virtuelle Azure, assurez-vous qu'aucune autre application n'est en train d'écrire sur le disque VHD. Sinon, vous pouvez commencer par créer une capture instantanée du disque VHD et copier ensuite la capture instantanée.

Si vous ne pouvez pas empêcher d'autres applications d'écrire sur les objets blob ou les fichiers pendant qu'ils sont copiés, gardez en tête qu'au moment où la tâche sera terminée, les ressources copiées n'auront peut-être plus une parité complète avec les ressources source.

## Copie d'objets blob Azure avec AzCopy

Les exemples ci-dessous démontrent différents scénarios de copie d'objets blob avec AzCopy.

### Copie d'un seul objet blob

**Chargement d'un fichier à partir du système de fichiers vers le stockage d'objets blob :**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Téléchargement d'un objet blob à partir du stockage d'objets blob sur le système de fichiers :**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Pour plus d'informations sur l'utilisation de vos clés d'accès de stockage, consultez [Afficher, copier et régénérer les clés d'accès de stockage](../storage-create-storage-account/#regeneratestoragekeys).

### Copie d'un objet blob via une copie côté serveur

Lorsque vous copiez un objet blob au sein d'un compte de stockage ou sur plusieurs comptes de stockage, une opération de copie côté serveur est exécutée. Pour plus d'informations sur les opérations de copie côté serveur, consultez [Introduction à la copie d'objets blob asynchrone entre plusieurs comptes](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Copie d'un objet blob au sein d'un compte de stockage :**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copie d'un objet blob sur plusieurs comptes de stockage :**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### Copie d'un objet blob à partir de la région secondaire 

Si le stockage géo-redondant avec accès en lecture est activé pour votre compte de stockage, vous pouvez alors copier des données à partir de la région secondaire.

**Copie d'un objet blob sur le compte primaire à partir du secondaire :**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Téléchargement d'un objet blob du secondaire vers un fichier du système de fichiers :**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Chargement d'un fichier vers un nouveau conteneur d'objets blob ou un répertoire virtuel

**Chargement d'un fichier vers un nouveau conteneur d'objets blob**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Remarque : si le conteneur de destination spécifié n'existe pas, AzCopy le crée et y charge le fichier.

**Chargement d'un fichier vers un nouveau répertoire virtuel d'objet blob**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Remarque : si le répertoire virtuel spécifié n’existe pas, AzCopy charge le fichier pour inclure le répertoire virtuel dans son nom (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### Téléchargement d'un objet blob vers un nouveau dossier

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si le dossier `C:\myfolder` n'existe pas encore, AzCopy le crée dans le système de fichiers et télécharge `abc.txt ` dans le nouveau dossier.

### Téléchargement de fichiers et de sous-dossiers d'un répertoire vers un conteneur, récursivement

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

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

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Après l'opération de copie, le conteneur cible inclut l'objet blob et ses captures instantanées. Si on part du principe que l'exemple ci-dessus comprend deux captures instantanées, le conteneur inclut l'objet blob et les captures instantanées suivants :

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Utilisation d'un fichier réponse pour spécifier les paramètres de ligne de commande

	AzCopy /@:"C:\myfolder\abc.txt"

Vous pouvez inclure n'importe quels paramètres de ligne de commande AzCopy dans un fichier réponse. AzCopy traite les paramètres du fichier comme s'ils avaient été spécifiés sur la ligne de commande, réalisant une substitution directe avec les contenus du fichier.

**Spécification d'un ou plusieurs fichiers réponse à une ligne**

Si un fichier réponse dénommé `source.txt` qui spécifie un conteneur source :

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Et un fichier réponse nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

	/Dest:C:\myfolder

Et un fichier réponse nommé `options.txt` qui spécifie les options pour AzCopy :

	/S /Y

Appellent AzCopy avec ces fichiers réponse résidant tous dans un répertoire `C:\responsefiles`, utilisez la commande suivante :

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Spécifier un fichier réponse multiligne**

Si un fichier réponse nommé `copyoperation.txt`, qui contient les lignes suivantes : Chaque paramètre AzCopy est spécifié sur sa propre ligne :

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

Appelle AzCopy avec ces fichiers réponse, utilisez cette commande :

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Remarque : chacun des paramètres AzCopy doit être spécifié sur une seule ligne. Par exemple, AzCopy échouera si vous séparez le paramètre en deux lignes, comme démontré ici pour le paramètre `/sourcekey` :

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### Spécification d'une signature d'accès partagé (SAP)
	
**Spécification d'une SAP pour le conteneur source utilisant l'option /sourceSAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Spécification d'une SAP pour le conteneur source sur l'URI du conteneur source**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Spécification d'une SAP pour le conteneur de destination utilisant l'option /destSAS**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Spécification d'une SAP pour les conteneurs source et de destination**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Spécification d'un dossier de fichier journal

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n'existe à aucun de ces emplacements, AzCopy considère l'opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l'opération incomplète. Si elles ne correspondent pas, il vous sera demandé soit d'écraser le fichier journal pour démarrer une nouvelle opération, soit d'annuler l'opération actuelle.

**Utilisation de l'emplacement par défaut pour le fichier journal**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si vous omettez l'option `/Z`, ou spécifiez l'option `/Z` sans le chemin du dossier, comme démontré ci-dessus, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l'opération en se basant sur le fichier journal.

**Spécification d'un emplacement personnalisé pour le fichier journal**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Cet exemple crée le fichier journal s'il n'existe pas déjà. S'il existe, AzCopy reprend l'opération en se basant sur le fichier journal.

**Reprise d'une opération AzCopy**

	AzCopy /Z:C:\journalfolder\

Cet exemple reprend la dernière opération, qui est susceptible de ne pas avoir abouti.


### Génération d'un fichier journal

**Écriture dans le fichier journal détaillé à l'emplacement par défaut**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si vous spécifiez l'option `/V` sans fournir de chemin de fichier pour le journal détaillé, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Écriture dans le fichier journal détaillé à l'emplacement personnalisé**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Remarque : si vous spécifiez un chemin relatif suivant l'option `/V`, tel que `/V:test/azcopy1.log`, le journal détaillé est alors créé dans le répertoire en cours d'utilisation dans un sous-dossier nommé `test`.


### Définition de l'heure de la dernière modification des fichiers téléchargés pour qu'elle soit identique à celle des objets blob source

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Exclusion des objets blob de l'opération de copie en se basant sur l'heure de leur dernière modification

Spécifiez l'option `/MT` pour comparer l'heure de la dernière modification de l'objet blob source et du fichier de destination.

**Exclusion des objets blob qui sont plus récents que le fichier de destination**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Exclusion des objets blob qui sont plus anciens que le fichier de destination**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Spécification du nombre d'opérations simultanées pour démarrer

L'option `/NC` spécifie le nombre d'opérations de copie simultanées. Par défaut, AzCopy démarre des opérations simultanées à huit fois le nombre de processeurs Core que vous avez. Si vous exécutez AzCopy sur un réseau à bande passante étroite, vous pouvez spécifier un nombre inférieur pour cette option afin d'éviter l'échec causé par la compétition de ressources.


### 	Exécution d'AzCopy sur les ressources d'objets blob dans l'émulateur de stockage

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

### Copier de manière synchronisée les objets blob entre deux points de terminaison Azure Storage

AzCopy copie par défaut les données entre deux points de terminaison de stockage de façon asynchrone. Par conséquent, l'opération de copie s'exécute dans l'arrière-plan à l'aide de la capacité de la bande passante, non soumise à un SLA en matière de vitesse de copie d'un objet blob. AzCopy vérifie périodiquement l'état de copie jusqu'à ce que la copie soit terminée ou ait échoué.

L’option `/SyncCopy` garantit que l’opération de copie a une vitesse constante. AzCopy effectue la copie synchrone en téléchargeant les objets blob à copier à partir de la source spécifiée dans la mémoire locale, puis en les téléchargeant sur la destination de stockage d'objets blob.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

Notez que `/SyncCopy` peut générer des coûts supplémentaires par rapport à la copie asynchrone. L’approche recommandée consiste à utiliser cette option dans la machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

### Spécifier le type de contenu MIME d'un objet blob de destination

Par défaut, AzCopy définit le type de contenu d'un objet blob de destination comme `application/octet-stream`. Depuis la version 3.1.0, vous pouvez spécifier explicitement le type de contenu via l'option `/SetContentType:[content-type]`. Cette syntaxe définit le type de contenu pour tous les objets blob dans une opération de copie.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Si vous spécifiez `/SetContentType` sans valeur, AzCopy définit chaque type de contenu d'objet blob ou de fichier en fonction de son extension de fichier.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Copie des fichiers d'un stockage de fichiers Azure avec AzCopy (version préliminaire uniquement)

Les exemples ci-dessous démontrent différents scénarios de copie de fichiers Azure avec AzCopy.

### Téléchargement d'un fichier à partir d'un partage de fichiers Azure vers le système de fichiers

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si la source spécifiée est un partage de fichiers Azure, vous devez soit spécifier le nom exact du fichier (*Exemple* : `abc.txt`) pour copier un seul fichier, soit spécifier l’option `/S` pour copier récursivement tous les fichiers dans le partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l'option `/S`.

### Téléchargement de fichiers et de dossiers d’un partage de fichiers Azure vers le système de fichiers, récursivement et spécification de la signature d’accès de partage

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceSAS:SAS /S

Remarque : les dossiers vides ne sont pas copiés.


### Chargement de fichiers et de dossiers du système de fichiers vers un partage de fichiers Azure, récursivement

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Remarque : les dossiers vides ne sont pas copiés.


### Chargement des fichiers correspondant au modèle de fichier spécifié vers un partage de fichiers Azure, récursivement

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

### Copier des fichiers de façon asynchrone dans le stockage de fichiers Azure

Le stockage de fichiers Azure prend en charge la copie asynchrone côté serveur.

Copie asynchrone depuis le stockage de fichiers vers celui-ci :

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

Copie asynchrone depuis le stockage de fichiers vers l’objet blob de blocs :
  
	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Copie asynchrone depuis le stockage d’objets blob de pages ou de blocs vers le stockage de fichiers :

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

Notez que la copie asynchrone depuis le stockage de fichiers vers un objet blob de pages n’est pas prise en charge.

### Copier des fichiers de façon synchrone dans le fichier de stockage Azure

Outre la copie asynchrone, l’utilisateur peut spécifier l’option `/SyncCopy` pour copier des données de façon synchrone depuis le stockage de fichiers vers celui-ci, depuis le stockage de fichiers vers le stockage d’objets blob et depuis le stockage d’objets blob vers le stockage de fichiers ; pour ce faire, AzCopy télécharge les données source dans la mémoire locale et les charge vers la destination.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
	
	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Pendant la copie depuis le stockage de fichier vers le stockage d’objets blob, le type d’objet blob par défaut est l’objet blob de blocs. L’utilisateur peut spécifier l’option `/BlobType:page` pour modifier le type d’objet blob de destination.

Notez que `/SyncCopy` peut générer des coûts supplémentaires par rapport à la copie asynchrone. L’approche recommandée consiste à utiliser cette option dans la machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.


## Copie d'entités de table Azure avec AzCopy (version préliminaire uniquement)

Les exemples ci-dessous illustrent différents scénarios de copie d'entités de table Azure avec AzCopy.

### Exportation d'entités dans le système de fichiers local

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy écrit un fichier manifeste dans le dossier de destination ou le conteneur d’objets blob spécifié. Le processus d'exportation utilise ce fichier manifeste pour localiser les fichiers de données nécessaires et effectuer la validation des données pendant l'importation. Le fichier manifeste utilise la convention de noms suivante par défaut :

	<account name>_<table name>_<timestamp>.manifest

L’utilisateur peut également spécifier l’option `/Manifest:<manifest file name>` pour définir le nom du fichier manifeste.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest


### Exportation d’entités au format de fichier de données JSON et CSV

Par défaut, AzCopy exporte les entités de table dans des fichiers JSON ; l’utilisateur peut spécifier l’option `/PayloadFormat:JSON|CSV` pour spécifier le type de fichier de données exporté.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Si le format de charge utile CSV est spécifié, outre les fichiers de données portant l’extension `.csv` situés à l’emplacement spécifié par le paramètre `/Dest`, AzCopy génère un fichier de schéma portant l’extension `.schema.csv` pour chaque fichier de données. Notez qu’AzCopy ne prend pas en charge l’« importation » de fichier de données CSV ; vous pouvez utiliser le format JSON pour exporter et importer des données de table.

### Exportation d'entités dans un objet blob Azure

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy crée un fichier de données JSON dans le dossier local ou dans le conteneur d'objets blob en respectant la convention de noms suivante :

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Le fichier de données JSON créé respecte le format de charge utile pour les métadonnées minimales. Pour des informations sur le format de charge utile, consultez la page [Format de charge utile pour les opérations du service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Notez que lorsque vous exportez des entités de table de stockage vers Storage Blob, AzCopy exportera les entités de table pour les fichiers de données temporaires locaux en premier lieu, puis les téléchargera vers l'objet blob. Ces fichiers de données temporaires sont placés dans le dossier du fichier journal avec le chemin d'accès par défaut « <code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code> », vous pouvez spécifier l’option /Z:[dossier de fichier journal\] pour modifier l’emplacement de dossier du fichier et ainsi modifier l'emplacement des fichiers de données temporaires. La taille des fichiers de données temporaires est définie par la taille de vos entités de table et la taille spécifiée avec l'option /SplitSize, bien que le fichier de données temporaire dans le disque local est supprimé instantanément. Une fois qu'il a été téléchargé vers l'objet Blob, assurez-vous que vous disposez de suffisamment d'espace sur le disque local pour stocker ces fichiers de données temporaires avant qu’ils soient supprimés,

### Fractionnement des fichiers exportés

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utilise un *index de volume* dans les noms des fichiers de données fractionnés pour distinguer les fichiers. L'index de volume se compose de deux parties : un *index de plage de clés de partition* et un *index de fichier fractionné*. Ces deux index commencent à zéro.

L'index de plage de clés de partition est égal à 0 si l'utilisateur ne spécifie pas l'option `/PKRS` (présentée dans la section suivante).

Exemple : supposons qu'AzCopy crée deux fichiers de données après que l'utilisateur a spécifié l'option `/SplitSize`. Les noms des fichiers de données qui en résultent peuvent être :

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Remarque : la valeur minimale possible pour l'option `/SplitSize` est 32 Mo. Si la destination spécifiée est un stockage d'objets blob, AzCopy fractionne le fichier de données lorsque sa taille atteint la limite de taille des objets blob (200 Go), que l'utilisateur ait spécifié ou non l'option `/SplitSize`.

### Exportation simultanée d'entités

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy lance des opérations simultanées d'exportation d'entités lorsque l'utilisateur spécifie l'option `/PKRS`. Chaque opération exporte une plage de clés de partition.

Remarque : l'option `/NC` contrôle également le nombre d'opérations simultanées. AzCopy utilise le nombre de processeurs Core comme valeur par défaut de `/NC` pendant la copie d'entités de table, même si l'option `/NC` n'a pas été spécifiée. Lorsque l'utilisateur spécifie l'option `/PKRS`, AzCopy utilise la plus petite des deux valeurs (plages de clés de partition par rapport aux opérations simultanées implicitement ou explicitement spécifiées) pour déterminer le nombre d'opérations simultanées à démarrer. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

### Importation simultanée d'entités

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

L'option `/EntityOperation` indique comment insérer des entités dans la table. Les valeurs possibles sont les suivantes :

- `InsertOrSkip` : ignore une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
- `InsertOrMerge` : fusionne une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
- `InsertOrReplace` : remplace une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.

Remarque : vous ne pouvez pas spécifier l'option `/PKRS` dans le scénario d'importation. À la différence du scénario d'exportation dans lequel vous devez spécifier l'option `/PKRS` pour démarrer des opérations simultanées, AzCopy lance par défaut des opérations simultanées lorsque vous importez des entités. Le nombre par défaut d'opérations simultanées démarrées est égal au nombre de processeurs Core. Cependant, vous pouvez spécifier un nombre différent d'opérations simultanées avec l'option `/NC`. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.


## Problèmes connus et les meilleures pratiques

#### Exécuter une instance de AzCopy sur un même ordinateur.
AzCopy est conçu pour optimiser l'utilisation de votre ressource de l'ordinateur afin d’accélérer le transfert de données. Nous vous recommandons d'exécuter une seule instance de AzCopy sur un même ordinateur et de spécifier l'option `/NC` si vous avez besoin de plus d'opérations simultanées. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

#### Activer les algorithmes MD5 compatibles FIPS pour AzCopy quand vous « utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature ».
Par défaut, AzCopy utilise l’implémentation MD5 .NET pour calculer le hachage MD5 pendant la copie d’objets, mais en raison de certaines exigences de sécurité, AzCopy doit activer le paramètre MD5 compatible FIPS.

Vous pouvez créer un fichier app.config `AzCopy.exe.config` avec la propriété `AzureStorageUseV1MD5` et le mettre à part avec AzCopy.exe.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>

	  </appSettings>
	</configuration>

Si la propriété AzureStorageUseV1MD5 a pour valeur true (valeur par défaut), AzCopy utilise l’implémentation MD5 .NET ; si elle a pour valeur false, AzCopy utilise l’algorithme MD5 compatible FIPS.

Notez que les algorithmes compatibles FIPS sont désactivés par défaut sur votre ordinateur Windows ; vous pouvez taper secpol.msc dans la fenêtre Exécuter et activer le commutateur « Chiffrement système : utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature » (Paramètres de sécurité -> Stratégies locales -> Options de sécurité).

## Versions d'AzCopy

| Version | Nouveautés |
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.2.0** | **Version préliminaire actuelle. Inclut toutes les fonctionnalités de V3.2.0. Prend également en charge les SAP pour le partage de fichiers de stockage, la copie asynchrone du stockage de fichiers, l’exportation des entités de table au format CSV et la spécification du nom de manifeste pendant l’exportation des entités de table**
| **V3.2.0** | **Version actuelle. Prend en charge un objet blob d’ajouts et le paramètre MD5 compatible FIPS**
| V4.1.0 | Inclut toutes les fonctionnalités de V3.1.0. Prend en charge la copie de fichiers et des objets blob de façon synchrone et spécifie le type de contenu pour les fichiers et les objets blob de destination
| V3.1.0 | Prend en charge la copie des objets blob de façon synchrone et spécifie le type de contenu pour les objets blob de destination.
| V4.0.0 | Comporte toutes les fonctionnalités V3.0.0. Prend également en charge la copie de fichiers de ou vers le stockage de fichiers Azure, ainsi que la copie d'entités de ou vers le stockage de tables Azure.
| V3.0.0 | Modifie la syntaxe de la ligne de commande AzCopy en exigeant les noms des paramètres. L'aide de la ligne de commande a été revue. Cette version prend en charge uniquement la copie de ou vers le stockage d'objets blob Azure.	
| V2.5.1 | Optimise les performances lorsque les options /xo et /xn sont utilisées. Résout les bogues concernant les caractères spéciaux dans les noms des fichiers sources et la corruption du fichier journal après une erreur de saisie de l'utilisateur dans la syntaxe de la ligne de commande.	
| V2.5.0 | Optimise les performances pour les scénarios de copie à grande échelle et introduit plusieurs améliorations importantes pour l'utilisation.
| V2.4.1 | Permet de spécifier le dossier de destination dans l'Assistant Installation.                     			
| V2.4.0 | Permet de charger et de télécharger des fichiers pour le stockage de fichiers Azure.
| V2.3.0 | Prend en charge les comptes de stockage géo-redondants avec accès en lecture.|
| V2.2.2 | Amélioré pour utiliser la version 3.0.3. de la bibliothèque cliente Azure Storage.
| V2.2.1 | Ne rencontre plus de problèmes de performance lors de la copie de grandes quantités de fichiers au sein du même compte de stockage.
| V2.2 | Permet de paramétrer le délimiteur de répertoire virtuel pour des noms d'objets blob. Permet de spécifier le chemin du fichier journal.|
| V2.1 | Fournit plus de 20 options pour prendre en charge le chargement, le téléchargement et les opérations de copie des objets blob efficacement.|


## Étapes suivantes

Pour plus d'informations sur Azure Storage et AzCopy, consultez les ressources suivantes :

### Documentation d'Azure Storage :

- [Introduction à Azure Storage](storage-introduction.md)
- [Stockage des fichiers dans le stockage d'objets blob](storage-dotnet-how-to-use-blobs.md)
- [Création d'un partage de fichiers SMB dans Azure avec le stockage de fichiers](storage-dotnet-how-to-use-files.md)

### Billets de blog Azure Storage :
- [AzCopy : Présentation de la copie synchrone et du type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy : Annonce de la disponibilité générale d'AzCopy 3.0 plus version préliminaire d'AzCopy 4.0 avec prise en charge de fichier et de table](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy : Optimisation pour les scénarios de copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy : Transfert des données avec mode reprise et jeton SAP](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

 

<!---HONumber=August15_HO6-->