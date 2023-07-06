#!/bin/bash

# create env variable. Ensure the ip is changed to represent the correct db vm
export DB_HOST=mongodb://172.31.28.98:27017/posts
printenv DB_HOST
echo "env variable created"


# Install dependencies - checks if the env variable is there
cd sparta_app/app
npm install
echo "Npm install success"

# Clearing and seeding db
echo "Clearing and seeding database..."
node seeds/seed.js
echo " --> Done!"

# run test app in the background
pm2 start app.js
