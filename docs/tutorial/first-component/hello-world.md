# 1. Building Your First Component

<div class="alert alert-info">
  <strong>Before you begin this tutorial, make sure you first follow the <a href="../setup.md">setup instructions</a> and have your local environment up and running.
  </strong>
</div>

<div class="alert">
  <h3>This tutorial covers:</h3>
  <ul>
    <li><strong><a href="#21-configuring-page-templates">1.1 Configuring Page Templates</a></strong></li>
    <li><strong><a href="#22-creating-your-component">1.2 Creating your Component</a></strong></li>
    <li><strong><a href="#23-rendering-your-component">1.3 Rendering Your Component</a></strong></li>
  </ul>
</div>

## 1.1 Configuring Page Templates
All template files in Flynt can be found under the theme root, in the `templates` directory. You can learn more about how Flynt handles page templates [here](../../workflow/theme-structure.md).

For this tutorial we will be using the default `template/page.php` template. This file contains only one line of code:

```php
Flynt\echoHtmlFromConfigFile('default.json');
```

<p><a href="https://github.com/bleech/wp-starter-plugin/blob/documentation/docs/api/Flynt.md#echogethtmlfromconfig" class="source-note">The source of this function can be found here in the Flynt Core plugin.</a></p>

For now, it is only important to know that our template config is actually loaded from `config/templates/default.json`.

Here we store our default page layout:

```json
{
  "name": "MainLayout",
  "dataFilter": "Flynt/DataFilters/WpBase",
  "areas": {
    "mainHeader": [
      {
        "name": "MainNavigation",
        "dataFilter": "Flynt/DataFilters/MainNavigation"
      }
    ],
    "mainTemplate": [
      {
        "name": "Template",
        "dataFilter": "Flynt/DataFilters/MainQuery/Single"
      }
    ]
  }
}
```

For a detailed look at how these template configurations work, [you can read more here](../workflow/theme-structure.md).

## 1.2 Creating your Component
All components are located in the `Components` directory. Create a new folder in this directory with the name `PostSlider`.

Flynt uses [Twig](http://twig.sensiolabs.org/) in conjunction with [Timber](timber.github.io/timber/) for view templates. To add a template for your component, create `Components/PostSlider/index.twig`. Your folder structure will now be:

```
flynt-theme/
└── Components/
   └── PostSlider/
       └── index.twig
```

Whilst the end goal is to make this component an interactive slider, for now we'll add some dummy data to our view template. Open `Components/PostSlider/index.twig` and enter the following:

```twig
<div is="flynt-post-slider">
  <div class="slider">
    <h1 class="slider-title">Hello World!</h1>
  </div>
</div>
```

Done! Next we need to render the component to the page.

## 1.3 Rendering Your Component

First we will create a new area for our Post Slider component.

Open `config/templates/default.json` and add a new area with the key `pageComponents`:

```json
{
  "name": "MainLayout",
  "dataFilter": "Flynt/DataFilters/WpBase",
  "areas": {
    ...
    "mainTemplate": [
      {
        "name": "Template",
        "dataFilter": "Flynt/DataFilters/MainQuery/Single",
        "areas": {
          "pageComponents": [
            {
              "name": "PostSlider"
            }
          ]
        }
      }
    ]
  }
}
```

Now that we have registered the area we need to output it.

Open the `Components/Template/index.twig` and replace `the_content()` with the `pageComponents` area:

```twig
<div is="wps-main-template">
  <div class="page-wrapper" role="document">
    <div class="main-header">
      {{ area('mainHeader') }}
    </div>
    <main class="main-content" role="main">
      {{ area('pageComponents') }}
    </main>
  </div>
</div>
```

Voilà! We're done.

Components defined within the `pageComponents` area in the `default.json` page template will now be output where the area is called. Load the front-end of your site and you will see your new component.

<div class="alert alert-steps">
  <h2>Next Steps</h2>

  <p>In the next section we will tackle making this content dynamic by adding user-editable content fields. We will then learn to manipulate this data before we pass it to the view and render it.</p>

  <p><a href="using-acf.md" class="btn btn-primary">Learn to add fields</a></p>
</div>