<properties 
    pageTitle="Utilisation de la redirection dans Azure RemoteApp" 
    description="En savoir plus sur la configuration et l&#39;utilisation de la redirection dans RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/29/2015" 
    ms.author="elizapo" />

# Utilisation de la redirection dans Azure RemoteApp

La redirection de périphériques permet à vos utilisateurs d'interagir avec des applications distantes à l'aide des périphériques connectés à leur ordinateur local, téléphone ou tablette. Par exemple, si vous avez fourni Skype via Azure RemoteApp, une caméra doit être installée sur l'ordinateur de votre utilisateur pour qu'il puisse utiliser Skype. Cela est également vrai pour les imprimantes, les haut-parleurs, les écrans et divers périphériques connectés à un port USB.

RemoteApp utilise le protocole RDP (Remote Desktop Protocol) et RemoteFX pour fournir la redirection.

## Quelle redirection est activée par défaut ?
Lorsque vous utilisez RemoteApp, les redirections suivantes sont activées par défaut. Les informations entre parenthèses indiquent le paramètre RDP.

- Lire des sons sur la machine locale (**Lire sur cet ordinateur**) (audiomode:i:0)
- Capturer des données audio à partir de la machine locale et les envoyer à l'ordinateur distant (**Enregistrer à partir de cet ordinateur**). (audiocapturemode:i:1)
- Imprimer sur des imprimantes locales (redirectprinters:i:1)
- Ports COM (redirectcomports:i:1)
- Périphérique à carte à puce (redirectsmartcards:i:1)
- Presse-papiers (capacité à copier et coller) (redirectclipboard:i:1)
- Lissage des polices ClearType (allowfontsmoothing:i:1)
- Rediriger tous les périphériques Plug-and-Play pris en charge (devicestoredirect:s:*)

## Quelle autre redirection est disponible ?
Deux options de redirection sont désactivées par défaut :

- Redirection de lecteur (mappage de lecteur) : les lecteurs de votre ordinateur local deviennent des lecteurs mappés dans la session à distance. Cela vous permet d'enregistrer ou d'ouvrir des fichiers à partir de vos lecteurs locaux pendant que vous travaillez dans la session à distance. 
- Redirection USB : vous pouvez utiliser les périphériques USB connectés à votre ordinateur local dans la session à distance.

## Modification de vos paramètres de redirection dans RemoteApp
Vous pouvez modifier les paramètres de redirection de périphériques pour une collection à l'aide de Microsoft Azure PowerShell avec le Kit de développement logiciel (SDK). Après avoir installé le nouveau PowerShell avec le Kit de développement logiciel(SDK), commencez par le configurer pour gérer votre abonnement, comme décrit dans [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Utilisez ensuite une commande semblable à la suivante pour définir les propriétés RDP personnalisées :

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"
    
(Notez que *`n* est utilisé comme délimiteur entre des propriétés individuelles.)

Pour obtenir la liste des propriétés RDP personnalisées qui sont configurées, exécutez l'applet de commande suivante. Notez que seules les propriétés personnalisées sont représentées en tant que résultats, et pas les propriétés par défaut :

    Get-AzureRemoteAppCollection -CollectionName <collection name> 
 
Lorsque vous définissez des propriétés personnalisées, vous devez chaque fois spécifier toutes les propriétés personnalisées ; sinon, le paramètre redevient désactivé.

### Exemples courants
Utilisez l'applet de commande suivante pour activer la redirection de lecteur :

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Utilisez l'applet de commande suivante pour activer la redirection USB et de lecteur :

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Utilisez l'applet de commande suivante pour désactiver le partage du Presse-papiers :

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT]Veillez à fermer complètement la session de tous les utilisateurs de la collection (et pas seulement à les déconnecter) avant de tester la modification. Pour vous assurer que les sessions d'utilisateurs sont complètement fermées, accédez à l'onglet **Sessions** de la collection dans le portail Azure et fermez la session de tous les utilisateurs qui sont déconnectés ou connectés. L'affichage des lecteurs locaux dans l'Explorateur de la session peut parfois prendre plusieurs secondes.

## Modification des paramètres de redirection USB sur votre client Windows

Si vous voulez utiliser la redirection USB sur un ordinateur qui se connecte à RemoteApp, deux actions doivent se produire. 1 - Votre administrateur doit activer la redirection USB au niveau de la collection à l'aide d'Azure PowerShell. 2 - Sur chaque périphérique sur lequel vous voulez utiliser la redirection USB, vous devez activer une stratégie de groupe qui l'autorise. Cette étape doit être effectuée pour chaque utilisateur qui souhaite utiliser la redirection USB.
   
> [AZURE.NOTE]La redirection USB avec Azure RemoteApp est prise en charge uniquement pour les ordinateurs Windows.

### Activation de la redirection USB pour la collection RemoteApp
Utilisez l'applet de commande suivante pour activer la redirection USB au niveau de la collection : Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Activation de la redirection USB pour l'ordinateur client

Pour configurer les paramètres de redirection USB sur votre ordinateur :

1. Ouvrez l'éditeur d'objets de stratégie de groupe (GPEDIT.MSC). (Exécutez gpedit.msc à partir d'une invite de commandes.)
2. Ouvrez **Configuration ordinateur\Stratégies\Modèles d'administration\Composants Windows\Services Bureau à distance\Client Connexion Bureau à distance\Redirection des périphériques USB RemoteFX**.
3. Double-cliquez sur **Autoriser la redirection RDP d'autres périphériques USB RemoteFX pris en charge à partir de cet ordinateur**.
4. Sélectionnez **Activé**, puis sélectionnez **Administrateurs et utilisateurs dans les droits d'accès à la redirection USB RemoteFX**.
5. Ouvrez une invite de commandes avec des privilèges d'administrateur et exécutez la commande suivante : 

    gpupdate /force
6. Redémarrez l'ordinateur.

Vous pouvez également utiliser l'outil de gestion des stratégie de groupe pour créer et appliquer la stratégie de redirection USB pour tous les ordinateurs de votre domaine :

1. Connectez-vous au contrôleur de domaine en tant qu'administrateur de domaine.
2. Ouvrez la Console de gestion des stratégies de groupe. (Cliquez sur **Démarrer > Outils d'administration > Gestion des stratégies de groupe**.)
3. Accédez au domaine ou à l'unité d'organisation pour lesquels vous voulez créer la stratégie.
4. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
5. Ouvrez **Configuration ordinateur\Stratégies\Modèles d'administration\Composants Windows\Services Bureau à distance\Client Connexion Bureau à distance\Redirection des périphériques USB RemoteFX**.
6. Double-cliquez sur **Autoriser la redirection RDP d'autres périphériques USB RemoteFX pris en charge à partir de cet ordinateur**.
7. Sélectionnez **Activé**, puis sélectionnez **Administrateurs et utilisateurs dans les droits d'accès à la redirection USB RemoteFX**.
8. Cliquez sur **OK**.  

<!---HONumber=July15_HO2-->