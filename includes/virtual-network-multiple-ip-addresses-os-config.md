## <a name="a-nameos-configaadd-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Ajouter des adresses IP à un système d’exploitation de machine virtuelle

Connectez-vous à une machine virtuelle que vous avez créée à l’aide de plusieurs adresses IP privées. Vous devez ajouter manuellement toutes les adresses IP privées (y compris l’adresse principale) que vous avez ajoutées à la machine virtuelle. Effectuez les étapes suivantes dans le système d’exploitation de votre machine virtuelle :

### <a name="windows"></a>Windows

1. Tapez *ipconfig /all*à l’invite de commandes.  Seule l’adresse IP privée *principale* est visible (via DHCP).
2. Saisissez *ncpa.cpl* dans l’invite de commandes pour ouvrir la fenêtre **Connexions réseau**.
3. Ouvrez les propriétés de **Connexion au réseau local**.
4. Double-cliquez sur Internet Protocol version 4 (IPv4).
5. Cliquez sur **Utiliser l’adresse IP suivante** et entrez les valeurs suivantes :

    * **Adresse IP**: entrez l’adresse IP privée *principale* .
    * **Masque de sous-réseau**: définissez cette option en fonction de votre sous-réseau. Par exemple, si le sous-réseau est un sous-réseau /24, le masque de sous-réseau est 255.255.255.0.
    * **Passerelle par défaut**: première adresse IP du sous-réseau. Si votre sous-réseau est 10.0.0.0/24, l’adresse IP de la passerelle est 10.0.0.1.
    * Cliquez sur **Utiliser l’adresse de serveur DNS suivante** et saisissez les valeurs ci-dessous :
        * **Serveur DNS préféré** : saisissez 168.63.129.16 si vous n’utilisez pas votre propre serveur DNS.  Si vous utilisez votre propre serveur DNS, entrez l’adresse IP de votre serveur.
    * Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajoutez chacune des adresses IP privées secondaires de l’étape 8 à l’interface réseau avec le même sous-réseau que celui de l’adresse IP principale.
    * Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** à nouveau pour fermer les paramètres de la carte réseau. Votre connexion RDP est rétablie.
6. Tapez *ipconfig /all*à l’invite de commandes. Toutes les adresses IP que vous avez ajoutées sont affichées et le protocole DHCP est désactivé.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Mettez à jour le fichier de configuration de l’interface réseau (en supposant que « eth0 » est utilisé).

    * Conservez l’élément de ligne existant pour dhcp. L’adresse IP principale reste configurée telle qu’elle était précédemment.
    * Ajoutez une configuration pour une adresse IP statique supplémentaire à l’aide des commandes suivantes :

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Un fichier .cfg doit s’afficher.
4. Ouvrez le fichier : vi *nom_de_fichier*.

    Les lignes suivantes doivent figurer à la fin du fichier :

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Ajoutez les lignes suivantes après les lignes qui existent dans ce fichier :

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

7. Réinitialisez l’interface réseau à l’aide de la commande suivante :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Exécutez les scripts ifup et ifdown sur la même ligne si vous utilisez une connexion à distance.
    >

8. Vérifiez que l’adresse IP est ajoutée à l’interface réseau à l’aide de la commande suivante :

    ```bash
    Ip addr list eth0
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS, etc.)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Entrez votre mot de passe et suivez les instructions qui s’affichent. Une fois que vous êtes l’utilisateur root, accédez au dossier Scripts réseau avec la commande suivante :

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Répertoriez les fichiers ifcfg connexes à l’aide de la commande suivante :

    ```bash
    ls ifcfg-*
    ```

    Vous devez voir *ifcfg-eth0* dans la liste de fichiers.

5. Copiez le fichier *ifcfg-eth0* et nommez-le *ifcfg-eth0:0* à l’aide de la commande suivante :

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Modifiez le fichier *ifcfg-eth0:0* à l’aide de la commande suivante :

    ```bash
    vi ifcfg-eth1
    ```

7. Donnez à l’appareil le nom approprié figurant dans le fichier, en l’occurrence *eth0:0*, avec la commande suivante :

    ```bash
    DEVICE=eth0:0
    ```

8. Modifiez la ligne *IPADDR = YourPrivateIPAddress* pour y indiquer l’adresse IP.
9. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

10. Redémarrez les services réseau et vérifiez que les modifications ont été appliquées en exécutant les commandes suivantes :

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste, dans le cas présent *eth0:0*.


<!--HONumber=Dec16_HO1-->


