### <a name="gwipnoconnection"></a> Pour modifier l’adresse IP de la passerelle de réseau local - sans connexion de passerelle

Utilisez l’exemple fourni pour modifier une passerelle de réseau local sans connexion de passerelle. Lorsque vous modifiez cette valeur, vous pouvez également modifier les préfixes d’adresse en même temps.

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Dans la zone **Adresse IP**, modifiez l’adresse IP.
3. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

### <a name="gwipwithconnection"></a>Pour modifier l’adresse IP de la passerelle de réseau local - avec une connexion de passerelle existante

Pour modifier une passerelle de réseau local qui dispose d’une connexion, vous devez d’abord supprimer la connexion. Une fois la connexion supprimée, vous pouvez modifier l’adresse IP de la passerelle et recréer une connexion. Vous pouvez également modifier les préfixes d’adresse en même temps. Cela entraînera une interruption de votre connexion VPN. Lorsque vous modifiez l’adresse IP de la passerelle, vous n’avez pas besoin de supprimer la passerelle VPN. Vous devez uniquement supprimer la connexion.
 
#### <a name="1-remove-the-connection"></a>1. Supprimez la connexion.

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres**, cliquez sur **Connexions**.
2. Cliquez sur **...**  sur la ligne de la connexion, puis cliquez sur **Supprimer**.
3. Cliquez sur **Save** pour enregistrer vos paramètres.

#### <a name="2-modify-the-ip-address"></a>2. Modifiez l’adresse IP.

Vous pouvez également modifier les préfixes d’adresse en même temps.

1. Dans la zone **Adresse IP**, modifiez l’adresse IP.
2. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

#### <a name="3-recreate-the-connection"></a>3. Recréez la connexion.

1. Accédez à la passerelle de réseau virtuel de votre réseau virtuel. (pas la passerelle de réseau local.)
2. Sur la passerelle de réseau virtuel, dans la section **Paramètres**, cliquez sur **Connexions**.
3. Cliquez sur **+Ajouter** pour ouvrir le panneau **Ajouter une connexion**.
4. Recréez votre connexion.
5. Cliquez sur **OK** pour créer la connexion.