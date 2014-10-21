<properties title="Integrate your Azure Website with an Azure Virtual Network" pageTitle="Integrate Azure Website with Azure VNet" description="Shows you how to connect an Azure Website to a new or existing Azure virtual network" metaKeywords="" services="web-sites,virtual-network" solutions="web,integration,infrastructure" documentationCenter="" authors="cephalin" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="cephalin"></tags>

# Intégrer votre site Web Azure à un réseau virtuel Azure

Ce document présente la fonctionnalité préliminaire d'intégration de réseau virtuel et explique comment la configurer avec votre site web Azure. Si vous ne connaissez pas les réseaux virtuels Azure, il s'agit d'une fonctionnalité qui vous permettra de créer des solutions hybrides avec vos ressources Azure et sur site.

Grâce à cette intégration, votre site Web a accès aux ressources se trouvant dans votre réseau virtuel sans que celui-ci puisse accéder à votre site Web. Cela peut notamment être le cas si votre site Web doit accéder à une base de données ou à des services Web exécutés sur des machines virtuelles dans votre VNET, voire dans votre propre centre de données. Cette fonctionnalité ne vous permet toutefois pas de monter un lecteur. De même, elle ne prend pas en charge actuellement l'intégration aux systèmes d'authentification dans votre VNET. Cette fonctionnalité est à un stade préliminaire et continuera d'être améliorée avant d'être mise à la disposition du grand public.

Pour plus de détails sur les réseaux virtuels Azure, consultez la rubrique « Vue d'ensemble des réseaux virtuels » relative aux cas d'utilisation et avantages d'un réseau virtuel Azure.

## Mise en route

Voici quelques informations à garder à l'esprit avant de connecter votre site à un réseau virtuel.

1.  Pour pouvoir être connectés à un réseau virtuel, les sites doivent être exécutés dans un plan d'hébergement Web dont le niveau de tarification est « Standard ». Les sites gratuits, partagés et basiques ne peuvent pas être connectés à un réseau virtuel.
2.  Si votre réseau virtuel cible existe déjà, il doit prendre en charge la connexion de point à site avec une passerelle de routage dynamique avant de pouvoir être connecté à un site Web. Vous ne pouvez pas activer le VPN de point à site si votre passerelle est configurée pour le routage statique.
3.  Votre plan d'hébergement Web peut comporter un maximum de 5 réseaux configurés. Un site Web ne peut être connecté qu'à un seul réseau à la fois. Ces 5 réseaux peuvent être utilisés par un nombre quelconque de sites Web faisant partie du même plan d'hébergement Web.

Vous pouvez connecter un site Web à un réseau virtuel nouveau ou existant. Si vous créez un nouveau réseau, une passerelle est alors préconfigurée pour vous. Notez que la création et la configuration d'un nouveau réseau virtuel prendra quelques minutes.

Si votre site Web n'est pas de niveau Standard, l'interface utilisateur vous en informe et vous donne accès aux différents niveaux de tarification. Cliquez sur le niveau de tarification approprié.

![][]

## Fonctionnement du système

Pour connecter votre site web Azure à votre VNET, cette fonctionnalité utilise la technologie de réseau VPN de point à site. L'architecture système des sites Web Azure étant mutualisée, il n'est pas possible de déployer les sites Web directement dans un VNET, comme c'est le cas avec les machines virtuelles. Grâce à la technologie de point à site, nous limitons l'accès réseau à la seule machine virtuelle hébergeant le site Web. L'accès au réseau est, par ailleurs, limité à ces hébergements afin que vos sites Web aient uniquement accès aux réseaux pour lesquels ils ont été configurés.

Le travail nécessaire pour sécuriser vos réseaux uniquement pour les sites Web qui nécessitent un accès empêche la création de connexions SMB. Bien que vous puissiez accéder à des ressources à distance, cela n'inclut pas la possibilité de monter un lecteur distant.

![][1]

Si vous n'avez pas configuré un serveur DNS avec votre réseau virtuel, vous devrez utiliser des adresses IP. Assurez-vous d'exposer les ports des points de terminaison souhaités via votre pare-feu. Pour tester la connexion, la seule méthode disponible à l'heure actuelle consiste à appeler le point de terminaison souhaité via un site Web ou une tâche Web. Les outils tels que la commande ping ou nslookup ne fonctionnent pas sur la console Kudu actuellement. Ce point sera amélioré prochainement.

## Connexion à un réseau préexistant

Pour connecter un site à un réseau virtuel, accédez à la lame de votre site Web, cliquez sur la vignette Réseau virtuel dans la section Mise en réseau, et sélectionnez l'un de vos réseaux préexistants.

![][2]

Le système crée ensuite un certificat pour authentifier votre VNET s'il s'agit du premier site Web de votre abonnement à établir une connexion à ce réseau. Pour voir le certificat, accédez au portail actuel, puis à Réseaux virtuels, sélectionnez le réseau, puis l'onglet Certificats.

Dans l'illustration ci-dessus, le réseau nommé cantConnectVnet est grisé et ne peut pas être sélectionné. Il ne peut y avoir que deux raisons à cela. Soit le VPN de point à site n'est pas activé sur votre réseau, soit vous n'avez pas déployé une passerelle de routage dynamique dans votre VNET. Lorsque ces deux conditions seront remplies, vous pourrez sélectionner le VNET pour l'intégration avec votre site Web.

## Création et connexion à un nouveau VNET

