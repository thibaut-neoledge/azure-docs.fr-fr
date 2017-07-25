<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Après avoir activé Azure Key Vault Integration, vous pouvez activer le chiffrement SQL Server sur votre machine virtuelle SQL. Tout d'abord, vous devez créer une clé asymétrique à l'intérieur de votre coffre de clés et une clé symétrique dans SQL Server sur votre machine virtuelle. Ensuite, vous serez en mesure d'exécuter les instructions T-SQL pour activer le chiffrement pour vos bases de données et sauvegardes.

Il existe plusieurs types de chiffrement que vous pouvez exploiter :

* [Chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Sauvegardes chiffrées](https://msdn.microsoft.com/library/dn449489.aspx)
* [Chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Les scripts Transact-SQL suivants fournissent des exemples pour chacune de ces options.

<a id="prerequisites-for-examples" class="xliff"></a>

### Configuration requise pour les exemples

Chaque exemple est basé sur les deux conditions préalables : une clé asymétrique de votre coffre de clés appelée **CONTOSO_KEY** et une information d’identification créée par le biais de la fonctionnalité AKV Integration appelée **Azure_EKM_TDE_cred**. Les commandes Transact-SQL suivantes installent cette configuration requise pour l’exécution des exemples.

``` sql
USE master;
GO

sp_configure [show advanced options], 1;
GO
RECONFIGURE;
GO

-- Enable EKM provider
sp_configure [EKM provider enabled], 1;
GO
RECONFIGURE;

--create provider
CREATE CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov
FROM FILE = 'C:\Program Files\SQL Server Connector for Microsoft Azure Key Vault\Microsoft.AzureKeyVaultService.EKM.dll';
GO

--create credential
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--must have sysadmin
ALTER LOGIN [TDE_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'keytestvault',  --key name
CREATION_DISPOSITION = OPEN_EXISTING;
```

<a id="transparent-data-encryption-tde" class="xliff"></a>

### Chiffrement transparent des données (TDE)

1. Créez une connexion SQL Server utilisable par le moteur de base de données pour le chiffrement transparent des données, puis ajoutez-lui les informations d'identification.

   ``` sql
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
   ```

1. Créez la clé de chiffrement de base de données qui sera utilisée pour le chiffrement transparent des données.

   ``` sql
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
   ```

<a id="encrypted-backups" class="xliff"></a>

### Sauvegardes chiffrées

1. Créez une connexion SQL Server utilisable par le moteur de base de données pour les sauvegardes chiffrées, puis ajoutez-lui les informations d'identification.

   ``` sql
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
   ```

1. Sauvegardez le chiffrement spécifiant la spécification de base de données avec la clé asymétrique stockée dans le coffre de clés.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

<a id="column-level-encryption-cle" class="xliff"></a>

### Chiffrement au niveau des colonnes (CLE)

Ce script crée une clé symétrique protégée par la clé asymétrique dans le coffre de clés et utilise ensuite la clé symétrique pour chiffrer les données dans la base de données.

``` sql
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
```

<a id="additional-resources" class="xliff"></a>

## Ressources supplémentaires

Pour plus d'informations sur l'utilisation de ces fonctionnalités de chiffrement, consultez [Utilisation d'EKM avec les fonctionnalités de chiffrement SQL Server (en Anglais)](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Notez que cet article suppose que vous disposez déjà de SQL Server exécuté sur une machine virtuelle Azure. Dans le cas contraire, consultez l’article [Approvisionnement d’une machine virtuelle SQL Server dans Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Pour d’autres conseils sur l’utilisation de SQL Server sur des machines virtuelles Azure, consultez l’article [Présentation de SQL Server sur les machines virtuelles Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).