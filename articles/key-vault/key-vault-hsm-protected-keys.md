<properties
	pageTitle="Génération et transfert de clés protégées par HSM pour le coffre de clés Azure | Microsoft Azure"
	description="Utilisez les informations présentes dans cette rubrique pour planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel à utiliser avec le coffre de clés Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/18/2015"
	ms.author="cabailey"/>
#Génération et transfert de clés HSM protégées pour Azure clé de coffre

##Introduction

Pour une meilleure garantie, lorsque vous utilisez le coffre de clés Azure, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Ce scénario est souvent appelé *Apportez votre propre clé * ou désigné par l’acronyme BYOK. Les modules HSM bénéficient d’une validation FIPS 140-2 de niveau 2. Le coffre de clés Azure utilise la famille nShield de modules HSM de Thales pour protéger vos clés.

Utilisez les informations présentes dans cette rubrique pour planifier, générer, puis transférer vos propres clés protégées par HSM à utiliser avec Azure clé de coffre.

>[AZURE.NOTE]Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](key-vault-whatis.md).
>
>Pour voir un didacticiel de mise en route incluant un coffre de clés pour les clés protégées par HSM, consultez la section [Prise en main du coffre de clés Azure](key-vault-get-started.md).

Plus d’informations sur la génération et le transfert d’une clé protégée par HSM sur Internet :

- Il est possible de générer la clé depuis une station de travail hors connexion, ce qui réduit la surface d’attaque.

- La clé est chiffrée à l’aide d’une clé Key Exchange Key (KEK), qui reste chiffrée jusqu’à ce qu’elle soit transférée vers le module de sécurité matériel du coffre de clés Azure. Seule une version chiffrée de la clé quitte la station de travail d’origine.

- Le jeu d’outils définit les propriétés sur votre clé de locataire qui lie votre clé au monde de sécurité du coffre de clés Azure. Par conséquent, votre clé ne peut être utilisée que par les modules de sécurité matériels du coffre de clés Azure qui l’ont réceptionnée et déchiffrée. Il est impossible d’exporter votre clé. Cette liaison est appliquée par les modules de sécurité matériels Thales.

- La clé KEK qui est utilisée pour chiffrer votre clé est générée dans les modules de sécurité matériels du coffre de clés Azure et n’est pas exportable. Les HSM garantissent qu’aucune version en clair de la clé KEK ne se trouve hors des HSM. En outre, le jeu d’outils inclut une attestation de Thales certifiant que la clé KEK n’est pas exportable et qu’il a été généré à l’intérieur d’un HSM authentique conçu par Thales.

- Le jeu d’outils inclut une attestation de Thales certifiant que le monde de sécurité du coffre de clés Azure a été généré sur un module de sécurité matériel authentique fabriqué par Thales. Ceci afin de garantir que Microsoft utilise du matériel authentique.

- Microsoft utilise des clés KEK et des mondes de sécurité distincts dans chaque zone géographique. Ainsi, vous êtes sûr que votre clé ne peut être utilisée que dans les centres de données de la région dans laquelle elle a été chiffrée. Par exemple, la clé d’un client européen ne peut pas être utilisée dans des centres de données d’Amérique du Nord ou d’ Asie.

##Autres informations sur les services de sécurité matériels Thales et Microsoft

Thales e-Security est un leader mondial de solutions de chiffrement de données et de cybersécurité pour les secteurs financiers, de haute technologie, de fabrication, gouvernemental et la technologie. Cumulant 40 ans d’expérience dans la protection de données d’entreprise et gouvernementales, les solutions Thales sont utilisées par quatre des cinq plus grandes compagnies productrice d’énergie et aérospatiales, 22 pays de l’OTAN et assurent la protection de plus de 80 % des transactions de paiement au niveau mondial.

