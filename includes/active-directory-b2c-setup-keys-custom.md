
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Ajouter à votre client B2C des clés de signature et chiffrement utilisables par les stratégies personnalisées

1. Accédez au panneau Identity Experience Framework (Infrastructure d'expérience d'identité) dans les paramètres de votre client B2C Azure AD.
1. Sélectionnez `Policy Keys` pour afficher les clés disponibles dans votre client. Si la clé `B2C_1A_TokenSigningKeyContainer` existe, ignorez-la.
1. Créez `TokenSigningKeyContainer`.  
 * Cliquez sur `+Add`.
 * Options > `Generate`.
 * Nom > `TokenSigningKeyContainer`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Type de clé > `RSA`.
 * Dates - Utilisez les valeurs par défaut.
 * Utilisation de la clé > `Signature`.
1. Cliquez sur `Create`
1. Si une clé nommée `B2C_1A_TokenEncryptionKeyContainer` existe, ignorez-la.
1. Créez `TokenEncryptionKeyContainer`.
 * Options > `Generate`.
 * Nom > `TokenSigningKeyContainer`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Type de clé > `RSA`.
 * Dates - Utilisez les valeurs par défaut.
 * Utilisation de la clé > `Encryption`
1. Cliquez sur `Create`


[!TIP]
Les étapes suivantes sont FACULTATIVES si vous souhaitez proposer des fournisseurs d’identité sociaux ou des fournisseurs d’identité fédérés à vos utilisateurs finaux.  Facebook fournit un bon point de départ pour en savoir plus sur les fournisseurs d’identité externes avec Azure AD B2C à l’aide de stratégies personnalisées.

1. Créez `FacebookSecret`.  Bien que facultative, cette étape est recommandée pour tester immédiatement votre capacité à fédérer en externe.  Cela crée un point de départ solide pour développer vos stratégies avec d’autres fournisseurs d’identité.
 * Cliquez sur `+Add`.
 * Options > `Manual`.
 * Nom > `FacebookSecret`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Secret > Entrez votre FacebookSecret à partir de developers.facebook.com.  *Ce n’est pas votre ID d’application Facebook.*
 * Utilisation de la clé > Signature
1. Cliquez sur `Create` et confirmez la création. Notez le nom.

[!NOTE]
Si vous utilisez des stratégies intégrées Azure AD B2C, vous utiliserez généralement le même secret pour les stratégies intégrées et personnalisées. 
