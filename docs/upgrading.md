# Guide de mise à jour

Ce guide précise comment mettre à jour depuis toute version antérieure à la version 1.0.

## Changements non rétro-compatibles

### Changements globaux

Dans la version 1.0, vous ne pouvez plus inclure tous les plugins avec l'instruction `ga('require', 'autotrack')`. Ce changement a été fait pour éviter que des utilisateurs n'activent accidentellement des comportements de plugins qu'il ne souhaitent pas.

Maintenant, tous les plugins autotrack doivent être requis individuellement ainsi que leurs options.

```html
<script>
window.ga=window.ga||function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date;
ga('create', 'UA-XXXXX-Y', 'auto');

// Les plugins doivent être requis individuellement
ga('require', 'eventTracker');
ga('require', 'outboundLinkTracker');
ga('require', 'urlChangeTracker');
// ...

ga('send', 'pageview');
</script>
<script async src="https://www.google-analytics.com/analytics.js"></script>
<script async src="path/to/autotrack.js"></script>
```

Dans toutes les versions 1.x, un avertissement sera affiché dans la console si vous incluez le plugin `autotrack`. Dans la version 2.0, cet avertissement sera supprimé.

### Changements individuels des plugins

#### [`mediaQueryTracker`](/docs/plugins/media-query-tracker.md)

- L'option `mediaQueryDefinitions` a été renommée en `definitions`.
- L'option `mediaQueryChangeTemplate` a été renommée en `changeTemplate`.
- L'option `mediaQueryChangeTimeout` a été renommée en `changeTimeout`.

#### `socialTracker`

- Le plugin `socialTracker` a été renommé en [`socialWidgetTracker`](/docs/plugins/social-widget-tracker.md) et ne supporte plus le tracking déclaratif d'interaction sociale (puisque cela peut être entièrement géré manuellement via le plugin [`eventTracker`](/docs/plugins/event-tracker.md)).

## Améliorations des plugins

### Améliorations globales

- Tous les plugins qui envoient des hits acceptent les deux options [`fieldsObj`](/docs/common-options.md#fieldsobj) et [`hitFilter`](/docs/common-options.md#hitfilter). Ces options peuvent être utilisées pour régler ou changer n'importe quel champ analytics.js valide avant que le hit ne soit envoyé.
- Tous les plugins qui envoient des hits à la suite d'une interaction utilisateur avec un élément du DOM permettent de [définir des valeurs de champs déclarativement](/docs/common-options.md#attributeprefix).

### Améliorations spécifiques des plugins

#### [`eventTracker`](/docs/plugins/event-tracker.md)

- Ajout du support pour le suivi déclaratif de n'importe quel évènement DOM, pas seulement les évènements `click` (par exemple `submit`, `contextmenu`, etc.)

#### [`outboundFormTracker`](/docs/plugins/outbound-form-tracker.md)

- Ajout du support pour le suivi des formulaires sans sous-arborescence shadow DOM.
- Ajout de la possibilité de personnaliser le sélecteur utilisé pour identifier les formulaires.
- Ajout de la fonction utilitaire `parseUrl` à la méthode `shouldTrackOutboundForm` pour  identifier ou exclure plus facilement les formulaires sortants.

#### [`outboundLinkTracker`](/docs/plugins/outbound-link-tracker.md)

- Ajout du support for DOM events other than `click` (e.g. `contextmenu`, `touchend`, etc.)
- Ajout du support for tracking links within shadow DOM subtrees.
- Ajout de la ability to customize the selector used to identify links.
- Ajout de la fonction utilitaire `parseUrl` à la méthode `shouldTrackOutboundLink` pour  identifier ou exclure plus facilement les liens sortants.

## Nouveaux plugins

Les nouveaux plugins suivants ont été ajoutés. Regardez leurs pages de documentation dédiées pour les détails d'utilisation.

- [`cleanUrlTracker`](/docs/plugins/clean-url-tracker.md)
- [`impressionTracker`](/docs/plugins/impression-tracker.md)
- [`pageVisibilityTracker`](/docs/plugins/page-visibility-tracker.md)
