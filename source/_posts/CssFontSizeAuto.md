# 响应式类型可通过可组合的 CSS 实用程序进行扩展

```css
:root {
  --min-size: 12;
  --max-size: 18;
  --container-min: 320;
  --container-max: 2400;
}

/* Setup size calculation for all max utilities */
.h1-max, .h2-max, .h3-max, .h4-max, .h5-max, .h6-max, .h7-max, .h8-max {
  --font-size: calc(var(--min-size) * 1px + (var(--max-size) - var(--min-size)) * (100cqw - var(--container-min) * 1px) / (var(--container-max) - var(--container-min)));
  font-size: clamp(var(--min-size) * 1px, var(--font-size), var(--max-size) * 1px);
}

.h1-max { --max-size: 128; }
.h2-max { --max-size: 96; }
.h3-max { --max-size: 64; }
.h4-max { --max-size: 48; }
.h5-max { --max-size: 32; }
.h6-max { --max-size: 24; }
.h7-max { --max-size: 16; }
.h8-max { --max-size: 12; }

.h1-min { --min-size: 128; }
.h2-min { --min-size: 96; }
.h3-min { --min-size: 64; }
.h4-min { --min-size: 48; }
.h5-min { --min-size: 32; }
.h6-min { --min-size: 24; }
.h7-min { --min-size: 16; }
.h8-min { --min-size: 12; }
```

```html
<!-- Mix and match as you wish -->
<h1 class="h5-min h1-max">...</h1>
<h2 class="h6-min h2-max">...</h2>
<h3 class="h7-min h3-max">...</h3>
<h4 class="h8-min h4-max">...</h4>
```

来源：

[Responsive type scales with composable CSS utilities](https://tobiasahlin.com/blog/responsive-fluid-css-type-scales/)
[git Demo](https://github.com/tobiasahlin/bendable)
