<properties 
    pageTitle="Notes de publication de StorSimple série 8000 Release Version - Juillet 2014 | Microsoft Azure"
    description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour la version de juillet 2014 de Microsoft Azure StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/19/2015"
    ms.author="v-sharos" />

# Notes de publication de StorSimple série 8000 Release Version - Juillet 2014 

## Vue d'ensemble

Les notes suivantes identifient les problèmes critiques de la version finale (GA) pour StorSimple série 8000 de Microsoft Azure StorSimple, publiée en juillet 2014. Cette version correspond à la version logicielle n° 6.3.9600.17215.

Sauf indication contraire, ces notes s’appliquent à tous les modèles d’appareil StorSimple. Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Avant de déployer votre solution Microsoft Azure StorSimple, tenez compte des informations suivantes.

## Problèmes connus dans cette version
Le tableau suivant récapitule les problèmes connus de cette version.
 
| N° | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique à un appareil physique | S’applique à un appareil virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Réinitialisation aux paramètres d’usine | Dans certains cas, lorsque vous effectuez une réinitialisation aux paramètres d’usine, l’appareil StorSimple peut se bloquer et afficher le message suivant : **La réinitialisation aux paramètres d’usine est en cours (phase 8)**. Cela se produit si vous appuyez sur Ctrl+C alors que l’applet de commande est en cours. | N’appuyez pas sur Ctrl+C après avoir lancé une réinitialisation aux paramètres d’usine. Si vous avez déjà effectué cette opération, contactez le support technique Microsoft. | Oui | Non |
| 2 | Disque quorum | Dans de rares cas, si la majorité des disques du boîtier EBOD d’un appareil 8600 sont déconnectés, ce qui signifie qu’il n’y a pas de disque quorum, le pool de stockage est hors connexion. Il reste hors connexion même si les disques sont reconnectés. | Vous devez redémarrer l’appareil. Si le problème persiste, contactez le support technique Microsoft. | Oui | Non |
| 3 | Échec des instantanés cloud | Dans de rares cas, un instantané cloud peut échouer et renvoyer l’erreur **Limite de sauvegarde maximale atteinte**. Cela se produit si vous avez plus de 255 clones en ligne sur un même appareil, provenant du volume d’origine qui a été supprimé. | | Oui | Oui |
| 4 | ID de contrôleur incorrect | Lorsqu’un contrôleur est remplacé, le contrôleur 0 peut apparaître comme contrôleur 1. Pendant le remplacement du contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme l’ID du contrôleur homologue. Dans de rares cas, ce comportement peut également se produire après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résout automatiquement une fois le contrôleur remplacé. | Oui | Non |
| 5 | Graphiques d’analyse de l’appareil | Dans le service StorSimple Manager, les graphiques d’analyse de l’appareil ne fonctionnent pas lorsque l’authentification de base ou NTLM est activée dans la configuration du serveur proxy pour l’appareil. | Modifiez la configuration du proxy web pour l’appareil inscrit auprès du service StorSimple Manager afin que l’authentification soit définie sur AUCUNE. Pour cela, exécutez l’applet de commande Set-HcsWebProxy de Windows PowerShell pour StorSimple. | Oui | Oui |
| 6 | Comptes de stockage | La suppression du compte de stockage à l’aide du service de stockage n’est pas prise en charge. En effet, cette opération donnerait lieu à une situation dans laquelle il serait impossible de récupérer les données utilisateur. | | Oui | Oui |
| 7 | Restauration automatique | La restauration automatique dans les 24 heures qui suivent une récupération d’urgence n’est pas prise en charge. | | Oui | Non |
| 8 | Basculement de l’appareil | Le basculement multiple d’un conteneur de volumes d’un même appareil source vers différents appareils cibles n’est pas pris en charge. Si un appareil inactif est basculé vers plusieurs appareils, les conteneurs de volumes du premier appareil basculé perdent la propriété des données. Après un basculement de ce type, les conteneurs de volumes concernés apparaissent ou se comportent différemment lorsque vous les affichez dans le portail de gestion. | | Oui | Non |
| 9 | Installation | Lors de l’installation de l’adaptateur StorSimple pour SharePoint, vous devez fournir une adresse IP d’appareil pour que l’installation s’effectue correctement. | | Oui | Non |
| 10 | Interfaces réseau | Les interfaces réseau DATA 2 et DATA 3 ont été échangées dans le logiciel. | Contactez le support technique Microsoft si vous devez configurer ces interfaces. | Oui | Non |


 

<!---HONumber=August15_HO8-->