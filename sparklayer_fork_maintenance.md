# Keeping this fork up to date

Steps for updating this repo with a new published version from upstream.

1. Clone this repo to your local machine
2. Configure upstream with `git remote add upstream git@github.com:Shopify/dawn`
3. Run `git fetch upstream`
4. Create a branch off of main named after the [latest](https://github.com/Shopify/dawn/releases) available version in the upstream repo (eg. v10) and switch to it
5. Merge the changes from the upstream tag, eg. `git merge v10.0.0`
6. Resolve any merge conflicts and commit
7. Push your branch to GitHub and open a PR

Create new branch
```bash
git switch -c 'upgrade-<tag-version>'
```

Merge changes
```bash
git merge refs/tags/<tag-version>
```

Remove all our SparkLayer theme-related changes (by checking out the upstream files)
```bash
git checkout refs/tags/<tag-version> -- assets config layout locales sections snippets templates
```

Perform a theme install from scratch (trust me, this is easier than trying to fix the conflicts)

theme.liquid snippet - put this just before closing `</head>` tag in `theme.liquid`:
```liquid
{%- if customer.metafields.sparklayer.authentication -%}
  <!-- Start of SparkLayer core script-->
  {%- # SPARKLAYER CONFIG AUTO REPLACEMENT -%}
  <!-- End of SparkLayer core script-->

  <!-- SparkLayer Sample CSS Code -->

  <style type="text/css">
    :root {
      /* Set the brand styling */
      --b2b-brand-color: #334fb4; /* Main brand colour*/
      --b2b-brand-color-hover: #617bd8; /* Main brand colour hover */
      --b2b-brand-font: Assistant, sans-serif; /* Main brand font */
      --b2b-brand-font-heading: Assistant, sans-serif; /* Main brand heading font */

      /* Update colours */
      --spark-default-body-color: #555555; /* Main body colour*/
      --spark-primary-color: #000000; /* Main title colour*/
      --spark-secondary-color: var(--b2b-brand-color); /* Main highlight colour*/
      --spark-link-color: var(--b2b-brand-color); /* Link colour*/

      /* Typefaces */
      --spark-font-default: var(--b2b-brand-font); /* Body typeface*/
      --spark-font-highlight: var(--b2b-brand-font-heading); /* Highlight typeface*/
      --spark-font-weight-default: 400; /* Default font weight*/
      --spark-font-weight-medium: 500; /* Medium font weight*/
      --spark-font-weight-heavy: 600; /* Bold font weight*/

      /* General */
      --spark-border-radius-default: 0; /* Default border radius*/
      --spark-drawer-max-width: 700px; /* Width of overlay*/
      --spark-drawer-max-width-wide: 1000px; /* Width of overlay in maximised view*/

      /* Font sizes */
      --spark-font-default-size: 14px; /* Default font size*/
      --spark-font-default-size-small: 14px; /* Default font size - small screens*/
      --spark-header-font: var(--b2b-brand-font-heading); /* Header typeface*/
      --spark-header-font-weight: 400; /* Header font weight*/
      --spark-h1-fontsize: 24px; /* Header 1 font size*/
      --spark-h2-fontsize: 22px; /* Header 2 font size*/
      --spark-h3-fontsize: 20px; /* Header 3 font size*/
      --spark-h4-fontsize: 16px; /* Header 4 font size*/
      --spark-h5-fontsize: 15px; /* Header 5 font size*/
      --spark-h6-fontsize: 14px; /* Header 6 font size*/

      --spark-pricing-fontsize: 18px; /* Font size of pricing*/
      --spark-pricing-font-weight: 400; /* Font weight of pricing*/
      --spark-pricing-fontsize-small: 16px; /* Font size of pricing on mobile*/

      --spark-product-code-fontsize: 16px; /* Font size of product code*/
      --spark-product-code-font-weight: 400; /* Font weight of product code*/
      --spark-product-code-fontsize-small: 14px; /* Font size of product code on mobile*/
      --spark-product-stockstatus-align: flex-start; /* Aligment of stock status for single variants*/

      /* Buttons */
      --spark-button-font-family: var(--b2b-brand-font-heading); /* Button typeface*/
      --spark-button-color: var(--b2b-brand-color); /* Default sutton colour*/
      --spark-button-color-highlight: var(--b2b-brand-color-hover); /* Default button colour hover*/
      --spark-button-large-color: var(--b2b-brand-color); /* Large button colour*/
      --spark-button-large-color-highlight: var(--b2b-brand-color-hover); /* Large button colour hover*/
      --spark-border-radius-button: 0; /* Button border radius*/
      --spark-button-font-weight: 400; /* Button font weight*/
      --spark-button-text-transform: none; /* Button text transform*/
      --spark-button-text-letter-spacing: 1px; /* Button letter spacing*/
      --spark-button-padding: 1.25rem 2.75rem; /* Button padding*/
      --spark-button-small-font-size: 15px; /* Small button font size*/

      /* Tables */
      --spark-table-border-color: #CCCCCC; /* Table border colour*/
      --spark-table-header-background-color: #F1F1F1; /* Table header background*/
      --spark-table-header-text-color: #222222; /* Table header font colour*/
      --spark-table-header-font-weight: 500; /* Table header font weight*/

      /* Product Card */
      --spark-product-card-button-radius: 0; /* Button radius*/
      --spark-product-card-button-padding: 0.75em 1em; /* Button padding*/
      --spark-product-card-pricing-font-size: 15px; /* Pricng font size*/
      --spark-product-card-pricing-font-size-small: 14px; /* Pricng font size on mobile*/
      --spark-product-card-select-min-height: 0; /* Set a min-height for select menu*/
    }

    /* This hides the default mini cart behaviour */
  
    .cart-count-bubble,
    shopify-payment-terms,
    quick-order-list,
    product-form,
    quick-add,
    quick-add-bulk,
    cart-drawer {
      display: none !important;
    }

    /* This sets product cards to bottom align the SparkLayer product card widget */
    .card-wrapper {
        justify-content: space-between;
        height: 100%;
    }

    .product__info-wrapper {
      z-index: 2;
    }

  </style>

  <!-- End SparkLayer Sample CSS Code -->
{%- endif -%}
```

Put this just after the opening `<body>` tag in `theme.liquid`:
```liquid
{%- if customer.tags contains 'b2b' or template contains 'customers/' or request.path == '/challenge'  or request.path == '/pages/b2b-login' -%}
{% assign showSiteForB2B = false %}
{% endif %}
{%- if showSiteForB2B == blank -%}
  {% assign showB2BLogin = false %}
{% endif %}
{%- if request.design_mode -%}
  {% comment %} Remove the login page for the customiser {% endcomment %}
  {% assign showB2BLogin = false %}
{% endif %}

{% comment %} Show content for logged in users {% endcomment %}
{%- unless showB2BLogin -%}

{%- if customer.tags contains 'b2b' -%}
  {%- if template contains 'customers/' or request.path == '/cart' -%}
    <script>
      window.location.href = '/index#spark-account';
    </script>
  {% endif %}
{% endif %}
```

And put this just before the closing `</body>` tag in `theme.liquid`:
```liquid
{% comment %} Show content for logged out users {% endcomment %}
  {%- if showB2BLogin -%}
    <script>
      window.location.href = '/pages/b2b-login';
    </script>
  {% endif %}
{% comment %} END {% endcomment %}
```
