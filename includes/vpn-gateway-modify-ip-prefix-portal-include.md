### <a name="noconnection"></a>Pour modifier des préfixes d’adresses IP de passerelle de réseau local - sans connexion de passerelle

#### <a name="to-add-additional-address-prefixes"></a>Pour ajouter des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Ajoutez l’espace d’adressage IP dans la zone *Ajouter une autre plage d’adresses*.
3. Cliquez sur **Save** pour enregistrer vos paramètres.

#### <a name="to-remove-address-prefixes"></a>Pour supprimer des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Cliquez sur **'...'** sur la ligne contenant le préfixe à supprimer.
3. Cliquez sur **Supprimer**.
4. Cliquez sur **Save** pour enregistrer vos paramètres.

### <a name="withconnection"></a>Pour modifier des préfixes d’adresses IP de passerelle de réseau local - avec une connexion de passerelle existante

Si vous disposez d’une connexion de passerelle et que vous souhaitez ajouter ou supprimer des préfixes d’adresses IP contenues dans votre passerelle de réseau local, vous devez suivre les étapes suivantes dans l’ordre. Cela entraînera une interruption de votre connexion VPN. Lorsque vous modifiez des préfixes d’adresses IP, vous n’avez pas besoin de supprimer la passerelle VPN. Vous devez uniquement supprimer la connexion.

#### <a name="1-remove-the-connection"></a>1. Supprimez la connexion.

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres**, cliquez sur **Connexions**.
2. Cliquez sur **...**  sur la ligne pour chaque connexion, puis cliquez sur **Supprimer**.
3. Cliquez sur **Save** pour enregistrer vos paramètres.

#### <a name="2-modify-the-address-prefixes"></a>2. Modifiez les préfixes d’adresse.

Pour ajouter des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Ajoutez l’espace d’adressage IP.
3. Cliquez sur **Save** pour enregistrer vos paramètres.

Pour supprimer des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Cliquez sur **...** sur la ligne contenant le préfixe que vous souhaitez supprimer.
3. Cliquez sur **Supprimer**.
4. Cliquez sur **Save** pour enregistrer vos paramètres.

#### <a name="3-recreate-the-connection"></a>3. Recréez la connexion.

1. Accédez à la passerelle de réseau virtuel de votre réseau virtuel. (pas la passerelle de réseau local.)
2. Sur la passerelle de réseau virtuel, dans la section **Paramètres**, cliquez sur **Connexions**.
3. Cliquez sur **+Ajouter** pour ouvrir le panneau **Ajouter une connexion**.
4. Recréez votre connexion.
5. Cliquez sur **OK** pour créer la connexion.