<properties 
   pageTitle="Créer des certificats auto-signés pour les connexions intersite de réseau virtuel point à site à l’aide de makecert | Microsoft Azure"
   description="Cet article explique comment utiliser makecert pour créer des certificats auto-signés sur Windows 10."
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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Utilisation des certificats auto-signés pour les connexions de point à site

Cet article vous aide à créer un certificat auto-signé à l’aide de **makecert**, puis à générer des certificats clients à partir de celui-ci. Les étapes s’appliquent à makecert sur Windows 10. Makecert a été validé pour créer des certificats compatibles avec les connexions P2S.

Pour les connexions P2S, la méthode recommandée pour les certificats consiste à utiliser votre solution de certificat d’entreprise, en veillant à émettre les certificats clients à l’aide du format de valeur de nom courant « nom@votredomaine.com», plutôt que du format « nom de domaine NetBIOS\\nom d’utilisateur ».

Si vous n’avez pas de solution d’entreprise, un certificat auto-signé est nécessaire pour autoriser les clients P2S à se connecter à un réseau virtuel. Même s’il a été déconseillé, makecert représente une méthode valide pour créer des certificats auto-signés compatibles avec les connexions P2S. Nous travaillons à l’élaboration d’une autre solution pour créer des certificats auto-signés, mais pour le moment, makecert est la méthode que nous vous recommandons d’utiliser.

## Créer un certificat auto-signé

Makecert est l’une des méthodes permettant de créer un certificat auto-signé. Les étapes suivantes vous guideront dans la création d’un certificat auto-signé à l’aide de makecert. Ces étapes ne sont pas spécifiques au modèle de déploiement. Elles sont valides pour le modèle Resource Manager et classique.

### Pour créer un certificat auto-signé

1. À partir d’un ordinateur exécutant Windows 10, téléchargez et installez le [SDK Windows pour Windows 10](https://dev.windows.com/fr-FR/downloads/windows-10-sdk).

2. Après l’installation, vous trouvez l’utilitaire makecert.exe dans ce chemin d’accès : C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Exemple : `C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Créez et installez un certificat dans le magasin de certificats personnels sur votre ordinateur. L’exemple suivant crée un fichier *.cer* correspondant que vous chargez sur Azure au moment de la configuration P2S. Exécutez la commande suivante, en tant qu’administrateur. Remplacez *ARMP2SRootCert* et *ARMP2SRootCert.cer* par le nom que vous souhaitez utiliser pour le certificat.<br><br>Le certificat se trouve dans vos certificats : Current User\\Personal\\Certificates.

    	makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Pour obtenir la clé publique

Dans le cadre de la configuration de la passerelle VPN pour les connexions point à site, la clé publique pour le certificat racine est chargée sur Azure.

1. Pour obtenir un fichier .cer du certificat, ouvrez **certmgr.msc**. Cliquez avec le bouton droit sur le certificat racine auto-signé, puis cliquez sur **Toutes les tâches** et sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.

3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).** Cliquez ensuite sur **Suivant**.

4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

 
### Exporter le certificat auto-signé (facultatif)

Vous souhaiterez peut-être exporter le certificat auto-signé et le stocker en toute sécurité. Si besoin est, vous pouvez l’installer ultérieurement sur un autre ordinateur et générer davantage de certificats clients ou exporter un autre fichier .cer. Tous les ordinateurs disposant d’un certificat client installé et configurés avec les paramètres de client VPN appropriés peuvent se connecter à votre réseau virtuel via P2S. Il est donc important que les certificats clients ne soient générés et installés que si cela est nécessaire, et que ce certificat auto-signé soit stocké en toute sécurité.

Pour exporter le certificat auto-signé au format .pfx, sélectionnez le certificat racine et suivez les mêmes étapes que celles décrites dans la section [Exporter un certificat client](#clientkey) pour l’exportation.

## Créer et installer des certificats clients

Vous n’installez pas le certificat auto-signé directement sur l’ordinateur client. Vous devez générer un certificat client à partir du certificat auto-signé. Ensuite, vous exportez et installez le certificat client sur l’ordinateur client. Les étapes suivantes ne sont pas spécifiques au modèle de déploiement. Elles sont valides pour le modèle Resource Manager et classique.

### Partie 1 - Générer un certificat client à partir d’un certificat auto-signé

Les étapes suivantes vous guident dans la génération d’un certificat client à partir d’un certificat auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat. Chaque certificat client peut alors être exporté et installé sur l’ordinateur client.

1. Sur l’ordinateur que vous avez utilisé pour créer le certificat auto-signé, ouvrez une invite de commandes comme administrateur.

2. Dans cet exemple, « ARMP2SRootCert » fait référence au certificat auto-signé que vous avez généré.
	- Remplacez *« ARMP2SRootCert »* par le nom du certificat racine auto-signé à partir duquel vous générez le certificat client.
	- Remplacez *ClientCertificateName* par le nom que vous souhaitez pour générer un certificat client.


	Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, cette opération entraîne la création d’un certificat client nommé « ClientCertificateName » dans votre magasin de certificats Personnel, généré à partir du certificat racine ARMP2SRootCert.

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Tous les certificats sont stockés sur votre ordinateur, dans votre dossier Certificates : Current User\\Personal\\Certificates store. Vous pouvez générer autant de certificats clients que nécessaire d’après cette procédure.

### <a name="clientkey"></a>Partie 2 - Exporter un certificat client

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

3. Dans la page **Format de fichier d’exportation**, vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez ensuite sur **Suivant**.
 
4. Dans la page **Sécurité**, vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat. Cliquez ensuite sur **Suivant**.

5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

6. Cliquez sur **Terminer** pour exporter le certificat.

### Partie 3 - Installer un certificat client

Chaque client que vous souhaitez connecter à votre réseau virtuel à l'aide d'une connexion point à site doit posséder un certificat client installé. Ce certificat s'ajoute au package de configuration VPN requis. Les étapes ci-dessous vous guident dans l’installation manuelle du certificat client.

1. Recherchez le fichier *.pfx* et copiez-le sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie sur **Utilisateur actuel**, puis cliquez sur **Suivant**.

2. N’apportez aucune modification à la page **Fichier à importer**. Cliquez sur **Next**.

3. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.

4. Dans la page **Magasin de certificats**, laissez l’emplacement par défaut, puis cliquez sur **Suivant**.

5. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l’installation du certificat, cliquez sur **Oui**. Le certificat est désormais importé.

## Étapes suivantes

Poursuivez votre configuration point à site.

- Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez [Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
- Pour connaître les étapes du modèle de déploiement **classique**, consultez [Configuration d’une connexion VPN de point à site à un réseau virtuel à l’aide du portail Classic](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0831_2016-->