## <a name="create-a-storage-account-using-the-azure-portal"></a>Créer un compte de stockage dans le portail Azure

Commencez par créer un compte de stockage à usage général pour les besoins de ce guide de démarrage rapide. 

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure. 
2. Dans le menu Hub, sélectionnez **Nouveau** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**. 
3. Entrez un nom unique pour votre compte de stockage. Gardez les règles suivantes à l’esprit lorsque vous nommez votre compte de stockage :
    - Le nom doit compter 3 à 24 caractères.
    - Le nom peut contenir uniquement des chiffres et des lettres minuscules.
4. Vérifiez que les valeurs par défaut suivantes sont définies : 
    - **Modèle de déploiement** est défini sur **Resource Manager**.
    - **Type de compte** est défini sur **Usage général**.
    - **Performances** est défini sur **Standard**.
    - **Réplication** est défini sur **Stockage localement redondant (LRS)**.
5. Sélectionnez votre abonnement. 
6. Pour **Groupe de ressources**, créez-en un et donnez-lui un nom unique. 
7. Sélectionnez le paramètre **Emplacement** à utiliser pour votre compte de stockage.
8. Cochez **Épingler au tableau de bord** et cliquez sur **Créer** pour créer votre compte de stockage. 

Une fois votre compte de stockage créé, il est épinglé au tableau de bord. Cliquez sur le compte pour l’ouvrir. Sous **Paramètres**, cliquez sur **Clés d’accès**. Sélectionnez la clé primaire et copiez la **Chaîne de connexion** associée dans le Presse-papiers, puis collez-la dans un éditeur de texte pour l’utiliser plus tard.