Outre la connexion à un VNET préexistant, vous pouvez également créer un nouveau VNET à partir de l'interface utilisateur du site Web et vous y connecter automatiquement. Pour cela, suivez le même chemin pour atteindre l'interface utilisateur du réseau virtuel et sélectionnez Créer un nouveau réseau virtuel. La fenêtre qui s'ouvre vous permet de nommer le réseau, de spécifier l'espace d'adresse et de définir les adresses des serveurs DNS que le réseau virtuel doit utiliser.

![][3]

La création d'un réseau virtuel avec des passerelles configurées peut prendre jusqu'à 30 minutes. Pendant ce temps, l'interface utilisateur vous informe que le processus est en cours et affiche le message suivant.

![][4]

Lorsque le réseau est associé au site Web, celui-ci peut accéder aux ressources du VNET via TCP ou UDP. Si vous voulez accéder à des ressources de votre système sur site disponibles sur votre VNET via un VPN de site à site, vous devez ajouter des itinéraires à votre réseau d'entreprise pour autoriser le trafic depuis votre réseau vers les adresses de point à site configurées dans votre VNET.

Une fois l'intégration terminée, le portail affichera des informations de base sur la connexion et indiquera un moyen de déconnecter le site Web du réseau, mais aussi de synchroniser les certificats utilisés pour authentifier la connexion. La synchronisation peut être nécessaire si un certificat a expiré ou a été révoqué.

![][5]

Gestion de la connexion du réseau virtuel
Vous pouvez afficher la liste de tous les réseaux virtuels actuellement associés à des sites dans un plan d'hébergement Web en visitant la lame de ce dernier. Un plan d'hébergement Web Standard peut être associé à un maximum de 5 réseaux.

Si le plan d'hébergement Web doit être mis à l'échelle vers un plan inférieur tel que Gratuit, Partagé ou Basique, les connexions au réseau virtuel utilisées par les sites Web dans le cadre de ce plan sont désactivées. Si le plan doit ensuite être remis à l'échelle vers un plan Standard, ces connexions réseau sont rétablies.

Dans Azure, il n'est actuellement pas possible de déplacer une machine virtuelle existante vers un réseau virtuel. La machine virtuelle doit être déployée dans le réseau virtuel au moment de sa création.

## Accès aux ressources sur site

Si vous travaillez avec un VNET configuré avec un VPN de site à site, une étape supplémentaire est nécessaire pour permettre l'accès à vos ressources sur site depuis le site Web Azure. Des itinéraires doivent être ajoutés à votre réseau sur site pour autoriser le trafic depuis votre réseau vers les adresses de point à site configurées dans votre VNET. Pour voir la plage d'adresses IP pour votre connexion de point à site, accédez à la zone Réseau dans le portail actuel, comme illustré.

![][6]

## Certificats

Lors de la connexion sécurisée avec votre VNET, un échange de certificats a lieu. Vous pouvez voir l'empreinte numérique du certificat public que les sites Web Azure génèrent pour le portail réseau actuel, comme illustré ci-dessous.

![][7]

Si les certificats se désynchronisent pour une raison quelconque, notamment en cas de suppression accidentelle d'un certificat sur le portail réseau, la connexion est interrompue. Pour résoudre ce problème, l'interface utilisateur du réseau virtuel de vos sites Web propose une action de synchronisation qui rétablit la connexion.

Cette action doit également être utilisée si vous ajoutez un DNS à votre réseau virtuel ou si vous ajoutez un VPN de site à site à votre réseau.

![][8]

## Comparaison et différences avec les connexions hybrides

Les sites Web Azure proposent une autre fonctionnalité similaire à l'intégration de réseau virtuel, appelée Connexions hybrides. Bien que, dans certains cas, elles puissent toutes deux être utilisées, l'une ne remplace pas l'autre. La fonctionnalité Connexions hybrides vous permet d'établir des connexions à plusieurs points de terminaison d'application dans un groupe de réseaux. La fonctionnalité Réseaux virtuels connecte votre site Web à un VNET qui peut être connecté à votre réseau sur site. Ceci fonctionne parfaitement pour autant que vos ressources se trouvent toutes dans la portée de ce réseau.

Une autre différence est que vous devez installer un agent de relais pour que la fonctionnalité Connexions hybrides fonctionne. Cet agent doit s'exécuter sur une instance Windows Server. Avec la fonctionnalité Réseau virtuel, en revanche, vous ne devez rien installer ; elle autorise l'accès aux ressources à distance indépendamment des systèmes d'exploitation d'hébergement.

À l'heure actuelle, il existe également des différences entre les niveaux de tarification pour ces deux fonctionnalités. En effet, aux niveaux les moins chers, la fonctionnalité Connexions hybrides est extrêmement utile pour les scénarios de développement et de test, et elle donne uniquement accès à un nombre limité de terminaux. La fonction Réseau virtuel, quant à elle, vous donne accès à tout ce qui se trouve dans le VNET ou qui y est connecté.

  []: ./media/web-sites-integrate-with-vnet/upgrade-to-standard.png
  [1]: ./media/web-sites-integrate-with-vnet/how-it-works.png
  [2]: ./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png
  [3]: ./media/web-sites-integrate-with-vnet/create-new-vnet.png
  [4]: ./media/web-sites-integrate-with-vnet/new-vnet-progress.png
  [5]: ./media/web-sites-integrate-with-vnet/vnet-status-portal.png
  [6]: ./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png
  [7]: ./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png
  [8]: ./media/web-sites-integrate-with-vnet/vnet-sync-connection.png
