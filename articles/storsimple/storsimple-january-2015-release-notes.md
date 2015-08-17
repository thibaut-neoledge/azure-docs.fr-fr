<properties 
    pageTitle="Notes de publication de StorSimple série 8000 Update 0.2 - Janvier 2015"
    description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour la version de janvier 2015 de Microsoft Azure StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="06/02/2015"
    ms.author="v-sharos" />


# Notes de publication de StorSimple série 8000 Update 0.2 - Janvier 2015

## Vue d'ensemble

Les notes suivantes identifient les problèmes critiques de la version de janvier 2015 de Microsoft Azure StorSimple. Elles contiennent également une liste des mises à jour du logiciel et du microprogramme StorSimple incluses dans cette version. Il s’agit de la deuxième version postérieure à la mise à disposition générale de la version StorSimple série 8000, en juillet 2014.
  
Cette mise à jour ne modifie pas la version logicielle de l’appareil physique, qui a été mise à jour en octobre. La version est toujours 6.3.9600.17312. L’image utilisée par l’image d’appareil virtuel a changé dans cette version. Par conséquent, tous les appareils virtuels créés après le 20/01/2015 indiquent 6.3.9600.17361 comme numéro de version.

Veuillez consulter les informations suivantes, qui figurent dans les notes de publication de la mise à jour de janvier 2015.

> [AZURE.IMPORTANT]

- Cette mise à jour n’est pas disponible via Windows Update et ne peut pas être installée comme les autres mises à jour. Votre appareil ne recevra pas cette mise à jour, même si vous avez appliqué les mises à jour à l’aide du portail de gestion. Cette mise à jour s’applique uniquement aux appareils virtuels créés après le 20 janvier 2015. 
 
- La version de janvier de StorSimple ne contient aucune mise à jour pour l’appareil physique StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles à l’appareil virtuel, y compris les correctifs de sécurité récents, mais celles-ci ne modifieront pas la version de l’appareil physique StorSimple.

## Nouveautés dans la version de janvier

