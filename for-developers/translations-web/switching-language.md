# Switching language

To switch between languages simply update `language` value inside `AppStore` state. If the value set is not corresponding to an available translation declared in `Locales` type, it will fall back into `en` translation.

This behavior is controlled by the effect inside `AppLoader` component.

