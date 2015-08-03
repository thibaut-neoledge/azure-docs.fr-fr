<properties 
	pageTitle="Intégrer une application web à un réseau virtuel Azure" 
	description="Explique comment connecter une application web Azure d’Azure App Service à un réseau virtuel Azure nouveau ou existant" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Intégrer une application web à un réseau virtuel Azure #
Ce document décrit la fonctionnalité préliminaire d’intégration au réseau virtuel et explique comment la configurer avec la fonctionnalité Web Apps d’[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Si vous ne connaissez pas les réseaux virtuels Azure, il s'agit d'une fonctionnalité qui vous permettra de créer des solutions hybrides avec vos ressources Azure et sur site.

Grâce à cette intégration, votre application web a accès aux ressources se trouvant dans votre réseau virtuel sans que celui-ci puisse accéder à votre application web. Cela peut notamment être le cas si votre application web doit accéder à une base de données ou à des services web exécutés sur des machines virtuelles de votre réseau virtuel, voire dans votre propre centre de données. Cette fonctionnalité ne vous permet toutefois pas de monter un lecteur. De même, elle ne prend pas en charge actuellement l’intégration aux systèmes d’authentification dans votre réseau virtuel. Cette fonctionnalité est à un stade préliminaire et continuera d'être améliorée avant d'être mise à la disposition du grand public.

Pour plus de détails sur les réseaux virtuels Azure, consultez la rubrique « Vue d'ensemble des réseaux virtuels » relative aux cas d'utilisation et avantages d'un réseau virtuel Azure.

## Prise en main ##
Voici quelques informations à garder à l’esprit avant de connecter votre application web à un réseau virtuel.

1.	Les applications web ne peuvent être connectées à un réseau virtuel que si elles s’exécutent dans un plan App Service présentant le niveau de tarification « Standard ». Les applications web des plans Gratuit, Partagé et De base ne peuvent pas être connectées à un réseau virtuel.
2.	Si votre réseau virtuel cible existe déjà, il doit prendre en charge la connexion de point à site avec une passerelle de routage dynamique avant de pouvoir être connecté à une application web. Vous ne pouvez pas activer le réseau privé virtuel (VPN) de point à site si votre passerelle est configurée avec un routage statique.
3.	Votre plan App Service peut comporter un maximum de 5 réseaux configurés. Une application web ne peut être connectée qu’à un seul réseau à la fois. Ces 5 réseaux sont utilisables par un nombre quelconque d’applications web appartenant au même plan App Service.  

Vous pouvez connecter un site Web à un réseau virtuel nouveau ou existant. Si vous créez un nouveau réseau, une passerelle est alors préconfigurée pour vous. Notez que la création et la configuration d'un nouveau réseau virtuel prendra quelques minutes.

Si votre application web ne correspond pas à un plan App Service Standard, l’interface utilisateur vous en informe et vous donne accès aux différents niveaux de tarification pour vous permettre de sélectionner le niveau approprié.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png)

## Fonctionnement du système ##
Pour connecter votre application web à votre réseau virtuel, cette fonctionnalité utilise la technologie de réseau VPN de point à site. La fonctionnalité Web Apps d’Azure App Service présente une architecture système mutualisée empêchant qu’une application web soit directement approvisionnée dans un réseau virtuel comme dans le cas des machines virtuelles. Grâce à la technologie de point à site, nous limitons l’accès réseau à la seule machine virtuelle hébergeant l’application web. Par ailleurs, l’accès au réseau est restreint à ces hôtes d’application web afin que vos applications web aient uniquement accès aux réseaux pour lesquels elles ont été configurées.

Le travail nécessaire pour sécuriser vos réseaux uniquement pour les applications web qui nécessitent un accès empêche la création de connexions SMB. Bien que vous puissiez accéder à des ressources à distance, cela n'inclut pas la possibilité de monter un lecteur distant.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
Si vous n'avez pas configuré un serveur DNS avec votre réseau virtuel, vous devrez utiliser des adresses IP. Assurez-vous d'exposer les ports des points de terminaison souhaités via votre pare-feu. Pour tester la connexion, la seule méthode disponible à l’heure actuelle consiste à appeler le point de terminaison souhaité via une application web ou une tâche web. Les outils tels que la commande ping ou nslookup ne fonctionnent pas sur la console Kudu actuellement. Ce point sera amélioré prochainement.

## Connexion à un réseau préexistant ##
Pour connecter une application web à un réseau virtuel, accédez au panneau de votre application web, cliquez sur la vignette Réseau virtuel dans la section Mise en réseau, puis sélectionnez l’un de vos réseaux préexistants.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
Le système crée ensuite un certificat pour l’authentification auprès de votre réseau virtuel s’il s’agit de la première application web de votre abonnement à établir une connexion à ce réseau. Pour voir le certificat, accédez au [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), cliquez sur Virtual Networks, sélectionnez le réseau, puis cliquez sur l’onglet Certificats.

Dans l'illustration ci-dessus, le réseau nommé cantConnectVnet est grisé et ne peut pas être sélectionné. Il ne peut y avoir que deux raisons à cela. Soit le VPN de point à site n’est pas activé sur votre réseau, soit vous n’avez pas approvisionné une passerelle de routage dynamique dans votre réseau virtuel. Lorsque ces deux conditions seront remplies, vous pourrez sélectionner le réseau virtuel pour l’intégration à votre application web.

