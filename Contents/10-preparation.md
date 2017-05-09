# Mise en place de l'environnement de travail {#ddui-qs:43ec59c9-4460-4981-81d4-ffc475843c24}

## Environnement d'exécution {#ddui-qs:4e94e0c3-332f-460a-8c77-221e33b1d239}

Pour dérouler ce tutoriel, il est nécessaire d'avoir un contexte dynacase.

Ce contexte peut être obtenu de 3 manières différentes.

### Virtualbox {#ddui-qs:2e9ea755-7177-407f-9f79-d9f7e39cacd5}

Une appliance virtualbox est disponible [sur notre site][virtualbox-appliances] : `dynacase-quick-start-ddui.ova`.
Il suffit de l'importer et de la démarrer.

<span class="flag inline nota-bene"></span> L'appliance mappe le port 80 de l'invité sur le port 8082 de l'hôte.

### Autre {#ddui-qs:7351b98e-5b10-46ed-a120-e165cba71d99}

Suivre le [manuel d'installation de Dynacase][man-inst] pour créer un environnement de votre choix.

## Environnement de développement {#ddui-qs:3a95f4ae-6bb8-4a72-a95f-9faa5ec91d18}

Pour suivre ce tutoriel, il vous faudra

-   les sources de l'application
    
    Ces sources sont disponibles sur le [compte github d'Anakeen][tuto-sources].
    Chaque étape du tutoriel est disponible sous la forme d'un tag, et pourra être téléchargée directement
    depuis [la page des releases][tuto-sources-releases].
    
-   les devtools
    
    +   Pour linux :
        
        le _developer toolkit_ est distribué sous la forme d'un phar téléchargeable [ici][devtools-linux].
        Il est ensuite utilisable avec votre interpréteur php (`php dynacase-devtool.phar`).
        
    +   Pour Windows :
        
        le _developer toolkit_ est distribué sous la forme d'une archive téléchargeable [ici][devtools-windows].
        Une fois dézippé, vous obtenez un fichier `dynacase-devtool.bat` qui doit être exécuté via la console windows.

<span class="flag inline nota-bene"></span> pour plus de détails sur le _developer toolkit_,
se reporter au [manuel de référence][devtools-ref]

## Accès aux sources {#ddui-qs:2979b656-db6f-47af-b3c1-92bc7ed5bf00}

Les sources de ce tutoriel sont [sur github][gh_sources].

Au cours du tutoriel, les fichiers modifiés pourront être téléchargés directement depuis github via un lien.

De plus, chaque étape est identifiée par un tag.
Vous pouvez accéder à ce tag au moyen de git si vous avez cloné le dépot,
ou vous pouvez télécharger directement une archive pour chaque étape depuis github via un lien.

<!-- links -->

[devtools-linux]:           https://eec.anakeen.com/public/tools/development/dynacase-devtool.phar
[devtools-ref]:             #devtools:
[devtools-windows]:         https://eec.anakeen.com/public/tools/development/dynacase-devtool-win32.zip
[gh_sources]:               https://github.com/Anakeen/dynacase-ddui-quickstart-code
[man-inst]:                 #manex-ref: "Installation & Exploitation"
[tuto-sources]:             https://github.com/Anakeen/dynacase-ddui-quickstart-code
[tuto-sources-releases]:    https://github.com/Anakeen/dynacase-ddui-quickstart-code/releases
[virtualbox-appliances]:    https://eec.anakeen.com/public/tools/appliances/
