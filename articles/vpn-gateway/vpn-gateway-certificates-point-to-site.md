<properties 
   pageTitle="Création de certificats auto-signés pour les configurations intersite de passerelle VPN point à site à l'aide de makecert | Microsoft Azure"
   description="Cet article contient les étapes pour l'utilisation de makecert pour créer des certificats racines auto-signés sur Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Utilisation des certificats racine auto-signés pour les configurations point à site

Les tâches suivantes vous aideront à créer un certificat racine à l'aide de makecert et générer des certificats clients à partir du certificat racine. Les étapes ci-dessous s'appliquent à l'utilisation de makecert sur Windows 10.

## Création d'un certificat racine auto-signé

Makecert est l’une des méthodes permettant de créer un certificat racine auto-signé. Les étapes ci-dessous vous guideront dans la création d'un certificat racine auto-signé à l'aide de makecert. Ces étapes ne sont pas spécifiques au modèle de déploiement. Elles sont valides pour le modèle Resource Manager et classique.

### Pour créer un certificat racine auto-signé

1. À partir d'un ordinateur exécutant Windows 10, téléchargez et installez le [Kit de développement logiciel (SDK) Windows pour Windows 10](https://dev.windows.com/fr-FR/downloads/windows-10-sdk).

2. Après l’installation, vous trouvez l’utilitaire makecert.exe dans ce chemin d’accès : C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Exemple :
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Créez et installez un certificat racine dans le magasin de certificats personnels sur votre ordinateur. L'exemple ci-dessous illustre la création d'un fichier *.cer* correspondant que vous chargerez plus tard. Exécutez la commande suivante en tant qu'administrateur, où *RootCertificateName* est le nom que vous souhaitez utiliser pour le certificat.

	Remarque : l'exécution de l'exemple suivant sans aucune modification entraîne la création d'un certificat racine et du fichier *RootCertificateName.cer* correspondant. Vous trouverez le fichier .cer dans le répertoire à partir duquel vous avez exécuté la commande. Le certificat se trouve dans votre dossier Certificates : Current User\\Personal\\Certificates.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Comme vous avez créé un certificat racine permettant de générer des certificats clients, il peut être utile d'exporter ce certificat avec sa clé privée et de l'enregistrer à un emplacement sûr à partir duquel il pourra être récupéré.

## Génération, exportation et installation de certificats clients

Vous pouvez générer un certificat client à partir d’un certificat racine auto-signé, puis l'exporter et l'installer. Les étapes ci-dessous ne sont pas spécifiques au modèle de déploiement. Elles sont valides pour le modèle Resource Manager et classique.

### Pour générer un certificat client à partir d'un certificat racine auto-signé.

Les étapes ci-dessous vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Chaque certificat client peut alors être exporté et installé sur l’ordinateur client.

1. Sur l’ordinateur que vous avez utilisé pour créer le certificat racine auto-signé, ouvrez une invite de commandes en tant qu’administrateur.

2. Accédez à l’emplacement où vous souhaitez enregistrer le fichier de certificat client. *RootCertificateName* fait référence au certificat racine auto-signé que vous avez généré. Si vous exécutez l’exemple suivant (en remplaçant « RootCertificateName » par le nom de votre certificat racine), cette opération entraînera la création d’un certificat client nommé « ClientCertificateName » dans votre magasin de certificats Personnel.

3. Tapez la commande suivante :

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Tous les certificats sont stockés sur votre ordinateur, dans votre dossier Certificates : Current User\\Personal\\Certificates store. Vous pouvez générer autant de certificats clients que nécessaire d’après cette procédure.

### Pour exporter un certificat client

1. Pour exporter un certificat client, utilisez *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. L’Assistant Exportation de certificat s’ouvre.

2. Dans l'Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

3. Dans la page **Format de fichier d'exportation**, vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez ensuite sur **Suivant**.
 
4. Dans la page **Sécurité**, vous devez protéger la clé privée. Si vous choisissez d'utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat, puis cliquez sur **Suivant**.

5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l'emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

6. Cliquez sur **Terminer** pour exporter le certificat.

### Pour installer un certificat client

Chaque client que vous souhaitez connecter à votre réseau virtuel à l'aide d'une connexion point à site doit posséder un certificat client installé. Ce certificat s'ajoute au package de configuration VPN requis. Les étapes ci-dessous vont vous guider dans l’installation du certificat client manuellement.

1. Recherchez le fichier *.pfx* et copiez-le sur l'ordinateur client. Sur l'ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie en tant qu'**Utilisateur actuel**, puis cliquez sur **Suivant**.

2. N'apportez aucune modification à la page **Fichier à importer**. Cliquez sur **Next**.

3. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.

4. Dans la page **Magasin de certificats**, laissez l'emplacement par défaut, puis cliquez sur **Suivant**.

5. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l'installation du certificat, cliquez sur **Oui**. Le certificat est désormais importé.

## Étapes suivantes

Poursuivez votre configuration point à site.

- Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez [Configurer une connexion point à site à un réseau virtuel à l'aide de PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Pour connaître les étapes du modèle de déploiement **classique**, consultez [Configurer une connexion VPN de point à site à un réseau virtuel](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0121_2016-->