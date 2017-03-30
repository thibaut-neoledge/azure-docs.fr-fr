---
title: "Résolution des problèmes de stockage Azure Files | Microsoft Docs"
description: "Résolution des problèmes de stockage Azure Files"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7f719fb38709f4bb7083b7f21a5979f7e0588d0f
ms.lasthandoff: 03/22/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Résolution des problèmes de stockage Azure Files
Cet article répertorie les problèmes courants liés au Stockage Fichier Microsoft Azure lorsque vous vous connectez à partir des clients Windows et Linux. Il fournit également les causes possibles et les solutions de ces problèmes.

**Problèmes généraux (qui se produisent dans les clients Windows et Linux)**

* [Erreur de quota lors de la tentative d’ouverture d’un fichier](#quotaerror)
* [Ralentissement des performances quand vous accédez au Stockage Fichier Azure depuis Windows ou Linux](#slowboth)
* [Guide de traçage des opérations de lecture et d’écriture dans le stockage de fichiers Azure](#traceop)

**Problèmes du client Windows**

* [Ralentissement des performances quand vous accédez au Stockage Fichier Azure depuis Windows 8.1 ou Windows Server 2012 R2](#windowsslow)
* [Erreur 53 lors de la tentative de montage d’un partage de fichiers Azure](#error53)
* [Erreur 87 Le paramètre est incorrect lors de la tentative de montage d’un partage de fichiers Azure](#error87)
* [Net use a réussi, mais je ne vois pas le partage de fichiers Azure monté ou la lettre de lecteur dans l’interface utilisateur de l’Explorateur Windows](#netuse)
* [Mon compte de stockage contient « / » et la commande net use échoue](#slashfails)
* [Mon application/service ne peut pas accéder au lecteur Azure Files monté.](#accessfiledrive)
* [Recommandations supplémentaires pour optimiser les performances](#additional)
* [Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le chiffrement » lors du chargement/de la copie des fichiers vers Azure Files](#encryption)

**Problèmes du client Linux**

* [Erreur d’E/S intermittente : Erreur « L’hôte est hors service (Erreur 112) » sur les partages de fichiers existants, ou l’interpréteur de commandes se bloque lors de l’exécution de listes de commandes sur le point de montage](#errorhold)
* [Erreur de montage 115 lors de la tentative de montage Azure Files sur la machine virtuelle Linux](#error15)
* [Partage de fichiers Azure monté sur la machine virtuelle Linux subissant une baisse des performances](#delayproblem)
* [Erreur de montage (11) : ressource temporairement indisponible lors du montage sur le noyau Ubuntu 4.8+](#ubuntumounterror)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>Erreur de quota lors de la tentative d’ouverture d’un fichier
Sous Windows, vous recevez des messages d’erreur semblables aux suivants :

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

Sous Linux, vous recevez des messages d’erreur semblables aux suivants :

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>Cause :
Le problème se produit car vous avez atteint la limite supérieure de handles ouverts simultanément autorisés pour un fichier.

### <a name="solution"></a>Solution
Réduisez le nombre de handles ouverts simultanément en fermant certains d’entre eux, puis réessayez. Pour plus d’informations, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](storage-performance-checklist.md).

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Ralentissement des performances lors de l’accès au Stockage Fichier depuis Windows ou Linux
* Si vous n’avez pas d’exigence de taille d’E/S minimum spécifique, nous vous recommandons d’utiliser une taille d’E/S de 1 Mo pour des performances optimales.
* Si vous connaissez la taille finale d’un fichier que vous étendez avec des écritures, et si votre logiciel ne présente pas de problèmes de compatibilité avec la fin pas encore écrite du fichier contenant des zéros, définissez la taille du fichier à l’avance au lieu que chaque écriture soit une écriture d’extension.
* Utilisez la méthode de copie appropriée :
      * Utilisez AZCopy pour les transferts entre deux partages de fichiers. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy).
      * Utilisez Robocopy entre un partage de fichiers et un ordinateur local. Pour plus d’informations, consultez [Multi-threaded robocopy for faster copies](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/).
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Ralentissement des performances lors de l’accès au Stockage Fichier depuis Windows 8.1 ou Windows Server 2012 R2
Pour les clients qui exécutent Windows 8.1 ou Windows Server 2012 R2, assurez-vous que le correctif logiciel [KB3114025](https://support.microsoft.com/kb/3114025) est installé. Ce correctif logiciel améliore les performances de création et d’ouverture des handles.

Vous pouvez exécuter le script suivant pour vérifier si le correctif logiciel a été installé sur :

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si le correctif logiciel est installé, la sortie suivante s’affiche :

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> Les images de Windows Server 2012 R2 dans Azure Marketplace ont le correctif logiciel KB3114025 installé par défaut à compter de décembre 2015.
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Guide de traçage des opérations de lecture et d’écriture dans le stockage de fichiers Azure

[Microsoft Message Analyser](https://www.microsoft.com/en-us/download/details.aspx?id=44226) peut vous montrer la requête d’un client en texte clair, et il existe une bonne relation entre les requêtes d’écriture et les transactions (en supposant que SMB ici n’est pas REST).  L’inconvénient est que vous devez exécuter cette procédure sur chaque client, ce qui demande beaucoup de temps si vous avez beaucoup de workers de machines virtuelles IaaS.

Si vous utilisez Message Analyze avec ProcMon, vous pouvez obtenir une bonne idée du code d’application en charge des transactions.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>Recommandations supplémentaires pour optimiser les performances
Ne créez ni n’ouvrez jamais un fichier d’E/S mises en cache qui demande un accès en écriture mais non un accès en lecture. Autrement dit, lorsque vous appelez **CreateFile()**, ne spécifiez jamais uniquement **GENERIC_WRITE**, mais indiquez toujours **GENERIC_READ | GENERIC_WRITE**. Un handle en écriture seule ne peut pas mettre en cache de petites écritures localement, même lorsqu’il s’agit du seul handle ouvert uniquement pour le fichier. Cela impose une pénalité élevée sur les performances pour les petites écritures. Notez que le mode « a » vers CRT **fopen()** ouvre un handle en écriture seule.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>« Erreur 53 » ou « Erreur 67 » lorsque vous essayez de monter ou de démonter un partage Azure File
Ce problème peut être provoqué par les conditions suivantes :

### <a name="cause-1"></a>Cause 1
« Une erreur système 53 est survenue. L’accès est refusé. » Pour des raisons de sécurité, les connexions aux partages Azure Files sont bloquées si le canal de communication n’est pas chiffré et si la tentative de connexion n’est pas effectuée depuis le centre de données sur lequel résident les partages Azure Files. Le chiffrement du canal de communication n’est pas fourni si le système d’exploitation client de l’utilisateur ne prend pas en charge le chiffrement SMB. Cela est indiqué par le message d’erreur « Une erreur système 53 s’est produite. L’accès est refusé. » lorsqu’un utilisateur tente de monter un partage de fichiers en local ou à partir d’un autre centre de données. Windows 8, Windows Server 2012 et les versions ultérieures de chaque demande de négociation incluant SMB 3.0, prenant en charge le chiffrement.

### <a name="solution-for-cause-1"></a>Solution pour la cause 1
Se connecter à partir d’un client qui répond aux exigences de Windows 8, Windows Server 2012 ou les versions ultérieures, ou qui se connecte à partir d’une machine virtuelle se trouvant sur le même centre de données que le compte de stockage Azure utilisé pour le partage Azure Files.

### <a name="cause-2"></a>Cause 2
Le message « Erreur système 53 » ou « Erreur système 67 » lorsque vous montez un partage Azure Files peut apparaître si les communications sortantes du port 445 vers le centre de données Azure Files sont bloquées. Cliquez [ici](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) pour afficher le récapitulatif des FAI qui autorisent ou interdisent l’accès depuis le port 445.

Comcast et certaines entreprises informatiques bloquent ce port. Pour comprendre s’il s’agit de la raison pour laquelle le message « Erreur système 53 » s’affiche, vous pouvez utiliser Portqry pour interroger le point de terminaison TCP:445. Si le point de terminaison TCP:445 est affiché comme filtré, le port TCP est bloqué. Voici un exemple de requête :

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si le port TCP 445 est bloqué par une règle sur le chemin d’accès réseau, vous verrez la sortie suivante :

**Port TCP 445 (service microsoft-ds) : FILTERED**

Pour plus d’informations sur l’utilisation de Portqry, consultez [Description de l’utilitaire de ligne de commande Portqry.exe](https://support.microsoft.com/kb/310099).

### <a name="solution-for-cause-2"></a>Solution pour la cause 2
Contactez votre service informatique pour ouvrir le port 445 sortant aux [plages IP Azure](https://www.microsoft.com/download/details.aspx?id=41653).

<a id="error87"></a>
### <a name="cause-3"></a>Cause 3
Un message « Erreur système 53 ou Erreur système 87 » peut également s’afficher si la communication NTLMv1 est activée sur le client. Le fait d’activer NTLMv1 crée un client moins sécurisé. Par conséquent, les communications sont bloquées pour Azure Files. Pour vérifier s’il s’agit de la cause de l’erreur, vérifiez que la sous-clé de Registre suivante est définie sur une valeur de 3 :

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

Pour plus d’informations, consultez la rubrique [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) sur TechNet.

### <a name="solution-for-cause-3"></a>Solution pour la cause 3
Pour résoudre ce problème, rétablissez la valeur LmCompatibilityLevel dans la clé de Registre HKLM\SYSTEM\CurrentControlSet\Control\Lsa à la valeur par défaut de 3.

Azure Files prend uniquement en charge l’authentification NTLMv2. Assurez-vous que la stratégie de groupe est appliquée aux clients. Cela empêchera que cette erreur ne se produise. En outre, il s’agit d’une pratique de sécurité recommandée. Pour plus d’informations, consultez [Comment configurer les clients pour qu’ils utilisent NTLMv2 à l’aide de la stratégie de groupe](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx)

Le paramètre de stratégie recommandé est **Envoyer uniquement les réponses NTLMv2**. Cela correspond à une valeur de Registre de 3. Les clients utilisent uniquement l’authentification NTLMv2, et ils utilisent la sécurité de la session NTLMv2 si le serveur la prend en charge. Les contrôleurs de domaine acceptent l’authentification LM, NTLM et NTLMv2.

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Net use a réussi, mais le partage Azure Files monté dans l’Explorateur Windows n’est pas accessible
### <a name="cause"></a>Cause :
Par défaut, l’Explorateur Windows ne s’exécute pas en tant qu’administrateur. Si vous exécutez **net use** à partir d’une invite de commandes administrateur, vous mappez le lecteur réseau « en tant qu’administrateur ». Étant donné que les lecteurs mappés sont centrés sur l’utilisateur, le compte d’utilisateur qui est connecté n’affiche pas les lecteurs s’ils sont montés sous un compte d’utilisateur différent.

### <a name="solution"></a>Solution
Montez le partage à partir d’une ligne de commande non administrateur. Vous pouvez également suivre [cette rubrique TechNet](https://technet.microsoft.com/library/ee844140.aspx) pour configurer la valeur de Registre **EnableLinkedConnections**.

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Mon compte de stockage contient « / » et la commande net use échoue
### <a name="cause"></a>Cause :
Lorsque la commande **net use** est exécutée dans l’invite de commandes (cmd.exe), elle est analysée à l’aide de l’ajout de « / » en tant qu’option de ligne de commande. Cela provoque l’échec du mappage du lecteur.

### <a name="solution"></a>Solution
Vous pouvez utiliser l’une des étapes suivantes pour contourner le problème :

•   Utilisez la commande PowerShell suivante :

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

À partir d’un fichier de commandes cela peut être fait comme suit

`Echo new-smbMapping ... | powershell -command –`

•   Placez des guillemets doubles autour de la clé pour résoudre ce problème, sauf si « / » est le premier caractère. Si c’est le cas, utilisez le mode interactif et entrez votre mot de passe séparément, ou régénérez vos clés pour obtenir une clé qui ne commence pas par le caractère barre oblique (/).

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Mon application/service ne peut pas accéder au lecteur Azure Files monté
### <a name="cause"></a>Cause :
Les lecteurs sont montés par l’utilisateur. Si votre application ou service s’exécute sous un compte d’utilisateur différent, les utilisateurs ne verront pas le lecteur.

### <a name="solution"></a>Solution
Montez le lecteur à partir du compte d’utilisateur sous lequel se trouve l’application. Vous pouvez le faire à l’aide d’outils tels que psexec.

Autre option pour **net use** : transmettre le nom du compte de stockage et la clé dans les paramètres de nom d’utilisateur et de mot de passe de la commande **net use**.

Après avoir suivi ces instructions, le message d’erreur suivant peut s’afficher : « Une erreur système 1312 s’est produite. Une session ouverte spécifiée n’existe pas. Elle est peut-être déjà terminée. » lorsque vous exécutez **net use** pour le compte de service système/réseau. Si cela se produit, vérifiez que le nom d’utilisateur transmis à **net use** inclut des informations de domaine (par exemple : « [nom du compte de stockage]..file.core.windows.net »).

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le chiffrement »
### <a name="cause"></a>Cause :
Les fichiers chiffrés par BitLocker peuvent être copiés vers Azure Files. Toutefois, le Stockage Fichier ne prend pas en charge le système de fichiers EFS NTFS. Par conséquent, vous utilisez probablement le système de fichiers EFS dans ce cas. Si vous avez des fichiers chiffrés par EFS, une opération de copie sur le Stockage Fichier peut échouer, sauf si la commande de copie déchiffre un fichier copié.

### <a name="workaround"></a>Solution de contournement
Pour copier un fichier sur le Stockage Fichier, vous devez d’abord le déchiffrer. Pour ce faire, vous pouvez utiliser l’une des méthodes suivantes :

•    Utilisez **copy /d**.

•    Définissez la clé de Registre suivante :

* Chemin d’accès = HKLM\Software\Policies\Microsoft\Windows\System
* Type de valeur = DWORD
* Nom = CopyFileAllowDecryptedRemoteDestination
* Valeur = 1

Toutefois, notez que la définition de la clé de Registre affecte toutes les opérations de copie sur les partages réseau.

<a id="errorhold"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Erreur « L’hôte est hors service (Erreur 112) » sur les partages de fichiers existants, ou l’interpréteur de commandes se bloque quand vous exécutez des listes de commandes sur le point de montage
### <a name="cause"></a>Cause :
Cette erreur se produit sur le client Linux, lorsque le client a été inactif pendant une période prolongée. Quand le client est inactif pendant un long moment, le client se déconnecte et la connexion expire. 

La connexion peut être inactive pour diverses raisons. Les échecs de communication qui empêchent le rétablissement d’une connexion TCP avec le serveur quand l’option de montage « soft » (valeur par défaut) est utilisée constituent l’une de ces raisons.

Des correctifs de reconnexion ne figurant pas dans des noyaux plus anciens peuvent en être une autre.

### <a name="solution"></a>Solution

Spécifier un montage forcé pousse le client à attendre jusqu'à ce qu’une connexion soit établie ou jusqu’à interruption explicite, et peut servir à empêcher les erreurs causées par les délais d’attente réseau. Toutefois, les utilisateurs doivent être conscients que cela peut entraîner une attente indéfinie et doit gérer l’arrêt d’une connexion si nécessaire.

Ce problème de reconnexion dans le noyau Linux est maintenant résolu dans le cadre des ensembles de modifications suivants :

* [Résoudre le problème de reconnexion pour ne pas différer la reconnexion de la session SMB3 longtemps après la reconnexion du socket](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)

* [Appeler le service d’écho immédiatement après la reconnexion du socket](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)

* [CIFS : Corriger une éventuelle corruption de la mémoire lors de la reconnexion](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)

* [CIFS : Corriger un éventuel double verrouillage du mutex lors de la reconnexion - pour les noyaux v4.9 et versions ultérieures](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) 

Toutefois, cette modification ne peut pas encore être transférées aux distributions Linux. Voici la liste des noyaux Linux populaires connus et autres correctifs de reconnexion : 4.4.40+ 4.8.16+ 4.9.1+.
Vous pouvez accéder aux versions du noyau recommandées ci-dessus pour sélectionner le correctif le plus récent.

### <a name="workaround"></a>Solution de contournement
Si vous ne parvenez pas à accéder aux dernières versions du noyau, vous pouvez contourner ce problème en conservant un fichier dans le partage Azure File dans lequel vous écrivez toutes au maximum toutes les 30 secondes. Il doit s’agir d’une opération d’écriture, telle que la réécriture de la date de création/modification du fichier. Sinon, vous pouvez obtenir des résultats mis en cache et votre opération peut ne pas déclencher la reconnexion. 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>« Erreur de montage 115 » lors de la tentative de montage d’Azure Files sur la machine virtuelle Linux
### <a name="cause"></a>Cause :
Les distributions Linux ne prennent pas encore en charge la fonctionnalité de chiffrement dans SMB 3.0. Dans certaines distributions, l’utilisateur peut recevoir un message d’erreur « 115 » s’il essaie de monter Azure Files en utilisant SMB 3.0 en raison d’une fonctionnalité manquante.

### <a name="solution"></a>Solution
Si le client SMB Linux utilisé ne prend pas en charge le chiffrement, montez Azure Files à l’aide de SMB 2.1 à partir d’une machine virtuelle Linux sur le même centre de données que le compte Stockage Fichier.

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>Partage de fichiers Azure monté sur la machine virtuelle Linux subissant une baisse des performances

La désactivation de la mise en cache peut entraîner une baisse des performances. Pour vérifier si la mise en cache est activée, recherchez "cache=".  *cache=none* indique que la mise en cache est désactivée. Veuillez remonter le partage avec la commande de montage par défaut ou ajouter explicitement l’option **cache=strict** à la commande de montage pour vérifier l’activation de la mise en cache par défaut ou du mode de mise en cache « strict ».

Dans certains scénarios, l’option de montage serverino peut entraîner l’exécution de stat par la commande ls sur chaque entrée de répertoire. Ce comportement entraîne une dégradation des performances lors de l’énumération d’un répertoire volumineux. Vous pouvez vérifier les options de montage dans l’entrée « /etc/fstab » :

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Vous pouvez également vérifier si les options appropriées sont utilisées en exécutant simplement la commande **sudo mount | grep cifs**, puis en examinant sa sortie :

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Si les options cache = strict ou serverino ne sont pas présentes, démontez puis remontez Azure Files en exécutant la commande mount à partir de la [documentation](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share). Revérifiez ensuite que les options sont correctes pour l’entrée « /etc/fstab ».

<a id="ubuntumounterror"></a>
## <a name="mount-error11-resource-temporarily-unavailable-when-mounting-to-ubuntu-48-kernel"></a>Erreur de montage (11) : ressource temporairement indisponible lors du montage sur le noyau Ubuntu 4.8+

### <a name="cause"></a>Cause :
Problème connu dans le noyau Ubuntu 16.10 (v.4.8) où le client indique prendre en charge le chiffrement alors que ce n’est pas le cas. 

### <a name="solution"></a>Solution
Jusqu’à ce qu’Ubuntu 16.10 soit corrigé, spécifiez l’option de montage « vers = 2.1 » ou utilisez Ubuntu 16.04.
## <a name="learn-more"></a>En savoir plus
* [Prise en main d’Azure File Storage sur Windows](storage-dotnet-how-to-use-files.md)
* [Prise en main d’Azure File Storage sur Linux](storage-how-to-use-files-linux.md)

