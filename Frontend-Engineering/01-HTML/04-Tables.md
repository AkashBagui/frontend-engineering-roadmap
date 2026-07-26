# HTML Tables

## Table Structure

HTML tables display data in rows and columns using a grid-like structure.

```
 ┌──────────────────────────────────────────────────────┐
 │                    <table>                            │
 │  ┌──────────────────────────────────────────────────┐ │
 │  │  <thead>     Header Row                          │ │
 │  │  ┌──────┬─────────┬──────┬─────────┬──────────┐ │ │
 │  │  │ Name │  Email  │ Role │ Status  │ Actions  │ │ │
 │  │  └──────┴─────────┴──────┴─────────┴──────────┘ │ │
 │  ├──────────────────────────────────────────────────┤ │
 │  │  <tbody>    Data Rows                            │ │
 │  │  ┌──────┬─────────┬──────┬─────────┬──────────┐ │ │
 │  │  │ John │ j@e.com │ Dev  │ Active  │ Edit Del │ │ │
 │  │  ├──────┼─────────┼──────┼─────────┼──────────┤ │ │
 │  │  │ Jane │ j@ne.com│ PM   │ Active  │ Edit Del │ │ │
 │  │  └──────┴─────────┴──────┴─────────┴──────────┘ │ │
 │  ├──────────────────────────────────────────────────┤ │
 │  │  <tfoot>    Summary Row                          │ │
 │  │  ┌──────┬─────────┬──────┬─────────┬──────────┐ │ │
 │  │  │ Total│ 2 users │  -   │ Active  │    -     │ │ │
 │  │  └──────┴─────────┴──────┴─────────┴──────────┘ │ │
 │  └──────────────────────────────────────────────────┘ │
 └──────────────────────────────────────────────────────┘
```

## Table Elements

| Element | Purpose |
|---------|---------|
| `<table>` | Container for the entire table |
| `<thead>` | Groups header rows (one per table) |
| `<tbody>` | Groups body rows (data) |
| `<tfoot>` | Groups footer rows (summary, totals) |
| `<tr>` | Table row |
| `<th>` | Header cell (bold, centered by default) |
| `<td>` | Data cell |
| `<caption>` | Title/description of the table |
| `<colgroup>` | Groups columns for styling |
| `<col>` | Column properties within `<colgroup>` |

## Basic Table

```html
<table>
    <caption>Employee Directory</caption>
    <thead>
        <tr>
            <th scope="col">Name</th>
            <th scope="col">Email</th>
            <th scope="col">Department</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>John Doe</td>
            <td>john@example.com</td>
            <td>Engineering</td>
        </tr>
        <tr>
            <td>Jane Smith</td>
            <td>jane@example.com</td>
            <td>Marketing</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td colspan="3">Total Employees: 2</td>
        </tr>
    </tfoot>
</table>
```

## Spanning Columns and Rows

### Colspan (merge columns)

```html
<table>
    <tr>
        <th colspan="2">Full Name</th>
        <th>Age</th>
    </tr>
    <tr>
        <td>John</td>
        <td>Doe</td>
        <td>30</td>
    </tr>
</table>
```

```
 ┌─────────────┬─────────────┬─────┐
 │   Full Name (colspan=2)   │ Age │
 ├─────────────┼─────────────┼─────┤
 │ John        │ Doe         │ 30  │
 └─────────────┴─────────────┴─────┘
```

### Rowspan (merge rows)

```html
<table>
    <tr>
        <th>Name</th>
        <td>John Doe</td>
        <td rowspan="2">Active</td>
    </tr>
    <tr>
        <th>Name</th>
        <td>Jane Smith</td>
    </tr>
</table>
```

```
 ┌──────┬─────────────┬────────┐
 │ Name │ John Doe    │ Active │
 ├──────┼─────────────┤ (row-  │
 │ Name │ Jane Smith  │ span=2)│
 └──────┴─────────────┴────────┘
```

## Complex Table Example

```html
<table>
    <caption>2026 Quarterly Sales Report ($)</caption>
    <thead>
        <tr>
            <th rowspan="2">Product</th>
            <th colspan="4">Quarterly Sales</th>
            <th rowspan="2">Total</th>
        </tr>
        <tr>
            <th scope="col">Q1</th>
            <th scope="col">Q2</th>
            <th scope="col">Q3</th>
            <th scope="col">Q4</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">Widget A</th>
            <td>12,000</td>
            <td>15,000</td>
            <td>18,000</td>
            <td>22,000</td>
            <td>67,000</td>
        </tr>
        <tr>
            <th scope="row">Widget B</th>
            <td>8,000</td>
            <td>9,500</td>
            <td>11,000</td>
            <td>14,000</td>
            <td>42,500</td>
        </tr>
        <tr>
            <th scope="row">Widget C</th>
            <td>5,000</td>
            <td>6,000</td>
            <td>7,500</td>
            <td>9,000</td>
            <td>27,500</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <th scope="row">Total</th>
            <td>25,000</td>
            <td>30,500</td>
            <td>36,500</td>
            <td>45,000</td>
            <td>137,000</td>
        </tr>
    </tfoot>
</table>
```