Cette mise à jour contient un correctif pour les volumes qui se mettent hors connexion sur l’appareil virtuel. (Voir [Problèmes résolus dans cette version](#issues-fixed-in-the-january-release).)

Cette mise à jour ne contient pas de nouvelles fonctions ou fonctionnalités.

## Problèmes résolus dans la version de janvier

Le tableau suivant décrit le problème résolu dans cette mise à jour.

|N°|Fonctionnalité|Problème|S’applique à un appareil physique|S’applique à un appareil virtuel 
|---|-------|-----|--------------------------|-------------------------
|1|Les volumes se mettent hors connexion|Lorsque des latences de cloud élevées persistent plusieurs minutes, les volumes de l’appareil virtuel StorSimple se mettent hors connexion sur les ordinateurs hôtes. Ce correctif augmente le seuil des latences de cloud, ce qui minimise les circonstances dans lesquelles les volumes se mettent hors connexion sur les ordinateurs hôtes.|Non|Oui  

## Problèmes connus dans la version de janvier

Le tableau suivant récapitule les problèmes connus de cette version.
 
|N°|Fonctionnalité|Problème|Commentaires/solution de contournement|S’applique à un appareil physique|S’applique à un appareil virtuel 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1|	Réinitialisation aux paramètres d’usine|	Dans certains cas, lorsque vous effectuez une réinitialisation aux paramètres d’usine, l’appareil StorSimple peut se bloquer et afficher le message suivant : **La réinitialisation aux paramètres d’usine est en cours (phase 8)**. Cela se produit si vous appuyez sur Ctrl+C alors que l’applet de commande est en cours.|	N’appuyez pas sur Ctrl+C après avoir lancé une réinitialisation aux paramètres d’usine. Si vous avez déjà effectué cette opération, contactez le support technique Microsoft.|Oui|Non|
|2|Disque quorum|	Dans de rares cas, si la majorité des disques du boîtier EBOD d’un appareil 8600 sont déconnectés, ce qui signifie qu’il n’y a pas de disque quorum, le pool de stockage est hors connexion. Il reste hors connexion même si les disques sont reconnectés.|Vous devez redémarrer l’appareil. Si le problème persiste, contactez le support technique Microsoft.|Oui |Non
|3|Échec des instantanés cloud|Dans de rares cas, un instantané cloud peut échouer et renvoyer l’erreur **Limite de sauvegarde maximale atteinte**. Cela se produit si vous avez plus de 255 clones en ligne sur un même appareil, provenant du volume d’origine qui a été supprimé.||Oui|Oui
|4|ID de contrôleur incorrect|Lorsqu’un contrôleur est remplacé, le contrôleur 0 peut apparaître comme contrôleur 1. Pendant le remplacement du contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme l’ID du contrôleur homologue. Dans de rares cas, ce comportement peut également se produire après un redémarrage du système.|Aucune action utilisateur n’est requise. Cette situation se résout automatiquement une fois le contrôleur remplacé.|Oui|Non 
|5|	Graphiques d’analyse de l’appareil|Dans le service StorSimple Manager, les graphiques d’analyse de l’appareil ne fonctionnent pas lorsque l’authentification de base ou NTLM est activée dans la configuration du serveur proxy pour l’appareil.|Modifiez la configuration du proxy web pour l’appareil inscrit auprès du service StorSimple Manager afin que l’authentification soit définie sur AUCUNE. Pour cela, exécutez l’applet de commande Set-HcsWebProxy de Windows PowerShell pour StorSimple.|Oui|Oui
|6|	Comptes de stockage|La suppression du compte de stockage à l’aide du service de stockage n’est pas prise en charge. En effet, cette opération donnerait lieu à une situation dans laquelle il serait impossible de récupérer les données utilisateur.|| Oui |	Oui
|7|Basculement de l’appareil|	Le basculement multiple d’un conteneur de volumes d’un même appareil source vers différents appareils cibles n’est pas pris en charge.|	Si un appareil inactif est basculé vers plusieurs appareils, les conteneurs de volumes du premier appareil basculé perdent la propriété des données. Après un basculement de ce type, les conteneurs de volumes concernés apparaissent ou se comportent différemment lorsque vous les affichez dans le portail de gestion.|Oui|Non
|8|	Installation|Lors de l’installation de l’adaptateur StorSimple pour SharePoint, vous devez fournir une adresse IP d’appareil pour que l’installation s’effectue correctement.||Oui|Non
|9|	Proxy web|Si HTTPS est défini comme protocole dans la configuration du proxy web, la communication appareil-service est altérée et l’appareil se met hors connexion. Des packages de prise en charge sont également générés, ce qui consomme de nombreuses ressources de l’appareil.|Vérifiez que le protocole défini pour l’URL du proxy web est bien HTTP. En savoir plus sur la [configuration du proxy web pour votre appareil](https://msdn.microsoft.com/library/azure/dn764937.aspx).|Oui |Non
|10|Proxy web|	Si vous configurez et activez le proxy web sur un appareil inscrit, vous devez redémarrer le contrôleur actif sur votre appareil.||	Oui |Non
|11|Latence de cloud élevée et charge de travail d’E/S élevée|Lorsque l’appareil StorSimple doit gérer à la fois des latences de cloud très élevées (de l’ordre de quelques secondes) et une charge de travail d’E/S élevée, ses volumes se détériorent et une défaillance peut se produire au niveau des E/S, avec l’erreur « appareil non prêt ».|Vous devez redémarrer les contrôleurs de l’appareil manuellement ou effectuer un basculement d’appareil pour résoudre ce problème.|Oui|Non

## Mises à jour de l’appareil physique dans la version de janvier

Cette mise à jour ne contient aucune autre modification de l’appareil StorSimple.

## Mises à jour pour le microprogramme et le contrôleur SAS (Serial Attached SCSI) dans la version de janvier

Cette version ne contient aucune mise à jour pour le microprogramme ou le contrôleur série SAS (Serial Attached SCSI). Le pilote a été mis à jour dans la version d’octobre 2014.

## Mises à jour de l’appareil virtuel dans la version de janvier

Cette version contient une image mise à jour pour l’appareil virtuel. Tous les appareils virtuels créés après le 20 janvier 2015 indiquent 6.3.9600.17361 comme numéro de version.

 

<!---HONumber=August15_HO6-->