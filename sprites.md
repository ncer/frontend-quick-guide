# Спрайты

Перед тем как начать собирать спрайты, не забудьте поставить соответствующие npm пакеты из `templates/package.json` (см. [Разворачивание проекта](deployment.md)) и настроить `gulp-config.js` (см. [Сборка проекта](build.md)).

Все исходники для спрайтов хранятся в папке `templates/src/sprites/`. Во время разработки спрайты собираются в `templates/src/images/`. А при запуске `gulp release` оттуда копируются в `templates/build/images/`.

Для удобства работы со спрайтами автоматически создаются и обновляются CSS миксины. Ниже все объяснения будут на примере Less миксинов.

Из коробки присутствуют тестовые спрайты и их исходники. После разворачивания проекта их можно удалить.

## PNG спрайты

Чтобы собрать PNG спрайт для страницы новостей, нужно зайти в `templates/src/sprites/png/`, создать папку `news` и положить в нее необходимые картинки. Сборщик соберет их в спрайт `news.png` (согласно названию папки) и положит в `templates/src/images/png-sprites/`.

Также сборщик соберет `news.less` со всеми необходимыми миксинами и положит их в папку `templates/src/less/sprites/png-sprites`.

Чтобы быстро подключить в Less иконку из спрайта, нужно добавить миксин `.bg-png(@png-<ИМЯ_КАРТИНКИ>)`. Например, вы хотите подключить картинку `favorite.png` \(которую предварительно положили в `templates/src/sprites/png/news/`\). Для этого нужно написать `.bg-png(@png-favorite)`.

```less
.selector {
  .bg-png(@png-favorite);
}
```

Остальные варианты подключения можно будет найти в сгенерированном миксине `templates/src/less/sprites/png-sprites/news.less`.

**Обратите внимание**: нельзя класть одноименные файлы в разные папки, т.к. они друг друга затрут. Зато можно переносить их между папками без потери связи.

## SVG спрайты

Чтобы собрать SVG спрайт, нужно зайти в `templates/src/sprites/svg/` и положить туда необходимые svg картинки \(обратите внимание: никаких дополнительных папок создавать не нужно\). Сборщик настроен на сборку SVG спрайтов в двух режимах одновременно: режиме CSS и режиме symbol. На выходе получатся 2 соответствующих спрайта: `sprite.svg` и `symbols.svg`. Сборщик положит их в `templates/src/images/`.

Эти два режима отличаются от остальных \(инлайн в HTML, инлайн в CSS, base64, SVG-stack\) почти 100%-й поддержкой во всех современных браузерах \(начиная с IE9 включительно\), кешируемостью, быстродействием и удобством работы. Подробнее об этом можно почитать, например, [тут](https://habrahabr.ru/post/276463/). 

Для поддержки старых IE в скрипты подключается полифилл [svg4everybody](https://github.com/jonathantneal/svg4everybody) (только если в `gulp-config.js` включена сборка SVG спрайтов).

Подключение иконки из `sprite.svg` аналогично подключению png спрайта за рядом исключений:

* спрайт собирается в папку `templates/src/images/`;
* миксины кладутся в `templates/src/less/sprites/svg-sprite.less`;
* иконка в Less подключается миксином `.bg-svg(@svg-<ИМЯ_КАРТИНКИ>)` (остальные варианты см. в генерируемом `svg-sprite.less`).

Для подключения иконки из `symbols.svg` предусмотрена специальная PHP функция, которая упрощает подключение.

```php
<? tpl('components/svg.tpl.php', ['id' => '<ИМЯ_КАРТИНКИ>']); ?>
```

Например, вы хотите подключить картинку `favorite.svg` \(которую предварительно положили в `templates/src/sprites/svg/`\). Для этого нужно написать:

```php
<? tpl('components/svg.tpl.php', ['id' => 'favorite']); ?>
```

Аналогичный модуль доступен в Jade (см. [Верстка на Jade](jade.md)).

```jade
include ../modules/_modules
+module(MODULE.SVG, {id: 'favorite'})
```

Дополнительно для `symbols.svg` генерируется демо-страница `templates/src/images/symbols-demo.html` со всеми SVG иконками проекта. По ней очень удобно сверять, какие иконки уже есть в проекте во избежание дублирования.

## Возможные проблемы

Если после добавления новых иконок вы не видите изменений в спрайтах, попробуйте удалить их из `templates/src/images/` (во время разработки) и из `templates/build/images/` (перед релизом), а также удалить миксины из `templates/src/less/sprites/`, и заново запустить сборщик.