# React Guide 


## create React project

```bash
npx create-react-app my-app  # npx npm5.2+

# npm command
npm init react-app my-app   # npm 6+

yarn create react-app my-app # yarn 0.25+
```

### React app struct

my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js
    └── setupTests.js

# ISSUE

## vscode jsx 注释失效 
> 由于babel ES6/ES7 插件,换成 Babel javascript

