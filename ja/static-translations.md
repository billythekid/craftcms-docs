# 静的メッセージの翻訳

ほとんどのウェブサイトやアプリには、テンプレートや PHP ファイルにハードコードされたいくつかの UI メッセージを持ちます。CMS のコンテンツによって動的に定義されていないため、それらは「静的メッセージ」と呼ばれます。

多言語のサイトやアプリを構築している場合、CMS 主導のコンテンツのようにそれらのメッセージも翻訳可能にする必要があるでしょう。

そのため、Craft は Yii の[メッセージ翻訳](https://www.yiiframework.com/doc/guide/2.0/en/tutorial-i18n#message-translation)機能を採用し、 特別な翻訳カテゴリを事前に定義しています。

- `site` はプロジェクトに属するメッセージに使用されます。
- `app` はコントロールパネルのメッセージに使用されます。
- それぞれのプラグインは、プラグインのハンドルに基づいて独自のカテゴリも取得します。

## メッセージの準備

最初のステップは、すべての静的メッセージをトランスレータを通して実行することです。テンプレートを操作している場合、[translate](dev/filters.md#translate-or-t) フィルタ（`|t`）を使用します。PHP コードを操作している場合、[Craft::t()](api:yii\BaseYii::t()) を使用します。

::: code
```twig
{# old #}
<a href="/contact">Contact us</a>

{# new #}
<a href="/contact">{{ 'Contact us'|t }}</a>
```
```php
// old
$label = 'Contact us';

// new
$label = Craft::t('site', 'Contact us');
```
:::

## 翻訳の提供

翻訳のためのメッセージを準備したら、実際の翻訳を提供する必要があります。

そのために、プロジェクトのベースディレクトリに `translations/` と呼ばれる新しいフォルダを作成し、その中に対象言語の ID を名前とした新しいフォルダを作成します。その中に、メッセージを作成したい翻訳カテゴリの名前をつけたファイルを作成します（プロジェクトメッセージのための `site.php`、Craft のコントロールパネルのメッセージを上書きするための `app.php`、または、プラグインのメッセージを上書きするための `<plugin-handle>.php`）。

例えば、プロジェクトのメッセージをドイツ語に翻訳する場合、プロジェクトのディレクトリ構造は次のようになります。

```
my-project.test/
├── config/
├── ...
└── translations/
    └── de/
        └── site.php
```

次に、テキストエディタで `site.php` を開き、ソースメッセージを翻訳メッセージにマップする配列を返すようにします。

```php
<?php

return [
    'Contact us' => 'Kontaktiere uns',
];
```

これで、ドイツ語サイトのメッセージ翻訳を処理する際、「Contact us」が「Kontaktiere uns」に置換されます。

### Message Parameters

Static messages can have [placeholder values](https://www.yiiframework.com/doc/guide/2.0/en/tutorial-i18n#message-parameters). For example:

```php
<?php

return [
    'Welcome back, {name}' => 'Willkommen zurück {name}',
];
```

To replace the placeholder values with dynamic values when translating the message, pass the `params` argument when using the [translate](dev/filters.md#translate-or-t) filter or calling [Craft::t()](api:yii\BaseYii::t()):

::: code
```twig
<a href="/contact">{{ 'Welcome back, {name}'|t(params = {
    name: currentUser.friendlyName,
}) }}</a>
```
```php
echo Craft::t('site', 'Welcome back, {name}', [
    'name' => Craft::$app->user->identity->friendlyName,
]);
```
:::
