# Backend sorting and reordering

- [Introduction](#introduction)
- [Configuring the reorder behavior](#configuring-reorder)
- [Displaying the reorder page](#reorder-display)
- [Override Sortable Partials](#override-sortable-partials)
- [Extending the model query](#extend-model-query)

<a name="introduction"></a>
## Introduction

The **Reorder behavior** is a controller [behavior](../services/behaviors) that provides features for sorting and reordering database records. The behavior provides a page called Reorder using the controller action `reorder`. This page displays a list of records with a drag handle allowing them to be sorted and in some cases restructured.

The behavior depends on a [model class](../database/model) which must implement one of the following [model traits](../database/traits):

1. [`Winter\Storm\Database\Traits\Sortable`](../database/traits#sortable)
1. [`Winter\Storm\Database\Traits\NestedTree`](../database/traits#nestedtree)

> **NOTE**: If adding sorting to a previously unsorted model under the control of a third party is desired, you can use the [`Winter\Storm\Database\Behaviors\Sortable`](../database/behaviors#sortable) behavior, which can be dynamically implemented. However, you will need to ensure that the model table has a `sort_order` column present on it.

In order to use the reorder behavior you should add it to the `$implement` property of the controller class. Also, the `$reorderConfig` class property should be defined and its value should refer to the YAML file used for configuring the behavior options.

```php
namespace Acme\Shop\Controllers;

class Categories extends Controller
{
    public $implement = [
        'Backend.Behaviors.ReorderController',
    ];

    public $reorderConfig = 'config_reorder.yaml';

    // [...]
}
```

<a name="configuring-reorder"></a>
## Configuring the behavior

The configuration file referred in the `$reorderConfig` property is defined in YAML format. The file should be placed into the controller's [views directory](controllers-ajax/#introduction). Below is an example of a configuration file:

```yaml
# ===================================
#  Reorder Behavior Config
# ===================================

# Reorder Title
title: Reorder Categories

# Attribute name
nameFrom: title

# Model Class name
modelClass: Acme\Shop\Models\Category

# Toolbar widget configuration
toolbar:
    # Partial for toolbar buttons
    buttons: reorder_toolbar
```

The configuration options listed below can be used.

<style>
    .attributes-table-precessor + table td:first-child,
    .attributes-table-precessor + table td:first-child > * { white-space: nowrap; }
</style>
<div class="attributes-table-precessor"></div>

Option | Description
------------- | -------------
`title` | used for the page title.
`nameFrom` | specifies which attribute should be used as a label for each record.
`modelClass` | a model class name, the record data is loaded from this model.
`toolbar` | reference to a Toolbar Widget configuration file, or an array with configuration.

<a name="reorder-display"></a>
## Displaying the reorder page

You should provide a [view file](controllers-ajax/#introduction) with the name **reorder.htm**. This view represents the Reorder page that allows users to reorder records. Since reordering includes the toolbar, the view file will consist solely of the single `reorderRender` method call.

```php
<?= $this->reorderRender() ?>
```

<a name="override-sortable-partials"></a>
## Override Sortable Partials

If you need to override default view of your reorder page, you have to copy

1. `modules/backend/behaviors/reordercontroller/partials/_container.htm`
2. `modules/backend/behaviors/reordercontroller/partials/_records.htm`

in

1. `plugins/yournamespace/yourplugin/yoursortablecontroller/_reorder_container.htm`
2. `plugins/yournamespace/yourplugin/yoursortablecontroller/_reorder_records.htm`


<a name="extend-model-query"></a>
## Extending the model query

The lookup query for the list [database model](../database/model) can be extended by overriding the `reorderExtendQuery` method inside the controller class. This example will ensure that soft deleted records are included in the list data, by applying the **withTrashed** scope to the query:

```php
public function reorderExtendQuery($query)
{
    $query->withTrashed();
}
```