## The `scope` Attribute

The `scope` attribute associates header cells with data cells, critical for accessibility.

```html
<th scope="col">    <!-- Header for a column -->
<th scope="row">    <!-- Header for a row -->
<th scope="colgroup"> <!-- Header for a column group -->
<th scope="rowgroup"> <!-- Header for a row group -->
```

### Visual: How `scope` works

```
                 scope="col"   scope="col"   scope="col"
                    │              │              │
                    ▼              ▼              ▼
             ┌────────────┬────────────┬────────────────┐
 scope="row"→│  Product   │  Price     │  Quantity      │
             ├────────────┼────────────┼────────────────┤
 scope="row"→│  Widget    │  $10       │  5             │
             └────────────┴────────────┴────────────────┘
```

## Styling Tables

### Basic CSS

```css
table {
    width: 100%;
    border-collapse: collapse;
    font-family: Arial, sans-serif;
}

th, td {
    border: 1px solid #ddd;
    padding: 12px 16px;
    text-align: left;
}

th {
    background-color: #f4f4f4;
    font-weight: 700;
}

tr:nth-child(even) {
    background-color: #f9f9f9;
}

tr:hover {
    background-color: #e8f4f8;
}

caption {
    caption-side: bottom;
    font-style: italic;
    margin-top: 8px;
}
```

### Responsive Tables

```css
/* Horizontal scroll on small screens */
.table-container {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
}
```

```html
<div class="table-container">
    <table>
        <!-- table content -->
    </table>
</div>
```

### Striped Table Pattern

```html
<table class="striped zebra">
    <!-- rows with alternating colors -->
</table>
```

```css
.striped tbody tr:nth-child(odd) {
    background-color: #f5f5f5;
}
```

## Accessibility in Tables

### Do's and Don'ts

| ✅ Do | ❌ Don't |
|-------|----------|
| Use `<th>` for header cells | Use `<td>` for headers |
| Use `scope` attribute | Skip `scope` ("browsers will figure it out") |
| Use `<caption>` for table purpose | Use empty `<th>` elements |
| Use `<thead>`/`<tbody>`/`<tfoot>` | Use tables for layout |
| Add `aria-label` if caption is not visible | Nest tables inside tables |

### Screen Reader Behavior

- `<caption>` is read before the table content
- `scope="col"` announces the column header when navigating down
- `scope="row"` announces the row header when navigating across
- Without proper headers, screen readers just say "data" for each cell

### Layout Tables (Legacy)

> **⚠️ Never use tables for page layout.** Use CSS Grid or Flexbox instead. Table layouts are:
> - Not responsive
> - Hard to maintain
> - Inaccessible (screen readers read linearly)

## Real-World Example: Pricing Table

```html
<table>
    <caption>Subscription Plans</caption>
    <thead>
        <tr>
            <th scope="col">Features</th>
            <th scope="col">Basic</th>
            <th scope="col">Pro</th>
            <th scope="col">Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">Price</th>
            <td>$9/mo</td>
            <td>$29/mo</td>
            <td>$99/mo</td>
        </tr>
        <tr>
            <th scope="row">Users</th>
            <td>1</td>
            <td>5</td>
            <td>Unlimited</td>
        </tr>
        <tr>
            <th scope="row">Storage</th>
            <td>5GB</td>
            <td>50GB</td>
            <td>500GB</td>
        </tr>
        <tr>
            <th scope="row">Support</th>
            <td>Email</td>
            <td>Email + Chat</td>
            <td>24/7 Phone</td>
        </tr>
    </tbody>
</table>
```

## Key Takeaways

1. Use `<thead>`, `<tbody>`, `<tfoot>` for semantic grouping.
2. Always use `<th>` for headers with `scope` attributes.
3. Use `colspan` and `rowspan` for spanning cells.
4. Use `<caption>` to describe the table.
5. Use `border-collapse: collapse` in CSS for clean borders.
6. Wrap tables in a scrollable container for mobile.

---

**Next:** [05-Forms.md](05-Forms.md) — HTML forms and input elements.
