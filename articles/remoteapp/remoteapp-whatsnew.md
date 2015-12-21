
<properties
    pageTitle="Nouveautés d’Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur les modifications et les améliorations apportées à Azure RemoteApp"
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
    ms.date="10/23/2015"
    ms.author="elizapo" />



# Nouveautés d’Azure RemoteApp

Un des avantages d’Azure RemoteApp est que nous nous efforçons de l’améliorer continuellement. Nous vous ferons part de toutes les améliorations sur cette page.

## Septembre 2015
- Ajout d’Infopath au modèle et à l’image de la galerie Microsoft Office 365. Si vous souhaitez partager Infopath, veillez à mettre à jour vos collections avec l’image la plus récente.
- Mises à jour des clients :
	- Le client Windows a été mis à jour pour permettre aux utilisateurs de partager des commentaires, notamment sur les problèmes de connexion.
	- Le client iOS a été mis à jour pour corriger l’envoi de messages par erreur et pour résoudre un problème d’expiration précoce des informations d’identification.
- Nous travaillons à tester la prise en charge d’Office 2016. Ces tests terminés, cherchez des images mises à jour.
- Nous avons publié un nouvel article sur les [différences entre les collections cloud et hybrides](remoteapp-collections.md). Il vous aidera à choisir le type de collection qui convient le mieux à vos applications (cloud uniquement, cloud et réseau virtuel ou hybride).
- Vous voudriez partager QuickBooks à l’aide d’Azure RemoteApp mais vous ne savez pas comment faire ? Consultez le [nouvel article d’Éric](remoteapp-quickbooks.md) qui vous explique la marche à suivre.

## Août 2015
De grands changements se sont produits en août. En voici les grandes lignes :

- Vous pouvez désormais utiliser un réseau virtuel Azure avec une collection cloud ! Consultez les [instructions de création cloud](remoteapp-create-cloud-deployment.md) pour connaître la marche à suivre.
- Il est désormais possible d’ajouter des applications au menu **Démarrer** pour le client Windows RemoteApp. Elles apparaissent ainsi dans la liste des applications et vous pouvez les épingler au menu **Démarrer** de Windows.
- Nous avons ajouté une nouvelle image à la galerie de machines virtuelles Azure : Hôte de session Bureau à distance Windows Server avec Microsoft Office 365 ProPlus.
- Nous avons corrigé le client Mac pour que les applications comportant des fenêtres modales arrêtent de se bloquer.
- Nous avons créé un document sur la manière dont vous pouvez utiliser votre [abonnement Office 365 ProPlus](remoteapp-officesubscription.md) avec Azure RemoteApp.
- Nous avons décrit la manière dont vous pouvez [sécuriser les applications et les données](remoteapp-secure.md) dans votre collection Azure RemoteApp.

## Juillet 2015

Juillet pose les bases des changements qui auron lieu en août, donc il n'y a pas grand chose à dire pour le moment, si ce n'est concernant les mises à jour de la documentation. Voici les modifications les plus récentes :

- Un onglet **Support** a été ajouté au portail pour vous permettre d’accéder plus facilement aux ressources de support, comme les forums.
- Les informations de dépannage pour la création d'une collection hybride ont été retravaillées. Découvrez [les derniers et meilleurs](remoteapp-hybridtrouble.md) conseils de dépannage, par exemple comment identifier les ports à configurer pour votre réseau virtuel.
- La création et l’enregistrement des [données utilisateur](remoteapp-upd.md) dans Azure RemoteApp ont été documentés.
- Le [verrouillage des applications](remoteapp-secure.md) a été documenté.
- Les [applets de commande Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx) ont été publiées.
- Et enfin, nous avons initié une conversation avec certains utilisateurs Azure RemoteApp concernant la terminologie. Prenez notes des modifications apportées à la façon dont nous faisons référence aux différentes options de collection.

## Juin 2015

Que de changements ! L’équipe a été bien occupée en juin :

- La [page d’accueil](https://www.remoteapp.windowsazure.com/) Azure RemoteApp a été repensée, constatez-le vous-même !
- Le logiciel a été mis à jour dans toutes les images disponibles dans le cadre de votre abonnement.
- Des améliorations ont été apportées aux collections hybrides, dont la compatibilité avec le tunneling forcé et la vérification de la taille du sous-réseau IP avant d’essayer de créer la collection.
- Nous avons découvert que le caractère générique * ne fonctionne pas pour les webcams. À la place, vous devez spécifier l’ID d’instance ou le GUID. Nous mettrons à jour les informations de redirection pour refléter ces modifications.
- Vous pouvez désormais ajouter un logiciel antivirus personnalisé à votre image lorsque vous créez une image de modèle à partir de la galerie Azure.

Et d’autres modifications seront ajoutées en juillet. Nous reviendrons donc bientôt avec une autre mise à jour.

## Mai 2015

Il y a eu un certain nombre d’ajouts et quelques mois depuis la création de cette rubrique. Cette liste triche donc un peu et couvre la période allant de début mars à fin mai. Découvrez ces nouvelles fonctionnalités :

- Tout automatiser. Azure RemoteApp dispose désormais d’[applets de commande dans le module Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
- [Création d’une image Azure RemoteApp à partir d’une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Accélère le téléchargement de votre image personnalisée dans Azure.
- Utilisez un réseau virtuel Azure au lieu d’un réseau virtuel RemoteApp pour connecter les ressources de votre réseau d’entreprise à Azure. Nous avons mis à jour les [instructions sur les collections hybrides](remoteapp-create-hybrid-deployment.md) pour vous aider à créer un réseau virtuel Azure (Étape 1).
- Et toujours au sujet des réseaux virtuels, découvrez les [nouveaux conseils](remoteapp-vnetsizing.md) sur les limites de taille et les limitations des réseaux virtuels.
- Et en parlant de limites : quelles sont ces [limites de service et leurs valeurs par défaut](remoteapp-servicelimits.md) ?

Vous voulez en savoir plus sur Azure RemoteApp ? L’équipe RemoteApp était présente en force à la conférence Ignite il y a quelques semaines. Regardez la vidéo d’Eric, [Bases de la gestion et de l’administration de Microsoft Azure RemoteApp](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Vous avez besoin de voir Azure RemoteApp en action ? Découvrez le didacticiel [Exécutez n’importe quelle application sur n’importe quel appareil ](remoteapp-anyapp.md). Il vous montre comment partager Access avec vos utilisateurs, en partageant même les fichiers de base de données. Nous avons également un didacticiel sur comment [exécuter Office 365](remoteapp-tutorial-o365anywhere.md) de la même manière sur n’importe quel appareil.

Merci de nous rester fidèle. Nous serons de retour le mois prochain avec d’autres mises à jour.


### Vos commentaires nous aideront à mieux vous servir
Saviez-vous qu’en plus de noter cet article et de rédiger des commentaires ci-dessous, vous pouviez modifier l’article lui-même ? Il manque des informations ? Des informations sont erronées ? Certains passages ne sont pas clairs ? Faites défiler l’écran vers le haut et cliquez sur **Modifier sur GitHub** pour apporter des modifications. Nous les passerons ensuite en revue, et une fois que nous les aurons confirmées, vos modifications et les améliorations seront visibles ici.

<!---HONumber=AcomDC_1210_2015-->