---
title: Configuration Options
weight: 23
group: reference
---

All configuration options for Saleina CMS are specified in a `config.yml` file, in the folder where you access the editor UI (usually in the `/admin` folder).

To see working configuration examples check out the [CMS demo site](https://demo.saleinacms.org). (No login required: click the login button and the CMS will open.) You can refer to the demo [configuration code](https://gitlab.com/saleina/saleinacms/blob/master/public/config.yml) to see how each option was configured.

You can find details about all configuration options below. Note that [YAML syntax](https://en.wikipedia.org/wiki/YAML#Basic_components) allows lists and objects to be written in block or inline style, and the code samples below include a mix of both.


## Backend

*This setting is required.*

The `backend` option specifies how to access the content for your site, including authentication.

## Logo

The `logo` option specifies a custom logo to display in the editor UI. It expects a url to an image file.

**Example**
```yaml
logo: "http://example.com/logo.png"
```

## Media and Public Folders

*This setting is required.*

Saleina CMS users can upload files to your repository using the Media Gallery. The following settings specify where these files are saved, and where they can be accessed on your built site.

**Options**

- `media_folder` (required): Folder path where uploaded files should be saved, relative to the base of the repo.
- `public_folder` (optional): Folder path where uploaded files will be accessed, relative to the base of the built site. For fields controlled by [file] or [image] widgets, the value of the field is generated by prepending this path to the filename of the selected file. Defaults to the value of `media_folder`, with an opening `/`.

**Example**

``` yaml
media_folder: "static/images/uploads"
public_folder: "/images/uploads"
```

Based on the settings above, if a user used an image widget field called `avatar` to upload and select an image called `philosoraptor.png`, the image would be saved to the repository at `/static/images/uploads/philosoraptor.png`, and the `avatar` field for the file would be set to `/images/uploads/philosoraptor.png`.

## Collections

*This setting is required.*

The `collections` setting is the heart of your Saleina CMS configuration, as it determines how content types and editor fields in the UI generate files and content in your repository. Each collection you configure displays in the left sidebar of the Content page of the editor UI, in the order they are entered into your Saleina CMS `config.yml` file.

`collections` accepts a list of collection objects, each with the following options:

- `name` (required): unique identifier for the collection, used as the key when referenced in other contexts.
- `Label`: label for the collection in the editor UI; defaults to the value of `name`
- `file` or `folder` (requires one of these): specifies the collection type and location; details in [Collection Types](/docs/collection-types)
- `create`: for `folder` collections only; `true` allows users to create new items in the collection; defaults to `false`
- `delete`: `false` prevents users from deleting items in a collection; defaults to `true`
- `type`: see detailed description below
- `slug`: see detailed description below
- `tabs` (required): see detailed description below

The last few options require more detailed information.

### `type`

These setting determines how collection files are parsed and saved. It's optional and by default Saleina CMS will assume `md`. If your collection contains a different file type or you'd like more control, you can set these field explicitly.

### `slug`

For folder collections where users can create new items, the `slug` option specifies a template for generating new filenames based on a file's creation date and `title` field. (This means that all collections with `create: true` must have a `title` field.)

**Available template tags:**

- `{{slug}}`: a url-safe version of the `title` field for the file
- `{{year}}`: 4-digit year of the file creation date
- `{{month}}`: 2-digit month of the file creation date
- `{{day}}`: 2-digit day of the month of the file creation date
- `{{hour}}`: 2-digit hour of the file creation date
- `{{minute}}`: 2-digit minute of the file creation date
- `{{second}}`: 2-digit second of the file creation date

**Example:**

```yaml
slug: "{{year}}-{{month}}-{{day}}_{{slug}}"
```

### `tabs`
The `tabs` option maps a collection of fields to editor tabs. The order of the tabs in your Saleina CMS `config.yml` file determines their order in the editor UI and in the saved file.

`tabs` accepts the following proprties

- `label` (required): label for the tab in the editor UI.
- `fields` (required): option maps editor UI widgets to field-value pairs in the saved file, the order of the fields in your Saleina CMS `config.yml` file determines their order in the editor UI and in the saved file.

`fields` accepts a list of collection objects, each with the following options:

- `name` (required): unique identifier for the field, used as the key when referenced in other contexts.
- `label`: label for the field in the editor UI; defaults to the value of `name`
- `widget`: defines editor UI and inputs and file field data types; details in [Widgets](/docs/widgets)
- `default`: specify a default value for a field; available for most widget types (see [Widgets](/docs/widgets) for details on each widget type)
- `required`: specify as `false` to make a field optional; defaults to `true`
- `pattern`: add field validation by specifying a string with a regex pattern.

In files with frontmatter, one field should be named `body`. This special field represents the section of the document (usually markdown) that comes after the frontmatter.

**Example:**

```yaml
tabs:
    - label: Basic
      fields:
        - {label: "Title", name: "title", widget: "string", pattern: ".{20,}"}
        - {label: "Layout", name: "layout", widget: "hidden", default: "blog"}
    - label: Body
      fields:
        - {label: "Featured Image", name: "thumbnail", widget: "image", required: false}
        - {label: "Body", name: "body", widget: "markdown"}
```