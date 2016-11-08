## Étapes suivantes
Après avoir activé Azure Key Vault Integration, vous pouvez activer le chiffrement SQL Server sur votre machine virtuelle SQL. Tout d'abord, vous devez créer une clé asymétrique à l'intérieur de votre coffre de clés et une clé symétrique dans SQL Server sur votre machine virtuelle. Ensuite, vous serez en mesure d'exécuter les instructions T-SQL pour activer le chiffrement pour vos bases de données et sauvegardes.

Il existe plusieurs types de chiffrement que vous pouvez exploiter :

* [Chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Sauvegardes chiffrées](https://msdn.microsoft.com/library/dn449489.aspx)
* [Chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Les scripts Transact-SQL suivants fournissent des exemples pour chacune de ces options.

> [!NOTE]
> Chaque exemple est basé sur les deux conditions préalables : une clé asymétrique de votre coffre de clés appelée **CONTOSO\_KEY** et des informations d'identification créées via la fonctionnalité AKV Integration appelées **Azure\_EKM\_TDE\_cred**.
> 
> 

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

Notez que cet article suppose que vous disposez déjà de SQL Server exécuté sur une machine virtuelle Azure. Dans le cas contraire, consultez[Approvisionnement d'une machine virtuelle SQL Server dans Azure](../articles/virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) Pour d'autres conseils sur l'utilisation de SQL Server sur des machines virtuelles Azure, voir [Vue d'ensemble de SQL Server dans Azure Virtual Machines](../articles/virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0413_2016-->