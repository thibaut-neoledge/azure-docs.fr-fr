<properties 
	pageTitle="Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour des machines virtuelles SQL Server sur Azure"
	description="Apprenez à automatiser la configuration du chiffrement de SQL Server pour une utilisation avec Azure Key Vault. Cette rubrique explique comment utiliser Azure Key Vault Integration avec des machines virtuelles SQL Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour des machines virtuelles SQL Server sur Azure

## Vue d'ensemble
Il existe plusieurs fonctionnalités de chiffrement SQL Server, telles que [ le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [le chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) et [le chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces types de chiffrement nécessitent que vous gériez et stockiez les clés de chiffrement que vous utilisez pour le chiffrement. Le service Azure Key Vault (coffre de clés Azure, AKV) est conçu pour optimiser la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [connecteur SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d'utiliser ces clés depuis Azure Key Vault.

Si vous exécutez SQL Server sur des ordinateurs sur site, vous devez [suivre la procédure d'accès à Azure Key Vault à partir de votre ordinateur SQL Server sur site](https://msdn.microsoft.com/library/dn198405.aspx). Mais, pour SQL Server dans des machines virtuelles Azure, vous pouvez gagner du temps à l'aide de la fonctionnalité *Azure Key Vault Integration*. Avec quelques applets de commande Azure PowerShell pour activer cette fonctionnalité, vous pouvez automatiser la configuration nécessaire pour qu'une machine virtuelle SQL puisse accéder à votre coffre de clés.

Lorsque cette fonctionnalité est activée, elle installe automatiquement le connecteur SQL Server, configure le fournisseur EKM pour accéder à Azure Key Vault et crée les informations d'identification vous permettant d'accéder à votre coffre. Si vous avez examiné les étapes décrites dans la documentation locale mentionnée précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule étape que vous devez encore exécuter manuellement consiste à créer le coffre de clé et des clés. À partir de là, la configuration complète de votre machine virtuelle SQL est automatisée. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter des instructions T-SQL pour crypter vos bases de données et vos sauvegardes comme vous y êtes habitué.

## Préparation pour AKV Integration
Il existe plusieurs conditions préalables pour utiliser Azure Key Vault Integration pour configurer votre machine virtuelle SQL Server :

1.	[Installation d'Azure Powershell](#install-azure-powershell)
2.	[Création d'un Azure Active Directory](#create-an-azure-active-directory)
3.	[Création d’un coffre de clés](#create-a-key-vault)

Les sections suivantes décrivent ces conditions préalables et les informations que vous devez collecter pour exécuter ultérieurement des applets de commande PowerShell.

### Installation d'Azure PowerShell
Assurez-vous que vous avez installé la dernière version du kit de développement logiciel (SDK) Azure PowerShell. Pour plus d'informations, consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

### Création d'un Azure Active Directory
Tout d'abord, vous devez avoir un [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (ADD) dans votre abonnement. Cela vous permet, entre autres avantages, d'accorder des droits d'accès à votre coffre de clés à certains utilisateurs et d'applications.

Inscrivez ensuite une application auprès d'ADD. Vous obtiendrez un compte Principal du service ayant un accès à votre coffre de clés dont votre machine virtuelle a besoin. Dans l'article Azure Key Vault, vous pouvez trouver ces étapes dans la section [Inscription d'une application auprès d'Azure Active Directory](../key-vault/key-vault-get-started.md#register), ou vous pouvez voir les étapes avec des captures d'écran dans la section **Obtention d'une identité pour l'application (en Anglais)** de [ce billet de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Avant d'effectuer ces étapes, notez que vous devez collecter les informations suivantes au cours de cette inscription. Elles seront nécessaires ultérieurement lorsque vous activerez Azure Key Vault Integration dans votre machine virtuelle SQL.

- Une fois l'application ajoutée, recherchez l'**ID CLIENT (CLIENT ID)** dans l'onglet **CONFIGURE (CONFIGURER)**. ![ID de Client Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	L'ID client est attribué ultérieurement au paramètre **$spName** (nom du principal du service) dans le script PowerShell pour activer Azure Key Vault Integration. 
- Au cours de ces étapes, lorsque vous créez votre clé, copiez également son secret comme indiqué dans la capture d'écran suivante. Cette clé secrète est attribuée ultérieurement au paramètre **$spSecret** (clé secrète du principal du service) dans le script PowerShell. ![Secret Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- Vous devez autoriser ce nouvel ID client pour disposer des autorisations d'accès suivantes : **chiffrer**, **déchiffrer**, **wrapKey**, **unwrapKey**, **signer**, et **vérifier**. Cette opération est effectuée avec l'applet de commande [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx). Pour plus d'informations, consultez [Autorisation de l'application à utiliser la clé ou le secret](../key-vault/key-vault-get-started.md#authorize).

### Création d’un coffre de clés
Pour utiliser Azure Key Vault pour stocker les clés que vous utiliserez pour le chiffrement dans votre machine virtuelle, vous devez accéder à un coffre de clés. Si vous n'avez pas déjà configuré votre coffre de clés, créez-le en suivant les étapes de la rubrique [Prise en main du coffre de clés Azure (Azure Key Vault)](../key-vault/key-vault-get-started.md). Avant d'effectuer ces étapes, vous devrez collecter certaines informations au cours de la configuration. Elles seront nécessaires ultérieurement lorsque vous activerez Azure Key Vault Integration dans votre machine virtuelle SQL.

Lorsque vous arrivez à l'étape de création d'un coffre de clés, notez la propriété **vaultUri** renvoyée. Il s'agit de l'URL du coffre de clés. Dans l'exemple illustré ci-dessous fourni à cette étape, le nom du coffre de clés est ContosoKeyVault. Par conséquent, l'URL du coffre de clés serait https://contosokeyvault.vault.azure.net/.

![Secret Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
L'URL du coffre de clés est attribuée ultérieurement au paramètre **$akvURL** dans le script PowerShell pour activer Azure Key Vault Integration.

## Configuration d'AKV Integration
Utilisez PowerShell pour configurer Azure Key Vault Integration. Les sections suivantes fournissent une vue d'ensemble des paramètres requis, puis un exemple de script PowerShell.

### Paramètres d'entrée
Le tableau suivant répertorie les paramètres requis pour exécuter le script PowerShell dans la section suivante.

|Paramètre|Description|Exemple|
|---|---|---|
|**$akvURL**|**L'URL du coffre de clés**|« https://contosokeyvault.vault.azure.net/ »|
|**$spName**|**Nom du principal du service**|« fde2b411-33d5-4e11-af04eb07b669ccf2 »|
|**$spSecret**|**Secret du principal du service**|« 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= »|
|**$credName**|**Nom d'identification** : AKV Integration crée des informations d'identification dans SQL Server, permettant ainsi à la machine virtuelle d'accéder au coffre de clés. Choisissez un nom pour cette identification.|« mycred1 »|
|**$vmName**|**Nom de machine virtuelle** : le nom d'une machine virtuelle SQL créée précédemment.|« myvmname »|
|**$serviceName**|**Nom du service** : le nom du service Cloud associé à la machine virtuelle SQL.|« mycloudservicename »|

### Activation d'AKV Integration avec PowerShell
L'applet de commande **New-AzureVMSqlServerKeyVaultCredentialConfig** crée un objet de configuration pour la fonctionnalité Azure Key Vault Integration. **Set-AzureVMSqlServerExtension** configure cette intégration avec le paramètre **KeyVaultCredentialSettings**. Les étapes suivantes indiquent comment utiliser ces commandes.

1. Dans Azure PowerShell, configurez tout d'abord les paramètres d'entrée avec des valeurs spécifiques comme décrit dans les sections précédentes de cette rubrique. Vous trouverez ci-dessous un exemple de script.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Utilisez ensuite le script suivant pour configurer et activer AKV Integration.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

L'extension de l'Agent SQL IaaS met à jour la machine virtuelle SQL avec cette nouvelle configuration.

## Étapes suivantes
Après avoir activé Azure Key Vault Integration, vous pouvez activer le chiffrement SQL Server sur votre machine virtuelle SQL. Tout d'abord, vous devez créer une clé asymétrique à l'intérieur de votre coffre de clés et une clé symétrique dans SQL Server sur votre machine virtuelle. Ensuite, vous serez en mesure d'exécuter les instructions T-SQL pour activer le chiffrement pour vos bases de données et sauvegardes.

Il existe plusieurs types de chiffrement que vous pouvez exploiter :

- [Chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Sauvegardes chiffrées](https://msdn.microsoft.com/library/dn449489.aspx)
- [Chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Les scripts Transact-SQL suivants fournissent des exemples pour chacune de ces options.

>[AZURE.NOTE]Chaque exemple est basé sur les deux conditions préalables : une clé asymétrique de votre coffre de clés appelée **CONTOSO\_KEY** et des informations d'identification créées via la fonctionnalité AKV Integration appelées **Azure\_EKM\_TDE\_cred**.

### Chiffrement transparent des données (TDE)
1. Créez une connexion SQL Server utilisable par le moteur de base de données pour le chiffrement transparent des données, puis ajoutez-lui les informations d'identification.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. Créez la clé de chiffrement de base de données qui sera utilisée pour le chiffrement transparent des données.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### Sauvegardes chiffrées
1. Créez une connexion SQL Server utilisable par le moteur de base de données pour les sauvegardes chiffrées, puis ajoutez-lui les informations d'identification.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Sauvegardez le chiffrement spécifiant la spécification de base de données avec la clé asymétrique stockée dans le coffre de clés.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Chiffrement au niveau des colonnes (CLE)
Ce script crée une clé symétrique protégée par la clé asymétrique dans le coffre de clés et utilise ensuite la clé symétrique pour chiffrer les données dans la base de données.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## Ressources supplémentaires
Pour plus d'informations sur l'utilisation de ces fonctionnalités de chiffrement, consultez [Utilisation d'EKM avec les fonctionnalités de chiffrement SQL Server (en Anglais)](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Notez que cet article suppose que vous disposez déjà de SQL Server exécuté sur une machine virtuelle Azure. Dans le cas contraire, consultez[Approvisionnement d'une machine virtuelle SQL Server dans Azure](virtual-machines-provision-sql-server.md) Pour d'autres conseils sur l'utilisation de SQL Server sur des machines virtuelles Azure, voir [Vue d'ensemble de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->