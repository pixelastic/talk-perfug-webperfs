Notes en vrac pour le plan :

Liste des systèmes en SaaS pour tester les connections
Webpage test et grunt pour automatiser
Regarder tache de Gaël Metais pour garder historique des perfs

Mettre le CSS en haut, pour permettre le plus rapidement possible au browser de
pouvoir faire un reflow de la page. Si micro optim, on peut ajouter du CSS
inline pour par exemple mettre des placeholders du header ou de la sidebar,
avec les bonnes couleurs et avoir un startRender très rapide.
Voir la conf du Guardian à la takeOff pour ça

Mettre le Javascript en bas, car le téléchargement du javascript bloque
l'execution. Pas complétement vrai aujourd'hui, car le js peut se charger de
manière asynchrone, mais nécessaire quand même qu'il ne bloque pas l'exce.

Prioriser ce qui doit se charger, par exemple les boutons de sharing, le
tracking des visites et la publicité doit arriver en dernier. Pillule difficile
à faire avaler au dessus, mais il faut bien montrer que le contenu est le plus
important (cf Guardian encore une fois). 

Il faut éviter au maximum le document.write, qui va changer complétement le
contenu de la page et donc frocément causer des reflow/repaint. Ca arrive
souvent avec les publivicités. Cc'est dur, car on ne sait jamais trop ce qui
est chargé par la régie externe.

Pas nécessaire de ccharger un gros framework pour peu d'anim. On peut faire du
vanillaJs si c'est juste afficher/masquer des éléments, et la plupart du temps
on peut même le faire en CSS, ce qui est encore plus rapide.
Il existe des micro-frameworks pour pas mal de taches, ou des builds sur mesure
pour éviter de charger tout Bootstrap ou jquer-ui

Si chargement de fonts, on peut ne sélectionner que les caractères nécessaires.
On peut virer les versions bolds et italique si pas nécessaire non plus. Et si
besoin, il est toujours aussi possible de les ajouter en base64 dans le CSS.

Principalement, il faut limiter le nombre de requetes. 1 pour le JS, 1 pour el
CSS. Si meilleure optim, 2 pour chaque, le principal d'abord, le secondaire
ensuite.


Plan de la prés : 
1/ pourquoi optimiser, les chiffres habituels sur l'influence sur le CA, et sur
le temps d'attente moyen
2/ optimisation coté serveur, on n'y gagne pas grand chose, on regarde le cycle
de requetes, et on essaie de mettre du cache, du gzip, de la concaténation, du
CDN
3/ on regarde coté client, en CSS ou en Javascript, comment le navigateur
interprete tout ça et ce qu'il ne faut pas faire
4/ finalement on regarde du coté de l'humain, comment on peut lui donner la
sensation que les choses vont vite

coté serveyr donc
On mets du cache sur les assets statiques. On peut gérer le cache avec
plusieurs headers. Inutile de rentrer dans le détail, juste les présenter
(pourquoi deux, lequel utiliser, Etag, etc). Comment invalider le cache ? Très
difficile à faire avec les headers, il vaut mieux simplement changer l'url.
Soit ajouter un timestamp dans l'url, ou si le fichier est généré
automatiquement, le md5 prends en compte la date de création.

On concatene ensuite les assets, surtout css et javascript. On peut aussi
concatener les images pour faire des sprites. Finalement, pour encore
économiser des requetes, on peut faire du base64 dans les fichiers CSS pour
à peu près tout. Rajouter la compatibilité navigateur, et les taches grunt qui
vont bien.

ON enchaine sur le GZIP qui permet de gagner beaucoup sur les fichiers textes,
surtout s'ils ont des patterns qui se repetent. D'où micro optim, on mets
toujours les règles CSS dans le même sens, ou les attributs HTML. On ne gzippe
pas les images, on utilise pour ça les algos de compression des images
directement. On choisit le bon format d'image. Jpeg pour les photos, png pour
la transparence (pas gif, qui n'a qu'un seul niveau de couleur). A la rigueur
gif pour un gif animé mais... wtf ?

Ensuite dans le browser, on parle du navigateur, de la manière dont il parse
les fichiers CSS, depuis la droite jusque la gauche, et donc comment faire des
règles CSS optimisées. Inutile de rentrer dans le détail, mais juste ne pas
utiliser les selecteurs universaux ou des selecteurs d'attributs à base de
regexp si possible.

On peut comment par parler du html, et de limiter le nombre de balises, c'est
pas énorme, mais ça aide quand mee un poiol.

Comment le naviagetru fait du rendering, quelle est la différence entre un
reflow et un repaint. repaint il doit tour recalculer, reflow il ne doit que
changer une couleur mais pas réorganiser les blocs.

on peut donc enchainer sur javascript, et si on doit changer des éléments HTML,
on ne les change pas à chaque tour de boucle, on mets un ééments DOM en mémoire
et on l'injecte en batch ensuyite pour éviter els repaints.

On peut ensuite parler du reste de ce qui consomme en Javascript, mais ce qui
consoimme le plus en général c'est l'injection dans le dom. Ou alors d'ajouter
des évenement au clic sur plein d'élément, alors qu'on peut utiliser le
bublling à bon escient pour n'en binder qu'un seul et donc plus facilement
pouvoir catcher les events.

On peut rapidement parler des différences GPU/CPU et de qui fait quoi et ce
qu'il ne faut pas faire, et renvoyer vers le talk de la takeoff.

Finalmeent, on parle de la preception utilisateur. Qu'il ets mieux de mettre un
placeholder pour dire qu'il se passe quelque chose, même si c'est pas encore
utilisable. Que l'oeil humain ne capte pas la vitesse en dessous de 300ms, donc
inutile de metre un spinner si en dessous, ça donnera la sensation que plus
lent, mais sinon le mettre. Eviter les spinner qui donnent une fausse
indication que ça va se terminer. Et ensuite, pas bloquer toute la page quand
ça charge, permettre l'accès à un menu pour faire uen autre action, pas obligé
de killer la page et que l'utilisateur se barre. Enfin, parle de Facebook qui
utilise le spinner de l'OS pour faire croire que c'est pas de sa faute si c'est
lent.




Utiliser une identité visuelle marrante pour lier tout ça. Les maisons GoT
? (Tyrion pour la compression). Ou BSG. Ou Bref. Utiliser le flying step pour
la compression gzip. Utiliser la métaphore du client/serveur (épisode du resto)
pour "il m'a regardé, je l'ai regardé" pour les échanges de requetes.


