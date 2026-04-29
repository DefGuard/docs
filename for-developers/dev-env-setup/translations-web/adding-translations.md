---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/for-developers/dev-env-setup/translations-web/adding-translations
---

# Adding translations

To add a new language you need to create a new folder to _**i18n/{new language code}**_ with **index.ts** file. For a reference you can check `pl` or `ko` translation folders.

### Types generation

#### Adding a new language

If you add a new translation make sure you run types generation command:

```sh
pnpm run generate-translation-types
```

This will ensure the translation is properly detected.

### Partial translations

We don't expect all translations to be always in sync with latest changes because they are an community effort, so even if UI is displaying an translation if anything is out of sync with base EN then the missing key will fallback to an EN version instead. In order for it to work the translation object needs to be merged with EN before export. Follow `pl` or `ko` translation folders for reference.

{% hint style="warning" %}
Make sure the newly added translation object has Translation type assigned to it in order to ensure it's linted properly, this helps to keep it in sync with base EN translation.
{% endhint %}
