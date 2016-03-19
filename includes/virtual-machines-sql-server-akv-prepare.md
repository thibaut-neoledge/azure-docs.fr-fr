## Préparation pour AKV Integration
Il existe plusieurs conditions préalables pour utiliser Azure Key Vault Integration pour configurer votre machine virtuelle SQL Server :

1.	[Installation d'Azure Powershell](#install-azure-powershell)
2.	[Création d'un Azure Active Directory](#create-an-azure-active-directory)
3.	[Création d’un coffre de clés](#create-a-key-vault)

Les sections suivantes décrivent ces conditions préalables et les informations que vous devez collecter pour exécuter ultérieurement des applets de commande PowerShell.

### Installation d'Azure PowerShell
Assurez-vous que vous avez installé la dernière version du kit de développement logiciel (SDK) Azure PowerShell. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md).

### Création d'un Azure Active Directory
Tout d’abord, votre abonnement doit comporter un [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (ADD). Cela vous permet, entre autres avantages, d'accorder des droits d'accès à votre coffre de clés à certains utilisateurs et d'applications.

Inscrivez ensuite une application auprès d'ADD. Vous obtiendrez un compte Principal du service ayant un accès à votre coffre de clés dont votre machine virtuelle a besoin. Dans l’article Coffre de clés Azure, vous pouvez trouver ces étapes dans la section [Inscription d’une application auprès d’Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register), ou vous pouvez voir les étapes avec des captures d’écran dans la section **Obtention d’une identité pour l’application** de [ce billet de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Avant d'effectuer ces étapes, notez que vous devez collecter les informations suivantes au cours de cette inscription. Elles seront nécessaires ultérieurement lorsque vous activerez Azure Key Vault Integration dans votre machine virtuelle SQL.

- Une fois l’application ajoutée, recherchez l’**ID CLIENT** dans l’onglet **CONFIGURER**. ![ID de Client Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
	
	L’ID client est affecté ultérieurement au paramètre **$spName** (nom du principal du service) dans le script PowerShell pour activer l’intégration du Coffre de clés Azure. 
- Au cours de ces étapes, lorsque vous créez votre clé, copiez également son secret comme indiqué dans la capture d'écran suivante. Ce secret de clé est affecté ultérieurement au paramètre **$spSecret** (secret du principal du service) dans le script PowerShell. ![Secret Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
- Vous devez autoriser ce nouvel ID client pour disposer des autorisations d’accès suivantes : **chiffrer**, **déchiffrer**, **wrapKey**, **unwrapKey**, **signer**, et **vérifier**. Cette opération s’effectue avec l’applet de commande [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx). Pour plus d’informations, consultez [Autorisation de l’application pour l’utilisation de la clé ou du secret](../articles/key-vault/key-vault-get-started.md#authorize).

### Création d’un coffre de clés
Pour utiliser Azure Key Vault pour stocker les clés que vous utiliserez pour le chiffrement dans votre machine virtuelle, vous devez accéder à un coffre de clés. Si vous n’avez pas déjà configuré votre coffre de clés, créez-le en suivant les étapes de la rubrique [Prise en main du coffre de clés Azure](../articles/key-vault/key-vault-get-started.md). Avant d'effectuer ces étapes, vous devrez collecter certaines informations au cours de la configuration. Elles seront nécessaires ultérieurement lorsque vous activerez Azure Key Vault Integration dans votre machine virtuelle SQL.

Lorsque vous arrivez à l’étape de création d’un coffre de clés, notez la propriété **vaultUri** renvoyée. Il s’agit de l’URL du coffre de clés. Dans l’exemple illustré ci-dessous et fourni à cette étape, le nom du coffre de clés est ContosoKeyVault. Par conséquent, l’URL du coffre de clés sera https://contosokeyvault.vault.azure.net/.

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

L’URL du coffre de clés est affectée ultérieurement au paramètre **$akvURL** dans le script PowerShell pour activer l’intégration du coffre de clés Azure.

<!---HONumber=AcomDC_1223_2015-->