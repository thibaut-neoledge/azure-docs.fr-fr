<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Configuration des points de terminaison sur une machine virtuelle

**Remarque** : si vous souhaitez vous connecter à vos machines virtuelles directement par nom d'hôte ou configurer des connexions entre différents locaux, consultez la page [Vue d'ensemble des réseaux virtuels Azure][1].

Toutes les machines virtuelles créées dans Azure et appartenant au même service cloud ou réseau virtuel peuvent communiquer automatiquement entre elles à l'aide d'un canal réseau privé. Toutefois, d'autres ressources sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour traiter le trafic réseau entrant vers la machine virtuelle.

Lors de la création d'une machine virtuelle dans le portail de gestion, vous pouvez créer ces points de terminaison, comme pour le Bureau à distance, l'accès distant Windows PowerShell ou Secure Shell (SSH). Après la création de la machine virtuelle, vous pouvez créer des points de terminaison supplémentaires le cas échéant. Vous pouvez aussi gérer le trafic entrant vers le port public en configurant des règles pour la liste de contrôle d'accès (ACL, access control list) réseau du point de terminaison. Cet article présente les deux tâches.

Chaque point de terminaison possède un port public et un port privé :

* Le port privé est utilisé en interne par la machine virtuelle pour écouter le trafic sur ce point de terminaison.

* Le port public est utilisé par l'équilibrage de charge Azure pour communiquer avec la machine virtuelle à partir de ressources externes. Après la création d'un point de terminaison, vous pouvez utiliser la liste de contrôle d'accès (ACL) réseau pour définir les règles permettant d'isoler et de contrôler le trafic entrant sur le port
  public. Pour plus d'informations, consultez la page [À propos des listes de contrôle d'accès réseau][2].

Des valeurs par défaut pour les ports et le protocole de ces points de terminaison sont fournies lorsque les points de terminaison sont créés via le portail de gestion. Pour tous les autres points de terminaison, vous indiquez les ports et le protocole lorsque vous les créez. Les ressources peuvent se connecter à un point de terminaison à l'aide du protocole TCP ou UDP. Le protocole TCP inclut les communications HTTP et HTTPS.

**Important** : la configuration du pare-feu est faite automatiquement pour les ports associés au Bureau à distance et à Secure Shell (SSH) ainsi que pour l'accès distant Windows PowerShell dans la plupart des cas. Pour les ports spécifiés pour tous les autres points de terminaison, aucune configuration du pare-feu n'est effectuée automatiquement dans le système d'exploitation invité. Lors de la création d'un point de terminaison, vous devez configurer les ports appropriés dans le pare-feu pour autoriser le trafic à distribuer via le point de terminaison.

### Création d'un point de terminaison

1.  Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure][3].

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à configurer.

3.  Cliquez sur **Endpoints**. La page Points de terminaison répertorie tous les points de terminaison de la machine virtuelle.
    
    ![Points de terminaison](./media/howto-setup-endpoints/endpointswindows.png)

4.  Cliquez sur **Ajouter**.
    
    La boîte de dialogue **Add Endpoint** s'affiche. Choisissez d'ajouter ou non le point de terminaison à un jeu d'équilibrage de la charge, puis cliquez sur la flèche pour continuer.

5.  Dans **Name**, entrez le nom du point de terminaison.

6.  Comme protocole, indiquez **TCP** ou **UDP**.

7.  Dans **Public Port** et **Private Port**, entrez les numéros de port à utiliser. Ces derniers peuvent être différents. Le port public est le point d'entrée des communications provenant de l'extérieur d'Azure et il est utilisé par l'équilibrage de charge Azure. Vous pouvez utiliser le port privé et des règles de pare-feu sur la machine virtuelle pour rediriger le trafic de façon pertinente pour votre application.

8.  Cliquez sur **Create a load-balancing set** si ce point de terminaison est le premier d'un jeu d'équilibrage de charge. Puis, sur la page **Configure the load-balanced set**, spécifiez un nom, un protocole et les détails de la sonde. Une sonde est requise pour surveiller l'état des jeux d'équilibrage de charge. Pour plus d'informations, consultez la page [Équilibrage de charge des machines virtuelles][4].

9.  Cliquez sur la coche pour créer le point de terminaison.
    
    Le point de terminaison est répertorié sur la page **Endpoints**.
    
    ![Création du point de terminaison réussie](./media/howto-setup-endpoints/endpointwindowsnew.png)

### Gestion de l'ACL sur un point de terminaison

Suivez cette procédure pour ajouter, modifier ou supprimer l'ACL sur un point de terminaison.

**Remarque** : si le point de terminaison fait partie d'un jeu d'équilibrage de charge, chaque modification faite sur l'ACL pour un point de terminaison est appliquée à tous les points de terminaison du jeu.

1.  Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure][3].

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à configurer.

3.  Cliquez sur **Endpoints**. La page Points de terminaison répertorie tous les points de terminaison de la machine virtuelle.
    
    ![Liste ACL](./media/howto-setup-endpoints/EndpointsShowsDefaultEndpointsForVM.PNG)

4.  Sélectionnez le point de terminaison approprié dans la liste.

5.  Cliquez sur **Manage ACL**.
    
    La boîte de dialogue **Specify ACL details** s'affiche.
    
    ![Spécifier les détails de l’ACL](./media/howto-setup-endpoints/EndpointACLdetails.PNG)

6.  Utilisez les lignes dans la liste pour ajouter, supprimer ou modifier les règles d'une ACL. La valeur Sous-réseau distant correspond à la plage d'adresses IP que la règle peut autoriser ou refuser. Les règles sont évaluées dans l'ordre, en commençant par la première règle et en terminant par la dernière. Cela signifie que les règles doivent être répertoriées de la moins restrictive à la plus restrictive. Pour plus d'informations et des exemples, consultez la page [À propos des listes de contrôle d'accès réseau][2].



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkId=303816
[3]: http://manage.windowsazure.com
[4]: http://www.windowsazure.com/fr-fr/manage/windows/common-tasks/how-to-load-balance-virtual-machines/