

disable markdown

settings -> Exteisons -> Prettier -> Disable Languages

Add item -> input `markdown`


or

```json
   "prettier.disableLanguages": [
        "vue",
        "markdown",
    ],
```


format on save
ctrl/cmd + shfit + P -> Open Settings (JSON)
```json
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true
  },
```
