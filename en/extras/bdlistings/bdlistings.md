---
title: "bdListings"
_old_id: "792"
_old_uri: "revo/bdlistings/bdlistings.bdlistings"
---

The bdListings snippet can be used to present search results, a generic listing overview and more.

## Snippet Properties

### General Properties

| Property Name | Description                                                                                                                      | Default Value |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| limit         | Maximum number of results, or 0 for all listings.                                                                                | 0             |
| offset        | Optional offset (for use with pagination, you can use getPage for that).                                                         | 0             |
| sort          | JSON string to allow sorting on multiple factors with different directions. Fields you can use in this property and &sortby are: |
- id
- title
- description
- keywords
- price
- createdon
- publisheduntil
- featured
- companyname
- contactinfo
- address
- phone
- email
- neighborhood
- zip
- city
- country
- website
- latitude
- longitude
- category\_name
- category\_description
- subcategory\_name (may not exist)
- subcategory\_description (may not exist)
- target\_name (may not exist)
- pricegroup\_display (may not exist) | ```
{"city": "ASC", "neighborhood": "ASC", "companyname": "ASC" }
``` |
| sortby | When &sort is empty or not a valid JSON string (hint: use &sort=`` to overrule the default), you can define a property to sort on with this. | title |
| sortdir | \[ASC|DESC\] When &sort is empty or not a valid JSON string you can define ascending or descending sort order with this property. | ASC |
| activeOnly | \[1|0\] If 1 (true) will only show active items. | 1 |
| featuredOnly | \[1|0\] If 1 (true) will only show featured items. | 0 |
| where | Optional JSON encoded limits. |  |
| acceptUrlParams | \[1|0\] Allow specific url params (see acceptedUrlParams) to override properties you set in the snippet call / default values. | 1 |
| acceptedUrlParams | Comma separated list of properties that are allowed to override what's set in the snippet call or default values. | query, keyword, target, pricegroup, city, category, subcategory, sort, listings |
| redirectResource | **Required if using click tracking.** The Resource ID to use as redirect resource. See [bdRedirect](extras/bdlistings/bdlistings.bdredirect "bdListings.bdRedirect") as well. | 39 |
| rowSeparator | Separator to use between listings. | a line break (\\n) |
| imageSeparator | Separator to use between images of a listing. | a line break (\\n) |
| emptyValue | A value (hint: could be a chunk tag) to display when there are no results. | <p>No listings found :(</p> (bdlistings.noresults lexicon key) |
| trackViews | \[1|0\] When 1, the snippet will increment the "views" field for listings in the displayed result set. Can be useful if you have certain lists you don't want to influence the view count. Added in 1.1.1-pl. | 1 |

## Filtering Properties

| Property Name | Description                                                                                                              |
| ------------- | ------------------------------------------------------------------------------------------------------------------------ |
| query         | Full-text search that uses a loose match against the title and description of a listing.                                 |
| keyword       | Full-text search that uses a loose match against the keyword field.                                                      |
| target        | ID of a target as defined in the component to match against.                                                             |
| pricegroup    | ID of a price group as defined in the component to match against.                                                        |
| city          | Loose match against the city field.                                                                                      |
| category      | Either the category name (exact match) or ID to filter on. As of 1.1.2 this will also search subcategories (name or ID). |
| subcategory   | Either the subcategory name (exact match) or ID to filter on.                                                            |
| listings      | If you want to show only a specific set of listings, this can be a comma separated list of their IDs.                    |

Tip: by default each of these are accepted as URL properties (or POST values) so they are great to offer as filtering options to your site visitor.

## Templating Chunks

### tplOuter

Wraps around all the individual items.

Default:

``` php
[[+wrapper]]
```

Accepted properties:

- wrapper: all individual items contained in their tplRow chunk, separated by the rowSeparator separator.
- total: the total amount of items in the result set.

### tplRow

Used to output a single listing.

Default:

``` php
<div>
    <h2>
        [[+redirect_url:notempty=`<a href="[[+redirect_url]]">[[+title]]</a>`:default=`[[+title]]`]]
    </h2>
    [[+images:notempty=`<p>[[+primaryimage:notempty=`<img src="[[+primaryimage:phpthumbof=`w=300&h=200`]]" alt="[[+title]]" /><br />`]] [[+images]]</p>`]]
    <p>In [[+city]] - [[+neighborhood]] - [[+companyname]] - <a href="[[+redirect_url]]">[[+companyname]]</a></p>
    [[+description:notempty=`<p>[[+description:nl2br]]</p>`]]
    [[+googlemap_url:notempty=`<a href="[[+googlemap_url]]" title="View on Google Maps"><img src="[[+googlemap_static]]" alt="Google Maps"></a>`]]
</div>
```

There's a lot of placeholders you can use there, and some properties have a direct influence on their value. Especially think of the redirect\_url (which is influenced by &redirectResource) and the googlemap\_static placeholder which is a link generated based on the staticMap\* properties (see further below).

Placeholders:

- title
- description
- keywords
- price
- pricegroup / pricegroup\_id (id)
- pricegroup\_display
- category / category\_id (id)
- category\_name
- category\_description
- subcategory / subcategory\_id (id)
- subcategory\_name
- subcategory\_description
- target / target\_id (id)
- target\_name
- createdon (use with strtotime and date output modifiers)
- publisheduntil (use with strtotime and date output modifiers)
- active (1/0)
- featured (1/0)
- extended (1/0)
- companyname
- contactinfo
- address
- phone
- email
- neighborhood
- zip
- city
- country
- website
- latitude
- longitude
- clicks
- views
- googlemap\_static: url to a static google map image (based on your map snippet properties, see further below). Empty if no lat/long is set.
- googlemap\_url: url to maps.google.com/maps?q=LATITUDE,LONGITUDE. Empty if no lat/long is set.
- redirect\_url: url to the &redirectResource resource with the "lid" property and the listing ID as its value, used to redirect to the website set for the listing while tracking clicks. Empty if no website is set.
- images: filled with individual images templated with the tplImage chunk, separated by the imageSeparator property.
- primaryimage: if you have an image marked as primary, this will have the link (so not the tplImage tpl) to the image. If you don't have an image marked as primary it will attempt to use the first image.
- primaryimagepath: path to the image set in primaryimage.

### tplImage (lower L then upper i)

Used to wrap around images inside a listing. (not used for the primaryimage property in the tplRow chunk)

Default:

``` php
<img src="[[+image:phpthumbof=`w=150&h=150&zc=1`]]" alt="[[+caption]]" />
```

Placeholders:

- image
- imagepath
- caption
- sortorder
- listing (id)
- primary (1|0)

## Static Map Properties

In the tplRow chunk there's a googlemap\_static placeholder. That's a simple image showing a map with the listing centered with a marker. The following properties influence its behavior, and I would suggest reading up on the Google Maps Static API if you are looking for more in-depth explanation of what goes on behind the scenes.

| Property Name        | Description                                                                                                                                                     | Default Value |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| staticMapWidth       | Width of the image to generate.                                                                                                                                 | 150           |
| staticMapHeight      | Height of the image to generate.                                                                                                                                | 150           |
| staticMapZoom        | Zoom level (1-23, depending on location)                                                                                                                        | 12            |
| staticMapType        | Type of map to show.                                                                                                                                            | hybrid        |
| staticMapMarkerColor | Color (some textual, otherwise hex) for the marker.                                                                                                             | red           |
| staticMapMarkerLabel | Text on the marker. Limited to 1 upper character or number.                                                                                                     | A             |
| staticMapMarkerSize  | \[tiny                                                                                                                                                          | mid           | small | medium\] Size of the marker. | medium |
| staticMapMarkerIcon  | Optionally an icon to use instead of the marker. Consult the Google Maps Static API for detailed usage and what you need to encode or not. An example could be: |
``` php
http://chart.apis.google.com/chart?chst=d_map_pin_icon&chld=cafe%7C996600
``` |  |
| staticMapMarkerIconShadow | \["true" or "false", actual text so NOT 1 or 0\] To use shadow or not when using a custom icon. | true |

## Examples

Last 5 listings, simple list format:

``` php
<h3>Lastest 5 Ads</h3>
<ul id="latest_ads">
[[!bdListings? &sort=`{"createdon":"DESC"}`  &tplRow=`bdl.listings.list` &limit=`5`]]
</ul>
```

bdl.listings.list chunk, pointing to resource 14 (search results page which has an uncached bdListings call on it and accepts URL parameters) with the listing ID as filter, meaning it will only show the one listing.

``` php
<li>
  <a href="[[~14? &listings=`[[+id]]`]]" title="[[+title]]">[[+title]]</a>
</li>
```