## Créer un réseau virtuel et s’y connecter ##
Outre la connexion à un réseau virtuel pré-existant, vous pouvez également créer un réseau virtuel à partir de l’interface utilisateur du portail Azure et vous connecter automatiquement à ce dernier. Pour cela, suivez le même chemin pour atteindre l'interface utilisateur du réseau virtuel et sélectionnez Créer un nouveau réseau virtuel. La fenêtre qui s'ouvre vous permet de nommer le réseau, de spécifier l'espace d'adresse et de définir les adresses des serveurs DNS que le réseau virtuel doit utiliser.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
La création d'un réseau virtuel avec des passerelles configurées peut prendre jusqu'à 30 minutes. Pendant ce temps, l'interface utilisateur vous informe que le processus est en cours et affiche le message suivant.

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Une fois que le réseau a été joint à l’application web, cette dernière peut accéder aux ressources de ce réseau virtuel via TCP ou UDP. Si vous voulez accéder à des ressources de votre système local disponibles sur votre réseau virtuel via un VPN de site à site, vous devez ajouter des itinéraires à votre réseau d’entreprise pour autoriser le trafic depuis votre réseau vers les adresses de point à site configurées dans votre réseau virtuel.

Une fois l’intégration terminée, le portail Azure affiche des informations de base sur la connexion et indique les procédures à suivre pour déconnecter l’application web du réseau et pour synchroniser les certificats utilisés pour authentifier la connexion. La synchronisation peut être nécessaire si un certificat a expiré ou a été révoqué.

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

##Gestion de la connexion du réseau virtuel##
Vous pouvez voir la liste de tous les réseaux virtuels actuellement associés à des applications web dans un Plan App Service en consultant le panneau de ce dernier. Un plan App Service standard peut être associé à un maximum de 5 réseaux.

S’il est nécessaire de faire évoluer le plan App Service vers un plan inférieur tel que Gratuit, Partagé ou De base, les connexions au réseau virtuel utilisées par les applications web dans le cadre de ce plan sont désactivées. Si le plan doit ensuite être remis à l'échelle vers un plan Standard, ces connexions réseau sont rétablies.

Dans Azure, il n'est actuellement pas possible de déplacer une machine virtuelle existante vers un réseau virtuel. La machine virtuelle doit être déployée dans le réseau virtuel au moment de sa création.

## Accès aux ressources sur site ##
Si vous travaillez avec un réseau virtuel configuré avec un VPN de site à site, une étape supplémentaire est nécessaire pour permettre l’accès à vos ressources locales depuis votre application web. Des itinéraires doivent être ajoutés à votre réseau sur site pour autoriser le trafic depuis votre réseau vers les adresses de point à site configurées dans votre réseau virtuel. Pour voir la plage d’adresses IP de votre connexion de point à site, accédez à la zone Réseau du portail Azure, comme illustré ici.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Certificats ##
L’établissement d’une connexion sécurisée avec votre réseau virtuel donne lieu à un échange de certificats. Vous pouvez voir l’empreinte numérique du certificat public que Web Apps génère à partir du portail Réseau actuel, comme illustré ci-dessous.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Si les certificats se désynchronisent pour une raison quelconque, notamment en cas de suppression accidentelle d’un certificat du portail Réseau, la connexion est interrompue. Pour résoudre ce problème, l’interface utilisateur du réseau virtuel de votre application web propose une action de synchronisation qui rétablit la connexion.

Cette action doit également être utilisée si vous ajoutez un DNS à votre réseau virtuel ou si vous ajoutez un VPN de site à site à votre réseau.

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Comparaison et différences avec les connexions hybrides ##
Web Apps propose une autre fonctionnalité semblable à l’intégration au réseau virtuel, appelée Connexions hybrides. Bien que, dans certains cas, elles puissent toutes deux être utilisées, l'une ne remplace pas l'autre. La fonctionnalité Connexions hybrides vous permet d'établir des connexions à plusieurs points de terminaison d'application dans un groupe de réseaux. La fonctionnalité Réseaux virtuels connecte votre application web à un réseau virtuel qui peut être connecté à votre réseau sur site. Ceci fonctionne parfaitement pour autant que vos ressources se trouvent toutes dans la portée de ce réseau.

Une autre différence est que vous devez installer un agent de relais pour que la fonctionnalité Connexions hybrides fonctionne. Cet agent doit s'exécuter sur une instance Windows Server. Avec la fonctionnalité Réseau virtuel, en revanche, vous ne devez rien installer ; elle autorise l'accès aux ressources à distance indépendamment des systèmes d'exploitation d'hébergement.

À l'heure actuelle, il existe également des différences entre les niveaux de tarification pour ces deux fonctionnalités. En effet, aux niveaux les moins chers, la fonctionnalité Connexions hybrides est extrêmement utile pour les scénarios de développement et de test, et elle donne uniquement accès à un nombre limité de terminaux. La fonction Réseau virtuel, quant à elle, vous donne accès à tout ce qui se trouve dans le VNET ou qui y est connecté.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=July15_HO4-->