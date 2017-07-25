
Chaque point de terminaison possède un *port public* et un *port privé* :

* Le port public est utilisé par l'équilibreur de charge Azure pour écouter le trafic entrant dans la machine virtuelle à partir d'Internet.
* Le port privé est utilisé par la machine virtuelle pour écouter le trafic entrant, généralement destiné à une application ou à un service en cours d'exécution sur la machine virtuelle.

Les valeurs par défaut pour le protocole IP et les ports TCP ou UDP pour des protocoles réseau bien connus sont fournies lorsque vous créez des points de terminaison avec le portail Azure. Pour les points de terminaison personnalisés, vous devrez spécifier le protocole IP correct (TCP ou UDP) et les ports publics et privés. Pour distribuer le trafic entrant au hasard entre plusieurs machines virtuelles, vous devrez créer un jeu d'équilibrage de la charge composé de plusieurs points de terminaison.

Après avoir créé un point de terminaison, vous pouvez utiliser une liste de contrôle d’accès (ACL) pour définir des règles permettant d’autoriser ou de refuser le trafic entrant vers le port public du point de terminaison en fonction de son adresse IP source. Toutefois, si la machine virtuelle se trouve dans un réseau virtuel Azure, vous devez utiliser à la place les groupes de sécurité réseau. Pour plus d'informations, consultez [À propos des groupes de sécurité réseau](../articles/virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> La configuration du pare-feu pour les machines virtuelles Azure s’effectue automatiquement pour les ports associés aux points de terminaison de connectivité à distance qu’Azure configure automatiquement. Pour les ports spécifiés pour tous les autres points de terminaison, aucune configuration n'est effectuée automatiquement pour le pare-feu de la machine virtuelle. Lorsque vous créez un point de terminaison pour la machine virtuelle, vous devez vous assurer que le pare-feu de la machine autorise également le trafic du protocole et le port privé correspondant à la configuration du point de terminaison. Pour configurer le pare-feu, consultez la documentation ou l’aide en ligne du système d’exploitation utilisé sur la machine virtuelle.
>
>

## <a name="create-an-endpoint"></a>Création d’un point de terminaison
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Machines virtuelles**, puis sur le nom de la machine virtuelle à configurer.
3. Dans le groupe **Paramètres**, cliquez sur **Points de terminaison**. La page **Points de terminaison** répertorie tous les points de terminaison actuels de la machine virtuelle. (Cet exemple est une machine virtuelle Windows. Par défaut, une machine virtuelle Linux affiche un point de terminaison pour SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. Dans la barre de commandes au-dessus des entrées de point de terminaison, cliquez sur **Ajouter**.
5. Sur la page **Ajouter un point de terminaison**, tapez le nom du point de terminaison dans **Nom**.
6. Dans **Protocole**, choisissez **TCP** ou **UDP**.
7. Dans **Port public**, tapez le numéro de port pour le trafic entrant depuis Internet. Dans **Port privé**, tapez le numéro de port que la machine virtuelle écoute. Ces derniers peuvent être différents. Assurez-vous que le pare-feu sur la machine virtuelle a été configuré pour autoriser le trafic correspondant au protocole (à l’étape 6) et au port privé.
10. Cliquez sur **OK**.

Le nouveau point de terminaison est répertorié dans la page **Points de terminaison** .

![Création du point de terminaison réussie](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Gestion de l’ACL sur un point de terminaison
Pour définir l'ensemble des ordinateurs qui peuvent envoyer du trafic, l'ACL sur un point de terminaison peut restreindre le trafic basé sur l'adresse IP source. Suivez cette procédure pour ajouter, modifier ou supprimer l’ACL sur un point de terminaison.

> [!NOTE]
> si le point de terminaison fait partie d’un jeu d’équilibrage de charge, chaque modification faite sur l’ACL pour un point de terminaison est appliquée à tous les points de terminaison du jeu.
>
>

Si la machine virtuelle se trouve dans un réseau virtuel Azure, nous vous recommandons d’utiliser les groupes de sécurité réseau à la place des ACL. Pour plus d'informations, consultez [À propos des groupes de sécurité réseau](../articles/virtual-network/virtual-networks-nsg.md).

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure.
2. Cliquez sur **Machines virtuelles**, puis sur le nom de la machine virtuelle à configurer.
3. Cliquez sur **Endpoints**. Sélectionnez le point de terminaison approprié dans la liste. La liste ACL se trouve en bas de la page.

   ![Spécifier les détails de l’ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Utilisez les lignes de la liste pour ajouter, supprimer ou modifier les règles d'une ACL et modifier leur ordre. La valeur du **Sous-réseau distant** est une plage d’adresses IP pour le trafic entrant depuis Internet que l’équilibreur de charge Azure utilise pour autoriser ou refuser le trafic en fonction de son adresse IP source. Veillez à spécifier la plage d'adresses IP au format CIDR, également connu sous le nom de format de préfixe adresse. Par exemple `10.1.0.0/8`.

 ![Nouvelle entrée ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Vous pouvez utiliser des règles pour autoriser uniquement le trafic provenant de certains ordinateurs correspondant à vos ordinateurs sur Internet ou refuser le trafic provenant de plages d'adresses spécifiques et connues.

Les règles sont évaluées dans l’ordre, en commençant par la première règle et en terminant par la dernière. Cela signifie que les règles doivent être répertoriées de la moins restrictive à la plus restrictive. Pour plus d’informations et obtenir des exemples, consultez [Qu’est-ce qu’une liste de contrôle d’accès réseau ?](../articles/virtual-network/virtual-networks-acl.md).
