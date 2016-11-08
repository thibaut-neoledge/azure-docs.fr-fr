1. Dans le portail, accédez à **Nouveau**, puis sélectionnez **Parcourir**. Sélectionnez **Passerelles de réseau virtuel** dans la liste.
2. Cliquez sur **Add**.
3. Nommez votre passerelle. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle. 
4. Dans **Réseau virtuel**, sélectionnez le réseau virtuel à connecter à cette passerelle.
5. Dans les paramètres du réseau virtuel, donnez un nom à votre adresse IP publique dans **Adresse IP publique**. Notez qu’il ne vous est pas demandé d’entrer une adresse IP, celle-ci étant affectée dynamiquement. Vous devez plutôt entrer le nom de l’objet d’adresse IP auquel l’adresse sera affectée. 
6. Dans **Type de réseau privé virtuel**, les choix proposés sont basés sur la stratégie et l’itinéraire. Veillez à sélectionner le type de passerelle VPN pris en charge par le scénario de configuration et, si votre configuration l’exige, par le périphérique de passerelle VPN que vous prévoyez d’utiliser.
7. Dans **Groupe de ressources**, choisissez **Sélectionner** et indiquez le groupe de ressources dans lequel réside votre réseau virtuel, à moins que votre configuration ne nécessite un autre choix.
8. Dans **Emplacement**, vérifiez que l’emplacement dans lequel se trouvent votre groupe de ressources et votre réseau virtuel est indiqué.
9. Cliquez sur **Create**. La vignette *Déploiement d’une passerelle de réseau virtuel* s’affiche dans le tableau de bord. La création d’une passerelle prend du temps, car de nombreuses opérations sont réalisées en arrière-plan. Prévoyez 15 minutes ou plus. Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.
10. Une fois la passerelle créée, examinez Réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît comme un appareil connecté. Le nom et l’adresse IP affectés à la passerelle sont affichés.

<!---HONumber=AcomDC_0114_2016-->