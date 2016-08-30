<properties 
    pageTitle="Comment rediriger les périphériques USB dans Azure RemoteApp ? | Microsoft Azure" 
    description="Apprenez à utiliser la redirection de périphériques USB dans Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# Comment rediriger les périphériques USB dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

La redirection de périphérique permet aux utilisateurs d’utiliser les périphériques USB raccordés à leur ordinateur ou à une tablette avec les applications de Azure RemoteApp. Par exemple, si vous avez partagé Skype via Azure RemoteApp, vos utilisateurs devront être en mesure d’utiliser les caméras de leurs périphériques mobiles.

Avant de poursuivre, assurez-vous de lire les informations de redirection USB dans [Utilisation de la redirection dans Azure RemoteApp](remoteapp-redirection.md). Toutefois, la commande nusbdevicestoredirect:s:* recommandée ne fonctionne pas pour les webcams USB et peut ne pas fonctionner pour certaines imprimantes USB ou périphériques multifonctions USB. Pour des raisons de conception et de sécurité, l’administrateur RemoteApp doit activer la redirection soit par le biais d’un identificateur global unique, soit par un ID d’instance de périphérique avant que vos utilisateurs puissent utiliser ces périphériques.

Bien que cet article traite de la redirection de caméra web, vous pouvez utiliser une approche similaire pour rediriger les imprimantes USB et les autres périphériques multifonctions USB qui ne sont pas redirigés par la commande **nusbdevicestoredirect:s:***.

## Options de redirection pour périphériques USB
RemoteApp Azure utilise les mécanismes très similaires à ceux qui sont disponibles pour les Services Bureau à distance pour rediriger des périphériques USB. La technologie sous-jacente vous permet de choisir la méthode de redirection convenant à un périphérique donné et d’obtenir le meilleur du haut niveau et de la redirection des périphériques USB RemoteFX grâce à la redirection de la commande **usbdevicestoredirect:s :**. Cette commande comporte quatre éléments :

| Ordre de traitement | Paramètre | Description |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | Sélectionne tous les périphériques non sélectionnés par la redirection de haut niveau. Remarque : Dès le départ, * ne fonctionne pas pour les webcams USB. |
| | {Identificateur global unique} | Sélectionnez tous les périphériques qui correspondent à la classe d’installation de périphérique spécifié. |
| | USB\\InstanceID | Sélectionne un périphérique USB spécifié pour l’ID d’instance donné. |
| 2 | -USB\\Instance ID | Supprime les paramètres de redirection du périphérique spécifié. |

## Redirection d’un périphérique USB à l’aide du GUID de la classe de périphérique
Il existe deux façons de trouver le GUID de la classe de périphérique qui peut être utilisé pour la redirection de périphérique.

La première option consiste à utiliser les [classes de configuration de périphérique définies par le système à la disposition des fournisseurs](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Sélectionnez la classe qui correspond le mieux au périphérique connecté à l’ordinateur local. Pour les appareils photo numériques, il peut s’agir de la classe de périphérique d’acquisition d’image ou de la classe de périphérique de capture vidéo. Vous devez faire des essais avec les classes de périphériques pour trouver le GUID de classe qui fonctionne avec le périphérique USB connecté localement (dans notre cas, la caméra web).

Il existe un meilleur moyen, ou une seconde possibilité de trouver le GUID de classe de périphérique spécifique :

1. Ouvrez le Gestionnaire de périphériques, localisez le périphérique qui va être redirigé, effectuez un clic droit dessus droit, puis ouvrez les propriétés. ![Ouvrez le Gestionnaire de périphériques](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Sur l’onglet **Détails**, choisissez la propriété **GUID de classe**. La valeur qui apparaît est le GUID de classe correspondant à ce type de périphérique. ![Propriétés de caméra](./media/remoteapp-usbredir/ra-classguid.png)
3. La valeur GUID de classe permet de rediriger les périphériques qui lui correspondent.

Par exemple :

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Vous pouvez combiner plusieurs redirections de périphérique dans la même applet de commande. Par exemple, pour rediriger le stockage local et une webcam USB, l’applet de commande se présente comme suit :

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Lorsque vous définissez la redirection de périphérique par GUID de classe, tous les périphériques correspondant à ce GUID de classe dans l’ensemble spécifié sont redirigés. Par exemple, s’il existe sur le réseau plusieurs ordinateurs qui utilisent les mêmes webcams USB. Vous pouvez exécuter une applet de commande unique pour rediriger toutes les webcams.

## Redirection d’un périphérique USB à l’aide de l’ID d’instance du périphérique

Si vous souhaitez un contrôle précis et contrôler la redirection par appareil, vous pouvez utiliser le paramètre de redirection **USB\\InstanceID**.

La partie la plus difficile de cette méthode consiste à trouver l’ID d’instance de périphérique USB. Vous devez avoir accès à l’ordinateur et à ce périphérique USB particulier. Exécutez ensuite les opérations qui suivent :

1. Activez la redirection de périphérique dans la Session Bureau à distance, comme décrit dans [Utilisation de mes périphériques et mes ressources dans une session Bureau à distance](http://windows.microsoft.com/fr-FR/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Ouvrez une connexion Bureau à distance, puis cliquez sur **Afficher les options**.
3. Cliquez sur **Enregistrer sous** pour enregistrer les paramètres de connexion actuels dans un fichier RDP. ![Enregistrer les paramètres dans un fichier RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Choisissez un nom de fichier et un emplacement, par exemple « MyConnection.rdp » et « Ce PC\\Documents », puis enregistrez le fichier.
5. Ouvrez le fichier MyConnection.rdp à l’aide d’un éditeur de texte et recherchez l’ID d’instance du périphérique que vous souhaitez rediriger.

À présent, utilisez l’ID d’instance dans l’applet de commande suivante :

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB<Device InstanceID value>"



### Vos commentaires nous aideront à mieux vous servir 
Saviez-vous qu’en plus de noter cet article et de rédiger des commentaires ci-dessous, vous pouviez modifier l’article lui-même ? Il manque des informations ? Des informations sont erronées ? Certains passages ne sont pas clairs ? Faites défiler l’écran vers le haut et cliquez sur **Modifier sur GitHub** pour apporter des modifications. Nous les passerons ensuite en revue, et une fois que nous les aurons confirmées, vos modifications et les améliorations seront visibles ici.

<!---HONumber=AcomDC_0817_2016-->