Microsoft a collaboré avec Thales pour améliorer encore les performances HSM. Ces améliorations vous permettent de bénéficier des avantages des services hébergés sans avoir à renoncer au contrôle de vos clés. Ces améliorations permettent en particulier à Microsoft de gérer les modules de sécurité matériels pour vous. Comme un service Cloud, le coffre de clés Azure évolue très rapidement pour répondre aux pics d’activité de votre organisation. Dans le même temps, votre clé est protégée à l’intérieur des HSM de Microsoft. Vous maîtrisez le cycle de vie de la clé, parce que vous générez la clé, puis la transférez vers les modules de sécurité matérielle de Microsoft.

##Mise en œuvre du principe BYOK (Apportez votre propre clé, pour le coffre de clés Azure

Utilisez les informations et les procédures qui suivent pour générer votre propre clé protégée HSM, puis la transférer vers le coffre de clés Azure. Le scénario BYOK.


##Configuration requise pour BYOK

Consultez le tableau qui suit pour connaître les conditions requises pour apporter votre propre clé (BYOK) dans le coffre de clés Azure.

|Prérequis|Plus d’informations|
|---|---|
|Abonnement à Azure|Pour créer un coffre de clés Azure, vous avez besoin d’un abonnement Azure : [Inscrivez-vous pour la version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/)|
|Un coffre de clés Azure qui prend en charge des modules de sécurité matériels|Pour plus d’informations sur les niveaux de service et les capacités du coffre de clés Azure, consultez le site web [Tarifs du coffre de clés Azure](http://azure.microsoft.com/pricing/details/key-vault/).|
|Modules de sécurité matérielle, cartes à puces et logiciel d’assistance de Thales|Vous devez disposer d’un accès au module de sécurité matérielle Thales et quelques notions sur les modules de sécurité matérielle d’HSM. Voir [Modules de sécurité matérielle Thales](https://www.thales-esecurity.com/msrms/buy) pour obtenir une liste des modèles compatibles ou acheter un module de sécurité matérielle si vous n’en n’avez pas encore.|
|Les matériels et le logiciel suivants :<ol><li> une station de travail x64 hors ligne avec système d’exploitation Windows 7 minimum et un logiciel Thales nShield version 11.50 minimum.<br/><br/>Si la station de travail exécute Windows 7, vous devez installer [install Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Une station de travail connectée à Internet avec système d’exploitation Windows 7 minimum.</li><li>Une clé USB ou un autre support de stockage amovible avec au moins 16 Mo d’espace libre.</li></ol>|Pour des raisons sécurité, nous conseillons de faire en sorte que la première station de travail ne soit pas connectée à un réseau. Cependant, cette modification n’est pas appliquée par programmation.<br/><br/>Notez que dans la procédure qui suit, cette station de travail est désignée comme une station de travail déconnectée.</p></blockquote><br/>En outre, si votre clé locataire est destinée à un réseau de production, nous vous recommandons d’utiliser un poste de travail distinct pour télécharger les outils et télécharger la clé locataire. À des fins de test, vous pouvez utiliser la même station de travail que la précédente.<br/><br/>Notez que dans la procédure qui suit, la station de travail est désignée comme une station de travail connectée à Internet.</p></blockquote><br/>|

##Générez et transférez votre clé sur le module de sécurité matériel du coffre de clés Azure

Pour générer et transférer votre clé vers le module de sécurité matériel du coffre de clés Azure, exécutez les 5 opérations qui suivent :

- [Étape 1 : Préparez votre station de travail connectée à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Étape 2 : Préparez votre station de travail déconnectée](#step-2-prepare-your-disconnected-workstation)
- [Étape 3 : Générez votre clé](#step-3-generate-your-key)
- [Étape 4 : Réparez votre clé pour le transfert](#step-4-prepare-your-key-for-transfer)
- [Étape 5 : Transférez votre clé vers le coffre de clés Azure](#step-5-transfer-your-key-to-azure-key-vault)

## Étape 1 : Préparez votre station de travail connectée à Interne
Pour cette première étape, exécutez les procédures qui suivent sur la station de travail connectée à Internet.


###Étape 1.1 : installer Azure PowerShell

Depuis la station de travail connectée à Internet, téléchargez et installez le module Azure PowerShell qui inclut les applets de commande servant à gérer le coffre de clés Azure. Cela nécessite au moins la version 0.8.13.

Pour connaître la procédure d’installation, consultez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

###Étape 1.2 : Obtenez votre ID d’abonnement Azure.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure en utilisant la commande suivante :

		Add-AzureAccount
Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Utilisez ensuite la commande [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

		Get-AzureSubscription
Dans le résultat, recherchez l’ID de l’abonnement que vous utiliserez pour le coffre de clés Azure. Vous aurez besoin cet ID d’abonnement ultérieurement.

Ne fermez pas la fenêtre Azure PowerShell.

###Étape 1.3 : Téléchargez le jeu d’outils BYOK pour Azure clé de coffre

Accédez au centre de téléchargement Microsoft Download Center et [téléchargez l’ensemble d’outils BYOK du coffre de clés Azure](http://www.microsoft.com/download/details.aspx?id=45345) correspondant à votre région :

|Région|Nom du package|Package de hachage SHA-256|
|---|---|---|
|Amérique du Nord|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|Europe|KeyVault-BYOK-outils-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|Asie|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|Amérique latine|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|
|Japon|KeyVault-BYOK-Tools-Japan.zip|DB512CD9472FDE2FD610522847DF05E4D7CD49A296EE4A2DD74D43626624A113|
|Australie|KeyVault-BYOK-Tools-Australia.zip|8EBC69E58E809A67C036B50BB4F1130411AD87A7464E0D61A9E993C797915967|

Pour valider l’intégrité de votre jeux d’outils BYOK, dans votre session Azure PowerShell, utilisez l’applet de commande [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx).

	Get-FileHash KeyVault-BYOK-Tools-*.zip

Le jeux d’outils contient les éléments suivants :

- Un package Key Exchange Key (KEK) dont le nom commence par **BYOK-KEK-pkg-**.
- Un package Security World dont le nom commence par **BYOK-SecurityWorld-pkg-**.
- Un script python nommé v**erifykeypackage.py.**
- Un fichier exécutable de ligne de commande nommé **KeyTransferRemote.exe** et les DLL associées.
- Un package redistribuable Visual C++ nommé **vcredist\_x64.exe**.

Copiez le package sur un lecteur USB ou autre support de stockage portable.

##Étape 2 : Préparez votre poste de travail déconnecté

Pour cette deuxième étape, procédez comme suit sur la station de travail non connectée à un réseau (Internet ou votre réseau interne).


###Étape 2.1 : Préparez le poste de travail déconnecté avec Thales HSM

Installer le logiciel de support nCipher (Thales) sur un ordinateur Windows, puis rattachez un module de sécurité matériel Thales à cet ordinateur.

Assurez-vous que les outils Thales se trouvent dans votre chemin d’accès (**%nfast\_home%\\bin** et **%nfast\_home%\\python\\bin**). Tapez ensuite la commande suivante :

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Pour plus d’informations, consultez le guide de l’utilisateur inclus dans le module de sécurité matériel Thales.

###Étape 2.2 : Installez le jeux d’outils BYOK sur le poste de travail déconnecté

Copiez le package d’outils BYOK de la clé USB ou de l’autre support de stockage portable, puis procédez comme suit :

1. Extrayez les fichiers du package téléchargé dans un dossier.
2. Depuis ce dossier, exécutez vcredist\_x64.exe.
3. Suivez les instructions pour installer les composants d’exécution Visual C++ pour Visual Studio 2012.

##Étape 3: Générez votre clé

Pour cette troisième étape, procédez comme suit sur la station de travail déconnectée.

###Étape 3.1 : Créez un monde de sécurité

Démarrez une invite de commande et exécutez le programme de nouveau monde Thales.

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Ce programme crée un fichier **Security World** à l’emplacement %NFAST\_KMDATA%\\local\\world, qui correspond au dossier C:\\ProgramData\\nCipher\\Key Management Data\\local. Vous pouvez utiliser des valeurs différentes pour le quorum, mais dans notre exemple, vous êtes invité à entrer trois cartes vierges et codes confidentiels pour chacune d’elles. Par la suite, chacune des deux cartes procure un accès total au monde de sécurité. Ces cartes constituent l’**ensemble de cartes administrateur** pour le nouveau monde de sécurité.

Faites ensuite ce qui suit :

- Sauvegardez le fichier de monde. Sécurisez et protégez le fichier de monde, les cartes administrateur et leurs codes confidentiels et assurez-vous que personne n’a accès à plusieurs cartes.

###Étape 3.2 : Validez le package téléchargé

Cette étape est facultative, mais recommandée afin que vous puissiez valider les éléments suivants :

- La clé KEK incluse dans le jeu d’outils a été générée à partir d’un module de sécurité matérielle Thales authentique.
- Le hachage du monde de sécurité inclus dans le jeu d’outils a été générée à partir d’un module de sécurité matérielle Thales authentique.
- La clé KEK n’est pas exportable.

>[AZURE.NOTE]Pour valider le package téléchargé, le module de sécurité matérielle doit être connecté, mis sous tension et disposer d’un monde de sécurité (comme celui que vous venez de créer).

Pour valider le package téléchargé :

1.	Exécutez le script verifykeypackage.py en rattachant une des valeurs suivantes, selon votre région :
	- Pour l’Amérique du Nord :

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- Pour l’Europe :

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- Pour l’Asie :

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- Pour l’Amérique latine :

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- Pour le Japon :

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
	- Pour l’Australie :

			python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1

	>[AZURE.TIP]Le logiciel Thales inclut un python dans %NFAST\_HOME%\\python\\bin
	
2.	Vérifiez que vous voyez bien ce qui suit, ce qui indique que la validation est réussie : **Résultat : RÉUSSITE**

Ce script valide la chaîne de signataire jusqu’à la clé racine Thales. Le hachage de cette clé racine est incorporé dans le script et sa valeur doit être **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Vous pouvez également confirmer cette valeur séparément en vous rendant sur le [site web de Thales](http://www.thalesesec.com/).

Vous êtes maintenant prêt à créer une clé.

###Étape 3.3 : créer une clé

Générez une clé à l’aide du programme **generatekey** de Thales.

Exécutez la commande suivante pour générer la clé :

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Lorsque vous exécutez cette commande, utilisez ces instructions :

- Remplacez la valeur *contosokey* d’**ident** et de **plainname** par n’importe quelle valeur de chaîne. Pour réduire la charge administrative et réduire le risque d’erreurs, nous vous recommandons d’utiliser la même valeur pour les deux. La valeur **ident** doit contenir uniquement des chiffres, des tirets et des lettres minuscules.

- Le pubexp est laissé vide (par défaut) dans cet exemple, mais vous pouvez définir des valeurs spécifiques. Pour plus d’informations, consultez la Thales

Cette commande crée un fichier de clé à jeton dans le dossier %NFAST\_KMDATA%\\local avec un nom commençant par **key\_simple\_** suivi de l’ident spécifié dans la commande. Par exemple : **key\_simple\_contosokey**. Ce fichier contient une clé chiffrée.

Sauvegardez ce fichier de clé à jeton dans un emplacement sûr.

>[AZURE.IMPORTANT]Lorsque vous transférez par la suite la clé dans le coffre de clés Azure, Microsoft ne peut pas vous la renvoyer, ce qui fait qu’il est extrêmement important de sauvegarder votre clé et votre monde de sécurité. Contactez Thales pour connaître les conseils et meilleures pratiques pour sauvegarder votre clé.

Vous êtes maintenant prêt à transférer votre clé vers coffre de clés Azure.

##Étape 4 : Préparez votre clé pour le transfert

Pour cette quatrième étape, procédez comme suit sur la station de travail déconnectée.

###Étape 4.1 : Créez une copie de votre clé avec des autorisations réduites

Pour limiter les autorisations sur votre clé, dans l’invite de commande, exécutez l’une des opérations suivantes, en fonction de votre région :

- Pour l’Amérique du Nord :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Pour l’Europe :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Pour l’Asie :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Pour l’Amérique latine :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Pour le Japon :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Pour l’Australie :

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1

quand vous exécutez cette commande, remplacez *contosokey* par la valeur spécifiée à l’**Étape 3.3 : créer une clé** de l’opération [Générer votre clé](#step-3-generate-your-key).

Vous devrez incorporer les cartes administrateur du monde sécurité.

Une fois la commande exécutée, vous verrez **Résultat : RÉUSSITE** et la copie de votre clé apparaissent avec des autorisations réduites dans le fichier nommé key\_xferacId\_<contosokey>.

###Étape 4.2 : Inspectez la nouvelle copie de la clé

Vous pouvez également exécuter les utilitaires Thales pour confirmer les autorisations minimales sur la nouvelle clé :

- aclprint.py :

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe :

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
quand vous exécutez cette commande, remplacez contosokey par la valeur spécifiée à l’**Étape 3.3 : créer une clé** de l’opération [Générer votre clé](#step-3-generate-your-key).

###Étape 4.3 : chiffrer votre clé à l’aide clé de Microsoft Exchange

Exécutez l’une des commandes suivantes, selon votre région :

- Pour l’Amérique du Nord :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Europe :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Asie :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Amérique latine :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le Japon :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Australie :

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Lorsque vous exécutez cette commande, utilisez ces instructions :

- Remplacez *contosokey* par l’identificateur que vous avez utilisé pour générer la clé à l’**Étape 3.3 : créer une clé** lors de l’opération [Générer votre clé](#step-3-generate-your-key).

- Remplacez *SubscriptionID* par l’ID d’abonnement Azure qui contient votre coffre de clés. Vous avez récupéré cette valeur auparavant, au cours de l’**Étape 1.2 : obtenir votre ID d’abonnement Azure** lors de l’opération [Préparer votre station de travail connectée à Internet](#step-1-prepare-your-internet-connected-workstation).

- Remplacez *ContosoFirstHSMKey* par une étiquette qui sera utilisée pour votre nom de fichier de sortie.

Une fois cette opération terminée, **Résultat : RÉUSSITE** apparaît et un nouveau fichier est ajouté au dossier en cours. Il porte le nom suivant : TransferPackage-*ContosoFirstHSMkey*.byok.

###Étape 4.4 : copiez votre package de transfert de clé vers la station de travail connectée à Internet 

Utilisez une clé USB ou un autre dispositif de stockage portable pour copier le fichier de sortie issu de la première étape (KeyTransferPackage-ContosoFirstHSMkey.byok) pour votre station de travail connectée à Internet.

##Étape 5 : transférer votre clé vers le coffre de clés Azure

Pour l’opération finale, sur la station de travail connectée à Internet, utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) pour télécharger le package de transfert de clé copié de la station de travail de la station de travail non connectée vers le module de sécurité matériel du coffre de clés Azure :

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Si le téléchargement réussit, les propriétés de la clé que vous venez de créer s’afficheront.

##Étapes suivantes

Vous pouvez maintenant utiliser cette clé protégée HSM dans votre coffre de clés. Pour plus d’informations, voir la section **Utiliser un module de sécurité matériel (HSM)** dans le didacticiel [Prise en main du coffre de clés Azure](key-vault-get-started.md).

<!---HONumber=Sept15_HO4-->