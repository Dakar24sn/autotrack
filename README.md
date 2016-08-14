# Autotrack [![Build Status](https://travis-ci.org/googleanalytics/autotrack.svg?branch=master)](https://travis-ci.org/googleanalytics/autotrack)

- [Aperçu](#aperçu)
- [Plugins](#plugins)
- [Installation et utilisation](#installation-et-utilisation)
  - [Charger autotrack via npm](#charger-autotrack-via-npm)
  - [Passer des options de configuration](#passer-des-options-de-configuration)
- [Configuration avancée](#configuration-avancée)
  - [Paquets personnalisés](#paquets-personnalisés)
  - [Utiliser autotrack avec de multiples trackers](#utiliser-autotrack-avec-de-multiples-trackers)
- [Support navigateur](#support-navigateur)
- [Traduction](#traduction)

## Aperçu

Le [code de suivi JavaScript](https://developers.google.com/analytics/devguides/collection/analyticsjs/) par défaut pour Google Analytics se lance lors du premier chargement d'une page web et envoie un signal de page vue à Google Analytics. Si vous voulez en savoir plus que simplement le nombres de pages vues (par exemple les évènements, les interactions sociales), vous allez devoir écrire du code pour récupérer ces informations vous-même.

Puisque la majorité des propriétaires de site se soucient des mêmes types d'interactions utilisateurs, les développeurs web se retrouvent à écrire encore et encore le même code  pour chaque nouveau site qu'ils bâtissent.

Autotrack a été crée pour résoudre ce problème. Il fournit un suivi par défaut pour les interactions dont se soucient la plupart des gens et il propose plusieurs fonctionnalités commodes (comme le suivi d'évènement déclaratif) pour comprendre plus facilement que jamais comment les gens utilisent votre site.

## Plugins

La bibliothèque `autotrack.js` est légère (6K gzippée) et inclus les plugins suivants. Par défaut tous les plugins sont fournis ensemble mais il peuvent être inclus et configurés également de manière individuelle. Ce tableau comporte une brève description de chaque plugin; vous pouvez cliquer sur le nom du plugin pour consulter la documentation complète et les instructions d'utilisation:

<table>
  <tr>
    <th align="left">Plugin</th>
    <th align="left">Description</th>
  </tr>
  <tr>
    <td><a href="/docs/plugins/clean-url-tracker.md"><code>cleanUrlTracker</code></a></td>
    <td>Assure la cohérence des chemins d'URL qui sont envoyés à Google Analytics; corrige le problème où des lignes distinctes dans vos rapports de pages pointent en fait vers la même page.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/event-tracker.md"><code>eventTracker</code></a></td>
    <td>Active le suivi d'évènement déclaratif à l'aide d'attributs HTML dans les balises.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/impression-tracker.md"><code>impressionTracker</code></a></td>
    <td>Vous permet de suivre lorsque des éléments sont visibles dans le viewport.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/media-query-tracker.md"><code>mediaQueryTracker</code></a></td>
    <td>Permet le suivi des media queries correspondantes et des changements de media queries.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/outbound-form-tracker.md"><code>outboundFormTracker</code></a></td>
    <td>Suivi automatique les soumissions de formulaire vers des domaines externes.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/outbound-link-tracker.md"><code>outboundLinkTracker</code></a></td>
    <td>Suivi automatique des clics sur les liens vers des domaines externes.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/page-visibility-tracker.md"><code>pageVisibilityTracker</code></a></td>
    <td>Suivi des changements d'état de visibilité de page, qui permet des indicateurs de session, de durée de session et de métriques de pages vues bien plus pertinents.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/social-widget-tracker.md"><code>socialWidgetTracker</code></a></td>
    <td>Suivi automatique des interactions avec les widgets officiels de Facebook et Twitter.</td>
  </tr>
  <tr>
    <td><a href="/docs/plugins/url-change-tracker.md"><code>urlChangeTracker</code></a></td>
    <td>Suivi automatique des changements d'URL pour les applications sur une seule page.</td>
  </tr>
</table>

**Avertissement :** autotrack est maintenu par des membres de l'équipe de la plateforme de développement de Google Analytics et s'adresse en priorité à un public de développeurs. Ce n'est pas un produit officiel de Google Analytics et ne donne pas droit au support Google Analytics 360. Les développeurs qui choisissent cette bibliothèque sont responsables de s'assurer que leur implémentation respecte les pré-requis des [conditions générales de Google Analytics](https://www.google.com/analytics/terms/fr.html) et les obligations légales de leur pays respectif.

## Installation et utilisation

Pour ajouter autotrack sur votre site, vous devez faire deux choses :

1. Charger le script `autotrack.js` sur votre page
2. Mettre à jour le [code de suivi](https://developers.google.com/analytics/devguides/collection/analyticsjs/tracking-snippet-reference) pour [inclure](https://developers.google.com/analytics/devguides/collection/analyticsjs/using-plugins) les différents plugins autotrack que vous souhaitez utiliser.

Si votre site inclus déjà le code de suivi JavaScript par défaut, vous pouvez le modifier pour ressembler à quelque chose comme ça :

```html
<script>
window.ga=window.ga||function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date;
ga('create', 'UA-XXXXX-Y', 'auto');

// Remplacez les lignes suivantes avec les plugins que vous souhaitez utiliser.
ga('require', 'eventTracker');
ga('require', 'outboundLinkTracker');
ga('require', 'urlChangeTracker');
// ...

ga('send', 'pageview');
</script>
<script async src='https://www.google-analytics.com/analytics.js'></script>
<script async src='path/to/autotrack.js'></script>
```

Bien évidemment, vous aurez à faire les modifications suivantes pour adapter autotrack à vos besoins:

- Remplacer `UA-XXXXX-Y` par votre [ID de tracking](https://support.google.com/analytics/answer/1032385)
- Remplacer La liste d'exemple de déclarations `require` de plugin avec les plugins que vous souhaitez utiliser.
- Remplacer `path/to/autotrack.js` par le véritable emplacement du fichier `autotrack.js` hébergé sur votre serveur.

**Note :** le [système de plugin analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/using-plugins) est conçu pour le support des scripts chargés de manière asynchrone, donc cela ne fait rien si `autotrack.js` est chargé avant ou après `analytics.js`. Cela est égal aussi si la bibliothèque `autotrack.js` est chargé seule ou empaquetée avec le reste de votre code Javascript.

### Charger autotrack via npm

Si vous utilisez nom et un chargeur de module comme [Browserify](http://browserify.org/), [Webpack](https://webpack.github.io/) ou [SystemJS](https://github.com/systemjs/systemjs), vous pouvez inclure autotrack dans votre build en l'incluant comme vous feriez avec n'importe quel autre module npm :

```sh
npm install autotrack
```

```js
// Dans votre code JavaScript
require('autotrack');
```

Le code ci-dessus va inclure tous les plugins dans votre fichier source généré. Si vous souhaitez seulement inclure un ensemble particulier de plugins, vous pouvez les inclure de manière individuelle :

```js
// Dans votre code JavaScript
require('autotrack/lib/plugins/clean-url-tracker');
require('autotrack/lib/plugins/outbound-link-tracker');
require('autotrack/lib/plugins/url-change-tracker');
// ...
```

Les exemples ci-dessus montrent comment inclure le code source du plugin dans votre fichier JavaScript généré, qui

The above examples show how to include the plugin source code in your final, generated JavaScript file, qui réalise la première étape du procédé d'installation en deux étapes.

Vous devez encore mettre à jour votre code de suivi et inclure les plugins que vous souhaitez utiliser :

```js
// Dans le code de suivi analytics.js
ga('create', 'UA-XXXXX-Y', 'auto');

// Replace the following lines with the plugins you want to use.
ga('require', 'cleanUrlTracker');
ga('require', 'outboundLinkTracker');
ga('require', 'urlChangeTracker');
// ...

ga('send', 'pageview');
```

**Note:** Attention à ne pas confondre le [`require`](https://nodejs.org/api/modules.html) de la déclaration d'inclusion du module node avec l'instruction [`require`](https://developers.google.com/analytics/devguides/collection/analyticsjs/command-queue-reference#require) du `analytics.js`. Lors du chargement d'autotrack à l'aide d'un chargeur de module npm, les deux instructions `require` doivent être utilisées.

### Passer des options de configuration

Tous les plugins autotrack acceptent un objet de configuration comme troisième paramètre à la commande `require`.

Certains des plugins (par exemple `outboundLinkTracker`, `socialWidgetTracker`, `urlChangeTracker`) ont un comportement par défaut valable pour la plupart des gens sans spécifier d'options. D'autres plugins (par exemple `cleanUrlTracker`, `impressionTracker`, `mediaQueryTracker`) nécessitent que certaines options de configuration soit définies pour pouvoir fonctionner.

Reportez vous à la documentation spécifique de chaque plugin pour connaître les options acceptées par chaque plugin (et quelle est leur valeur par défaut s'il y en a une).

## Configuration avancée

### Paquets personnalisés

La librairie autotrack est construite de façon modulaire et chaque plugin inclus ses propres dépendances, donc vous pouvez créer un paquet personnalisé en utilisant un empaqueteur de script comme [Browserify](http://browserify.org/).

L'exemple suivant montre comment créer un paquet qui inclus seulement les plugins `eventTracker` et `outboundLinkTracker` :

```sh
browserify lib/plugins/event-tracker lib/plugins/outbound-link-tracker
```

Lors de la génération d'un paquet personnalisé, assurez vous de mettre à jour le code de suivi pour n'inclure que les plugins présents dans votre paquet. Inclure un plugin qui n'est pas présent dans votre paquet va créer une dépendance non satisfaire, ce qui empêchera les commandes ultérieures de fonctionner.

Si vous utilisez déjà un chargeur de module comme [Browserify](http://browserify.org/), [Webpack](https://webpack.github.io/) ou [SystemJS](https://github.com/systemjs/systemjs) pour générer votre JavaScript, vous pouvez sauter l'étape ci-dessus et simplement inclure les plugins comme décrit dans la section [Charger autotrack via npm](#loading-autotrack-via-npm) section.

### Utiliser autotrack avec de multiples trackers

Tous les plugins autotrack supportent les _tracker_ multiples et fonctionnent en spécifiant le nom du _tracker_ dans la commande `require`. L'exemple suivant crée deux _tracker_ et inclus différents plugins autotrack pour chacun.

```js
// Crée deux trackers, un nommé `tracker1` et un nommé `tracker2`.
ga('create', 'UA-XXXXX-Y', 'auto', 'tracker1');
ga('create', 'UA-XXXXX-Z', 'auto', 'tracker2');

// Plugins inclus pour le tracker1.
ga('tracker1.require', 'eventTracker');
ga('tracker1.require', 'socialWidgetTracker');

// Plugins inclus pour le tracker2.
ga('tracker2.require', 'eventTracker');
ga('tracker2.require', 'outboundLinkTracker');
ga('tracker2.require', 'pageVisibilityTracker');

// Envoie le pageview initial de chaque tracker.
ga('tracker1.send', 'pageview');
ga('tracker2.send', 'pageview');
```

## Support navigateur

Autotrack va fonctionner en toute sécurité et sans erreur dans un navigateur, car la détection de fonctionnalité est toujours utilisée pour tout code potentiellement non supporté. Toutefois, autotrack ne va détecter les fonctionnalités supportés que dans le navigateur qui l'utilise. Par exemple, un utilisateur qui utilise Internet Explorer 9 ne pourra pas suivre l'utilisation des media queries, car les medias queries elles-mêmes ne sont pas supportées par Internet Explorer 8.

Tous les plugins autotrack sont [testés via Sauce Labs](https://saucelabs.com/u/autotrack) dans les navigateurs suivants :

<table>
  <tr>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/chrome/chrome_48x48.png" alt="Chrome"><br>
      ✔
    </td>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/firefox/firefox_48x48.png" alt="Firefox"><br>
      ✔
    </td>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/safari/safari_48x48.png" alt="Safari"><br>
      6+
    </td>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/edge/edge_48x48.png" alt="Edge"><br>
      ✔
    </td>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/internet-explorer/internet-explorer_48x48.png" alt="Internet Explorer"><br>
      9+
    </td>
    <td align="center">
      <img src="https://raw.github.com/alrra/browser-logos/master/opera/opera_48x48.png" alt="Opera"><br>
      ✔
    </td>
  </tr>
</table>

## Traduction

Cette traduction française de la documentation d'[autotrack de Google Analytics](https://github.com/googleanalytics/autotrack) n'est pas officielle et peut être inexacte ou plus à jour.

_Dernière mise à jour : 14 Août 2016_

Si vous avez une correction à soumettre pour améliorer cette traduction, merci d'[ouvrir une issue](/issues) dans ce dépôt.
