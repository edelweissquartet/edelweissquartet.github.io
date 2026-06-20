# Edelweiss Quartet — Website

Website for the [Edelweiss Jazz Quartet](https://edelweissquartet.github.io), a Paris-based jazz quartet blending romantic lyricism with rock and jazz energy.

## Live site

[https://edelweissquartet.github.io](https://edelweissquartet.github.io)

## Structure

```
├── index.html          # Single-page site
├── css/                # Stylesheets (Bootstrap, Supersized, custom)
├── js/                 # Scripts (jQuery, Supersized slider, custom)
├── img/
│   ├── slider-images/  # Full-screen background photos
│   ├── favicons/
│   └── ...
└── sitemap.xml
```

## Development

Static site — no build step required. Open `index.html` directly in a browser or serve locally:

```bash
npx serve .
# or
python3 -m http.server
```

## Deployment

Hosted on [GitHub Pages](https://pages.github.com). Every push to `main` deploys automatically.

## Credits

- Template: [Brushed](https://themes.alessioatzeni.com/html/brushed/) by Alessio Atzeni
- Photos: L. Guilpain, J.P. Scortani-Dohr, Y. De Langhe, S. Ghez
