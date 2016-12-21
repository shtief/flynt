# 2. Using Advanced Custom Fields (ACF)

<div class="alert alert-info">
  <strong>A requirement of this tutorial is using the Wordpress Plugin <a href="https://www.advancedcustomfields.com/">Advanced Custom Fields (ACF)</a>. Please make sure this is installed and enabled before continuing.</strong>
</div>

<div class="alert">
  <h3>This tutorial covers:</h3>
  <ul>
    <li><strong><a href="#21-adding-acf-fields">2.1 Adding ACF Fields</a></strong></li>
    <li><strong><a href="#22-adding-a-field-group">2.2 Adding a Field Group</a></strong></li>
    <li><strong><a href="#23-displaying-field-content">2.3 Displaying Field Content</a></strong></li>
    <li><strong><a href="#24-understanding-the-flynt-data-flow">2.4 Understanding the Flynt Data Flow</a></strong></li>
    <li><strong><a href="#25-taking-our-component-further">2.5 Taking our Component Further</a></strong></li>
  </ul>
</div>

## 2.1 Adding ACF Fields
Advanced Custom Fields (ACF) is a Wordpress plugin to make adding custom meta fields easy and intuitive, with a straight-forward API and seamless integration into the back-end of Wordpress. With Flynt, ACF is used to add user-editable fields at the component level.

To get started, add a single ACF text field to the `PostSlider` component.

Create `Components/PostSlider/fields.json` and add the code below:

```json
{
  "fields": [
    {
      "name": "title",
      "label": "Title",
      "type": "text",
      "required": 1
    }
  ]
}
```

The folder structure will now resemble the following:

```
flynt-theme/
└── Components/
   └── PostSlider/
       └── index.twig
       └── fields.json
```

That's all we need to do to register a new field for a component.

