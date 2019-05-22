# hughpearse.github.io.admin
## Administrative project for my blog

Install Node Version Manager (Nvm)

`sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash`

Install Node.js with Nvm

`sudo nvm install stable`

Install Hexo with Node.Js Package Manager (Npm)

`sudo npm install -g hexo`

Initialize Hexo project

`git clone https://github.com/hughpearse/hughpearse.github.io.admin.git`

Navigate to folder

`cd hughpearse.github.io.admin`

Download Hexo Plugins

`npm install`

Build and run locally

`hexo clean; hexo generate; hexo server`

Then in your browser you can visit

* http://localhost:4000/
* http://localhost:4000/admin/

Deploy/push to remote sites

`hexo deploy`

