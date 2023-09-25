# University of Exeter Map Embed

## Dataset Specification

This embed uses a CSV or Google sheet as its dataset. The sheet must obey a few rules for it to work.
There are firstly two types of column that this embed uses. The first type is just a basic column, where all the values appear in the dataset unchanged. Then the next type of column is a multi-value column, where you specify a few possible values that the column has, and then each row in the dataset will specify whether it contains that value, or not.

Firstly the sheet must have 2 header rows, where the very top row contains column headers/special flags.

To make a basic column, put the name of the column on the first row, and then the row directly below that should be empty. For example:

![Basic Column](./basic-column.png)

To make a multi-value column, there's a few things you need to do:

1. Have the column heading in the first row in the sheet, on the furthest left column in the sheet where the multi-value column will start
2. Figure out how many unique values this column can have and add columns to the right of the header column for as many unique values you have, header column included. E.g. if I had a `Likes` column, which could be `Fish`, `Pizza`, `Burgers`, and `Chips` I would have 4 columns total, with `Likes` in the first column heading.
3. Fill in the rest of the column headings on the first row in the sheet with `&`. So for example, I should now have something looking like this: ![Multi-value first row](./multi-value-first-row.png)
4. On second row, fill in each of the unique values across the entire row. Order doesn't matter, just as long as you're consistent. Keeping with the example, it now looks like this: ![Multi-value heading rows](./multi-value-heading-rows.png)
5. Time to fill in your values, where in each of the rows below, if the row contains the unique value in the second heading row, then put `yes` in the cell, otherwise leave it blank. The full working example now looks like this: ![Multi-value column](./multi-value-column.png)

## Creating A Dataset Config

Dataset configs are written using both YAML as well as Markdown, where the YAML will appear as front-matter in the markdown config file.

An example config template:

```text
---
latitude: LAT_COLUMN_HEADER
longitude: LON_COLUMN_HEADER
shortDescription: DESC_COLUMN_HEADER
filters:
  A_COLUMN_HEADER: Radio
  B_COLUMN_HEADER: Combo
---

## {{TITLE_COLUMN_HEADER}}

---

{{SUMMARY_COLUMN_HEADER}}

[View online]({{HYPERLINK_COLUMN_HEADER}})
```

Firstly, there are landmarks in this file, where the first line with the `---`, must be within any config file. Similarly, there's a next set of `---` which signifies the end of the YAML config (There is a third set as well, however that's part of the markdown and means a horizontal line separator, more on this below).

After that will come the Markdown which specifies how to render the preview of the publication (more on this below).

## YAML Specification

To learn how YAML works, you can have a look at [https://learnxinyminutes.com/docs/yaml/](https://learnxinyminutes.com/docs/yaml/).
This isn't necessary, however, as you should be able to use the above template, as well as using the instructions below.

### Latitude, Longitude, And Short Description

These lines in the YAML specification, should be set to the headers of the corresponding columns in your dataset.

### Filters

Filters are what appear on the sidebar menu, where they can either be one of two types: a `Radio` filter, or a `Combo` filter.

Combo filters are useful for cases where publications can have multiple values for a column. This is because they let you select multiple values at once, and then decide whether the results must contain some of the selected options, or if they must contain every single selected option.

Radio filters are good for single value columns, as they only let you select one value at a time, where the resulting publications must have the same value in that column. They can, however be used for multiple value columns as well, since you can also only select a single value in a Combo filter.

As the template has, filters are defined with the column header you want to filter with and the filter type, like so:

```yaml
filters:
  A_COLUMN_HEADER: Radio
  B_COLUMN_HEADER: Combo
```

You can also have as many filters as you like.

## Markdown Publication Preview Specification

In the template, after the YAML there's some markdown text. If you don't know how markdown works, then refer to this guide: [https://commonmark.org/help/](https://commonmark.org/help/), or for more advanced usage, use: [https://spec.commonmark.org/0.30/](https://spec.commonmark.org/0.30/).

There are also some bits in the markdown which are like so: `{{ some_column_header }}`. This will insert the corresponding value in the `some_column_header` column from the selected publication.

## Embed Setup

Firstly, place an iframe in your HTML, where you'd like the embed to go. Then also add the following script tag:

```html
<script src="<BASE_URL>/embed.js"></script>
```

Where you should replace `<BASE_URL>` with the base URL of where the embed is being hosted

### Iframe Data Attributes

There are some required and some conditional data attributes. These are:

- Required:
  - `data-map-embed`: Contains the BASE URL of the embed itself
  - `data-config`: Contains the URL of the dataset config markdown file
  - `data-mapbox-token`: Contains the public token for mapbox
- Then there's also attributes which will configure the source, where you must include one source
  - Google sheets source:
    - `data-google-sheet`: Contains the google sheet ID (to find this, refer to the below)
    - `data-gid`: Contains the gid search parameter on a google sheet
  - Custom CSV source:
    - `data-csv`: Contains the URL of the raw CSV itself

To find the google sheets attribute values, use:

```text
https://docs.google.com/spreadsheets/d/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/edit#gid=0123456789
                                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^          ^^^^^^^^^^
                                       [data-google-sheet]                                   [data-gid]
```

Once you've done the above, the iframe tag should look something like this:

```html
<iframe
  title="Map Embed"
  data-map-embed="https://map-embed-base-url.com/"
  data-config="./dataset-config.md"
  data-mapbox-token="pk.XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.XXXXXXXXXXXXXXXXXXXX-w"
  data-google-sheet="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
  data-gid="0123456789"
></iframe>
```