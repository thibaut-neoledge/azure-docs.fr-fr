<!--author=SharS last changed: 9/17/15-->

#### Pour installer des mises à jour périodiques via Windows PowerShell pour StorSimple

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Saisissez le mot de passe. Le mot de passe par défaut est *Password1*. 

2.  À l’invite de commandes, tapez :

     `Get-HcsUpdateAvailability`
    
    Le système vous indique si des mises à jour sont disponibles et si elles risquent ou non de provoquer une interruption de service.

3.  À l’invite de commandes, tapez :

     `Start-HcsUpdate`

    La mise à jour démarre.

> [AZURE.IMPORTANT]
>
> - Cette commande s’applique uniquement aux mises à jour périodiques. Vous exécutez cette commande sur un seul contrôleur, mais les deux contrôleurs sont mis à jour. 
> - Il est possible qu’un basculement de contrôleur se produise pendant la mise à jour. Celui-ci n’a aucune incidence sur la disponibilité ou le fonctionnement du système.

<!---HONumber=Sept15_HO4-->