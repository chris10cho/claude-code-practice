# Unit Converter

A single-page converter for torque, speed, rotational speed, force, and pressure. One HTML file, no libraries, no build step.

## Use it

Open `index.html` in a browser. Pick a category, type a value, choose the from/to units. The table below the result shows the value in every unit of that category.

## How it works

Each unit stores one factor to its category's base unit (N·m, m/s, rad/s, N, Pa). Converting is `value × from_factor ÷ to_factor`.

## Adding a unit

Add a line to the `CATEGORIES` object in `index.html`:

```js
"kgf": 9.80665,   // 1 kgf = 9.80665 N
```

## Notes

- Rotational speed (rpm, rad/s, deg/s, Hz) is its own tab because it can't be converted to linear speed without a radius.
- `inH₂O` uses the 4 °C water value (249.08891 Pa).
- kgf and kgf·cm use standard gravity (9.80665 m/s²).
