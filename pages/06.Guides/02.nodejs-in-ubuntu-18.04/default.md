---
title: 'Installing NodeJS in Ubuntu 18.04'
published: true
date: '06-08-2019 03:32'
visible: true
hero_classes: ''
hero_image: ''
content:
    items: '- ''@self.children'''
    limit: '5'
    order:
        by: date
        dir: desc
    pagination: '1'
    url_taxonomy_filters: '1'
---

#Install NodeJS on Ubuntu 18.04

NodeJS has quickly become an extremely prevelent technology for hosting dynamic applications on the internet. It allows the expansive functionality and power of JavaScript to run as a back-end language, opening the doors to an ever-expanding world of programming options. In this tutorial, we'll show you how to install NodeJS and get started with a NodeJS App on your VPS running Ubuntu 18.04. 

##What is NodeJS?

[NodeJS](https://nodejs.org/en/about/) is an asynchronous, event-driven JavaScript runtime environment. The primary benefit of using NodeJS as your back-end platform is its ability to incorporate concurrent connections without over-utilizing I/O operations, thus saving a lot of overhead in CPU power and allowing much greater scalability. Additionally, it's fully compatible with all your favorite JavaScript framworks, libraries and extensions, such as [React](https://reactjs.org), [JQuery](https://jquery.com/), [Angular](https://expressjs.com/), and more. 

##Prerequisites

This guide will assume the following:

- Ubuntu 18.04 server (although desktop should still work)
- Non-root user
- General understanding of Command Line tools

##Let's Get Started

To begin, let's make sure the operating system is up-to-date. 

```bash
sudo apt update && sudo apt upgrade -y
```

If you get presented with any GUI-style prompts, the defaults are fine 99% of the time. 

Next, we'll want to install NodeJS. The best way to install depends on desired outcome, but I'll touch on two of the most common. 

###Install Using Apt

Ubuntu 18.04 apt repositories include a relatively recent version of NodeJS that can be installed using apt directly. 

```bash
sudo apt install -y nodejs
```

This will also install `npm`, the Node Package Manager. `npm` is used to install node modules, frameworks, apps, etc. You can now test the install by passing the *version* flag. 

```bash
node -v
v8.10.0
```

Testing `npm`'s version as well: 

```bash
npm -v
3.5.2
```

This is enough to begin working with NodeJS. Some may wish to have a newer version available, or have more control over the version in general. That's where node version manager comes into play. 

###Installing using Node Version Manager

Some prefer to have more control over the version installed, and even in use at any given time Node Version Manager, or `nvm` as referenced in commands, provides that control. Let's check out that method. 

First, we have to actually install `nvm`. The best way to install nvm is from the source. You can find the latest install commands for nvm [here](https://github.com/nvm-sh/nvm), which can be used to both install *and* update your `nvm` instance.

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

The install script will run, asking you to close and reopen your terminal to enable nvm. Do so, after which you can check to make sure the command works, and see what `nvm` version is currently installed. 

```bash
nvm --version
0.34.0
```

I highly encourage you to read through the help text of `nvm` (run ```nvm --help```), as it provides functionality that many will find extraordinarily useful. However, for now, let's setup the latest version of NodeJS. 

```bash 
nvm install --lts
```

This is a simple command to tell nvm to install the latest version of the current LTS (long-term support) version of NodeJS. If you have not previously used `nvm` to install a NodeJS version, this command will furthermore setup the "default" alias to this version. If you wish to install a specific version, you can specify the version you desire, as well. 

```bash
nvm install 10.10.0
```

Once you have the version you want installed, use `nvm` to switch to that version as the actively used version. 

```bash
nvm use 10.10.0 
Now using node v10.10.0 (npm v6.4.1)
```

or

```bash
nvm use default
Now using node v10.16.1 (npm v6.9.0)
```

##Getting Started using NodeJS

Fantastic! We're on our way, now! If you've made it this far, you've likely already got an idea for what you're planning to use NodeJS for, so get to it! Your NodeJS framework is ready to run your app. Here's a demo "hello world" app, as displayed on the NodeJS [getting started](https://nodejs.org/en/docs/guides/getting-started-guide/) page. Replace **SERVER_IP_ADDRESS** with your server's public-facing IP Address, or if you're running this on a local desktop version of Ubuntu 18.04, you can use "127.0.0.1" as depicted on the getting started guide. 

```js
const http = require('http');

const hostname = 'SERVER_IP_ADDRESS';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

Save this as `app.js` in a folder on your NodeJS host, then run: 

```bash
node app.js
Server running at http://SERVER_IP_ADDRESS:3000/
```

You should now be able to navigate to your VPS IP Address on port 3000 and view the text displayed. 

![https://shr.ggez.me/06-08-e4cb.png](https://shr.ggez.me/06-08-e4cb.png)

Now get started! Your app eagerly awaits its creation. 