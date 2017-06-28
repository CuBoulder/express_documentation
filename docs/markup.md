# Admin UI Style Guide

## Grids, Columns & Cards

Using the example below, you can create various grids, columns, or card interfaces. If you want simple columns, use 'express-columns' and 'express-column' classes instead of 'express-cards' and 'express-card'. 

You can control the size of the column or card using the Bootstrap grid classes. The grid is 12 columns wide.

### Grid Class Examples

col-lg-3 : Element is 3 columns wide on a large screen (>= 1200 pixels wide)
col-md-4 : Element is 4 columns wide on a medium screen (>= 960 pixels wide)
col-sm-6 : Element is 6 columns wide on a small screen (>= 768 pixels wide)
col-xs-12 : Element is 12 columns wide on a mobile device.

Classes can be chained together to create a responsive grid:

```
<div class="express-card col-lg-3 col-md-4 col-sm-6 col-xs-12">
```

### Sample Markup

```
  <div class="express-cards">
  <div class="express-card col-lg-4 col-md-4 col-sm-6 col-xs-12">
    <div class="content">
      <h3>Card Title</h3>
      <p>
        Vivamus conubia Eleifend ridiculus pretium vivamus aenean. Et dolor rhoncus sapien curabitur a cum. Accumsan sem habitant. Tristique natoque tristique semper euismod porta mattis cum phasellus amet dapibus tincidunt nam, semper cum condimentum. Urna ipsum. Tortor. Consectetuer pharetra dis sociosqu.
      </p>
    </div>
  </div>
  <div class="express-card col-lg-4 col-md-4 col-sm-6 col-xs-12">
    <div class="content">
      <h3>Card Title</h3>
      <p>
        Vivamus conubia Eleifend ridiculus pretium vivamus aenean. Et dolor rhoncus sapien curabitur a cum. Accumsan sem habitant. Tristique natoque tristique semper euismod porta mattis cum phasellus amet dapibus tincidunt nam, semper cum condimentum. Urna ipsum. Tortor. Consectetuer pharetra dis sociosqu.
      </p>
    </div>
  </div>
  <div class="express-card col-lg-4 col-md-4 col-sm-6 col-xs-12">
    <div class="content">
      <h3>Card Title</h3>
      <p>
        Potenti duis. Ligula pellentesque. Dis erat vivamus habitasse conubia dictum proin cursus sollicitudin vehicula. Nullam velit enim inceptos morbi pharetra. Est urna faucibus ipsum, nisi inceptos curae; fermentum nunc facilisi conubia urna fermentum. Eros. Rutrum dictumst inceptos at non lorem.
      </p>
    </div>
  </div>
</div>
```

## Select Links

Provide a user with a list of choices/destinations.

### Sample Markup

(The ID attribute on the UL is required)

```
<div class="express-select-links">
  <button>Select One</button>
  <ul id="express-cache-select-options">
    <li>
      <a href="#">Choice Number One</a>
    </li>
    <li>
      <a href="#">Choice Number Two</a>
    </li>
    <li>
      <a href="#">Choice Number Three</a>
    </li>
    <li>
      <a href="#">Choice Number Four</a>
    </li>
  </ul>
</div>
```
