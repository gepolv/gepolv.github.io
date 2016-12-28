**create a Heroku account.
make sure you have your credit cared information stored in Heroku. Do not worry. It will not charge you. Because we will need to use mLab's MongoDB.

```

mkdir store
cd store
git clone https://github.com/reactioncommerce/reaction.git
cd reaction
heroku create
heroku apps:rename myteststore --app (current-name)
heroku addons:create mongolab --app myteststore
heroku config:set ROOT_URL=http://myteststore.herokuapp.com
heroku buildpacks:set https://github.com/swrdfish/meteor-buildpack-horse.git
git push heroku master

```

Note: change "mytestshop" to a different name.

There is a better buildpack for Heroku:
https://github.com/AdmitHub/meteor-buildpack-horse

then you can access: 



other resource:
http://www.growthux.com/ux-html-css-js-growth-hack/installing-meteor-on-heroku