If you are already familiar with ACF, you will notice that these field options (e.g. "required") are exactly the same as those provided [natively by ACF](https://www.advancedcustomfields.com/resources/text/). This is the case for all fields we author with Flynt's `fields.json`.

Before this field will be visible in the back-end, however, we still need to define in which situations these fields should be available to the editor. We will do this in the next section by adding a new "Field Group".

<a href="https://github.com/bleech/wp-starter-snippets" class="source-note source-note--info">ACF offers around 20 different field types. To make the process of authoring these fields simpler, install our fields.json snippets for Atom or Sublime Text.</a>

<div class="alert">
  <p>You can see the full list of available fields and their options in the <strong><a href="https://www.advancedcustomfields.com/resources/#field-types">official ACF documentation</a></strong>.</p>

  <p>We also have documentation on how best to use several of the ACF Pro field types with Flynt:</p>

  <br>

  <ul>
    <li><strong><a href="../theme-development/advanced/flexible-content.md">Using the ACF Pro "Flexible Content" Field</a></strong></li>
    <li><strong><a href="../theme-development/advanced/repeaters.md">Using the ACF Pro "Repeater" Field</a></strong></li>
    <li><strong><a href="../theme-development/advanced/options-page.md">Using the ACF Pro "Options" Page</a></strong></li>
  </ul>
</div>

## 2.2 Adding a Field Group

All field group configuration files can be found in the `config/fieldGroups` directory. For this tutorial we will modify the default `pageComponents` configuration.

Open `config/fieldGroups/pageComponents.json` and replace the contents with the following:

```json
{
  "name": "pageComponents",
  "title": "Page Components",
  "fields": [
    "Flynt/Components/PostSlider/Fields"
  ],
  "location": [
    [
      {
        "param": "post_type",
        "operator": "==",
        "value": "page"
      }
    ]
  ]
}
```

In the "fields" array, we specifically pull in the fields from our Post Slider component. If we also had more components, we could also pull these into our Page Components group. For example:

```json
{
  "name": "pageComponents",
  "title": "Page Components",
  "fields": [
    "Flynt/Components/PostSlider/Fields",
    "Flynt/Components/ExampleComponent/Fields",
    "Flynt/Components/AnotherComponent/Fields"
  ]
}
```

Below this, we are also setting the location where the field group should be displayed to the "Page" post type.

```json
"location": [
  [
    {
      "param": "post_type",
      "operator": "==",
      "value": "page"
    }
  ]
]
```

<a class="source-note source-note--info" href="https://www.advancedcustomfields.com/resources/custom-location-rules/">
As with the field settings, we are writing our location rules using the same configuration options as Advanced Custom Fields. We strongly recommend reading more about these rules in the official ACF documentation.</a>

That's it! Navigate to the backend of Wordpress and create a new page. At the bottom, you'll now see a section for your Post Slider component with a field labeled "Title".

Add the text "Our Featured Posts" into the title field and save the page.

Next, we'll move on to displaying this content on the front-end.

## 2.3 Displaying Field Content
We can now display the title in our front-end [Twig](twig.sensiolabs.org) template.

Open `Components/PostSlider/index.twig` and update it with the following:

```twig
<div is="flynt-post-slider">
  <div class="slider">
    <h1 class="slider-title">{{ title }}</h1>
  </div>
</div>
```

That's all there is to it! All of the component's fields are automatically available in the component's view.

## 2.4 Understanding the Flynt Data Flow

At this point it is important to understand how the Flynt Core plugin is passing this data to the view. In actual fact, the data function uses the data passed to the template referenced by its keys. This can be understood much easier with the flowchart below:

<pre class="language- flowchart">
  <code>
  +------------------------------+
  |    Template Configuration    |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |         DataFilters          |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |      modifyComponentData     |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |        Rendered HTML         |
  +------------------------------+
  </code>
</pre>

<!-- <pre class="language- flowchart">
  <code>
  +------------------------------+
  |    Template Configuration    |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |         Parent Data          |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |    Initial Component Config  |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |         DataFilters          |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |         Custom Data          |
  +--------------+---------------+
                 |
                 |
  +--------------v---------------+
  |       modifyComponentData    |
  +--------------+---------------+
                 |
       Pass data to template
                 |
  +--------------v---------------+
  |        Rendered HTML         |
  +------------------------------+
  </code>
</pre> -->

<a href="/add-link" class="source-note">To dig into this more, read through the full flowchart in the Flynt Core plugin documentation.</a>

## 2.5 Taking our Component Further
We will now create an image slider by pulling the featured image from a list of posts selected by the user.

Open `Modules/PostSlider/fields.json` and add a post object field to the component:

```json
{
  "fields": [
    {
      "name": "title",
      "label": "Title",
      "type": "text",
      "required": 1
    },
    {
      "name": "posts",
      "label": "Posts",
      "type": "post_object",
      "post_type": ["post"],
      "return_format": "object",
      "multiple": 1,
      "required": 1
    }
  ]
}
```

To continue, create a few dummy posts and add a featured image to each one. You can grab some sample images from [Unsplash](https://unsplash.com).

Now open up your page in the backend and you will now see our new field, with the label "Posts". Select your dummy posts and save the page.

In `Components/PostSlider/index.twig`, we can now loop through our posts and output the title and featured image for each one:

```twig
<div is="flynt-post-slider">
  <div class="slider">
    <h1 class="slider-title">{{ title }}</h1>
    <div class="slider-items">
      {% for post in posts %}
        <div class="slider-item">
          <h2>{{ post.title }}</h2>
          <img src="{{ post.thumbnail.src  }}" alt="{{ post.title }}">
        </div>
      {% endfor %}
    </div>
  </div>
</div>
```

Here, Timber's default Wordpress image handling provides us with our featured image data directly with the use of `post.thumbnail`. To quote the Timber documentation:

> Automatically, Timber will interpret images attached to a post’s thumbnail field (“Featured Image” in the admin) and treat them as TimberImages.

[If you are not familiar with Timber, we recommend reading more about this in their documentation.](http://timber.github.io/timber/#image-cookbook)

<div class="alert alert-steps">
  <h2>Next Steps</h2>

  <p>We now have a simple component that takes data from our fields and outputs them on the front-end! But what if we want do pull other data in our component? The next section explores passing additional data to our component.</p>

  <p><a href="modify-data.md" class="btn btn-primary">Learn to modify component data</a></p>
</div>