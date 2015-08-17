<properties
   pageTitle="Configuration d’une connexion VPN de point à site à un réseau virtuel Azure | Microsoft Azure"
   description="Connectez-vous en tout sécurité à votre réseau virtuel Azure en créant une connexion VPN de point à site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2015"
   ms.author="cherylmc"/>

# Configuration d’une connexion VPN de point à site à un réseau virtuel

La configuration d’une connexion de point à site s’effectue en plusieurs étapes, mais constitue un excellent moyen de disposer d’une connexion sécurisée entre votre ordinateur et votre réseau virtuel sans nécessiter l’acquisition et la configuration d’un périphérique VPN. La configuration d’un VPN de point à site comprend trois parties principales : le réseau virtuel et la passerelle VPN, les certificats utilisés pour l’authentification, ainsi que le client VPN utilisé pour la connexion au réseau virtuel. L’ordre dans lequel vous configurez chacun de ces éléments étant important, n’ignorez et ne devancez aucune étape.

1. [Créer un réseau virtuel et une passerelle VPN](#create-a-virtual-network-and-a-vpn-gateway)
2. [Créer vos certificats](#create-your-certificates)
3. [Configurer votre client VPN](#configure-your-vpn-client)

## Créer un réseau virtuel et une passerelle VPN

Une connexion de point à site nécessite un réseau virtuel avec une passerelle de routage dynamique. Les étapes ci-dessous vous guideront tout au long de la création de ces deux éléments.

### Créez un réseau virtuel

1. Connectez-vous au **portail de gestion**.
1. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.
1. Sur la page **Détails du réseau virtuel**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.
	- **Nom** : nommez votre réseau virtuel. Par exemple, attribuez-lui le nom « VNetEast ». Il s’agit du nom auquel vous ferez référence lors du déploiement des machines virtuelles et des instances PaaS (platform as a service) sur ce réseau virtuel.
	- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans la région Est des États-Unis, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.
1. Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.
	- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans la liste déroulante. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Si vous souhaitez utiliser le service de résolution de noms Azure par défaut, laissez cette section vide.
	- **Configuration VPN de point à site** : cochez cette case.
1. Dans la page **Connectivité de point à site**, spécifiez la plage d’adresses IP qui déterminera l’adresse IP de vos clients VPN au moment de la connexion. Il existe quelques règles concernant les plages d’adresses que vous pouvez spécifier. Vous devez impérativement vérifier que la plage que vous spécifiez ne chevauche aucune des plages situées sur votre réseau local.
1. Entrez les informations ci-dessous, puis cliquez sur la flèche Suivant.
 - **Espace d’adressage** : incluez l’adresse IP de départ et le CIDR (nombre d’adresses).
 - **Ajouter un espace d’adressage** : cette opération n’est requise que si la conception de votre réseau l’exige.
1. Dans la page **Espaces d’adresses du réseau virtuel**, indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques qui seront affectées aux machines virtuelles et aux autres instances de rôle que vous déployez dans ce réseau virtuel. Il est particulièrement important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Pour ce faire, vous devez contacter votre administrateur réseau, qui peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.
1. Entrez les informations ci-après, puis cliquez sur la coche pour commencer à créer votre réseau virtuel.
 - **Espace d’adressage** : ajoutez la plage d’adresses IP internes que vous voulez utiliser pour ce réseau virtuel, notamment l’adresse IP de départ et le nombre d’adresses. Il est important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Pour ce faire, vous devez contacter votre administrateur réseau, qui peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.
 - **Ajouter un sous-réseau** : aucun sous-réseau supplémentaire n’est requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui disposeront d’adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
 - **Ajouter un sous-réseau de passerelle** : le sous-réseau de passerelle est requis pour un VPN de point à site. Cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel.
1. Après la création de votre réseau virtuel, le statut **Créé** apparaît sous **Statut** sur la page des réseaux dans le portail de gestion. Une fois votre réseau virtuel créé, vous pouvez procéder à la création de votre passerelle de routage dynamique.

### Créer une passerelle de routage dynamique

1. Dans le **Portail de gestion**, au niveau de la page **Réseaux**, cliquez sur le réseau virtuel que vous venez de créer, puis accédez à la page **Tableau de bord**.
1. Au bas de la page Tableau de bord, cliquez sur **Créer une passerelle**. Vous voyez apparaître le message suivant : **Souhaitez-vous créer une passerelle pour réseau virtuel « votre\_réseau » ?**. Cliquez sur **Oui** pour initialiser la création de la passerelle. Cette opération peut prendre environ 15 minutes.

## Créer vos certificats

Les certificats sont utilisés pour authentifier les clients VPN relatifs aux VPN de point à site. Cette procédure comporte plusieurs étapes. Exécutez chacune de ces étapes dans l’ordre en utilisant les liens ci-dessous.

1. [Générer un certificat racine auto-signé](#generate-a-self-signed-root-certificate) : seuls les certificats racines auto-signés sont pris en charge pour l’instant.
2. [Charger le fichier de certificat racine dans le Portail de gestion](#upload-the-root-certificate-file-to-the-management-portal)
3. [Générer un certificat client](#generate-a-client-certificate)
4. [Exporter et installer le certificat client](#export-and-install-the-client-certificate)

### Générer un certificat racine auto-signé

1. L’une des solutions pour créer un certificat X.509 consiste à utiliser l’outil de création de certificats (makecert.exe). Pour utiliser makecert, téléchargez et installez [Microsoft Visual Studio Express 2013 pour Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), disponible gratuitement.
2. Accédez au dossier **Visual Studio Tools** et lancez l’invite de commandes en tant qu’administrateur.
3. La commande figurant dans l’exemple ci-dessous crée et installe un certificat racine dans le magasin de certificats Personnel de votre ordinateur, et crée également un fichier *.cer* correspondant que vous chargerez par la suite dans le Portail de gestion.
4. Accédez au répertoire dans lequel vous souhaitez stocker le fichier .cer, puis exécutez la commande ci-après, où *RootCertificateName* correspond au nom à utiliser pour le certificat. L’exécution de l’exemple ci-après sans aucune modification entraînera la création d’un certificat racine et du fichier *RootCertificateName.cer* correspondant.

>[AZURE.NOTE]Comme vous avez créé un certificat racine permettant de générer des certificats clients, il peut être utile d'exporter ce certificat avec sa clé privée et de l'enregistrer à un emplacement sûr à partir duquel il pourra être récupéré.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Charger le fichier de certificat racine dans le Portail de gestion

1. Lorsque vous avez généré un certificat racine auto-signé dans la procédure précédente, vous avez également créé un fichier *.cer*. Vous allez à présent charger ce fichier dans le Portail de gestion. Notez que le fichier .cer ne contient pas la clé privée du certificat racine.
1. Dans le Portail de gestion, au niveau de la page **Certificats** de votre réseau virtuel, cliquez sur **Télécharger un certificat racine**.
1. Dans la page **Télécharger un certificat**, recherchez le certificat racine .cer, puis cliquez sur la coche correspondante.

### Générer un certificat client

1. Sur l’ordinateur que vous avez utilisé pour créer le certificat racine auto-signé, ouvrez une fenêtre Invite de commandes de Visual Studio en tant qu’administrateur.
2. Accédez à l’emplacement où vous souhaitez enregistrer le client fichier de certificat client. *RootCertificateName* fait référence au certificat racine auto-signé que vous avez généré. Si vous exécutez l’exemple ci-dessous (en remplaçant « RootCertificateName » par le nom de votre certificat racine), cette opération entraînera la création d’un certificat client nommé « ClientCertificateName » dans votre magasin de certificats Personnel.
3. Tapez la commande suivante :

    makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Tous les certificats sont stockés dans le magasin de certificats Personnel de votre ordinateur. Utilisez *certmgr* pour vérifier ce point. Vous pouvez générer autant de certificats clients que nécessaire d’après cette procédure. Nous vous recommandons de créer un certificat client unique pour chaque ordinateur que vous souhaitez connecter au réseau virtuel.

### Exporter et installer le certificat client

1. Vous devez installer un certificat client sur chaque ordinateur que vous voulez connecter au réseau virtuel. Il est donc probable que vous créerez plusieurs certificats clients que vous devrez ensuite exporter. Pour exporter les certificats clients, utilisez *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**.
2. Exportez le *certificat client* avec la clé privée. Il s’agit d’un fichier *.pfx*. Prenez soin d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.
3. Copiez le fichier *.pfx* sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* pour l’installer. Entrez le mot de passe lorsque vous y êtes invité. Ne modifiez pas l’emplacement d’installation.

## Configurer votre client VPN

Pour la connexion au réseau virtuel, vous devrez également configurer votre client VPN. Le client nécessite un certificat client et la configuration appropriée du client VPN pour la connexion.

### Créer le package de configuration du client VPN

1. Dans le Portail de gestion, au niveau de la page Tableau de bord de votre réseau virtuel, accédez au menu **aperçu rapide** dans le coin droit, puis cliquez sur le package VPN s’appliquant au client que vous souhaitez connecter à votre réseau virtuel. Les systèmes d’exploitation clients pris en charge sont les suivants :
 - Windows 7 (32 bits et 64 bits)
 - Windows Server 2008 R2 (64 bits uniquement)
 - Windows 8 (32 bits et 64 bits)
 - Windows 8.1 (32 bits et 64 bits)
 - Windows Server 2012 (64 bits uniquement)
 - Windows Server 2012 R2 (64 bits uniquement)

1. Sélectionnez le package de téléchargement correspondant au système d’exploitation client sur lequel il sera installé :
 - Pour les clients 32 bits, sélectionnez **Télécharger le package VPN client 32 bits**.
 - Pour les clients 64 bits, sélectionnez **Télécharger le package VPN client 64 bits**.
1. La création du package client prendra quelques minutes. Une fois le package généré, vous serez en mesure de télécharger le fichier. Le fichier *.exe* que vous téléchargez peut être stocké en toute sécurité sur votre ordinateur local.
1. Après avoir généré et téléchargé le package client VPN depuis le Portail de gestion, vous pouvez l’installer sur l’ordinateur client à partir duquel vous souhaitez vous connecter à votre réseau virtuel. Si vous prévoyez d'installer le package client VPN sur plusieurs ordinateurs clients, assurez-vous que chacun d'entre eux dispose également d'un certificat client. Le package client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe aucun logiciel supplémentaire.

### Installer le package de configuration VPN sur le client et démarrer la connexion

1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel, puis double-cliquez sur le fichier .exe. Une fois le package installé, vous pouvez démarrer la connexion VPN. Notez que le package de configuration n’est pas signé par Microsoft. Si vous le souhaitez, vous pouvez signer le package à l’aide du service de signature de votre organisation ou le signer vous-même au moyen de l’outil [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx). Il vous est possible d’utiliser le package sans le signer. Toutefois, si le package n’est pas signé, un avertissement s’affiche au moment où vous l’installez.
2. Sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous venez de créer. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**.
3. Vous voyez apparaître un message contextuel vous permettant de créer un certificat auto-signé pour le point de terminaison de passerelle. Cliquez sur **Continuer** pour utiliser des privilèges élevés.
4. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion.
5. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.
6. Vous êtes à présent connecté à votre réseau virtuel et vous disposez d’un accès complet à l’ensemble des services et machines virtuelles hébergés dans votre réseau virtuel.

### Vérifier la connexion VPN

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses de la plage d’adresses de connectivité de point à site que vous avez indiquée lorsque vous avez créé votre réseau virtuel. Les résultats doivent être semblables à ce qui suit :

Exemple :



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled



## Étapes suivantes


Pour plus d’informations sur la connectivité de réseau virtuel intersite, consultez la rubrique [À propos de la connectivité intersite sécurisée de réseau virtuel](http://go.microsoft.com/fwlink/p/?LinkID=532884).

Si vous souhaitez configurer une connexion VPN de site à site, voir l’article [Configuration d’un réseau virtuel avec une connexion VPN de site à site](vpn-gateway-site-to-site-create.md).

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Pour plus d’informations, consultez la rubrique [Création d’une machine virtuelle personnalisée](../virtual-machines/virtual-machines-create-custom.md).

 

<!---HONumber=August15_HO6-->