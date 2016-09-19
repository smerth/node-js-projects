# Automation with NPM

This tutorital shows how to set up npm scripts to run grunt command and also pre and post commands

```json
  "scripts": {
    "prestart": "grunt",
    "start": "node app",
    "predev": "grunt",
    "dev": "open http://localhost:3000 & node-dev app & grunt watch"
  }
```

Install
```bash
npm install
```

Run the app
```bash
npm start
```

Run the app in dev mode with grunt watch
```bash
npm run dev
```

You need to use "run" to call an npm script you define (start is a "named script" in npm so it is hardwired into npm and you can use that name and call it without using the "run" keyword.)