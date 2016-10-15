# No sudo in your npm install -g

You can also fix the permissions so that you don't have to use sudo. Take a look at this npm documentation:

https://docs.npmjs.com/getting-started/fixing-npm-permissions#option-1-change-the-permission-to-npms-default-directory

In fish shell:

`sudo chown -R (whoami) (npm config get prefix)/{lib/node_modules,bin,share} `
